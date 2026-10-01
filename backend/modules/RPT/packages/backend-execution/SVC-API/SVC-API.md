<!-- source: PHASE:SVC-API -->
<!-- traces: DBF-RPT-001, DBF-RPT-002, DBF-RPT-003, DBF-RPT-004, DBF-RPT-005, DBF-RPT-006, DBF-RPT-007, DBF-RPT-008, DBF-RPT-009, DBF-RPT-010, DBF-RPT-011, DBF-RPT-012, DBF-RPT-013, DBF-RPT-014, DBF-RPT-015, DBF-RPT-016, DBF-RPT-017, DBF-RPT-018, DBF-RPT-022, DBF-RPT-023, DBF-RPT-024, DBF-RPT-025, DBF-RPT-026, DBF-RPT-031, DBF-RPT-032, DBF-RPT-033, DBF-RPT-034, DBF-RPT-035, DBF-RPT-036, DBF-RPT-041, DBF-RPT-042, DBF-RPT-043, REQ-RPT-001, REQ-RPT-005, REQ-RPT-008, REQ-RPT-018, REQ-RPT-021, REQ-RPT-022, REQ-RPT-023, REQ-RPT-024, REQ-RPT-025, REQ-RPT-026, REQ-RPT-027, REQ-RPT-028, REQ-RPT-029, REQ-RPT-030, REQ-RPT-031, REQ-RPT-032, REQ-RPT-040, REQ-RPT-041, REQ-RPT-043, REQ-RPT-050 -->
<!-- PHASE:SVC-API:START traces=DBF-RPT-001,DBF-RPT-002,DBF-RPT-003,DBF-RPT-004,DBF-RPT-005,DBF-RPT-006,DBF-RPT-007,DBF-RPT-008,DBF-RPT-009,DBF-RPT-010,DBF-RPT-011,DBF-RPT-012,DBF-RPT-013,DBF-RPT-014,DBF-RPT-015,DBF-RPT-016,DBF-RPT-017,DBF-RPT-018,DBF-RPT-022,DBF-RPT-023,DBF-RPT-024,DBF-RPT-025,DBF-RPT-026,DBF-RPT-031,DBF-RPT-032,DBF-RPT-033,DBF-RPT-034,DBF-RPT-035,DBF-RPT-036,DBF-RPT-041,DBF-RPT-042,DBF-RPT-043,REQ-RPT-001,REQ-RPT-005,REQ-RPT-008,REQ-RPT-018,REQ-RPT-021,REQ-RPT-022,REQ-RPT-023,REQ-RPT-028,REQ-RPT-032,REQ-RPT-040,REQ-RPT-043 -->
## PHASE SVC-API — SVC+API

### Service layer — the Check result port implementation (`CheckRunCommandService`, `CheckRunQueryService`)
All writes are READ_WRITE in the caller's transaction (ADR-RPT-012); timestamps are those the Check Engine hands over, except createdAt / updatedAt (DEFAULT SYSTIMESTAMP; updatedAt set to SYSTIMESTAMP on every UPDATE).

**`createCheckRun(serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt)` → checkId** (port operation; REQ-RPT-001 … REQ-RPT-004, REQ-RPT-015, REQ-RPT-030)
  1. RULE-RPT-001 — any value absent or blank → `CheckRunIncompleteException` (RPT-400-CHECK-RUN-INCOMPLETE) "The Check run was not stored: {field} is missing." (REQ-RPT-003).
  2. RULE-RPT-006 — a code outside its enum (status not RUNNING / AWAITING_DOCUMENTS at creation counts as outside) → `UnknownCodeException` (RPT-422-UNKNOWN-CODE) "Not stored: `{value}` is not a code of {lookupKey}." (REQ-RPT-015).
  3. RULE-RPT-002 — status vs fetch mode → `InitialStatusMismatchException` (RPT-422-INITIAL-STATUS-MISMATCH) with the RULE message (REQ-RPT-004).
  4. Persist: INSERT INTO RPT_CHECK_RUN (SERVICE_CODE DBF-RPT-002, VERSION_NUMBER DBF-RPT-003, FETCH_MODE DBF-RPT-004, REQUEST_NUMBER DBF-RPT-005, EMPLOYEE_ID DBF-RPT-006 — both exactly as received, REQ-RPT-002; CHECK_STATUS DBF-RPT-007, STARTED_AT DBF-RPT-008); CHECK_RUN_ID DBF-RPT-001 by the identity clause; every result and decision column NULL; return CHECK_RUN_ID as checkId. A second Check of the same request is simply another row — nothing is looked up or copied (REQ-RPT-030).
  - Concurrency: NONE — the identifier is allocated by the identity clause; no read precedes the insert.

**`markRunning(checkId, runningSince)`** (port operation; REQ-RPT-005 … REQ-RPT-007)
  1. UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'RUNNING' (DBF-RPT-007), RUNNING_SINCE = COALESCE(RUNNING_SINCE, :runningSince) (DBF-RPT-009), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING').
  2. 0 rows → read QR-RPT-001: no row → `CheckNotFoundException` (RPT-404-CHECK-NOT-FOUND) "Check {checkId} was not found." (REQ-RPT-007); row ended → `CheckEndedException` (RPT-409-CHECK-ENDED) "Check {checkId} has already ended; its status cannot change." (RULE-RPT-003, REQ-RPT-006).
  - Concurrency: the conditional UPDATE is the guard — a concurrent end makes it update 0 rows.

**`completeCheck(checkId, overallStatus, findings, documentOutcomes, unreadQueries, metadata)`** (port operation; REQ-RPT-008 … REQ-RPT-017, REQ-RPT-019, REQ-RPT-020, REQ-RPT-053)
  1. Load the Check Run (QR-RPT-001) with a locking read `FOR UPDATE`; none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-007); COMPLETED / FAILED → RPT-409-CHECK-ENDED (REQ-RPT-020); AWAITING_DOCUMENTS → `CheckNotRunningException` (RPT-409-CHECK-NOT-RUNNING) "Check {checkId} is not running; it cannot be completed." (RULE-RPT-003, REQ-RPT-006).
  2. Validate the whole report in memory, in this order, before any write (ADR-RPT-012): RULE-RPT-006 codes (REQ-RPT-015) → RULE-RPT-004 metadata vs the loaded row (`MetadataMismatchException`, RPT-422-METADATA-MISMATCH; REQ-RPT-013) → RULE-RPT-007 every finding complete (`FindingIncompleteException`, RPT-422-FINDING-INCOMPLETE; REQ-RPT-016) and every unread query with name and detail → RULE-RPT-008 reasons (`DocumentReasonMismatchException`, RPT-422-DOCUMENT-REASON-MISMATCH; REQ-RPT-017) → RULE-RPT-005 COMPLIANT guard (`CompliantNotVerifiedException`, RPT-422-COMPLIANT-NOT-VERIFIED; REQ-RPT-014). Messages exactly as the DATA-DOM rules state them.
  3. Persist in this order: UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'COMPLETED' (DBF-RPT-007), OVERALL_STATUS (DBF-RPT-011), COMPARISON_MODEL (DBF-RPT-012), ENDED_AT (DBF-RPT-010), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS = 'RUNNING'; INSERT one RPT_FINDING per finding (POSITION DBF-RPT-022 = index from 1, CONDITION_TEXT DBF-RPT-023, FINDING_OUTCOME DBF-RPT-024, EVIDENCE DBF-RPT-025, NOTE DBF-RPT-026, CHECK_RUN_ID DBF-RPT-027); one RPT_CHECK_DOCUMENT per document outcome (POSITION DBF-RPT-031, DOCUMENT_TYPE DBF-RPT-032, SOURCE_MODE DBF-RPT-033, READ_STATUS DBF-RPT-034, UNREADABLE_REASON DBF-RPT-035, DETAIL DBF-RPT-036, CHECK_RUN_ID DBF-RPT-037); one RPT_UNREAD_QUERY per unread query (POSITION DBF-RPT-041, QUERY_NAME DBF-RPT-042, DETAIL DBF-RPT-043, CHECK_RUN_ID DBF-RPT-044) — order kept as received (REQ-RPT-010, REQ-RPT-011, REQ-RPT-012). No document content and no query rows exist in the carried values or in any column (REQ-RPT-019).
  4. A database failure during step 3 → the exception propagates (`ReportNotStoredException`, RPT-500-REPORT-NOT-STORED) and the caller's transaction rolls back whole: no part of the report remains, OVERALL_STATUS stays NULL and the Check stays RUNNING; the exception reaches the Check Engine, which then fails the Check INTERNAL_ERROR (REQ-RPT-009, AC-RPT-062).
  - Concurrency: the `FOR UPDATE` read and the conditional UPDATE serialise a completion against a concurrent fail — the second finds the row ended and is refused with RPT-409-CHECK-ENDED.

**`failCheck(checkId, failureReason, detail, endedAt)`** (port operation; REQ-RPT-018, REQ-RPT-051, REQ-RPT-053)
  1. RULE-RPT-010 — reason, detail (not blank) and endedAt present, endedAt ≥ STARTED_AT → else `FailureIncompleteException` (RPT-400-FAILURE-INCOMPLETE) "The failure of Check {checkId} was not stored: {field} is missing." (REQ-RPT-051); RULE-RPT-006 on the reason (REQ-RPT-015).
  2. UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'FAILED' (DBF-RPT-007), FAILURE_REASON (DBF-RPT-013), FAILURE_DETAIL (DBF-RPT-014), ENDED_AT (DBF-RPT-010), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING'); OVERALL_STATUS and COMPARISON_MODEL stay NULL and no Finding is written (REQ-RPT-018).
  3. 0 rows → as markRunning step 2 (RPT-404-CHECK-NOT-FOUND / RPT-409-CHECK-ENDED).
  - Concurrency: the conditional UPDATE is the guard.

**`getCheck(checkId)` → {checkId, status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt}** (port operation; REQ-RPT-021) — READ_ONLY; QR-RPT-001 projected; none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-007).

**`listUnfinishedChecks()` → list of {checkId, status, startedAt}** (port operation; REQ-RPT-022) — READ_ONLY; SELECT CHECK_RUN_ID, CHECK_STATUS, STARTED_AT FROM RPT_CHECK_RUN WHERE CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING') ORDER BY STARTED_AT (IDX_RPT_CHECK_RUN_CHECK_STATUS); none → empty list.

### Service layer — the in-process interface `ReportStore` offered to Host Integration (contract-rpt.md)
Injected into INT (profile `module_interface: in_process`). Value objects are Java records with unmodifiable lists. RPT never calls an Approval API and never sets a decision on its own (REQ-RPT-037; AIAS-4) — there is no outbound HTTP client in RPT.

**`readCheck(checkId)` → CheckReport** — the same service method that serves API-RPT-001 (`CheckReportQueryService.read`).
  - Honours: CON-RPT-003

**`listChecksOfRequest(serviceCode, requestNumber)` → ChecksOfRequest** — the same service method that serves API-RPT-002.
  - Honours: CON-RPT-004

**`readDecisionAgreement(serviceCode)` → list of AgreementRow** — the same service method that serves API-RPT-003.
  - Honours: CON-RPT-005

**`recordDecision(checkId, employeeDecision, decidedBy, approvalApiExecuted)` → RecordedDecision {checkId, employeeDecision, decidedBy, decidedAt, approvalApiExecuted}** — READ_WRITE, its own transaction (REQUIRED from INT). (REQ-RPT-032 … REQ-RPT-039)
  - Honours: CON-RPT-006
  1. RULE-RPT-013 — decision not APPROVED / REJECTED, decidedBy blank, or approvalApiExecuted absent → `DecisionIncompleteException` (RPT-400-DECISION-INCOMPLETE) with the RULE message (REQ-RPT-035).
  2. RULE-RPT-014 — REJECTED with approvalApiExecuted = true → `ApprovalFlagOnRejectionException` (RPT-422-APPROVAL-FLAG-ON-REJECTION) "The decision was not recorded: only an APPROVED decision is executed through the Approval API." (REQ-RPT-039).
  3. Persist: UPDATE RPT_CHECK_RUN SET EMPLOYEE_DECISION (DBF-RPT-015), DECIDED_BY (DBF-RPT-016 — exactly as received, REQ-RPT-002), DECIDED_AT = SYSTIMESTAMP (DBF-RPT-017, ADR-RPT-009), APPROVAL_API_EXECUTED (DBF-RPT-018, REQ-RPT-036), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS = 'COMPLETED' AND EMPLOYEE_DECISION IS NULL. Nothing else of the row or of its report changes (REQ-RPT-020).
  4. 0 rows → read QR-RPT-001: none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-038); a decision present → `DecisionAlreadyRecordedException` (RPT-409-DECISION-ALREADY-RECORDED) "Check {checkId} already has an Employee Decision." (RULE-RPT-011, REQ-RPT-033); status not COMPLETED → `CheckNotCompletedException` (RPT-409-CHECK-NOT-COMPLETED) "Check {checkId} is not completed; a decision can only be recorded on a completed Check." (RULE-RPT-012, REQ-RPT-034).
  - Concurrency: the conditional UPDATE is the guard — two simultaneous decisions both pass validation, the database lets exactly one update the row; the other updates 0 rows and is refused with RPT-409-DECISION-ALREADY-RECORDED.

### Service layer — the scheduled purge (`ReportPurgeService`, ADR-RPT-004, ADR-RPT-010)
`@Scheduled(cron = aias.reports.purge-schedule)`; no HTTP API, no caller (REQ-RPT-042 … REQ-RPT-046, REQ-RPT-052, REQ-RPT-054).
  1. retention-days absent or < 1 → log "Report purge skipped: no valid report retention period is configured." and return; nothing deleted (REQ-RPT-044).
  2. cutOff = now − retention-days; SELECT CHECK_RUN_ID FROM RPT_CHECK_RUN WHERE CHECK_STATUS IN ('COMPLETED', 'FAILED') AND ENDED_AT < :cutOff (IDX_RPT_CHECK_RUN_ENDED_AT) — AWAITING_DOCUMENTS and RUNNING rows are never selected, whatever their age (REQ-RPT-045); rows ended within the period are never selected (REQ-RPT-042).
  3. For each id, in its own transaction (`REQUIRES_NEW`): DELETE FROM RPT_CHECK_RUN WHERE CHECK_RUN_ID = :id AND CHECK_STATUS IN ('COMPLETED', 'FAILED') AND ENDED_AT < :cutOff — FK_RPT_FINDING_RPT_CHECK_RUN, FK_RPT_CHECK_DOCUMENT_RPT_CHECK_RUN and FK_RPT_UNREAD_QUERY_RPT_CHECK_RUN cascade the Findings, Check Documents and Unread Queries; the Employee Decision is a column of the row (REQ-RPT-043). A failure rolls back that Check Run only, which stays whole (REQ-RPT-052); before moving to the next id the purge logs at WARN "Report purge kept Check run {checkId}: its deletion failed ({cause})." — {cause} is the database failure's message, never row content (REQ-RPT-054, ADR-RPT-020) — and continues; a kept Check run is not counted.
  4. Log "Report purge deleted {n} Check runs ended before {cutOff}." (REQ-RPT-046).
  - Concurrency: two instances purging at once delete the same row at most once; the second DELETE finds 0 rows and counts nothing.

### In-process rejection codes (typed exceptions of the result port and of `ReportStore` — ADR-RPT-012, ADR-RPT-013)
| Code | Rule / REQ | Exception | Message en | Message ar |
|---|---|---|---|---|
| RPT-400-CHECK-RUN-INCOMPLETE | RULE-RPT-001 | CheckRunIncompleteException | The Check run was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-422-INITIAL-STATUS-MISMATCH | RULE-RPT-002 | InitialStatusMismatchException | The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual. / The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-ENDED | RULE-RPT-003 | CheckEndedException | Check {checkId} has already ended; its status cannot change. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-NOT-RUNNING | RULE-RPT-003 | CheckNotRunningException | Check {checkId} is not running; it cannot be completed. | PENDING ADR-RPT-013 |
| RPT-404-CHECK-NOT-FOUND | REQ-RPT-007, REQ-RPT-038 | CheckNotFoundException | Check {checkId} was not found. | PENDING ADR-RPT-013 |
| RPT-422-METADATA-MISMATCH | RULE-RPT-004 | MetadataMismatchException | The report of Check {checkId} was not stored: its metadata {field} {value} differs from the Check run ({stored}). / The report of Check {checkId} was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-422-COMPLIANT-NOT-VERIFIED | RULE-RPT-005 | CompliantNotVerifiedException | The report of Check {checkId} was not stored: COMPLIANT needs every finding SATISFIED and every service query read. | PENDING ADR-RPT-013 |
| RPT-422-UNKNOWN-CODE | RULE-RPT-006 | UnknownCodeException | Not stored: `{value}` is not a code of {lookupKey}. | PENDING ADR-RPT-013 |
| RPT-422-FINDING-INCOMPLETE | RULE-RPT-007 | FindingIncompleteException | The report of Check {checkId} was not stored: finding {position} has no {field}. | PENDING ADR-RPT-013 |
| RPT-422-DOCUMENT-REASON-MISMATCH | RULE-RPT-008 | DocumentReasonMismatchException | The report of Check {checkId} was not stored: document {position} is UNREADABLE without a reason. / The report of Check {checkId} was not stored: document {position} is {readStatus} and cannot carry a reason. | PENDING ADR-RPT-013 |
| RPT-400-FAILURE-INCOMPLETE | RULE-RPT-010 | FailureIncompleteException | The failure of Check {checkId} was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-409-DECISION-ALREADY-RECORDED | RULE-RPT-011 | DecisionAlreadyRecordedException | Check {checkId} already has an Employee Decision. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-NOT-COMPLETED | RULE-RPT-012 | CheckNotCompletedException | Check {checkId} is not completed; a decision can only be recorded on a completed Check. | PENDING ADR-RPT-013 |
| RPT-400-DECISION-INCOMPLETE | RULE-RPT-013 | DecisionIncompleteException | The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. / The decision was not recorded: say whether it was executed through the Approval API. | PENDING ADR-RPT-013 |
| RPT-422-APPROVAL-FLAG-ON-REJECTION | RULE-RPT-014 | ApprovalFlagOnRejectionException | The decision was not recorded: only an APPROVED decision is executed through the Approval API. | PENDING ADR-RPT-013 |
| RPT-500-REPORT-NOT-STORED | REQ-RPT-009 | ReportNotStoredException | The report of Check {checkId} was not stored. | PENDING ADR-RPT-013 |

### HTTP endpoints
Controller `CheckReportController` → services `CheckReportQueryService`, `DecisionAgreementQueryService`. RPT exposes no POST, PUT or DELETE (ADR-RPT-006); every value is returned exactly as stored, as JSON string data — no HTML, no template rendering (REQ-RPT-027).

<!-- API:API-RPT-001:START traces=REQ-RPT-023,REQ-RPT-024,REQ-RPT-025,REQ-RPT-026,REQ-RPT-027,DBF-RPT-001,DBF-RPT-002,DBF-RPT-003,DBF-RPT-004,DBF-RPT-005,DBF-RPT-006,DBF-RPT-007,DBF-RPT-008,DBF-RPT-009,DBF-RPT-010,DBF-RPT-011,DBF-RPT-012,DBF-RPT-013,DBF-RPT-014,DBF-RPT-015,DBF-RPT-016,DBF-RPT-017,DBF-RPT-018,DBF-RPT-022,DBF-RPT-023,DBF-RPT-024,DBF-RPT-025,DBF-RPT-026,DBF-RPT-031,DBF-RPT-032,DBF-RPT-033,DBF-RPT-034,DBF-RPT-035,DBF-RPT-036,DBF-RPT-041,DBF-RPT-042,DBF-RPT-043 -->
### API-RPT-001 — Read a Check and its report
Entity       : ENT-RPT-001
Endpoint     : /api/v1/checks/{checkId}   verb: GET
Layers       : controller CheckReportController → service CheckReportQueryService
Request      : path checkId (DBF-RPT-001, integer int64, required); no query parameter; no body
Response     : 200 · CheckReport {checkId (DBF-RPT-001), status (DBF-RPT-007), serviceCode (DBF-RPT-002), versionNumber (DBF-RPT-003), fetchMode (DBF-RPT-004), requestNumber (DBF-RPT-005), employeeId (DBF-RPT-006), startedAt (DBF-RPT-008), runningSince (DBF-RPT-009), endedAt (DBF-RPT-010), overallStatus (DBF-RPT-011, COMPLETED only), comparisonModel (DBF-RPT-012, COMPLETED only), failureReason (DBF-RPT-013, FAILED only), failureDetail (DBF-RPT-014, FAILED only), findings [FindingView {position (DBF-RPT-022), condition (DBF-RPT-023), outcome (DBF-RPT-024), evidence (DBF-RPT-025), note (DBF-RPT-026)}], documents [DocumentView {position (DBF-RPT-031), documentType (DBF-RPT-032), sourceMode (DBF-RPT-033), readStatus (DBF-RPT-034), unreadableReason (DBF-RPT-035), detail (DBF-RPT-036)}], unreadQueries [UnreadQueryView {position (DBF-RPT-041), queryName (DBF-RPT-042), detail (DBF-RPT-043)}], decision {employeeDecision (DBF-RPT-015), decidedBy (DBF-RPT-016), decidedAt (DBF-RPT-017), approvalApiExecuted (DBF-RPT-018)} or null} · lists empty until COMPLETED · not paginated · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-RPT-013)
Errors       : RPT-400-CHECK-ID-INVALID (400, PLATFORM-STD) · RPT-404-CHECK-NOT-FOUND (404, PLATFORM-STD — REQ-RPT-025; also a purged Check) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate checkId → load the Check Run (QR-RPT-001) → none → RPT-404-CHECK-NOT-FOUND → COMPLETED: load Findings (QR-RPT-002), Check Documents (QR-RPT-003), Unread Queries (QR-RPT-004), each ordered by POSITION → map to CheckReport, each finding one entry with its evidence (REQ-RPT-024); FAILED: reason and detail, no Overall Status (REQ-RPT-026); writes nothing
Repository   : QR-RPT-001, QR-RPT-002, QR-RPT-003, QR-RPT-004 · FIND_ONE / FIND_BY_CRITERIA · join NONE (four single-table reads) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes; the four reads run in one READ_ONLY transaction so a report is read as one consistent state
Security     : none — no permission model, endpoints are open per the SRS (caller authentication and report-viewing rights deferred, raw-idea A2, domain-profile D4; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-003, CON-RPT-001
Covers       : the data this endpoint returns is written only by the in-process result port and decision procedures of this phase, which implement and are listed here for the coverage check: REQ-RPT-001, REQ-RPT-002, REQ-RPT-003, REQ-RPT-004, REQ-RPT-005, REQ-RPT-006, REQ-RPT-007, REQ-RPT-008, REQ-RPT-009, REQ-RPT-010, REQ-RPT-011, REQ-RPT-012, REQ-RPT-013, REQ-RPT-014, REQ-RPT-015, REQ-RPT-016, REQ-RPT-017, REQ-RPT-018, REQ-RPT-019, REQ-RPT-020, REQ-RPT-021, REQ-RPT-022, REQ-RPT-032, REQ-RPT-033, REQ-RPT-034, REQ-RPT-035, REQ-RPT-036, REQ-RPT-037, REQ-RPT-038, REQ-RPT-039, REQ-RPT-051, REQ-RPT-053; the purge (REQ-RPT-042, REQ-RPT-043, REQ-RPT-044, REQ-RPT-045, REQ-RPT-046, REQ-RPT-052, REQ-RPT-054) is why a purged Check answers RPT-404-CHECK-NOT-FOUND; CORE carries REQ-RPT-047, REQ-RPT-048, REQ-RPT-049
<!-- API:API-RPT-001:END -->

<!-- API:API-RPT-002:START traces=REQ-RPT-028,REQ-RPT-029,REQ-RPT-030,REQ-RPT-031,REQ-RPT-050,DBF-RPT-001,DBF-RPT-002,DBF-RPT-005,DBF-RPT-007,DBF-RPT-008,DBF-RPT-010,DBF-RPT-011,DBF-RPT-015 -->
### API-RPT-002 — List the Checks of a request
Entity       : ENT-RPT-001
Endpoint     : /api/v1/checks   verb: GET
Layers       : controller CheckReportController → service CheckReportQueryService
Request      : query serviceCode (DBF-RPT-002, string ≤ 100, required) · query requestNumber (DBF-RPT-005, string ≤ 100, required) — both matched exactly as sent; no body
Response     : 200 · ChecksOfRequest {total (count of every Check of the pair), checks [CheckSummary {checkId (DBF-RPT-001), status (DBF-RPT-007), overallStatus (DBF-RPT-011), startedAt (DBF-RPT-008), endedAt (DBF-RPT-010), employeeDecision (DBF-RPT-015)}] — at most 100, newest first (STARTED_AT DESC, CHECK_RUN_ID DESC)} · not paginated (fixed cap, REQ-RPT-031) · no envelope
Validations  : RULE-RPT-009 — A request's Checks need both keys · trigger: on list Checks of a request · statement: The system shall require a service code and a request number, both present and not blank, to list the Checks of a request. · message (en): Both a service code and a request number are needed to list Checks. · message (ar): PENDING ADR-RPT-013
Errors       : RPT-400-REQUEST-KEYS-MISSING (400, RULE-RPT-009) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate RULE-RPT-009 → newest 100 (QR-RPT-005) and total (QR-RPT-006), both with serviceCode and requestNumber as bound parameters (REQ-RPT-050) → ChecksOfRequest; each Check is its own row, nothing merged across Checks (REQ-RPT-030); writes nothing
Repository   : QR-RPT-005, QR-RPT-006 · FIND_BY_CRITERIA / COUNT · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-004
<!-- API:API-RPT-002:END -->

<!-- API:API-RPT-003:START traces=REQ-RPT-040,REQ-RPT-041,DBF-RPT-002,DBF-RPT-003,DBF-RPT-011,DBF-RPT-015 -->
### API-RPT-003 — Read the decision agreement of a service
Entity       : ENT-RPT-001
Endpoint     : /api/v1/decision-agreement   verb: GET
Layers       : controller CheckReportController → service DecisionAgreementQueryService
Request      : query serviceCode (DBF-RPT-002, string ≤ 100, required); no body
Response     : 200 · array of AgreementRow {versionNumber (DBF-RPT-003), overallStatus (DBF-RPT-011), employeeDecision (DBF-RPT-015), count} ordered by versionNumber DESC, overallStatus, employeeDecision · only Checks with a recorded decision · empty array when none · not paginated · no envelope
Validations  : RULE-RPT-015 — Decision agreement needs a service code · trigger: on read decision agreement · statement: The system shall require a service code, present and not blank, to read the decision agreement. · message (en): A service code is needed to read the decision agreement. · message (ar): PENDING ADR-RPT-013
Errors       : RPT-400-SERVICE-CODE-MISSING (400, RULE-RPT-015) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate RULE-RPT-015 → aggregate (QR-RPT-007, serviceCode bound) → AgreementRow list; writes nothing
Repository   : QR-RPT-007 · AGGREGATE · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-005, CON-RPT-002
<!-- API:API-RPT-003:END -->

<!-- PHASE:SVC-API:END -->
