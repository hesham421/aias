# BACKEND TEST PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Stage : P4   Framework : agnostic (the consumer repo chooses its tool; this plan names none)
Sources : _state/current-srs.md (v1, AC 61) · current-registry-srs.md · current-registry-db.md (XM 0) · current-backend-execution-plan.md (units PORTS, SVC-API; CORE, DATA-DOM, ALIGN-BE no_tests; CROSS-MOD 0 edges) · current-api-spec.yaml (API-RPT-001 … API-RPT-003) · dependency-graph (no RPT XM edge)
Open ADRs : none BLOCKED — applied ADR-RPT-006, ADR-RPT-012, ADR-RPT-013, ADR-RPT-015, ADR-RPT-018
TCs : 61 (TC-RPT-001 … TC-RPT-061) — one per AC; RULE-SCENARIOS 31 · API-SCENARIOS 29 · MODEL-EVAL 1
══════════════════════════════════════════════════════════════════

Every TC derives from one AC. In-process operations (the Check result port, the decision procedure, the purge — ADR-RPT-006) are named on the `Exercises` line; HTTP cases cite the API id and read the shape in api-spec-rpt.yaml. Errors over HTTP are ProblemDetail (RFC 9457) → {type, title, status, detail, code}; in-process refusals are typed exceptions carrying the same code and message (ADR-RPT-013). Arabic messages are `PENDING ADR-RPT-013`. Values of the open REG lists (service code, document type) are placeholders carried by value (ADR-RPT-015). INT-XM is absent: RPT declares no XM edge (registry-db XM 0).

<!-- PHASE:TEST-PLAN-BE:START traces=AC-RPT-001,AC-RPT-002,AC-RPT-003,AC-RPT-004,AC-RPT-005,AC-RPT-006,AC-RPT-007,AC-RPT-008,AC-RPT-009,AC-RPT-010,AC-RPT-011,AC-RPT-012,AC-RPT-013,AC-RPT-014,AC-RPT-015,AC-RPT-016,AC-RPT-017,AC-RPT-018,AC-RPT-019,AC-RPT-020,AC-RPT-021,AC-RPT-022,AC-RPT-023,AC-RPT-024,AC-RPT-025,AC-RPT-026,AC-RPT-027,AC-RPT-028,AC-RPT-029,AC-RPT-030,AC-RPT-031,AC-RPT-032,AC-RPT-033,AC-RPT-034,AC-RPT-035,AC-RPT-036,AC-RPT-037,AC-RPT-038,AC-RPT-039,AC-RPT-040,AC-RPT-041,AC-RPT-042,AC-RPT-043,AC-RPT-044,AC-RPT-045,AC-RPT-046,AC-RPT-047,AC-RPT-048,AC-RPT-049,AC-RPT-050,AC-RPT-051,AC-RPT-052,AC-RPT-053,AC-RPT-054,AC-RPT-055,AC-RPT-056,AC-RPT-057,AC-RPT-058,AC-RPT-059,AC-RPT-060,AC-RPT-061,REQ-RPT-001,REQ-RPT-002,REQ-RPT-003,REQ-RPT-004,REQ-RPT-005,REQ-RPT-006,REQ-RPT-007,REQ-RPT-008,REQ-RPT-009,REQ-RPT-010,REQ-RPT-011,REQ-RPT-012,REQ-RPT-013,REQ-RPT-014,REQ-RPT-015,REQ-RPT-016,REQ-RPT-017,REQ-RPT-018,REQ-RPT-019,REQ-RPT-020,REQ-RPT-021,REQ-RPT-022,REQ-RPT-023,REQ-RPT-024,REQ-RPT-025,REQ-RPT-026,REQ-RPT-027,REQ-RPT-028,REQ-RPT-029,REQ-RPT-030,REQ-RPT-031,REQ-RPT-032,REQ-RPT-033,REQ-RPT-034,REQ-RPT-035,REQ-RPT-036,REQ-RPT-037,REQ-RPT-038,REQ-RPT-039,REQ-RPT-040,REQ-RPT-041,REQ-RPT-042,REQ-RPT-043,REQ-RPT-044,REQ-RPT-045,REQ-RPT-046,REQ-RPT-047,REQ-RPT-048,REQ-RPT-049,REQ-RPT-050,REQ-RPT-051,REQ-RPT-052,REQ-RPT-053 -->
## PHASE TEST-PLAN-BE

<!-- SUB:RULE-SCENARIOS:START traces=AC-RPT-003,AC-RPT-004,AC-RPT-005,AC-RPT-006,AC-RPT-007,AC-RPT-008,AC-RPT-009,AC-RPT-010,AC-RPT-012,AC-RPT-016,AC-RPT-017,AC-RPT-018,AC-RPT-019,AC-RPT-020,AC-RPT-021,AC-RPT-023,AC-RPT-035,AC-RPT-039,AC-RPT-040,AC-RPT-041,AC-RPT-042,AC-RPT-045,AC-RPT-046,AC-RPT-049,AC-RPT-050,AC-RPT-051,AC-RPT-052,AC-RPT-053,AC-RPT-054,AC-RPT-059,AC-RPT-060,REQ-RPT-003,REQ-RPT-004,REQ-RPT-005,REQ-RPT-006,REQ-RPT-007,REQ-RPT-009,REQ-RPT-013,REQ-RPT-014,REQ-RPT-015,REQ-RPT-016,REQ-RPT-017,REQ-RPT-018,REQ-RPT-020,REQ-RPT-029,REQ-RPT-033,REQ-RPT-034,REQ-RPT-035,REQ-RPT-038,REQ-RPT-039,REQ-RPT-041,REQ-RPT-042,REQ-RPT-043,REQ-RPT-044,REQ-RPT-045,REQ-RPT-046,REQ-RPT-051,REQ-RPT-052 -->
### RULE-SCENARIOS

<!-- TC:TC-RPT-003:START traces=AC-RPT-003,REQ-RPT-003,RULE-RPT-001 -->
### TC-RPT-003 — Incomplete Check run refused
Derived from : AC-RPT-003  (REQ-RPT-003)
Exercises    : in-process result port createCheckRun (implements CON-CHK-006) — no HTTP operation (ADR-RPT-006)
Rule / code  : RULE-RPT-001 → RPT-400-CHECK-RUN-INCOMPLETE
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check run exists
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. createCheckRun with employeeId blank, every other value valid
Expected     : refused with CheckRunIncompleteException code RPT-400-CHECK-RUN-INCOMPLETE; en: "The Check run was not stored: employeeId is missing." · ar: PENDING ADR-RPT-013; 0 Check runs stored
Test data    : employeeId blank; ⟨SVC-A⟩ for the service code
<!-- TC:TC-RPT-003:END -->

<!-- TC:TC-RPT-004:START traces=AC-RPT-004,REQ-RPT-004,RULE-RPT-002 -->
### TC-RPT-004 — AWAITING_DOCUMENTS with fetch mode path refused
Derived from : AC-RPT-004  (REQ-RPT-004)
Exercises    : in-process result port createCheckRun (implements CON-CHK-006) — no HTTP operation (ADR-RPT-006)
Rule / code  : RULE-RPT-002 → RPT-422-INITIAL-STATUS-MISMATCH
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check run exists
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. createCheckRun with fetchMode path and status AWAITING_DOCUMENTS
               2. insert the same row directly into RPT_CHECK_RUN in the service schema
Expected     : step 1 refused with code RPT-422-INITIAL-STATUS-MISMATCH; en: "The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual." · ar: PENDING ADR-RPT-013; step 2 refused by CHECK constraint CHK_RPT_CHECK_RUN_AWAITING; 0 Check runs stored
Test data    : `path`; AWAITING_DOCUMENTS
<!-- TC:TC-RPT-004:END -->

<!-- TC:TC-RPT-005:START traces=AC-RPT-005,REQ-RPT-004,RULE-RPT-002 -->
### TC-RPT-005 — Manual Check starting RUNNING refused
Derived from : AC-RPT-005  (REQ-RPT-004)
Exercises    : in-process result port createCheckRun (implements CON-CHK-006) — no HTTP operation (ADR-RPT-006)
Rule / code  : RULE-RPT-002 → RPT-422-INITIAL-STATUS-MISMATCH
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check run exists
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. createCheckRun with fetchMode manual and status RUNNING
Expected     : refused with code RPT-422-INITIAL-STATUS-MISMATCH; en: "The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS." · ar: PENDING ADR-RPT-013; 0 Check runs stored
Test data    : `manual`; RUNNING
<!-- TC:TC-RPT-005:END -->

<!-- TC:TC-RPT-006:START traces=AC-RPT-006,REQ-RPT-005,RULE-RPT-003 -->
### TC-RPT-006 — Mark RUNNING from AWAITING_DOCUMENTS
Derived from : AC-RPT-006  (REQ-RPT-005)
Exercises    : in-process result port markRunning (implements CON-CHK-007) — no HTTP operation
Rule / code  : RULE-RPT-003 (allowed transition AWAITING_DOCUMENTS → RUNNING)
Package      : PORTS
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 501 is AWAITING_DOCUMENTS with no running time
Host data    : none
Steps        : 1. markRunning(501, 2026-10-01T09:05:00Z)
               2. read Check 501 through getCheck / API-RPT-001
Expected     : Check 501 has status RUNNING and runningSince 2026-10-01T09:05:00Z
Test data    : Check 501; 2026-10-01T09:05:00Z
<!-- TC:TC-RPT-006:END -->

<!-- TC:TC-RPT-007:START traces=AC-RPT-007,REQ-RPT-005,RULE-RPT-003 -->
### TC-RPT-007 — Mark RUNNING again keeps the first running time
Derived from : AC-RPT-007  (REQ-RPT-005)
Exercises    : in-process result port markRunning (implements CON-CHK-007) — no HTTP operation
Rule / code  : RULE-RPT-003 (allowed transition RUNNING → RUNNING)
Package      : PORTS
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Check 502 is RUNNING with runningSince 2026-10-01T09:01:00Z
Host data    : none
Steps        : 1. markRunning(502, 2026-10-01T09:02:00Z)
               2. read Check 502
Expected     : Check 502 stays RUNNING and runningSince stays 2026-10-01T09:01:00Z (first running time kept)
Test data    : Check 502; 09:01:00Z stored; 09:02:00Z sent
<!-- TC:TC-RPT-007:END -->

<!-- TC:TC-RPT-008:START traces=AC-RPT-008,REQ-RPT-006,RULE-RPT-003 -->
### TC-RPT-008 — Ended Check cannot be marked RUNNING
Derived from : AC-RPT-008  (REQ-RPT-006)
Exercises    : in-process result port markRunning (implements CON-CHK-007) — no HTTP operation
Rule / code  : RULE-RPT-003 → RPT-409-CHECK-ENDED
Package      : PORTS
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Check 503 is COMPLETED
Host data    : none
Steps        : 1. markRunning(503, any time)
Expected     : refused with code RPT-409-CHECK-ENDED; en: "Check 503 has already ended; its status cannot change." · ar: PENDING ADR-RPT-013; Check 503 stays COMPLETED
Test data    : Check 503
<!-- TC:TC-RPT-008:END -->

<!-- TC:TC-RPT-009:START traces=AC-RPT-009,REQ-RPT-006,RULE-RPT-003 -->
### TC-RPT-009 — Check not RUNNING cannot be completed
Derived from : AC-RPT-009  (REQ-RPT-006)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation
Rule / code  : RULE-RPT-003 → RPT-409-CHECK-NOT-RUNNING
Package      : PORTS
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Check 504 is AWAITING_DOCUMENTS
Host data    : none
Steps        : 1. completeCheck(504, a valid report)
Expected     : refused with code RPT-409-CHECK-NOT-RUNNING; en: "Check 504 is not running; it cannot be completed." · ar: PENDING ADR-RPT-013; Check 504 stays AWAITING_DOCUMENTS with 0 Findings, 0 Check Documents, 0 Unread Queries
Test data    : Check 504
<!-- TC:TC-RPT-009:END -->

<!-- TC:TC-RPT-010:START traces=AC-RPT-010,REQ-RPT-007 -->
### TC-RPT-010 — Unknown Check on the result port
Derived from : AC-RPT-010  (REQ-RPT-007)
Exercises    : in-process result port failCheck (implements CON-CHK-009) — no HTTP operation
Rule / code  : REQ-RPT-007 → RPT-404-CHECK-NOT-FOUND
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check run 999 exists
Host data    : none
Steps        : 1. failCheck(999, TIMED_OUT, a detail, an end time)
Expected     : refused with code RPT-404-CHECK-NOT-FOUND; en: "Check 999 was not found." · ar: PENDING ADR-RPT-013; nothing stored
Test data    : Check 999
<!-- TC:TC-RPT-010:END -->

<!-- TC:TC-RPT-012:START traces=AC-RPT-012,REQ-RPT-009,RULE-RPT-006 -->
### TC-RPT-012 — Report stored whole or not at all
Derived from : AC-RPT-012  (REQ-RPT-009)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation
Rule / code  : RULE-RPT-006 → RPT-422-UNKNOWN-CODE (report not stored)
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 506 is RUNNING
Host data    : none
Steps        : 1. completeCheck(506, …) with 3 findings, the third with outcome `PASSED`
               2. count the rows of Check 506 in RPT_FINDING, RPT_CHECK_DOCUMENT, RPT_UNREAD_QUERY
Expected     : the call is refused as not stored; Check 506 stays RUNNING with overallStatus null; 0 Findings, 0 Check Documents, 0 Unread Queries
Test data    : outcome `PASSED` (not a FINDING_OUTCOME code)
<!-- TC:TC-RPT-012:END -->

<!-- TC:TC-RPT-016:START traces=AC-RPT-016,REQ-RPT-013,RULE-RPT-004 -->
### TC-RPT-016 — Metadata disagreeing with the Check run refused
Derived from : AC-RPT-016  (REQ-RPT-013)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation
Rule / code  : RULE-RPT-004 → RPT-422-METADATA-MISMATCH
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 510 is RUNNING for ⟨SVC-A⟩ version 3
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. completeCheck(510, …) with metadata versionNumber 4
Expected     : refused with code RPT-422-METADATA-MISMATCH; en: "The report of Check 510 was not stored: its metadata versionNumber 4 differs from the Check run (3)." · ar: PENDING ADR-RPT-013; nothing stored
Test data    : stored version 3; metadata version 4
<!-- TC:TC-RPT-016:END -->

<!-- TC:TC-RPT-017:START traces=AC-RPT-017,REQ-RPT-014,RULE-RPT-005 -->
### TC-RPT-017 — COMPLIANT refused unless every finding is SATISFIED
Derived from : AC-RPT-017  (REQ-RPT-014)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation
Rule / code  : RULE-RPT-005 → RPT-422-COMPLIANT-NOT-VERIFIED
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 511 is RUNNING
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. completeCheck(511, COMPLIANT, …) with a finding "⟨DOC-TYPE-2⟩ present" whose outcome is NOT_SATISFIED
Expected     : refused with code RPT-422-COMPLIANT-NOT-VERIFIED; en: "The report of Check 511 was not stored: COMPLIANT needs every finding SATISFIED and every service query read." · ar: PENDING ADR-RPT-013; nothing stored; COMPLIANT is never stored unless every finding is SATISFIED
Test data    : COMPLIANT; NOT_SATISFIED; ⟨DOC-TYPE-2⟩ = the AC's identity-card document type
<!-- TC:TC-RPT-017:END -->

<!-- TC:TC-RPT-018:START traces=AC-RPT-018,REQ-RPT-015,RULE-RPT-006 -->
### TC-RPT-018 — Code outside its closed list refused (port and CHECK constraints)
Derived from : AC-RPT-018  (REQ-RPT-015)
Exercises    : in-process result port failCheck (implements CON-CHK-009) — no HTTP operation; direct write to the service schema
Rule / code  : RULE-RPT-006 → RPT-422-UNKNOWN-CODE
Package      : PORTS
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: Check 512 is RUNNING
Host data    : none
Steps        : 1. failCheck(512, `CRASHED`, a detail, an end time)
               2. UPDATE RPT_CHECK_RUN SET FAILURE_REASON = 'CRASHED' for Check 512 directly in the service schema
               3. for each closed column (CHECK_STATUS, FETCH_MODE, OVERALL_STATUS, FAILURE_REASON, FINDING_OUTCOME, SOURCE_MODE, READ_STATUS, UNREADABLE_REASON, EMPLOYEE_DECISION) write one value outside its list directly
Expected     : step 1 refused with code RPT-422-UNKNOWN-CODE; en: "Not stored: `CRASHED` is not a code of CHECK_FAILURE_REASON." · ar: PENDING ADR-RPT-013; Check 512 stays RUNNING; steps 2–3 refused by the CHECK constraints CHK_RPT_CHECK_RUN_FAILURE_REASON, CHK_RPT_CHECK_RUN_CHECK_STATUS, CHK_RPT_CHECK_RUN_FETCH_MODE, CHK_RPT_CHECK_RUN_OVERALL_STATUS, CHK_RPT_FINDING_FINDING_OUTCOME, CHK_RPT_CHECK_DOCUMENT_SOURCE_MODE, CHK_RPT_CHECK_DOCUMENT_READ_STATUS, CHK_RPT_CHECK_DOCUMENT_UNREADABLE_REASON, CHK_RPT_CHECK_RUN_EMPLOYEE_DECISION
Test data    : `CRASHED`; one out-of-list value per closed column (marked `<OUT-OF-LIST>`)
<!-- TC:TC-RPT-018:END -->

<!-- TC:TC-RPT-019:START traces=AC-RPT-019,REQ-RPT-016,RULE-RPT-007 -->
### TC-RPT-019 — Finding without evidence refused
Derived from : AC-RPT-019  (REQ-RPT-016)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation
Rule / code  : RULE-RPT-007 → RPT-422-FINDING-INCOMPLETE
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 513 is RUNNING
Host data    : none
Steps        : 1. completeCheck(513, …) with a finding "GPA at least 3.0" whose evidence is blank
Expected     : refused with code RPT-422-FINDING-INCOMPLETE; en: "The report of Check 513 was not stored: finding 1 has no evidence." · ar: PENDING ADR-RPT-013; nothing stored
Test data    : evidence blank
<!-- TC:TC-RPT-019:END -->

<!-- TC:TC-RPT-020:START traces=AC-RPT-020,REQ-RPT-017,RULE-RPT-008 -->
### TC-RPT-020 — UNREADABLE document without a reason refused (port and CHECK constraint)
Derived from : AC-RPT-020  (REQ-RPT-017)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation; direct write to the service schema
Rule / code  : RULE-RPT-008 → RPT-422-DOCUMENT-REASON-MISMATCH
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 514 is RUNNING
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. completeCheck(514, …) with a document outcome ⟨DOC-TYPE-2⟩ UNREADABLE and no reason
               2. insert an RPT_CHECK_DOCUMENT row with READ_STATUS UNREADABLE and UNREADABLE_REASON null directly in the service schema
Expected     : step 1 refused with code RPT-422-DOCUMENT-REASON-MISMATCH; en: "The report of Check 514 was not stored: document 1 is UNREADABLE without a reason." · ar: PENDING ADR-RPT-013; step 2 refused by CHECK constraint CHK_RPT_CHECK_DOCUMENT_REASON; nothing stored
Test data    : UNREADABLE, no reason
<!-- TC:TC-RPT-020:END -->

<!-- TC:TC-RPT-021:START traces=AC-RPT-021,REQ-RPT-018,API-RPT-001,RULE-RPT-003 -->
### TC-RPT-021 — Failed Check stored with its reason
Derived from : AC-RPT-021  (REQ-RPT-018)
Exercises    : in-process result port failCheck (implements CON-CHK-009) — no HTTP operation; read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : RULE-RPT-003 (allowed transition RUNNING → FAILED)
Package      : PORTS
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 515 is RUNNING
Host data    : none
Steps        : 1. failCheck(515, TIMED_OUT, "Check exceeded 300 s", 2026-10-01T09:06:00Z)
               2. read Check 515
Expected     : Check 515 is FAILED, failureReason TIMED_OUT, failureDetail "Check exceeded 300 s", endedAt 2026-10-01T09:06:00Z, overallStatus null, 0 findings
Test data    : TIMED_OUT; "Check exceeded 300 s"; 2026-10-01T09:06:00Z
<!-- TC:TC-RPT-021:END -->

<!-- TC:TC-RPT-023:START traces=AC-RPT-023,REQ-RPT-020,RULE-RPT-003 -->
### TC-RPT-023 — Ended report never changes
Derived from : AC-RPT-023  (REQ-RPT-020)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation
Rule / code  : RULE-RPT-003 → RPT-409-CHECK-ENDED
Package      : PORTS
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Check 517 is COMPLETED with overallStatus NOT_COMPLIANT and 3 findings
Host data    : none
Steps        : 1. completeCheck(517, COMPLIANT, …) again
               2. read Check 517
Expected     : refused with code RPT-409-CHECK-ENDED; en: "Check 517 has already ended; its status cannot change." · ar: PENDING ADR-RPT-013; Check 517 keeps NOT_COMPLIANT and its 3 findings
Test data    : Check 517
<!-- TC:TC-RPT-023:END -->

<!-- TC:TC-RPT-035:START traces=AC-RPT-035,REQ-RPT-029,API-RPT-002,RULE-RPT-009 -->
### TC-RPT-035 — Listing refused without a service code
Derived from : AC-RPT-035  (REQ-RPT-029)
Exercises    : API-RPT-002 GET /api/v1/checks
Rule / code  : RULE-RPT-009 → RPT-400-REQUEST-KEYS-MISSING
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Checks exist for request `1001`
Host data    : none
Steps        : 1. GET /api/v1/checks?requestNumber=1001 (no serviceCode)
Expected     : 400 ProblemDetail code RPT-400-REQUEST-KEYS-MISSING; en: "Both a service code and a request number are needed to list Checks." · ar: PENDING ADR-RPT-013; no list returned
Test data    : requestNumber `1001`; serviceCode absent
<!-- TC:TC-RPT-035:END -->

<!-- TC:TC-RPT-039:START traces=AC-RPT-039,REQ-RPT-033,RULE-RPT-011 -->
### TC-RPT-039 — Second decision refused — the decision is final
Derived from : AC-RPT-039  (REQ-RPT-033)
Exercises    : in-process ReportStore.recordDecision (honours CON-RPT-006, called by Host Integration) — no HTTP operation
Rule / code  : RULE-RPT-011 → RPT-409-DECISION-ALREADY-RECORDED
Package      : SVC-API
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Check 532 is COMPLETED with decision REJECTED by `E-3307`
Host data    : none
Steps        : 1. recordDecision(532, APPROVED, `E-4410`, false)
               2. read Check 532
Expected     : refused with code RPT-409-DECISION-ALREADY-RECORDED; en: "Check 532 already has an Employee Decision." · ar: PENDING ADR-RPT-013; Check 532 keeps REJECTED by "E-3307" — the decision is recorded once and is final
Test data    : REJECTED / `E-3307` stored; APPROVED / `E-4410` sent
<!-- TC:TC-RPT-039:END -->

<!-- TC:TC-RPT-040:START traces=AC-RPT-040,REQ-RPT-034,RULE-RPT-012 -->
### TC-RPT-040 — Decision on a non-completed Check refused
Derived from : AC-RPT-040  (REQ-RPT-034)
Exercises    : in-process ReportStore.recordDecision (honours CON-RPT-006, called by Host Integration) — no HTTP operation
Rule / code  : RULE-RPT-012 → RPT-409-CHECK-NOT-COMPLETED
Package      : SVC-API
Scenario     : STATE · data class INVALID · language ALL
Preconditions: Check 533 is FAILED
Host data    : none
Steps        : 1. recordDecision(533, APPROVED, `E-3307`, false)
Expected     : refused with code RPT-409-CHECK-NOT-COMPLETED; en: "Check 533 is not completed; a decision can only be recorded on a completed Check." · ar: PENDING ADR-RPT-013; no decision recorded
Test data    : Check 533
<!-- TC:TC-RPT-040:END -->

<!-- TC:TC-RPT-041:START traces=AC-RPT-041,REQ-RPT-035,RULE-RPT-013 -->
### TC-RPT-041 — Decision code outside EMPLOYEE_DECISION refused
Derived from : AC-RPT-041  (REQ-RPT-035)
Exercises    : in-process ReportStore.recordDecision (honours CON-RPT-006, called by Host Integration) — no HTTP operation
Rule / code  : RULE-RPT-013 → RPT-400-DECISION-INCOMPLETE
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 534 is COMPLETED with no decision
Host data    : none
Steps        : 1. recordDecision(534, `MAYBE`, `E-3307`, false)
Expected     : refused with code RPT-400-DECISION-INCOMPLETE; en: "The decision was not recorded: `MAYBE` is not APPROVED or REJECTED." · ar: PENDING ADR-RPT-013; no decision recorded
Test data    : `MAYBE`
<!-- TC:TC-RPT-041:END -->

<!-- TC:TC-RPT-042:START traces=AC-RPT-042,REQ-RPT-035,RULE-RPT-013 -->
### TC-RPT-042 — Decision without the deciding employee refused
Derived from : AC-RPT-042  (REQ-RPT-035)
Exercises    : in-process ReportStore.recordDecision (honours CON-RPT-006, called by Host Integration) — no HTTP operation
Rule / code  : RULE-RPT-013 → RPT-400-DECISION-INCOMPLETE
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 535 is COMPLETED with no decision
Host data    : none
Steps        : 1. recordDecision(535, APPROVED, decidedBy blank, false)
Expected     : refused with code RPT-400-DECISION-INCOMPLETE; en: "The decision was not recorded: the deciding employee is missing." · ar: PENDING ADR-RPT-013; no decision recorded
Test data    : decidedBy blank
<!-- TC:TC-RPT-042:END -->

<!-- TC:TC-RPT-045:START traces=AC-RPT-045,REQ-RPT-038 -->
### TC-RPT-045 — Decision for an unknown Check refused
Derived from : AC-RPT-045  (REQ-RPT-038)
Exercises    : in-process ReportStore.recordDecision (honours CON-RPT-006, called by Host Integration) — no HTTP operation
Rule / code  : REQ-RPT-038 → RPT-404-CHECK-NOT-FOUND
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check run 997 exists
Host data    : none
Steps        : 1. recordDecision(997, APPROVED, `E-3307`, false)
Expected     : refused with code RPT-404-CHECK-NOT-FOUND; en: "Check 997 was not found." · ar: PENDING ADR-RPT-013; nothing recorded
Test data    : Check 997
<!-- TC:TC-RPT-045:END -->

<!-- TC:TC-RPT-046:START traces=AC-RPT-046,REQ-RPT-039,RULE-RPT-014 -->
### TC-RPT-046 — Approval API flag on a rejection refused (service and CHECK constraint)
Derived from : AC-RPT-046  (REQ-RPT-039)
Exercises    : in-process ReportStore.recordDecision (honours CON-RPT-006, called by Host Integration) — no HTTP operation; direct write to the service schema
Rule / code  : RULE-RPT-014 → RPT-422-APPROVAL-FLAG-ON-REJECTION
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 538 is COMPLETED with no decision
Host data    : none
Steps        : 1. recordDecision(538, REJECTED, `E-3307`, approvalApiExecuted true)
               2. UPDATE RPT_CHECK_RUN for Check 538 with EMPLOYEE_DECISION 'REJECTED' and APPROVAL_API_EXECUTED 1 directly in the service schema
Expected     : step 1 refused with code RPT-422-APPROVAL-FLAG-ON-REJECTION; en: "The decision was not recorded: only an APPROVED decision is executed through the Approval API." · ar: PENDING ADR-RPT-013; step 2 refused by CHECK constraint CHK_RPT_CHECK_RUN_DECISION; no decision recorded
Test data    : REJECTED; true
<!-- TC:TC-RPT-046:END -->

<!-- TC:TC-RPT-049:START traces=AC-RPT-049,REQ-RPT-041,API-RPT-003,RULE-RPT-015 -->
### TC-RPT-049 — Decision agreement refused without a service code
Derived from : AC-RPT-049  (REQ-RPT-041)
Exercises    : API-RPT-003 GET /api/v1/decision-agreement
Rule / code  : RULE-RPT-015 → RPT-400-SERVICE-CODE-MISSING
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: decided Checks exist
Host data    : none
Steps        : 1. GET /api/v1/decision-agreement (no serviceCode)
Expected     : 400 ProblemDetail code RPT-400-SERVICE-CODE-MISSING; en: "A service code is needed to read the decision agreement." · ar: PENDING ADR-RPT-013; nothing returned
Test data    : serviceCode absent
<!-- TC:TC-RPT-049:END -->

<!-- TC:TC-RPT-050:START traces=AC-RPT-050,REQ-RPT-042 -->
### TC-RPT-050 — Report inside the retention period kept by the purge
Derived from : AC-RPT-050  (REQ-RPT-042)
Exercises    : scheduled ReportPurgeService (no caller, no HTTP operation — ADR-RPT-010)
Rule / code  : —
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: aias.reports.retention-days = 365; Check 539 ended 364 days ago with its records
Host data    : none
Steps        : 1. run the purge
Expected     : Check 539 and all its Findings, Check Documents, Unread Queries and decision are still stored
Test data    : 365 days; ended 364 days ago (inside the period)
<!-- TC:TC-RPT-050:END -->

<!-- TC:TC-RPT-051:START traces=AC-RPT-051,REQ-RPT-043,API-RPT-001 -->
### TC-RPT-051 — Expired Check run purged with all its records
Derived from : AC-RPT-051  (REQ-RPT-043)
Exercises    : scheduled ReportPurgeService (no caller, no HTTP operation — ADR-RPT-010); read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: aias.reports.retention-days = 365; Check 540 ended 366 days ago with 3 findings, 2 Check Documents, 1 unread query and decision APPROVED
Host data    : none
Steps        : 1. run the purge
               2. count rows of Check 540 in RPT_CHECK_RUN, RPT_FINDING, RPT_CHECK_DOCUMENT, RPT_UNREAD_QUERY
               3. GET /api/v1/checks/540
Expected     : 0 rows in each of the four tables (hard delete, cascade); API-RPT-001 answers 404 RPT-404-CHECK-NOT-FOUND
Test data    : 365 days; ended 366 days ago
<!-- TC:TC-RPT-051:END -->

<!-- TC:TC-RPT-052:START traces=AC-RPT-052,REQ-RPT-044 -->
### TC-RPT-052 — No retention period, no purge
Derived from : AC-RPT-052  (REQ-RPT-044)
Exercises    : scheduled ReportPurgeService (no caller, no HTTP operation — ADR-RPT-010)
Rule / code  : —
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no aias.reports.retention-days configured; Check 541 ended 1000 days ago
Host data    : none
Steps        : 1. run the purge
               2. read the application log
Expected     : Check 541 still stored; the log holds "Report purge skipped: no valid report retention period is configured."
Test data    : retention absent; 1000 days
<!-- TC:TC-RPT-052:END -->

<!-- TC:TC-RPT-053:START traces=AC-RPT-053,REQ-RPT-045 -->
### TC-RPT-053 — Unfinished Check never purged
Derived from : AC-RPT-053  (REQ-RPT-045)
Exercises    : scheduled ReportPurgeService (no caller, no HTTP operation — ADR-RPT-010)
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class EDGE · language ALL
Preconditions: aias.reports.retention-days = 30; Check 542 is RUNNING, started 40 days ago
Host data    : none
Steps        : 1. run the purge
Expected     : Check 542 still stored
Test data    : 30 days; RUNNING; 40 days old
<!-- TC:TC-RPT-053:END -->

<!-- TC:TC-RPT-054:START traces=AC-RPT-054,REQ-RPT-046 -->
### TC-RPT-054 — Purge outcome logged
Derived from : AC-RPT-054  (REQ-RPT-046)
Exercises    : scheduled ReportPurgeService (no caller, no HTTP operation — ADR-RPT-010)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: aias.reports.retention-days = 365; 7 Check runs ended more than 365 days ago
Host data    : none
Steps        : 1. run the purge at 2026-10-02T02:00:00Z (clock fixed)
               2. read the application log
Expected     : the log holds "Report purge deleted 7 Check runs ended before 2025-10-02T02:00:00Z."
Test data    : 7 runs; 2026-10-02T02:00:00Z
<!-- TC:TC-RPT-054:END -->

<!-- TC:TC-RPT-059:START traces=AC-RPT-059,REQ-RPT-051,RULE-RPT-010 -->
### TC-RPT-059 — Incomplete failure refused
Derived from : AC-RPT-059  (REQ-RPT-051)
Exercises    : in-process result port failCheck (implements CON-CHK-009) — no HTTP operation
Rule / code  : RULE-RPT-010 → RPT-400-FAILURE-INCOMPLETE
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 543 is RUNNING
Host data    : none
Steps        : 1. failCheck(543, INTERNAL_ERROR, blank detail, an end time)
Expected     : refused with code RPT-400-FAILURE-INCOMPLETE; en: "The failure of Check 543 was not stored: detail is missing." · ar: PENDING ADR-RPT-013; Check 543 stays RUNNING
Test data    : detail blank
<!-- TC:TC-RPT-059:END -->

<!-- TC:TC-RPT-060:START traces=AC-RPT-060,REQ-RPT-052 -->
### TC-RPT-060 — Purge deletes each Check run whole
Derived from : AC-RPT-060  (REQ-RPT-052)
Exercises    : scheduled ReportPurgeService (no caller, no HTTP operation — ADR-RPT-010)
Rule / code  : —
Package      : SVC-API
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: Checks 544 and 545 have expired; the deletion of a Finding of Check 544 is made to fail (fault injected)
Host data    : none
Steps        : 1. run the purge
               2. read both Checks and the log
Expected     : Check 544 still stored with all its records; Check 545 no longer exists; the log counts 1 deleted Check run
Test data    : Checks 544, 545
<!-- TC:TC-RPT-060:END -->
<!-- SUB:RULE-SCENARIOS:END -->

<!-- SUB:API-SCENARIOS:START traces=AC-RPT-001,AC-RPT-002,AC-RPT-011,AC-RPT-013,AC-RPT-014,AC-RPT-015,AC-RPT-022,AC-RPT-024,AC-RPT-025,AC-RPT-026,AC-RPT-027,AC-RPT-028,AC-RPT-029,AC-RPT-030,AC-RPT-031,AC-RPT-032,AC-RPT-033,AC-RPT-034,AC-RPT-036,AC-RPT-037,AC-RPT-038,AC-RPT-043,AC-RPT-044,AC-RPT-047,AC-RPT-048,AC-RPT-055,AC-RPT-056,AC-RPT-058,REQ-RPT-001,REQ-RPT-002,REQ-RPT-008,REQ-RPT-010,REQ-RPT-011,REQ-RPT-012,REQ-RPT-019,REQ-RPT-021,REQ-RPT-022,REQ-RPT-023,REQ-RPT-024,REQ-RPT-025,REQ-RPT-026,REQ-RPT-027,REQ-RPT-028,REQ-RPT-030,REQ-RPT-031,REQ-RPT-032,REQ-RPT-036,REQ-RPT-037,REQ-RPT-040,REQ-RPT-047,REQ-RPT-048,REQ-RPT-050,AC-RPT-061,REQ-RPT-053 -->
### API-SCENARIOS

<!-- TC:TC-RPT-001:START traces=AC-RPT-001,REQ-RPT-001 -->
### TC-RPT-001 — Check run created, identifier returned
Derived from : AC-RPT-001  (REQ-RPT-001)
Exercises    : in-process result port createCheckRun (implements CON-CHK-006) — no HTTP operation (ADR-RPT-006); read back through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: no Check run exists
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. createCheckRun(serviceCode ⟨SVC-A⟩, versionNumber 3, fetchMode path, requestNumber `1001`, employeeId `E-2041`, status RUNNING, startedAt 2026-10-01T09:00:00Z)
               2. read the returned checkId through API-RPT-001
Expected     : a new checkId is returned; exactly 1 Check run is stored; API-RPT-001 answers 200 with serviceCode ⟨SVC-A⟩, versionNumber 3, fetchMode path, requestNumber "1001", employeeId "E-2041", status RUNNING, startedAt 2026-10-01T09:00:00Z
Test data    : ⟨SVC-A⟩ = the AC's service code; version 3; `path`; `1001`; `E-2041`; RUNNING; 2026-10-01T09:00:00Z
<!-- TC:TC-RPT-001:END -->

<!-- TC:TC-RPT-002:START traces=AC-RPT-002,REQ-RPT-002,API-RPT-001 -->
### TC-RPT-002 — Host identifiers kept exactly as sent
Derived from : AC-RPT-002  (REQ-RPT-002)
Exercises    : in-process result port createCheckRun (implements CON-CHK-006) — no HTTP operation (ADR-RPT-006); read back through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: no Check run exists
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. createCheckRun with requestNumber `00-1001/A` and employeeId ` e.2041 ` (leading and trailing space), other values valid
               2. read the Check through API-RPT-001
Expected     : requestNumber is "00-1001/A" and employeeId is " e.2041 " — byte-identical, no trim, no case change
Test data    : `00-1001/A`; ` e.2041 `; ⟨SVC-A⟩ for the service code
<!-- TC:TC-RPT-002:END -->

<!-- TC:TC-RPT-011:START traces=AC-RPT-011,REQ-RPT-008,API-RPT-001 -->
### TC-RPT-011 — Completed report stored
Derived from : AC-RPT-011  (REQ-RPT-008)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation; read back through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 505 is RUNNING
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. completeCheck(505, NOT_COMPLIANT, 3 findings, 2 document outcomes, 1 unread query, metadata with model `gemini-flash-lite` and end time 2026-10-01T09:10:00Z agreeing with the Check run)
               2. read Check 505 through API-RPT-001
Expected     : Check 505 is COMPLETED, overallStatus NOT_COMPLIANT, comparisonModel "gemini-flash-lite", endedAt 2026-10-01T09:10:00Z; 3 findings, 2 documents, 1 unread query
Test data    : NOT_COMPLIANT; `gemini-flash-lite`; 2026-10-01T09:10:00Z; counts 3 / 2 / 1
<!-- TC:TC-RPT-011:END -->

<!-- TC:TC-RPT-013:START traces=AC-RPT-013,REQ-RPT-010,API-RPT-001 -->
### TC-RPT-013 — Report order kept
Derived from : AC-RPT-013  (REQ-RPT-010)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation; read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 507 is RUNNING
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. completeCheck(507, …) with findings on conditions "GPA at least 3.0", "⟨DOC-TYPE-1⟩ present", "⟨DOC-TYPE-2⟩ present" in that order
               2. read Check 507 through API-RPT-001
Expected     : findings returned at positions 1, 2, 3 in the same order as handed over
Test data    : conditions as in the AC, with ⟨DOC-TYPE-1⟩ / ⟨DOC-TYPE-2⟩ for the two document types
<!-- TC:TC-RPT-013:END -->

<!-- TC:TC-RPT-014:START traces=AC-RPT-014,REQ-RPT-011,API-RPT-001 -->
### TC-RPT-014 — Missing and unreadable documents kept with their reason
Derived from : AC-RPT-014  (REQ-RPT-011)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation; read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Check 508 is RUNNING
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. completeCheck(508, …) with document outcomes ⟨DOC-TYPE-1⟩ READ, ⟨DOC-TYPE-2⟩ MISSING, ⟨DOC-TYPE-1⟩ UNREADABLE with reason TOO_LARGE and detail "file of 31 MB exceeds 20 MB"
               2. read Check 508
Expected     : 3 documents stored and returned; the third has unreadableReason TOO_LARGE and detail "file of 31 MB exceeds 20 MB"; the MISSING one is present, not skipped
Test data    : READ / MISSING / UNREADABLE; TOO_LARGE; detail text as in the AC
<!-- TC:TC-RPT-014:END -->

<!-- TC:TC-RPT-015:START traces=AC-RPT-015,REQ-RPT-012,API-RPT-001 -->
### TC-RPT-015 — Unread service query kept
Derived from : AC-RPT-015  (REQ-RPT-012)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation; read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 509 is RUNNING
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. completeCheck(509, NEEDS_MANUAL_REVIEW, …, unread query `request_details` with detail "more than 500 rows")
               2. read Check 509
Expected     : 1 unread query `request_details` with detail "more than 500 rows" is stored and returned
Test data    : NEEDS_MANUAL_REVIEW; `request_details`; "more than 500 rows"
<!-- TC:TC-RPT-015:END -->

<!-- TC:TC-RPT-022:START traces=AC-RPT-022,REQ-RPT-019,API-RPT-001 -->
### TC-RPT-022 — No document content kept
Derived from : AC-RPT-022  (REQ-RPT-019)
Exercises    : API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Check 516 is COMPLETED with a document outcome ⟨DOC-TYPE-1⟩ READ
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. read Check 516 through API-RPT-001
               2. inspect the columns of RPT_CHECK_DOCUMENT
Expected     : the document entry holds exactly position (1), documentType, sourceMode, readStatus READ, unreadableReason (null) and detail — none of them, and no column, holds the document's raw text, image data or file bytes
Test data    : ⟨DOC-TYPE-1⟩ READ
<!-- TC:TC-RPT-022:END -->

<!-- TC:TC-RPT-024:START traces=AC-RPT-024,REQ-RPT-021 -->
### TC-RPT-024 — One Check read for the Check Engine
Derived from : AC-RPT-024  (REQ-RPT-021)
Exercises    : in-process result port getCheck (implements CON-CHK-010) — no HTTP operation
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 518 is AWAITING_DOCUMENTS for ⟨SVC-A⟩ version 3, fetchMode manual, request `1001`, employee `E-2041`
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. getCheck(518)
Expected     : returns checkId 518, status AWAITING_DOCUMENTS, serviceCode ⟨SVC-A⟩, versionNumber 3, fetchMode manual, requestNumber "1001", employeeId "E-2041" and the start time
Test data    : values as in the AC
<!-- TC:TC-RPT-024:END -->

<!-- TC:TC-RPT-025:START traces=AC-RPT-025,REQ-RPT-022 -->
### TC-RPT-025 — Unfinished Checks listed oldest first
Derived from : AC-RPT-025  (REQ-RPT-022)
Exercises    : in-process result port listUnfinishedChecks (implements CON-CHK-011) — no HTTP operation
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Checks 519 (RUNNING, started 09:00), 520 (AWAITING_DOCUMENTS, started 08:30) and 521 (COMPLETED) exist
Host data    : none
Steps        : 1. listUnfinishedChecks()
Expected     : returns [520, 519] in that order (oldest first), each with status and startedAt; 521 absent
Test data    : Checks 519, 520, 521
<!-- TC:TC-RPT-025:END -->

<!-- TC:TC-RPT-026:START traces=AC-RPT-026,REQ-RPT-022 -->
### TC-RPT-026 — No unfinished Check — empty list
Derived from : AC-RPT-026  (REQ-RPT-022)
Exercises    : in-process result port listUnfinishedChecks (implements CON-CHK-011) — no HTTP operation
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: every stored Check is COMPLETED or FAILED
Host data    : none
Steps        : 1. listUnfinishedChecks()
Expected     : returns an empty list
Test data    : —
<!-- TC:TC-RPT-026:END -->

<!-- TC:TC-RPT-027:START traces=AC-RPT-027,REQ-RPT-023,API-RPT-001 -->
### TC-RPT-027 — Running Check read without report
Derived from : AC-RPT-027  (REQ-RPT-023)
Exercises    : API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 522 is RUNNING
Host data    : none
Steps        : 1. GET /api/v1/checks/522
Expected     : 200; status RUNNING with serviceCode, versionNumber, requestNumber, startedAt, runningSince; overallStatus null; findings, documents, unreadQueries empty arrays
Test data    : Check 522
<!-- TC:TC-RPT-027:END -->

<!-- TC:TC-RPT-028:START traces=AC-RPT-028,REQ-RPT-023,API-RPT-001 -->
### TC-RPT-028 — Completed Check read with its report
Derived from : AC-RPT-028  (REQ-RPT-023)
Exercises    : API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 523 is COMPLETED with overallStatus NOT_COMPLIANT, 3 findings, 2 Check Documents, 0 unread queries, no decision
Host data    : none
Steps        : 1. GET /api/v1/checks/523
Expected     : 200; status COMPLETED, overallStatus NOT_COMPLIANT, comparisonModel present, 3 findings, 2 documents, 0 unread queries, decision null
Test data    : Check 523
<!-- TC:TC-RPT-028:END -->

<!-- TC:TC-RPT-029:START traces=AC-RPT-029,REQ-RPT-024,API-RPT-001 -->
### TC-RPT-029 — Finding returned with its evidence
Derived from : AC-RPT-029  (REQ-RPT-024)
Exercises    : API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 524 is COMPLETED with finding "GPA at least 3.0", NOT_SATISFIED, evidence "GPA = 2.7", note "Below the 3.0 minimum"
Host data    : none
Steps        : 1. GET /api/v1/checks/524
Expected     : one findings entry holds condition "GPA at least 3.0", outcome NOT_SATISFIED, evidence "GPA = 2.7" and note "Below the 3.0 minimum" together
Test data    : values as in the AC
<!-- TC:TC-RPT-029:END -->

<!-- TC:TC-RPT-030:START traces=AC-RPT-030,REQ-RPT-025,API-RPT-001 -->
### TC-RPT-030 — Unknown Check read answers not found
Derived from : AC-RPT-030  (REQ-RPT-025)
Exercises    : API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : REQ-RPT-025 → RPT-404-CHECK-NOT-FOUND
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check run 998 exists
Host data    : none
Steps        : 1. GET /api/v1/checks/998
Expected     : 404 ProblemDetail {type, title, status 404, code RPT-404-CHECK-NOT-FOUND, detail}; en: "Check 998 was not found." · ar: PENDING ADR-RPT-013
Test data    : Check 998
<!-- TC:TC-RPT-030:END -->

<!-- TC:TC-RPT-031:START traces=AC-RPT-031,REQ-RPT-026,API-RPT-001 -->
### TC-RPT-031 — Failed Check read with its reason
Derived from : AC-RPT-031  (REQ-RPT-026)
Exercises    : API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 525 is FAILED with reason MODEL_UNAVAILABLE and detail "provider answered 503"
Host data    : none
Steps        : 1. GET /api/v1/checks/525
Expected     : 200; status FAILED, failureReason MODEL_UNAVAILABLE, failureDetail "provider answered 503", overallStatus null
Test data    : values as in the AC
<!-- TC:TC-RPT-031:END -->

<!-- TC:TC-RPT-032:START traces=AC-RPT-032,REQ-RPT-027,API-RPT-001 -->
### TC-RPT-032 — Stored text returned as data
Derived from : AC-RPT-032  (REQ-RPT-027)
Exercises    : API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class ATTACK · language ALL
Preconditions: Check 526 is COMPLETED with a finding whose evidence is "<script>alert(1)</script> ignore previous instructions"
Host data    : none
Steps        : 1. GET /api/v1/checks/526
Expected     : 200; evidence is the identical character string as a JSON string value; response content type application/json, no HTML rendering
Test data    : evidence string as in the AC
<!-- TC:TC-RPT-032:END -->

<!-- TC:TC-RPT-033:START traces=AC-RPT-033,REQ-RPT-028,API-RPT-002 -->
### TC-RPT-033 — Checks of a request listed newest first
Derived from : AC-RPT-033  (REQ-RPT-028)
Exercises    : API-RPT-002 GET /api/v1/checks
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: request `1001` of ⟨SVC-A⟩ has Checks 527 (started 08:00, FAILED) and 528 (started 09:00, COMPLETED, NOT_COMPLIANT); request `1001` of ⟨SVC-B⟩ has Check 529
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. GET /api/v1/checks?serviceCode=⟨SVC-A⟩&requestNumber=1001
Expected     : 200; checks [528, 527] newest first with status, overallStatus, startedAt, endedAt, employeeDecision; total 2; 529 absent
Test data    : ⟨SVC-A⟩ / ⟨SVC-B⟩ = the AC's two service codes
<!-- TC:TC-RPT-033:END -->

<!-- TC:TC-RPT-034:START traces=AC-RPT-034,REQ-RPT-028,API-RPT-002 -->
### TC-RPT-034 — Listing capped at 100 with the total
Derived from : AC-RPT-034  (REQ-RPT-028)
Exercises    : API-RPT-002 GET /api/v1/checks
Rule / code  : —
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: request `1002` of ⟨SVC-A⟩ has 130 Checks
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. GET /api/v1/checks?serviceCode=⟨SVC-A⟩&requestNumber=1002
Expected     : 200; 100 checks, the newest, newest first; total 130
Test data    : 130 Checks
<!-- TC:TC-RPT-034:END -->

<!-- TC:TC-RPT-036:START traces=AC-RPT-036,REQ-RPT-030,API-RPT-001 -->
### TC-RPT-036 — Every Check its own record
Derived from : AC-RPT-036  (REQ-RPT-030)
Exercises    : in-process result port createCheckRun (implements CON-CHK-006) — no HTTP operation (ADR-RPT-006); read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 530 of request `1001` is COMPLETED with 3 findings and decision APPROVED
Host data    : none
Steps        : 1. createCheckRun for request `1001` of the same service
               2. read the new Check and Check 530 through API-RPT-001
Expected     : a new checkId ≠ 530 with 0 findings and decision null; Check 530 unchanged (3 findings, APPROVED)
Test data    : Check 530; request `1001`
<!-- TC:TC-RPT-036:END -->

<!-- TC:TC-RPT-037:START traces=AC-RPT-037,REQ-RPT-031,API-RPT-002 -->
### TC-RPT-037 — At most 100 Checks in one listing
Derived from : AC-RPT-037  (REQ-RPT-031)
Exercises    : API-RPT-002 GET /api/v1/checks
Rule / code  : —
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: request `1003` of ⟨SVC-A⟩ has 101 Checks
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. GET /api/v1/checks?serviceCode=⟨SVC-A⟩&requestNumber=1003
Expected     : 200; exactly 100 checks; total 101
Test data    : 101 Checks (limit 100 + 1)
<!-- TC:TC-RPT-037:END -->

<!-- TC:TC-RPT-038:START traces=AC-RPT-038,REQ-RPT-032,API-RPT-001 -->
### TC-RPT-038 — Employee Decision recorded
Derived from : AC-RPT-038  (REQ-RPT-032)
Exercises    : in-process ReportStore.recordDecision (honours CON-RPT-006, called by Host Integration) — no HTTP operation; read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 531 is COMPLETED with overallStatus COMPLIANT and no decision
Host data    : none
Steps        : 1. recordDecision(531, APPROVED, `E-3307`, approvalApiExecuted false)
               2. GET /api/v1/checks/531
Expected     : decision {employeeDecision APPROVED, decidedBy "E-3307", approvalApiExecuted false, decidedAt set}; overallStatus and findings unchanged
Test data    : APPROVED; `E-3307`; false
<!-- TC:TC-RPT-038:END -->

<!-- TC:TC-RPT-043:START traces=AC-RPT-043,REQ-RPT-036,API-RPT-001 -->
### TC-RPT-043 — Execution through the Approval API recorded
Derived from : AC-RPT-043  (REQ-RPT-036)
Exercises    : in-process ReportStore.recordDecision (honours CON-RPT-006, called by Host Integration) — no HTTP operation; read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 536 is COMPLETED with no decision
Host data    : none
Steps        : 1. recordDecision(536, APPROVED, `E-3307`, approvalApiExecuted true)
               2. GET /api/v1/checks/536
Expected     : decision APPROVED with approvalApiExecuted true
Test data    : APPROVED; true
<!-- TC:TC-RPT-043:END -->

<!-- TC:TC-RPT-044:START traces=AC-RPT-044,REQ-RPT-037 -->
### TC-RPT-044 — The Report Store never approves
Derived from : AC-RPT-044  (REQ-RPT-037)
Exercises    : Check 537 left undecided; outbound traffic of the RPT package observed
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Check 537 is COMPLETED with overallStatus COMPLIANT for a service whose Approval API is enabled
Host data    : none
Steps        : 1. let 24 hours (clock advanced) pass with no decision handed over
               2. read Check 537; inspect outbound HTTP calls made by RPT classes
Expected     : decision null; 0 calls to any Approval API from RPT
Test data    : 24 h
<!-- TC:TC-RPT-044:END -->

<!-- TC:TC-RPT-047:START traces=AC-RPT-047,REQ-RPT-040,API-RPT-003 -->
### TC-RPT-047 — Decision agreement counted per version
Derived from : AC-RPT-047  (REQ-RPT-040)
Exercises    : API-RPT-003 GET /api/v1/decision-agreement
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: ⟨SVC-A⟩ version 3 has decided Checks 4 COMPLIANT+APPROVED, 1 COMPLIANT+REJECTED, 2 NOT_COMPLIANT+REJECTED; version 2 has 1 NEEDS_MANUAL_REVIEW+APPROVED; 3 undecided Checks exist
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. GET /api/v1/decision-agreement?serviceCode=⟨SVC-A⟩
Expected     : 200; rows (3, COMPLIANT, APPROVED, 4), (3, COMPLIANT, REJECTED, 1), (3, NOT_COMPLIANT, REJECTED, 2), (2, NEEDS_MANUAL_REVIEW, APPROVED, 1); undecided Checks not counted
Test data    : counts as in the AC
<!-- TC:TC-RPT-047:END -->

<!-- TC:TC-RPT-048:START traces=AC-RPT-048,REQ-RPT-040,API-RPT-003 -->
### TC-RPT-048 — Decision agreement empty for a service with no decision
Derived from : AC-RPT-048  (REQ-RPT-040)
Exercises    : API-RPT-003 GET /api/v1/decision-agreement
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: no decided Check exists for ⟨SVC-B⟩
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. GET /api/v1/decision-agreement?serviceCode=⟨SVC-B⟩
Expected     : 200; empty array
Test data    : ⟨SVC-B⟩ = the AC's second service code
<!-- TC:TC-RPT-048:END -->

<!-- TC:TC-RPT-055:START traces=AC-RPT-055,REQ-RPT-047 -->
### TC-RPT-055 — No access to host data
Derived from : AC-RPT-055  (REQ-RPT-047)
Exercises    : every RPT operation (result port, decision, the three reads, the purge) with the host connection monitored
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: the service runs with a Report Store and an activated `main-db` connection
Host data    : none
Steps        : 1. exercise createCheckRun, markRunning, completeCheck, failCheck, getCheck, listUnfinishedChecks, recordDecision, API-RPT-001, API-RPT-002, API-RPT-003 and the purge
               2. count queries sent through any host connection by RPT; run the architecture rule over the RPT package
Expected     : 0 host queries from RPT; no RPT class imports the query channel types
Test data    : `main-db`
<!-- TC:TC-RPT-055:END -->

<!-- TC:TC-RPT-056:START traces=AC-RPT-056,REQ-RPT-048,API-RPT-001 -->
### TC-RPT-056 — No file opened
Derived from : AC-RPT-056  (REQ-RPT-048)
Exercises    : API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class ATTACK · language ALL
Preconditions: a completed report holds a Check Document whose detail names the path "/data/att/1001/t.pdf"
Host data    : none
Steps        : 1. GET the Check through API-RPT-001 with file-system access monitored
Expected     : detail returned as the text "/data/att/1001/t.pdf"; 0 files opened
Test data    : path as in the AC
<!-- TC:TC-RPT-056:END -->

<!-- TC:TC-RPT-058:START traces=AC-RPT-058,REQ-RPT-050,API-RPT-002 -->
### TC-RPT-058 — Read filters bound as parameters
Derived from : AC-RPT-058  (REQ-RPT-050)
Exercises    : API-RPT-002 GET /api/v1/checks
Rule / code  : —
Package      : SVC-API
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: a Check of request `1001` exists
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. GET /api/v1/checks?serviceCode=⟨SVC-A⟩&requestNumber=1001' OR '1'='1 (URL-encoded)
Expected     : 200; checks empty; total 0; no other request's Check returned
Test data    : requestNumber `1001' OR '1'='1`
<!-- TC:TC-RPT-058:END -->

<!-- TC:TC-RPT-061:START traces=AC-RPT-061,REQ-RPT-053 -->
### TC-RPT-061 — Hand-over with an undeclared field cannot reach the store
Derived from : AC-RPT-061  (REQ-RPT-053)
Exercises    : the result port operations completeCheck and failCheck — value types inspected (ADR-RPT-018)
Rule / code  : — (structural; an undeclared value in a declared code field stays RULE-RPT-006 → RPT-422-UNKNOWN-CODE, TC-RPT-018)
Package      : PORTS
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: the RPT result port implementation is on the classpath
Host data    : none
Steps        : 1. list, by reflection, the record components of the finding, document outcome, unread query and metadata value types accepted by completeCheck, and the parameters of failCheck
               2. run the architecture rule over those types: no component typed as a map, a byte array or an untyped object, and no "extra attributes" holder
               3. list the columns of RPT_FINDING, RPT_CHECK_DOCUMENT and RPT_UNREAD_QUERY
Expected     : finding = {condition, outcome, evidence, note}; document outcome = {documentType, sourceMode, readStatus, reason, detail}; unread query = {queryName, detail}; metadata = {serviceCode, versionNumber, fetchMode, comparisonModel, employeeId, startedAt, endedAt}; failCheck = (checkId, failureReason, detail, endedAt); the architecture rule passes (0 open components); no table holds a column beyond its DBF-bound fields and createdAt / updatedAt
Test data    : —
<!-- TC:TC-RPT-061:END -->
<!-- SUB:API-SCENARIOS:END -->

<!-- SUB:MODEL-EVAL:START traces=AC-RPT-057,REQ-RPT-049 -->
### MODEL-EVAL

<!-- TC:TC-RPT-057:START traces=AC-RPT-057,REQ-RPT-049 -->
### TC-RPT-057 — No model call
Derived from : AC-RPT-057  (REQ-RPT-049)
Exercises    : every RPT operation with model traffic monitored
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: a comparison model is configured for the service
Host data    : none
Steps        : 1. create, complete, read, decide and purge a Check run
               2. count requests to any model endpoint from RPT; run the architecture rule (no RPT class imports Spring AI)
Expected     : 0 model requests from RPT; rule passes — repeated on every model change with the fixed known-result request set
Test data    : —
<!-- TC:TC-RPT-057:END -->
<!-- SUB:MODEL-EVAL:END -->

<!-- PHASE:TEST-PLAN-BE:END -->

## TC TRACEABILITY INDEX

| AC | REQ | TC | API / operation | RULE → code | Package |
|---|---|---|---|---|---|
| AC-RPT-001 | REQ-RPT-001 | TC-RPT-001 | in-process | — | PORTS |
| AC-RPT-002 | REQ-RPT-002 | TC-RPT-002 | API-RPT-001 | — | PORTS |
| AC-RPT-003 | REQ-RPT-003 | TC-RPT-003 | in-process | RULE-RPT-001 → RPT-400-CHECK-RUN-INCOMPLETE | PORTS |
| AC-RPT-004 | REQ-RPT-004 | TC-RPT-004 | in-process | RULE-RPT-002 → RPT-422-INITIAL-STATUS-MISMATCH | PORTS |
| AC-RPT-005 | REQ-RPT-004 | TC-RPT-005 | in-process | RULE-RPT-002 → RPT-422-INITIAL-STATUS-MISMATCH | PORTS |
| AC-RPT-006 | REQ-RPT-005 | TC-RPT-006 | in-process | RULE-RPT-003 (allowed transition AWAITING_DOCUMENTS → RUNNING) | PORTS |
| AC-RPT-007 | REQ-RPT-005 | TC-RPT-007 | in-process | RULE-RPT-003 (allowed transition RUNNING → RUNNING) | PORTS |
| AC-RPT-008 | REQ-RPT-006 | TC-RPT-008 | in-process | RULE-RPT-003 → RPT-409-CHECK-ENDED | PORTS |
| AC-RPT-009 | REQ-RPT-006 | TC-RPT-009 | in-process | RULE-RPT-003 → RPT-409-CHECK-NOT-RUNNING | PORTS |
| AC-RPT-010 | REQ-RPT-007 | TC-RPT-010 | in-process | REQ-RPT-007 → RPT-404-CHECK-NOT-FOUND | PORTS |
| AC-RPT-011 | REQ-RPT-008 | TC-RPT-011 | API-RPT-001 | — | PORTS |
| AC-RPT-012 | REQ-RPT-009 | TC-RPT-012 | in-process | RULE-RPT-006 → RPT-422-UNKNOWN-CODE (report not stored) | PORTS |
| AC-RPT-013 | REQ-RPT-010 | TC-RPT-013 | API-RPT-001 | — | PORTS |
| AC-RPT-014 | REQ-RPT-011 | TC-RPT-014 | API-RPT-001 | — | PORTS |
| AC-RPT-015 | REQ-RPT-012 | TC-RPT-015 | API-RPT-001 | — | PORTS |
| AC-RPT-016 | REQ-RPT-013 | TC-RPT-016 | in-process | RULE-RPT-004 → RPT-422-METADATA-MISMATCH | PORTS |
| AC-RPT-017 | REQ-RPT-014 | TC-RPT-017 | in-process | RULE-RPT-005 → RPT-422-COMPLIANT-NOT-VERIFIED | PORTS |
| AC-RPT-018 | REQ-RPT-015 | TC-RPT-018 | in-process | RULE-RPT-006 → RPT-422-UNKNOWN-CODE | PORTS |
| AC-RPT-019 | REQ-RPT-016 | TC-RPT-019 | in-process | RULE-RPT-007 → RPT-422-FINDING-INCOMPLETE | PORTS |
| AC-RPT-020 | REQ-RPT-017 | TC-RPT-020 | in-process | RULE-RPT-008 → RPT-422-DOCUMENT-REASON-MISMATCH | PORTS |
| AC-RPT-021 | REQ-RPT-018 | TC-RPT-021 | API-RPT-001 | RULE-RPT-003 (allowed transition RUNNING → FAILED) | PORTS |
| AC-RPT-022 | REQ-RPT-019 | TC-RPT-022 | API-RPT-001 | — | SVC-API |
| AC-RPT-023 | REQ-RPT-020 | TC-RPT-023 | in-process | RULE-RPT-003 → RPT-409-CHECK-ENDED | PORTS |
| AC-RPT-024 | REQ-RPT-021 | TC-RPT-024 | in-process | — | PORTS |
| AC-RPT-025 | REQ-RPT-022 | TC-RPT-025 | in-process | — | PORTS |
| AC-RPT-026 | REQ-RPT-022 | TC-RPT-026 | in-process | — | PORTS |
| AC-RPT-027 | REQ-RPT-023 | TC-RPT-027 | API-RPT-001 | — | SVC-API |
| AC-RPT-028 | REQ-RPT-023 | TC-RPT-028 | API-RPT-001 | — | SVC-API |
| AC-RPT-029 | REQ-RPT-024 | TC-RPT-029 | API-RPT-001 | — | SVC-API |
| AC-RPT-030 | REQ-RPT-025 | TC-RPT-030 | API-RPT-001 | REQ-RPT-025 → RPT-404-CHECK-NOT-FOUND | SVC-API |
| AC-RPT-031 | REQ-RPT-026 | TC-RPT-031 | API-RPT-001 | — | SVC-API |
| AC-RPT-032 | REQ-RPT-027 | TC-RPT-032 | API-RPT-001 | — | SVC-API |
| AC-RPT-033 | REQ-RPT-028 | TC-RPT-033 | API-RPT-002 | — | SVC-API |
| AC-RPT-034 | REQ-RPT-028 | TC-RPT-034 | API-RPT-002 | — | SVC-API |
| AC-RPT-035 | REQ-RPT-029 | TC-RPT-035 | API-RPT-002 | RULE-RPT-009 → RPT-400-REQUEST-KEYS-MISSING | SVC-API |
| AC-RPT-036 | REQ-RPT-030 | TC-RPT-036 | API-RPT-001 | — | PORTS |
| AC-RPT-037 | REQ-RPT-031 | TC-RPT-037 | API-RPT-002 | — | SVC-API |
| AC-RPT-038 | REQ-RPT-032 | TC-RPT-038 | API-RPT-001 | — | SVC-API |
| AC-RPT-039 | REQ-RPT-033 | TC-RPT-039 | in-process | RULE-RPT-011 → RPT-409-DECISION-ALREADY-RECORDED | SVC-API |
| AC-RPT-040 | REQ-RPT-034 | TC-RPT-040 | in-process | RULE-RPT-012 → RPT-409-CHECK-NOT-COMPLETED | SVC-API |
| AC-RPT-041 | REQ-RPT-035 | TC-RPT-041 | in-process | RULE-RPT-013 → RPT-400-DECISION-INCOMPLETE | SVC-API |
| AC-RPT-042 | REQ-RPT-035 | TC-RPT-042 | in-process | RULE-RPT-013 → RPT-400-DECISION-INCOMPLETE | SVC-API |
| AC-RPT-043 | REQ-RPT-036 | TC-RPT-043 | API-RPT-001 | — | SVC-API |
| AC-RPT-044 | REQ-RPT-037 | TC-RPT-044 | in-process | — | SVC-API |
| AC-RPT-045 | REQ-RPT-038 | TC-RPT-045 | in-process | REQ-RPT-038 → RPT-404-CHECK-NOT-FOUND | SVC-API |
| AC-RPT-046 | REQ-RPT-039 | TC-RPT-046 | in-process | RULE-RPT-014 → RPT-422-APPROVAL-FLAG-ON-REJECTION | SVC-API |
| AC-RPT-047 | REQ-RPT-040 | TC-RPT-047 | API-RPT-003 | — | SVC-API |
| AC-RPT-048 | REQ-RPT-040 | TC-RPT-048 | API-RPT-003 | — | SVC-API |
| AC-RPT-049 | REQ-RPT-041 | TC-RPT-049 | API-RPT-003 | RULE-RPT-015 → RPT-400-SERVICE-CODE-MISSING | SVC-API |
| AC-RPT-050 | REQ-RPT-042 | TC-RPT-050 | in-process | — | SVC-API |
| AC-RPT-051 | REQ-RPT-043 | TC-RPT-051 | API-RPT-001 | — | SVC-API |
| AC-RPT-052 | REQ-RPT-044 | TC-RPT-052 | in-process | — | SVC-API |
| AC-RPT-053 | REQ-RPT-045 | TC-RPT-053 | in-process | — | SVC-API |
| AC-RPT-054 | REQ-RPT-046 | TC-RPT-054 | in-process | — | SVC-API |
| AC-RPT-055 | REQ-RPT-047 | TC-RPT-055 | in-process | — | PORTS |
| AC-RPT-056 | REQ-RPT-048 | TC-RPT-056 | API-RPT-001 | — | SVC-API |
| AC-RPT-057 | REQ-RPT-049 | TC-RPT-057 | in-process | — | PORTS |
| AC-RPT-058 | REQ-RPT-050 | TC-RPT-058 | API-RPT-002 | — | SVC-API |
| AC-RPT-059 | REQ-RPT-051 | TC-RPT-059 | in-process | RULE-RPT-010 → RPT-400-FAILURE-INCOMPLETE | PORTS |
| AC-RPT-060 | REQ-RPT-052 | TC-RPT-060 | in-process | — | SVC-API |
| AC-RPT-061 | REQ-RPT-053 | TC-RPT-061 | in-process | — (structural, ADR-RPT-018) | PORTS |

Package → TC: PORTS: 30 (TC-RPT-001 …) · SVC-API: 31 (TC-RPT-022 …) · CORE, DATA-DOM, ALIGN-BE: no_tests (profile) · CROSS-MOD: 0 edges, no unit
XM → TC: none (0 XM)

## COVERAGE

AC covered 61/61 ✓ · REQ covered 53/53 ✓ · API covered 3/3 (API-RPT-001, API-RPT-002, API-RPT-003) ✓ · XM edges covered 0/0 (none declared) · retention purge: TC-RPT-050 … TC-RPT-054, TC-RPT-060 · one-time final decision: TC-RPT-039 · COMPLIANT only when every finding SATISFIED: TC-RPT-017 · closed-list CHECK constraints: TC-RPT-004, TC-RPT-018, TC-RPT-020, TC-RPT-046
