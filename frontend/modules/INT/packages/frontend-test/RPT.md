<!-- source: PHASE:INT-UXD / SUB:RPT -->
<!-- context: INT-UXD-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-045, AC-INT-047, AC-INT-050, AC-INT-051, AC-INT-052, AC-INT-053, REQ-INT-040, REQ-INT-042, REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, SCR-INT-001, SCR-INT-002, UXD-INT-001, UXD-INT-002, UXD-INT-003, UXD-INT-004 -->
<!-- SUB:RPT:START traces=REQ-INT-040,REQ-INT-042,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,AC-INT-045,AC-INT-047,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,UXD-INT-001,UXD-INT-002,UXD-INT-003,UXD-INT-004,SCR-INT-001,SCR-INT-002 -->
### RPT — Report Store

<!-- TC:TC-INT-071:START traces=UXD-INT-001,REQ-INT-042,AC-INT-047,SCR-INT-001 -->
### TC-INT-071 — The Checks of a request render the Report Store's values
Derived from : UXD-INT-001 (REQ-INT-042, AC-INT-047)
Exercises    : SCR-INT-001 `/` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-001
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-006 answers Check 703 (COMPLETED, NOT_COMPLIANT, 09:00, 09:01, REJECTED) with total 1; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the Checks of the request.
Expected     : Each field of the entry renders the value API-INT-006 returned: 703, COMPLETED, NOT_COMPLIANT, 09:00, 09:01, REJECTED.
Test data    : Check 703 (AC-INT-047)
<!-- TC:TC-INT-071:END -->

<!-- TC:TC-INT-072:START traces=UXD-INT-001,REQ-INT-040,AC-INT-045,SCR-INT-001 -->
### TC-INT-072 — The Checks of a request show the empty and error states
Derived from : UXD-INT-001 (REQ-INT-040, AC-INT-045)
Exercises    : SCR-INT-001 `/` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-001
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); first INT's read API-INT-006 answers HTTP 500 (INT-500), then it answers an empty list with total 0
Host data    : none
Steps        : 1. Open the Checks of the request (read fails). 2. Retry (read answers no Check).
Expected     : 1 → the screen's error state with a retry, no blank screen. 2 → the screen's empty state, "Start a Check" still offered.
Test data    : answers: HTTP 500, then empty
<!-- TC:TC-INT-072:END -->

<!-- TC:TC-INT-073:START traces=UXD-INT-002,REQ-INT-045,AC-INT-050,SCR-INT-002 -->
### TC-INT-073 — The report header and recorded decision render the Report Store's values
Derived from : UXD-INT-002 (REQ-INT-045, AC-INT-050)
Exercises    : SCR-INT-002 `/checks/704` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-002
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-005 answers Check 704 as in AC-INT-050, and Check 711 with the decision of AC-INT-058; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 704. 2. Open Check 711.
Expected     : 1 → status, service code, version, fetch mode, request number, employee, times, Overall Status and model render the served values. 2 → APPROVED, "E-3307", 11:05 and "executed through the Approval API" render.
Test data    : Checks 704, 711
<!-- TC:TC-INT-073:END -->

<!-- TC:TC-INT-074:START traces=UXD-INT-002,REQ-INT-045,AC-INT-050,SCR-INT-002 -->
### TC-INT-074 — The report shows its error state when the Report Store read fails
Derived from : UXD-INT-002 (REQ-INT-045, AC-INT-050)
Exercises    : SCR-INT-002 `/checks/704` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-002
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); INT's read API-INT-005 answers HTTP 500 (INT-500)
Host data    : none
Steps        : 1. Open Check 704.
Expected     : The screen's error state with a retry; no header, no panes, no blank screen.
Test data    : answer: HTTP 500
<!-- TC:TC-INT-074:END -->

<!-- TC:TC-INT-075:START traces=UXD-INT-003,REQ-INT-046,AC-INT-051,SCR-INT-002 -->
### TC-INT-075 — Findings render the Report Store's values beside their evidence
Derived from : UXD-INT-003 (REQ-INT-046, AC-INT-051)
Exercises    : SCR-INT-002 `/checks/705` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-002
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-005 answers Check 705 with the finding of AC-INT-051; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 705.
Expected     : The finding entry renders the served condition, outcome, evidence and note side by side.
Test data    : Check 705 (AC-INT-051)
<!-- TC:TC-INT-075:END -->

<!-- TC:TC-INT-076:START traces=UXD-INT-003,REQ-INT-046,AC-INT-051,SCR-INT-002 -->
### TC-INT-076 — A report without findings shows the empty findings pane
Derived from : UXD-INT-003 (REQ-INT-046, AC-INT-051)
Exercises    : SCR-INT-002 `/checks/<checkId>` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-002
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: INT's read API-INT-005 answers a COMPLETED Check with an empty findings list; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open that Check.
Expected     : The findings pane shows its empty state; the rest of the report renders; no blank screen.
Test data    : checkId: placeholder; findings: none
<!-- TC:TC-INT-076:END -->

<!-- TC:TC-INT-077:START traces=UXD-INT-004,REQ-INT-047,AC-INT-052,SCR-INT-002 -->
### TC-INT-077 — Documents and unread queries render the Report Store's values
Derived from : UXD-INT-004 (REQ-INT-047, AC-INT-052)
Exercises    : SCR-INT-002 `/checks/706` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-002
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-005 answers Check 706 (AC-INT-052) and Check 707 (AC-INT-053); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 706. 2. Open Check 707.
Expected     : 1 → TRANSCRIPT READ and ID_CARD UNREADABLE, TOO_LARGE, "12 MB exceeds 10 MB" render. 2 → `request_details` with "query timed out" renders.
Test data    : Checks 706, 707
<!-- TC:TC-INT-077:END -->

<!-- TC:TC-INT-078:START traces=UXD-INT-004,REQ-INT-048,AC-INT-053,SCR-INT-002 -->
### TC-INT-078 — A report without documents or unread queries shows the empty panes
Derived from : UXD-INT-004 (REQ-INT-048, AC-INT-053)
Exercises    : SCR-INT-002 `/checks/<checkId>` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-002
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: INT's read API-INT-005 answers a COMPLETED Check with empty documents and unread queries lists; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open that Check.
Expected     : The documents pane and the service queries pane show their empty state; no blank screen.
Test data    : checkId: placeholder; documents, unread queries: none
<!-- TC:TC-INT-078:END -->
<!-- SUB:RPT:END -->
