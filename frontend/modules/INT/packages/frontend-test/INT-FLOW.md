<!-- source: PHASE:TEST-PLAN-FE / SUB:INT-FLOW -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-026, AC-INT-043, AC-INT-049, AC-INT-061, AC-INT-062, AC-INT-078, REQ-INT-022, REQ-INT-038, REQ-INT-044, REQ-INT-055, REQ-INT-056, REQ-INT-066, SCR-INT-001, SCR-INT-002, SCR-INT-005 -->
<!-- SUB:INT-FLOW:START traces=REQ-INT-022,REQ-INT-038,REQ-INT-044,REQ-INT-055,REQ-INT-056,AC-INT-026,AC-INT-043,AC-INT-049,AC-INT-061,AC-INT-062,SCR-INT-001,SCR-INT-002,SCR-INT-005,REQ-INT-066,AC-INT-078 -->
### INT-FLOW — the module lifecycle: start → follow → report → decide → retry

<!-- TC:TC-INT-066:START traces=AC-INT-049,REQ-INT-044,SCR-INT-001 -->
### TC-INT-066 — A Check started from the frontend appears first
Derived from : AC-INT-049  (REQ-INT-044)
Exercises    : SCR-INT-001 `/` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the frontend was opened with `scholarship-request`, `REQ-2026-0042` and employee `E-3307`; the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); the requests to API-INT-001 are captured
Host data    : none
Steps        : 1. Press "Start a Check".
Expected     : A Check of `scholarship-request` for request "REQ-2026-0042" by employee "E-3307" is accepted (API-INT-001 request body carries those three values unchanged) and appears first in the list.
Test data    : scholarship-request · REQ-2026-0042 · E-3307
<!-- TC:TC-INT-066:END -->

<!-- TC:TC-INT-067:START traces=AC-INT-061,REQ-INT-055,SCR-INT-002 -->
### TC-INT-067 — A running Check is read again every 5 seconds
Derived from : AC-INT-061  (REQ-INT-055)
Exercises    : SCR-INT-002 `/checks/715` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-002
Scenario     : STATE · data class BOUNDARY · language ALL
Preconditions: the polling interval is 5 seconds and Check 715 is RUNNING (every read answers RUNNING); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); the reads of API-INT-005 are counted
Host data    : none
Steps        : 1. Open Check 715. 2. Keep it open for 20 seconds without reloading.
Expected     : Check 715 is read 4 more times without the employee reloading.
Test data    : polling interval 5 s; 20 s; Check 715
<!-- TC:TC-INT-067:END -->

<!-- TC:TC-INT-068:START traces=AC-INT-062,REQ-INT-056,SCR-INT-002 -->
### TC-INT-068 — Reading stops when the Check ends
Derived from : AC-INT-062  (REQ-INT-056)
Exercises    : SCR-INT-002 `/checks/716` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-002
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 716 is RUNNING and open on the screen; the next read answers COMPLETED; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); the reads of API-INT-005 are counted
Host data    : none
Steps        : 1. Keep Check 716 open until a read shows it COMPLETED. 2. Keep it open for at least two more polling intervals.
Expected     : The report of Check 716 is shown and no further read of Check 716 is made while it stays open.
Test data    : Check 716
<!-- TC:TC-INT-068:END -->

<!-- TC:TC-INT-069:START traces=AC-INT-026,REQ-INT-022,SCR-INT-005 -->
### TC-INT-069 — The recorded decision is shown after recording
Derived from : AC-INT-026  (REQ-INT-022)
Exercises    : SCR-INT-005 `/checks/628/decision` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-005
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 628 is COMPLETED and undecided, without the Approval API; the frontend was launched with employee `E-4410`; the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the employee decision of Check 628. 2. Choose APPROVED and press "Record decision".
Expected     : The answer of API-INT-004 carries Check 628, decision APPROVED, decided by "E-4410", a recording time and executed through the Approval API false; the screen returns to the report of Check 628, which shows APPROVED, "E-4410" and the recording time, without "executed through the Approval API".
Test data    : checkId 628; E-4410
<!-- TC:TC-INT-069:END -->

<!-- TC:TC-INT-070:START traces=AC-INT-043,REQ-INT-038,SCR-INT-005 -->
### TC-INT-070 — After a failed approval the employee can submit again
Derived from : AC-INT-043  (REQ-INT-038)
Exercises    : SCR-INT-005 `/checks/641/decision` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-005
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: TC-INT-065 happened; API-INT-004 now answers HTTP 201 with executed through the Approval API true; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); the requests to API-INT-004 are captured
Host data    : none
Steps        : 1. On the employee decision of Check 641, press "Record decision" again.
Expected     : Exactly one new decision request is sent; the screen returns to the report of Check 641, which shows decision APPROVED and "executed through the Approval API".
Test data    : checkId 641
<!-- TC:TC-INT-070:END -->
<!-- TC:TC-INT-101:START traces=AC-INT-078,REQ-INT-066,SCR-INT-002 -->
### TC-INT-101 — A failed background read keeps the shown Check and shows a retry notice
Derived from : AC-INT-078  (REQ-INT-066)
Exercises    : SCR-INT-002 `/checks/730` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-002
Scenario     : STATE · data class EDGE · language ALL
Preconditions: the polling interval is 5 seconds; API-INT-005 answers Check 730 RUNNING on the first read, HTTP 500 (INT-500) on the second read, and RUNNING again on the third read; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 730 and wait for the first read. 2. Wait for the second (failing) read. 3. Wait for the third read.
Expected     : 1 → Check 730 is shown as RUNNING. 2 → Check 730 is still shown as RUNNING, with the header and status unchanged; the non-blocking notice "Refresh failed; retrying" is shown; the screen's error state is not shown. 3 → Check 730 is shown as RUNNING and the notice is gone. The reads continue at the 5-second interval throughout.
Test data    : polling interval 5 s; Check 730 (AC-INT-078)
<!-- TC:TC-INT-101:END -->
<!-- SUB:INT-FLOW:END -->
