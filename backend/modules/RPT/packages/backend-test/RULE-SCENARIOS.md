<!-- source: PHASE:TEST-PLAN-BE / SUB:RULE-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-RPT-003, AC-RPT-004, AC-RPT-005, AC-RPT-006, AC-RPT-007, AC-RPT-008, AC-RPT-009, AC-RPT-010, AC-RPT-012, AC-RPT-016, AC-RPT-017, AC-RPT-018, AC-RPT-019, AC-RPT-020, AC-RPT-021, AC-RPT-023, AC-RPT-035, AC-RPT-039, AC-RPT-040, AC-RPT-041, AC-RPT-042, AC-RPT-045, AC-RPT-046, AC-RPT-049, AC-RPT-050, AC-RPT-051, AC-RPT-052, AC-RPT-053, AC-RPT-054, AC-RPT-059, AC-RPT-060, AC-RPT-062, AC-RPT-063, AC-RPT-064, API-RPT-001, API-RPT-002, API-RPT-003, REQ-RPT-003, REQ-RPT-004, REQ-RPT-005, REQ-RPT-006, REQ-RPT-007, REQ-RPT-009, REQ-RPT-013, REQ-RPT-014, REQ-RPT-015, REQ-RPT-016, REQ-RPT-017, REQ-RPT-018, REQ-RPT-020, REQ-RPT-029, REQ-RPT-033, REQ-RPT-034, REQ-RPT-035, REQ-RPT-038, REQ-RPT-039, REQ-RPT-041, REQ-RPT-042, REQ-RPT-043, REQ-RPT-044, REQ-RPT-045, REQ-RPT-046, REQ-RPT-051, REQ-RPT-052, REQ-RPT-054, RULE-RPT-001, RULE-RPT-002, RULE-RPT-003, RULE-RPT-004, RULE-RPT-005, RULE-RPT-006, RULE-RPT-007, RULE-RPT-008, RULE-RPT-009, RULE-RPT-010, RULE-RPT-011, RULE-RPT-012, RULE-RPT-013, RULE-RPT-014, RULE-RPT-015 -->
<!-- SUB:RULE-SCENARIOS:START traces=AC-RPT-003,AC-RPT-004,AC-RPT-005,AC-RPT-006,AC-RPT-007,AC-RPT-008,AC-RPT-009,AC-RPT-010,AC-RPT-012,AC-RPT-016,AC-RPT-017,AC-RPT-018,AC-RPT-019,AC-RPT-020,AC-RPT-021,AC-RPT-023,AC-RPT-035,AC-RPT-039,AC-RPT-040,AC-RPT-041,AC-RPT-042,AC-RPT-045,AC-RPT-046,AC-RPT-049,AC-RPT-050,AC-RPT-051,AC-RPT-052,AC-RPT-053,AC-RPT-054,AC-RPT-059,AC-RPT-060,AC-RPT-062,AC-RPT-063,AC-RPT-064,REQ-RPT-003,REQ-RPT-004,REQ-RPT-005,REQ-RPT-006,REQ-RPT-007,REQ-RPT-009,REQ-RPT-013,REQ-RPT-014,REQ-RPT-015,REQ-RPT-016,REQ-RPT-017,REQ-RPT-018,REQ-RPT-020,REQ-RPT-029,REQ-RPT-033,REQ-RPT-034,REQ-RPT-035,REQ-RPT-038,REQ-RPT-039,REQ-RPT-041,REQ-RPT-042,REQ-RPT-043,REQ-RPT-044,REQ-RPT-045,REQ-RPT-046,REQ-RPT-051,REQ-RPT-052,REQ-RPT-054 -->
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
Expected     : the call is refused before any write with UnknownCodeException (RPT-422-UNKNOWN-CODE) "Not stored: `PASSED` is not a code of FINDING_OUTCOME." — the validation path of REQ-RPT-009; the write-failure path RPT-500-REPORT-NOT-STORED is TC-RPT-062; Check 506 stays RUNNING with overallStatus null; 0 Findings, 0 Check Documents, 0 Unread Queries
Test data    : outcome `PASSED` (not a FINDING_OUTCOME code)
<!-- TC:TC-RPT-012:END -->

<!-- TC:TC-RPT-062:START traces=AC-RPT-062,REQ-RPT-009 -->
### TC-RPT-062 — Database failure while storing a report leaves nothing stored
Derived from : AC-RPT-062  (REQ-RPT-009)
Exercises    : in-process result port completeCheck (implements CON-CHK-008) — no HTTP operation; caller transaction joined (ADR-RPT-012)
Rule / code  : REQ-RPT-009 → RPT-500-REPORT-NOT-STORED
Package      : PORTS
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: Check 546 is RUNNING; the INSERT of the second RPT_FINDING row of Check 546 is made to fail (fault injected), all other writes succeed
Host data    : none
Steps        : 1. inside a caller transaction, completeCheck(546, …) with a valid report of 3 findings, 2 document outcomes, 1 unread query
               2. after the caller's transaction ends, read Check 546 and count its rows in RPT_FINDING, RPT_CHECK_DOCUMENT, RPT_UNREAD_QUERY
Expected     : the call raises ReportNotStoredException (RPT-500-REPORT-NOT-STORED) "The report of Check 546 was not stored." and it reaches the caller (the Check Engine); the caller's transaction is rolled back whole; Check 546 stays RUNNING with overallStatus null and comparisonModel null; 0 Findings, 0 Check Documents, 0 Unread Queries
Test data    : Check 546; fault on Finding position 2
<!-- TC:TC-RPT-062:END -->

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

<!-- TC:TC-RPT-063:START traces=AC-RPT-063,REQ-RPT-018,API-RPT-001,RULE-RPT-003 -->
### TC-RPT-063 — Check awaiting documents failed with its reason
Derived from : AC-RPT-063  (REQ-RPT-018)
Exercises    : in-process result port failCheck (implements CON-CHK-009) — no HTTP operation; read through API-RPT-001 GET /api/v1/checks/{checkId}
Rule / code  : RULE-RPT-003 (allowed transition AWAITING_DOCUMENTS → FAILED)
Package      : PORTS
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 547 of fetch mode `manual` is AWAITING_DOCUMENTS (never marked RUNNING)
Host data    : none
Steps        : 1. failCheck(547, UPLOAD_WINDOW_EXPIRED, "Upload window of 30 min expired", 2026-10-01T09:40:00Z)
               2. read Check 547
Expected     : Check 547 is FAILED, failureReason UPLOAD_WINDOW_EXPIRED, failureDetail "Upload window of 30 min expired", endedAt 2026-10-01T09:40:00Z, overallStatus null, runningSince null, 0 findings; the row satisfies CHK_RPT_CHECK_RUN_RESULT
Test data    : UPLOAD_WINDOW_EXPIRED; "Upload window of 30 min expired"; 2026-10-01T09:40:00Z
<!-- TC:TC-RPT-063:END -->

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

<!-- TC:TC-RPT-064:START traces=AC-RPT-064,REQ-RPT-054 -->
### TC-RPT-064 — Purge failure logged with its Check run and cause
Derived from : AC-RPT-064  (REQ-RPT-054)
Exercises    : scheduled ReportPurgeService (no caller, no HTTP operation — ADR-RPT-010, ADR-RPT-020)
Rule / code  : —
Package      : SVC-API
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: retention period 30 days; Checks 548 and 549 ended 40 days ago; the deletion of Check 548 is made to fail with the cause "lock wait timeout" (fault injected)
Host data    : none
Steps        : 1. run the purge
               2. read both Checks and the captured log lines in order
Expected     : the log holds, at WARN, "Report purge kept Check run 548: its deletion failed (lock wait timeout)." before the closing line "Report purge deleted 1 Check runs ended before {cutOff}."; no row content appears in the log; Check 548 still stored with all its records; Check 549 no longer exists
Test data    : Checks 548, 549; cause "lock wait timeout"
<!-- TC:TC-RPT-064:END -->
<!-- SUB:RULE-SCENARIOS:END -->
