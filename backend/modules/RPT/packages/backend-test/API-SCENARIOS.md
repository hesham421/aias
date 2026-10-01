<!-- source: PHASE:TEST-PLAN-BE / SUB:API-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-RPT-001, AC-RPT-002, AC-RPT-011, AC-RPT-013, AC-RPT-014, AC-RPT-015, AC-RPT-022, AC-RPT-024, AC-RPT-025, AC-RPT-026, AC-RPT-027, AC-RPT-028, AC-RPT-029, AC-RPT-030, AC-RPT-031, AC-RPT-032, AC-RPT-033, AC-RPT-034, AC-RPT-036, AC-RPT-037, AC-RPT-038, AC-RPT-043, AC-RPT-044, AC-RPT-047, AC-RPT-048, AC-RPT-055, AC-RPT-056, AC-RPT-058, AC-RPT-061, API-RPT-001, API-RPT-002, API-RPT-003, REQ-RPT-001, REQ-RPT-002, REQ-RPT-008, REQ-RPT-010, REQ-RPT-011, REQ-RPT-012, REQ-RPT-019, REQ-RPT-021, REQ-RPT-022, REQ-RPT-023, REQ-RPT-024, REQ-RPT-025, REQ-RPT-026, REQ-RPT-027, REQ-RPT-028, REQ-RPT-030, REQ-RPT-031, REQ-RPT-032, REQ-RPT-036, REQ-RPT-037, REQ-RPT-040, REQ-RPT-047, REQ-RPT-048, REQ-RPT-050, REQ-RPT-053 -->
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
