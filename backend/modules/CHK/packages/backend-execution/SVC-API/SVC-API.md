<!-- source: PHASE:SVC-API -->
<!-- traces: DBF-CHK-002, DBF-CHK-003, DBF-CHK-004, REQ-CHK-001, REQ-CHK-003, REQ-CHK-009, REQ-CHK-044, REQ-CHK-055, REQ-CHK-057, REQ-CHK-076, REQ-CHK-077, REQ-CHK-078, REQ-CHK-079, REQ-CHK-080 -->
<!-- PHASE:SVC-API:START traces=DBF-CHK-002,DBF-CHK-003,DBF-CHK-004,REQ-CHK-001,REQ-CHK-003,REQ-CHK-009,REQ-CHK-044,REQ-CHK-055,REQ-CHK-057,REQ-CHK-076,REQ-CHK-077,REQ-CHK-078,REQ-CHK-079,REQ-CHK-080 -->
## PHASE SVC-API — SVC+API

### Service layer — the in-process interface `CheckEngine` (contract-chk.md)
Injected into INT (profile `module_interface: in_process`). Value objects are Java records with unmodifiable lists. Service classes: `CheckStartService`, `CheckConfirmationService`, `CheckPipeline`, `CheckEndingService`, `DeadlineCheckService`, `StartupRecoveryService`, `ModelEvaluationRunner`.

**`startCheck(serviceCode, requestNumber, employeeId)` → StartedCheck {checkId, status}** — READ_WRITE, one transaction; the pipeline is submitted after commit.
  - Honours: CON-CHK-004
  1. REQ-CHK-004: any value absent or blank → `StartIncompleteException` (CHK-400-START-INCOMPLETE), message "A Check needs a service code, a request number and the employee's identity."; nothing created.
  2. RULE-CHK-001 — integrate (XM-CHK-001):
     - RULE-CHK-001 — Checks only for an available service · trigger: on Check start · statement: The system shall refuse to start a Check when its service code is not held by the service registry or the service's available flag is false. · message (en): The service "{serviceCode}" is not available for Checks. · message (ar): PENDING ADR-CHK-018 · effect: `ServiceNotAvailableException` (CHK-422-SERVICE-NOT-AVAILABLE), nothing created (REQ-CHK-005).
  3. integrate (XM-CHK-002, XM-CHK-003, XM-CHK-004) — load the current version; a connection not activated → `ConnectionNotActivatedException` (CHK-422-CONNECTION-NOT-ACTIVATED), message "The service "{serviceCode}" cannot be checked: connection "{connectionName}" is not activated in this environment.", nothing created (REQ-CHK-006).
     - RULE-CHK-007 — One service package version per Check · trigger: on Check start; on resuming a `manual` Check · statement: The system shall resolve every service package read of a Check by the service code and version number recorded at the Check's start. · message (en): Check {checkId} could not be run: version {versionNumber} of "{serviceCode}" could not be resolved. · message (ar): PENDING ADR-CHK-018 · effect: the loaded version is kept in the Check's `CheckContext`; a failed resolution later → FAILED / INTERNAL_ERROR with the message as detail (REQ-CHK-007, REQ-CHK-008).
  4. Result port `createCheckRun(serviceCode, versionNumber, fetchMode, requestNumber, employeeId — both exactly as received, REQ-CHK-002; status RUNNING for `path` / `blob`, AWAITING_DOCUMENTS for `manual`; startedAt = now)` → checkId (REQ-CHK-001, REQ-CHK-003, REQ-CHK-056).
  5. Persist: INSERT INTO CHK_ACTIVE_CHECK (CHECK_ID DBF-CHK-002 = checkId, CHECK_STATUS DBF-CHK-003 = the same status, DEADLINE_AT DBF-CHK-004 = now + `aias.check.upload-window` for AWAITING_DOCUMENTS or now + `aias.check.timeout` for RUNNING); ACTIVE_CHECK_ID by the identity clause; CREATED_AT / UPDATED_AT by DEFAULT SYSTIMESTAMP (REQ-CHK-076; RULE-CHK-008, RULE-CHK-009).
  6. Commit; for RUNNING submit `CheckPipeline.run(checkId, CheckContext)` to the bounded executor and return {checkId, status} at once (REQ-CHK-001). A second Check of the same request is simply another start — nothing is looked up, reused or cancelled (REQ-CHK-066).
  - Concurrency: the Check identifier is allocated by the result port; UQ_CHK_ACTIVE_CHECK_CHECK_ID makes a second Active Check for it impossible (RULE-CHK-008).

**`confirmUploads(checkId)` → {checkId, status RUNNING}** — READ_WRITE.
  - Honours: CON-CHK-005
  1. Locking read: SELECT CHECK_ID, CHECK_STATUS FROM CHK_ACTIVE_CHECK WHERE CHECK_ID = :checkId FOR UPDATE (DBF-CHK-002, DBF-CHK-003).
  2. No row → result port `getCheck(checkId)`: unknown → `CheckNotFoundException` (CHK-404-CHECK-NOT-FOUND), message "Check {checkId} does not exist." (REQ-CHK-058); known → `CheckNotAwaitingDocumentsException` (CHK-409-CHECK-NOT-AWAITING-DOCUMENTS) with its status (RULE-CHK-010, REQ-CHK-059). Row with CHECK_STATUS = RUNNING → the same exception with status RUNNING.
  3. UPDATE CHK_ACTIVE_CHECK SET CHECK_STATUS = 'RUNNING' (DBF-CHK-003), DEADLINE_AT = now + `aias.check.timeout` (DBF-CHK-004), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_ID = :checkId (REQ-CHK-077, REQ-CHK-052); result port `markRunning(checkId, now)` (REQ-CHK-057).
  4. Commit; result port `getCheck` gives serviceCode + versionNumber → integrate (XM-CHK-002) resolves exactly that version (RULE-CHK-007) → submit the pipeline.
  - Concurrency: the deadline check and this confirmation both start from the same row; the `FOR UPDATE` lock serialises them — whichever commits first wins; the loser finds the row gone (ended) or RUNNING and refuses.

**`CheckPipeline.run(checkId, CheckContext)`** — the fixed steps, in this order, for every service (REQ-CHK-009). Each step first tests the deadline; every exception not named below → FAILED / INTERNAL_ERROR (REQ-CHK-010).
  1. Service queries — PORTS-QUERY (REQ-CHK-011 … REQ-CHK-016, REQ-CHK-049, REQ-CHK-050).
  2. Documents — `DocumentPort.fetch` (REQ-CHK-017; REQ-CHK-023 on failure).
  3. Deterministic checks, part 1 — required documents:
     - RULE-CHK-004 — Each required document type decided from Document Access outcomes · trigger: on the deterministic checks · statement: The system shall decide the finding of each required document type from the Document Access outcomes of that type: SATISFIED with at least one READ, otherwise UNDETERMINED with at least one UNREADABLE, otherwise NOT_SATISFIED. · message (en): Required document "{documentType}": {readStatus}{reasonSuffix}. · message (ar): PENDING ADR-CHK-018 · effect: one finding per required type read through XM-CHK-004, the message as its note, the reason and detail as evidence for UNREADABLE, "MISSING" for a missing one (REQ-CHK-019 … REQ-CHK-022).
  4. Comparison — PORTS-MODEL gate, then `ComparisonModelPort.compare` (REQ-CHK-030 … REQ-CHK-037, REQ-CHK-064, REQ-CHK-072). Exceptions: `ModelNotPermittedException` → FAILED / MODEL_NOT_PERMITTED (REQ-CHK-073); `ModelUnavailableException` → FAILED / MODEL_UNAVAILABLE (REQ-CHK-053); `ModelOutputInvalidException` → FAILED / MODEL_OUTPUT_INVALID (REQ-CHK-039).
  5. Deterministic checks, part 2 — verification of the model's findings (ADR-CHK-003), each finding independently:
     - evidence missing → UNDETERMINED (REQ-CHK-040); evidence not found (exact substring, whitespace-normalised) in the query results or READ content → UNDETERMINED (REQ-CHK-029);
     - explicit condition: valueFound not in the data → UNDETERMINED (REQ-CHK-026); limit not in the service knowledge → UNDETERMINED with the RULE-CHK-006 message as note (REQ-CHK-027):
       - RULE-CHK-006 — Limit stated in the service knowledge · trigger: on the deterministic checks · statement: The system shall accept the limit of an explicit value or date condition only when that limit appears literally in the service knowledge of the Check's version. · message (en): The limit "{limit}" of this condition is not stated in the service knowledge; please verify the condition yourself. · message (ar): PENDING ADR-CHK-018
     - value or limit not parseable as `BigDecimal` or as an ISO-8601 / `dd/MM/yyyy` date → UNDETERMINED (REQ-CHK-028); otherwise the comparison (`>=`, `>`, `<=`, `<`, `=`, `before`, `after`, `on or before`, `on or after`) is recomputed in code and its result replaces the model's outcome (REQ-CHK-024, REQ-CHK-025).
     - one finding per condition: the model's findings plus the required-document findings (REQ-CHK-038).
  6. Overall Status (REQ-CHK-041 … REQ-CHK-043): any NOT_SATISFIED → NOT_COMPLIANT; else any UNDETERMINED or any unread query → NEEDS_MANUAL_REVIEW; else COMPLIANT.
  7. Ending — `CheckEndingService.complete(checkId, report)` with metadata {serviceCode, versionNumber, fetchMode, comparisonModel = `aias.check.comparison-model.model`, employeeId, startedAt, endedAt} (REQ-CHK-044, REQ-CHK-045). CHK never calls a host approval endpoint, never reads the approval API definition and never records an Employee Decision (REQ-CHK-032, REQ-CHK-033; AIAS-4).
  - When `run` returns, the `CheckContext` (package, query results, outcomes, content, model input and output) is cleared and dropped (REQ-CHK-065).

**`CheckEndingService.complete(checkId, report)` / `fail(checkId, reason, detail)`** — READ_WRITE; the single ending path for every outcome.
  1. DELETE FROM CHK_ACTIVE_CHECK WHERE CHECK_ID = :checkId (DBF-CHK-002) (REQ-CHK-078). 0 rows deleted → another path already ended the Check: return, hand nothing, notify nothing (REQ-CHK-079).
  2. 1 row deleted → result port `completeCheck(...)` (status COMPLETED, no document content — REQ-CHK-044, REQ-CHK-047) or `failCheck(checkId, reason, detail, endedAt)` (exactly one reason, no Overall Status — REQ-CHK-054). A `completeCheck` failure → `failCheck(INTERNAL_ERROR)` in the same transaction (REQ-CHK-048).
  3. After commit: `DocumentPort.endCheck(checkId)` — on every ending path, COMPLETED or FAILED (REQ-CHK-061), retried once then logged (REQ-CHK-062).
  - Concurrency: the DELETE is the guard — two ending paths (timeout and completion, window expiry and confirmation) both reach step 1; the database lets exactly one of them delete the row; the other deletes 0 rows and stops.

**`DeadlineCheckService.run()`** — `@Scheduled(fixedDelay = aias.check.deadline-check-interval)`, READ_ONLY read then one ending per row.
  - SELECT CHECK_ID, CHECK_STATUS FROM CHK_ACTIVE_CHECK WHERE DEADLINE_AT <= SYSTIMESTAMP (DBF-CHK-002, DBF-CHK-003, DBF-CHK-004; IDX_CHK_ACTIVE_CHECK_DEADLINE_AT) → for AWAITING_DOCUMENTS: `fail(UPLOAD_WINDOW_EXPIRED)` (REQ-CHK-060); for RUNNING: cancel the pipeline's `Future` (interrupt) and `fail(TIMED_OUT)` (REQ-CHK-051); a model answer arriving later finds the row gone and is discarded (REQ-CHK-079) (REQ-CHK-080).
  - The running pipeline also holds its own deadline (`aias.check.timeout` from RUNNING, REQ-CHK-052) and tests it before every step.

**`StartupRecoveryService.onApplicationReady()`** — runs once before the deadline check is scheduled and before `CheckEngine` accepts calls.
  1. Result port `listUnfinishedChecks()` → for each: `failCheck(checkId, INTERRUPTED, "interrupted by a restart of the service", now)` and `DocumentPort.endCheck(checkId)` (REQ-CHK-055).
  2. DELETE FROM CHK_ACTIVE_CHECK (every row left by the earlier run) (REQ-CHK-081).
  - Concurrency: NONE — single-threaded, before any other path runs.

**`ModelEvaluationRunner`** — Spring profile `model-eval`, run on every comparison model change (MODEL-EVAL test phase, AIAS-10; ADR-CHK-012).
  - Reads the fixed known-result request set delivered with the service (`model-eval/known-result-set/*.json`: synthetic request data, synthetic document contents, its service package and its expected Overall Status; at least one per Overall Status — REQ-CHK-069), runs each through the same `CheckPipeline` with in-memory query, document and result-port test doubles and the configured comparison model, and writes a run report with one row per request {request, expected, reached} (REQ-CHK-070); any difference → run reported failed, naming the request (REQ-CHK-071). It sends only synthetic data (environment data class SYNTHETIC).

### In-process rejection codes (typed exceptions of `CheckEngine` — mapped by INT to ProblemDetail, ADR-CHK-018)
| Code | Rule / REQ | Exception | Message en | Message ar |
|---|---|---|---|---|
| CHK-400-START-INCOMPLETE | REQ-CHK-004 | StartIncompleteException | A Check needs a service code, a request number and the employee's identity. | PENDING ADR-CHK-018 |
| CHK-422-SERVICE-NOT-AVAILABLE | RULE-CHK-001 | ServiceNotAvailableException | The service "{serviceCode}" is not available for Checks. | PENDING ADR-CHK-018 |
| CHK-422-CONNECTION-NOT-ACTIVATED | REQ-CHK-006 | ConnectionNotActivatedException | The service "{serviceCode}" cannot be checked: connection "{connectionName}" is not activated in this environment. | PENDING ADR-CHK-018 |
| CHK-404-CHECK-NOT-FOUND | REQ-CHK-058 | CheckNotFoundException | Check {checkId} does not exist. | PENDING ADR-CHK-018 |
| CHK-409-CHECK-NOT-AWAITING-DOCUMENTS | RULE-CHK-010 | CheckNotAwaitingDocumentsException | Check {checkId} is not waiting for documents; its status is {status}. | PENDING ADR-CHK-018 |

### HTTP endpoint
Controller `ActiveCheckController` → service `ActiveCheckQueryService`. CHK exposes no POST, PUT or DELETE (ADR-CHK-017).

<!-- API:API-CHK-001:START traces=REQ-CHK-076,REQ-CHK-077,REQ-CHK-080,DBF-CHK-002,DBF-CHK-003,DBF-CHK-004 -->
### API-CHK-001 — Read the Active Check of a Check
Entity       : ENT-CHK-001
Endpoint     : /api/v1/active-checks/{checkId}   verb: GET
Layers       : controller ActiveCheckController → service ActiveCheckQueryService
Request      : path checkId (DBF-CHK-002, integer int64, required); no query parameter; no body
Response     : 200 · ActiveCheckView {checkId (DBF-CHK-002), checkStatus (DBF-CHK-003, AWAITING_DOCUMENTS | RUNNING), deadlineAt (DBF-CHK-004)} · not paginated · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-CHK-018)
Errors       : CHK-400-CHECK-ID-INVALID (400, PLATFORM-STD) · CHK-404-ACTIVE-CHECK-NOT-FOUND (404, PLATFORM-STD — the Check has ended or does not exist) · CHK-500 (500, PLATFORM-STD)
Orchestration : validate checkId → load the row (QR-CHK-001, filter on DBF-CHK-002) → map to ActiveCheckView; writes nothing
Repository   : QR-CHK-001 · FIND_ONE · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-CHK-018
Covers       : CHK's only HTTP surface (ADR-CHK-017). It shows the state the in-process start, confirmation and deadline check write (REQ-CHK-076, REQ-CHK-077, REQ-CHK-080). The in-process procedures of this phase and of PORTS implement, and are listed here for the coverage check: REQ-CHK-001, REQ-CHK-002, REQ-CHK-003, REQ-CHK-004, REQ-CHK-005, REQ-CHK-006, REQ-CHK-007, REQ-CHK-008, REQ-CHK-009, REQ-CHK-010, REQ-CHK-011, REQ-CHK-012, REQ-CHK-013, REQ-CHK-014, REQ-CHK-015, REQ-CHK-016, REQ-CHK-017, REQ-CHK-018, REQ-CHK-019, REQ-CHK-020, REQ-CHK-021, REQ-CHK-022, REQ-CHK-023, REQ-CHK-024, REQ-CHK-025, REQ-CHK-026, REQ-CHK-027, REQ-CHK-028, REQ-CHK-029, REQ-CHK-030, REQ-CHK-031, REQ-CHK-032, REQ-CHK-033, REQ-CHK-034, REQ-CHK-035, REQ-CHK-036, REQ-CHK-037, REQ-CHK-038, REQ-CHK-039, REQ-CHK-040, REQ-CHK-041, REQ-CHK-042, REQ-CHK-043, REQ-CHK-044, REQ-CHK-045, REQ-CHK-046, REQ-CHK-047, REQ-CHK-048, REQ-CHK-049, REQ-CHK-050, REQ-CHK-051, REQ-CHK-052, REQ-CHK-053, REQ-CHK-054, REQ-CHK-055, REQ-CHK-056, REQ-CHK-057, REQ-CHK-058, REQ-CHK-059, REQ-CHK-060, REQ-CHK-061, REQ-CHK-062, REQ-CHK-063, REQ-CHK-064, REQ-CHK-065, REQ-CHK-066, REQ-CHK-067, REQ-CHK-068, REQ-CHK-069, REQ-CHK-070, REQ-CHK-071, REQ-CHK-072, REQ-CHK-073, REQ-CHK-074, REQ-CHK-075, REQ-CHK-078, REQ-CHK-079, REQ-CHK-081, REQ-CHK-082
<!-- API:API-CHK-001:END -->

<!-- PHASE:SVC-API:END -->
