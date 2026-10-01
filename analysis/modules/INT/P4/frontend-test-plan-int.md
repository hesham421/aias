# FRONTEND TEST PLAN — Host Integration (INT) — employee frontend
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias   Stage : P4 (test-gen)   Framework : agnostic (profile.stack.testing.frontend)
Sources: _state/current-srs.md (AC-INT-001 … AC-INT-074) · current-registry-srs.md · current-frontend-execution-plan.md (SCR-INT-001 … SCR-INT-005, F1–F4 SUBs, UXD-INT-001 … UXD-INT-008, §3.0 message binding) · current-api-spec.yaml (api-spec-int.yaml) (served by the mock server) — all v1
Open ADRs: ADR-INT-017 (Arabic messages PENDING) · ADR-INT-018 / ADR-INT-021 (frontend binding) · ADR-INT-019 / ADR-INT-022 (derivation choices) · ADR-INT-020 (INT reads; DOC listing gap) — 0 BLOCKED
══════════════════════════════════════════════════════════════════

Framework note: the plan is framework-neutral — each TC block below is the whole contract; the consumer repository
chooses its tool and turns each TC into a test. Screens and routes are the frontend plan's (F4); every refusal
text a case asserts is bound in the frontend plan's §3.0 message binding (`text:`). Every case runs against the
mock server serving api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022). The launch query
string is `?serviceCode=…&requestNumber=…&employeeId=…` (ADR-INT-018 (4)). 23 frontend ACs plus 3 frontend cases of
backend ACs on SCR-INT-005 (ADR-INT-019 (2)); 16 integration cases for 8 UXD.

<!-- PHASE:TEST-PLAN-FE:START traces=REQ-INT-016,REQ-INT-017,REQ-INT-020,REQ-INT-022,REQ-INT-023,REQ-INT-036,REQ-INT-038,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-057,AC-INT-020,AC-INT-021,AC-INT-024,AC-INT-026,AC-INT-027,AC-INT-041,AC-INT-043,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-063,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005 -->
## PHASE TEST-PLAN-FE

<!-- SUB:UI-FLOWS:START traces=REQ-INT-016,REQ-INT-017,REQ-INT-020,REQ-INT-023,REQ-INT-036,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-057,AC-INT-020,AC-INT-021,AC-INT-024,AC-INT-027,AC-INT-041,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-063,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005 -->
### UI-FLOWS — per-screen behaviour

<!-- TC:TC-INT-045:START traces=AC-INT-020,REQ-INT-016,SCR-INT-003 -->
### TC-INT-045 — The document type choices are the service's required types
Derived from : AC-INT-020  (REQ-INT-016)
Exercises    : SCR-INT-003 `/checks/622/documents` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 622 runs `manual-service`, which requires TRANSCRIPT and ID_CARD; the Check is AWAITING_DOCUMENTS (the form is offered only then); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 622. 2. Open the document type control.
Expected     : The document type choices are exactly TRANSCRIPT and ID_CARD.
Test data    : checkId 622; manual-service; TRANSCRIPT, ID_CARD
<!-- TC:TC-INT-045:END -->

<!-- TC:TC-INT-046:START traces=AC-INT-021,REQ-INT-017,SCR-INT-003 -->
### TC-INT-046 — The documents already uploaded are listed
Derived from : AC-INT-021  (REQ-INT-017)
Exercises    : SCR-INT-003 `/checks/623/documents` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-003
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 623 has one uploaded TRANSCRIPT `t.pdf` of 300 KB; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 623.
Expected     : The list shows one entry: TRANSCRIPT, "t.pdf", 300 KB.
Test data    : checkId 623; t.pdf; 300 KB
<!-- TC:TC-INT-046:END -->

<!-- TC:TC-INT-047:START traces=AC-INT-024,REQ-INT-020,SCR-INT-004 -->
### TC-INT-047 — The uploads and the required types without upload are shown before confirming
Derived from : AC-INT-024  (REQ-INT-020)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-004
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 626 requires TRANSCRIPT and ID_CARD and only a TRANSCRIPT was uploaded; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626; do not submit.
Expected     : The screen shows the TRANSCRIPT as uploaded and ID_CARD as having no upload, before the confirmation is submitted (no request to API-INT-003 has been sent).
Test data    : checkId 626; TRANSCRIPT, ID_CARD
<!-- TC:TC-INT-047:END -->

<!-- TC:TC-INT-048:START traces=AC-INT-027,REQ-INT-023,SCR-INT-005 -->
### TC-INT-048 — The decision carries the identity the host passed
Derived from : AC-INT-027  (REQ-INT-023)
Exercises    : SCR-INT-005 `/checks/629/decision` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-005
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the host opened the frontend with employee `E-5120`; Check 629 is COMPLETED and undecided; the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); the requests to API-INT-004 are captured
Host data    : none
Steps        : 1. Open the employee decision of Check 629 (launch employeeId E-5120). 2. Choose APPROVED. 3. Press "Record decision".
Expected     : The decision request carries deciding employee "E-5120" (`decidedBy`); no control asks for the employee.
Test data    : checkId 629; E-5120
<!-- TC:TC-INT-048:END -->

<!-- TC:TC-INT-049:START traces=AC-INT-045,REQ-INT-040,SCR-INT-001 -->
### TC-INT-049 — The Checks of the request are shown newest first
Derived from : AC-INT-045  (REQ-INT-040)
Exercises    : SCR-INT-001 `/` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: request `REQ-2026-0042` of `scholarship-request` has Checks 701 (started 09:00) and 702 (started 10:30); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the frontend with `scholarship-request`, `REQ-2026-0042` and employee `E-3307`.
Expected     : The list shows Check 702 first and Check 701 second.
Test data    : scholarship-request · REQ-2026-0042 · E-3307 · Checks 701, 702
<!-- TC:TC-INT-049:END -->

<!-- TC:TC-INT-050:START traces=AC-INT-046,REQ-INT-041,SCR-INT-001,RULE-INT-004 -->
### TC-INT-050 — Without the request number no Check is shown
Derived from : AC-INT-046  (REQ-INT-041)
Exercises    : SCR-INT-001 `/?serviceCode=scholarship-request&employeeId=E-3307`
Rule / code  : RULE-INT-004 → — (frontend check, no catalog code)
Package      : F3-SCR-INT-001
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: the frontend is opened with service `scholarship-request` and employee `E-3307` but no request number; the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); the requests are captured
Host data    : none
Steps        : 1. Open the frontend; let the screen load.
Expected     : No Check is listed (no request to API-INT-006 is sent) and the screen shows en: "Open this screen from the host system for one request." · ar: PENDING ADR-INT-017.
Test data    : scholarship-request · E-3307 · no request number
<!-- TC:TC-INT-050:END -->

<!-- TC:TC-INT-051:START traces=AC-INT-047,REQ-INT-042,SCR-INT-001 -->
### TC-INT-051 — Each Check shows its identifier, status, result, times and decision
Derived from : AC-INT-047  (REQ-INT-042)
Exercises    : SCR-INT-001 `/` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-001
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 703 of the opened request is COMPLETED, NOT_COMPLIANT, started 09:00, ended 09:01, decision REJECTED; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the Checks of the request.
Expected     : The entry of Check 703 shows status COMPLETED, Overall Status NOT_COMPLIANT, start 09:00, end 09:01 and decision REJECTED.
Test data    : Check 703
<!-- TC:TC-INT-051:END -->

<!-- TC:TC-INT-052:START traces=AC-INT-048,REQ-INT-043,SCR-INT-001 -->
### TC-INT-052 — The total is shown when not every Check is listed
Derived from : AC-INT-048  (REQ-INT-043)
Exercises    : SCR-INT-001 `/` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-001
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: the opened request has 104 Checks (the Report Store lists 100 with total 104); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the Checks of the request.
Expected     : 100 Checks are listed and the screen states that the request has 104 Checks (en: "This request has 104 Checks." — the ui-ux-spec label; ar label "لهذا الطلب 104 فحصًا.").
Test data    : 104 Checks; 100 listed
<!-- TC:TC-INT-052:END -->

<!-- TC:TC-INT-053:START traces=AC-INT-050,REQ-INT-045,SCR-INT-002 -->
### TC-INT-053 — The report header shows what the report was built on
Derived from : AC-INT-050  (REQ-INT-045)
Exercises    : SCR-INT-002 `/checks/704` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 704 is COMPLETED on `scholarship-request` version 3, fetch mode `path`, request `REQ-2026-0042`, employee `E-3307`, model `gemini-flash-lite`; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 704.
Expected     : The screen shows COMPLETED, `scholarship-request`, version 3, `path`, "REQ-2026-0042", "E-3307", its start and end times, its Overall Status and model "gemini-flash-lite".
Test data    : Check 704; Overall Status: as served (placeholder)
<!-- TC:TC-INT-053:END -->

<!-- TC:TC-INT-054:START traces=AC-INT-051,REQ-INT-046,SCR-INT-002 -->
### TC-INT-054 — Every finding is one entry beside its evidence
Derived from : AC-INT-051  (REQ-INT-046)
Exercises    : SCR-INT-002 `/checks/705` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 705 has a finding "GPA at least 3.0", NOT_SATISFIED, evidence "2.7", note "Below the minimum"; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 705.
Expected     : One entry shows "GPA at least 3.0", NOT_SATISFIED, "2.7" and "Below the minimum" together.
Test data    : Check 705
<!-- TC:TC-INT-054:END -->

<!-- TC:TC-INT-055:START traces=AC-INT-052,REQ-INT-047,SCR-INT-002 -->
### TC-INT-055 — Documents read and unreadable are shown with the reason
Derived from : AC-INT-052  (REQ-INT-047)
Exercises    : SCR-INT-002 `/checks/706` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 706 has TRANSCRIPT READ, ID_CARD UNREADABLE with reason TOO_LARGE and detail "12 MB exceeds 10 MB"; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 706.
Expected     : The documents show TRANSCRIPT as read and ID_CARD as unreadable with reason TOO_LARGE and detail "12 MB exceeds 10 MB".
Test data    : Check 706
<!-- TC:TC-INT-055:END -->

<!-- TC:TC-INT-056:START traces=AC-INT-053,REQ-INT-048,SCR-INT-002 -->
### TC-INT-056 — Service queries not read are shown
Derived from : AC-INT-053  (REQ-INT-048)
Exercises    : SCR-INT-002 `/checks/707` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 707 has unread query `request_details` with detail "query timed out"; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 707.
Expected     : The screen shows `request_details` as not read with "query timed out".
Test data    : Check 707
<!-- TC:TC-INT-056:END -->

<!-- TC:TC-INT-057:START traces=AC-INT-054,REQ-INT-049,SCR-INT-002 -->
### TC-INT-057 — A report with a missing document shows its stored status and the missing document
Derived from : AC-INT-054  (REQ-INT-049)
Exercises    : SCR-INT-002 `/checks/708` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 708 is COMPLETED with Overall Status NEEDS_MANUAL_REVIEW and ID_CARD MISSING; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 708.
Expected     : The screen shows Overall Status NEEDS_MANUAL_REVIEW and ID_CARD as missing.
Test data    : Check 708
<!-- TC:TC-INT-057:END -->

<!-- TC:TC-INT-058:START traces=AC-INT-055,REQ-INT-049,SCR-INT-002 -->
### TC-INT-058 — COMPLIANT is never presented beside a missing document
Derived from : AC-INT-055  (REQ-INT-049)
Exercises    : SCR-INT-002 `/checks/<checkId>` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-002
Scenario     : STATE · data class EDGE · language ALL
Preconditions: a report reaches the frontend with Overall Status COMPLIANT and a MISSING ID_CARD outcome (served by the mock); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open that Check.
Expected     : The screen shows en: "Not verified — a required document is missing" · ar: PENDING ADR-INT-017 instead of COMPLIANT, with ID_CARD as missing; the word COMPLIANT is not shown as the Overall Status.
Test data    : checkId: placeholder; Overall Status COMPLIANT; ID_CARD MISSING
<!-- TC:TC-INT-058:END -->

<!-- TC:TC-INT-059:START traces=AC-INT-056,REQ-INT-050,SCR-INT-002 -->
### TC-INT-059 — A failed Check shows its reason and no Overall Status
Derived from : AC-INT-056  (REQ-INT-050)
Exercises    : SCR-INT-002 `/checks/709` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 709 is FAILED with reason TIMED_OUT and detail "The Check exceeded 120 seconds."; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 709.
Expected     : The screen shows FAILED, TIMED_OUT and "The Check exceeded 120 seconds." and no Overall Status.
Test data    : Check 709
<!-- TC:TC-INT-059:END -->

<!-- TC:TC-INT-060:START traces=AC-INT-057,REQ-INT-051,SCR-INT-002 -->
### TC-INT-060 — Report texts are shown as plain text
Derived from : AC-INT-057  (REQ-INT-051)
Exercises    : SCR-INT-002 `/checks/710` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-002
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: Check 710 has a finding whose evidence is `<script>alert(1)</script> <a href="x">here</a>`; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 710.
Expected     : The evidence is shown literally as the characters `<script>alert(1)</script> <a href="x">here</a>`; no script runs and no link is shown.
Test data    : Check 710
<!-- TC:TC-INT-060:END -->

<!-- TC:TC-INT-061:START traces=AC-INT-058,REQ-INT-052,SCR-INT-002 -->
### TC-INT-061 — A recorded decision is shown
Derived from : AC-INT-058  (REQ-INT-052)
Exercises    : SCR-INT-002 `/checks/711` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-002
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 711 holds decision APPROVED by `E-3307` at 11:05, executed through the Approval API true; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 711.
Expected     : The screen shows APPROVED, "E-3307", 11:05 and "executed through the Approval API".
Test data    : Check 711
<!-- TC:TC-INT-061:END -->

<!-- TC:TC-INT-062:START traces=AC-INT-059,REQ-INT-053,SCR-INT-002 -->
### TC-INT-062 — Upload and confirmation are offered while documents are awaited
Derived from : AC-INT-059  (REQ-INT-053)
Exercises    : SCR-INT-002 `/checks/712` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-002
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 712 is AWAITING_DOCUMENTS; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 712.
Expected     : The document upload and the upload confirmation are offered and the decision is not.
Test data    : Check 712
<!-- TC:TC-INT-062:END -->

<!-- TC:TC-INT-063:START traces=AC-INT-060,REQ-INT-054,SCR-INT-002 -->
### TC-INT-063 — The decision is offered only on a completed, undecided Check
Derived from : AC-INT-060  (REQ-INT-054)
Exercises    : SCR-INT-002 `/checks/713`, `/checks/714` (+ launch query string)
Rule / code  : —
Package      : F4-SCR-INT-002
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 713 is COMPLETED with no decision and Check 714 is COMPLETED with decision APPROVED; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open Check 713. 2. Open Check 714.
Expected     : 1 → the decision is offered on Check 713. 2 → the decision is not offered on Check 714.
Test data    : Checks 713, 714
<!-- TC:TC-INT-063:END -->

<!-- TC:TC-INT-064:START traces=AC-INT-063,REQ-INT-057,SCR-INT-001 -->
### TC-INT-064 — The frontend calls only published operations
Derived from : AC-INT-063  (REQ-INT-057)
Exercises    : SCR-INT-001 … SCR-INT-005 (every route)
Rule / code  : —
Package      : F2-SCR-INT-001
Scenario     : STATE · data class VALID · language ALL
Preconditions: the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022); the frontend's network traffic is recorded
Host data    : none
Steps        : 1. Run the full path once: open the Checks of a request, start a Check, open it, upload a document, confirm, open a completed Check, record a decision. 2. List the operations (method + path template) the frontend called and compare each with the published API document of Host Integration (api-spec-int.yaml).
Expected     : Every operation the frontend calls appears in the published API document.
Test data    : values from the TCs above (placeholders)
<!-- TC:TC-INT-064:END -->

<!-- TC:TC-INT-065:START traces=AC-INT-041,REQ-INT-036,SCR-INT-005 -->
### TC-INT-065 — A failed approval is shown on the decision screen and nothing is recorded
Derived from : AC-INT-041  (REQ-INT-036)
Exercises    : SCR-INT-005 `/checks/641/decision` (+ launch query string)
Rule / code  : INT-502-APPROVAL-API-FAILED
Package      : F2-SCR-INT-005
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: Check 641 of `approve-service` version 2 is COMPLETED and undecided; API-INT-004 answers HTTP 502 with code INT-502-APPROVAL-API-FAILED (the host Approval API answered 500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the employee decision of Check 641. 2. Choose APPROVED. 3. Press "Record decision".
Expected     : The decision form shows en: "The approval was not executed: the host Approval API answered 500. Nothing was recorded; you can try again." · ar: PENDING ADR-INT-017; APPROVED stays chosen; "Record decision" is enabled again; the screen does not navigate; Check 641 holds no decision (its read shows none).
Test data    : checkId 641; host answer 500
<!-- TC:TC-INT-065:END -->
<!-- SUB:UI-FLOWS:END -->

<!-- SUB:INT-FLOW:START traces=REQ-INT-022,REQ-INT-038,REQ-INT-044,REQ-INT-055,REQ-INT-056,AC-INT-026,AC-INT-043,AC-INT-049,AC-INT-061,AC-INT-062,SCR-INT-001,SCR-INT-002,SCR-INT-005 -->
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
<!-- SUB:INT-FLOW:END -->
<!-- PHASE:TEST-PLAN-FE:END -->

<!-- PHASE:INT-UXD:START traces=REQ-INT-016,REQ-INT-017,REQ-INT-020,REQ-INT-040,REQ-INT-042,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,AC-INT-020,AC-INT-021,AC-INT-024,AC-INT-045,AC-INT-047,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,UXD-INT-001,UXD-INT-002,UXD-INT-003,UXD-INT-004,UXD-INT-005,UXD-INT-006,UXD-INT-007,UXD-INT-008,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004 -->
## PHASE INT-UXD

Grouped per source (owner) module — 16 cases, 2 per UXD (rendered · empty/owner failure).

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

<!-- SUB:REG:START traces=REQ-INT-016,REQ-INT-020,AC-INT-020,AC-INT-024,UXD-INT-005,UXD-INT-008,SCR-INT-003,SCR-INT-004 -->
### REG — Service Registry

<!-- TC:TC-INT-079:START traces=UXD-INT-005,REQ-INT-016,AC-INT-020,SCR-INT-003 -->
### TC-INT-079 — The document type choices render the Service Registry's required types
Derived from : UXD-INT-005 (REQ-INT-016, AC-INT-020)
Exercises    : SCR-INT-003 `/checks/622/documents` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-003
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: Check 622 runs `manual-service` and is AWAITING_DOCUMENTS; INT's read API-INT-008 answers requiredDocumentTypes TRANSCRIPT and ID_CARD for the Check's version; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 622; open the document type control.
Expected     : The choices are the values API-INT-008 returned: TRANSCRIPT and ID_CARD.
Test data    : checkId 622 (AC-INT-020)
<!-- TC:TC-INT-079:END -->

<!-- TC:TC-INT-080:START traces=UXD-INT-005,REQ-INT-016,AC-INT-020,SCR-INT-003 -->
### TC-INT-080 — No choice is offered when the Service Registry read fails
Derived from : UXD-INT-005 (REQ-INT-016, AC-INT-020)
Exercises    : SCR-INT-003 `/checks/622/documents` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-003
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: Check 622 is AWAITING_DOCUMENTS; INT's read API-INT-008 answers HTTP 500 (INT-500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 622.
Expected     : No document type choice is offered, "Upload" is disabled and the screen's error state is shown; no blank screen.
Test data    : checkId 622; answer: HTTP 500
<!-- TC:TC-INT-080:END -->

<!-- TC:TC-INT-081:START traces=UXD-INT-008,REQ-INT-020,AC-INT-024,SCR-INT-004 -->
### TC-INT-081 — The required types with no upload render from the Service Registry
Derived from : UXD-INT-008 (REQ-INT-020, AC-INT-024)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-004
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-008 answers requiredDocumentTypes TRANSCRIPT and ID_CARD for Check 626's version; Document Access lists one TRANSCRIPT; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626.
Expected     : ID_CARD — the required type API-INT-008 returned with no uploaded document — is listed as having no upload; TRANSCRIPT is not.
Test data    : checkId 626 (AC-INT-024)
<!-- TC:TC-INT-081:END -->

<!-- TC:TC-INT-082:START traces=UXD-INT-008,REQ-INT-020,AC-INT-024,SCR-INT-004 -->
### TC-INT-082 — The confirmation shows its error state when the Service Registry read fails
Derived from : UXD-INT-008 (REQ-INT-020, AC-INT-024)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-004
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: INT's read API-INT-008 answers HTTP 500 (INT-500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626.
Expected     : The screen's error state with a retry; "Confirm uploads" is disabled; no blank screen.
Test data    : checkId 626; answer: HTTP 500
<!-- TC:TC-INT-082:END -->
<!-- SUB:REG:END -->

<!-- SUB:DOC:START traces=REQ-INT-017,REQ-INT-020,AC-INT-021,AC-INT-024,UXD-INT-006,UXD-INT-007,SCR-INT-003,SCR-INT-004 -->
### DOC — Document Access

<!-- TC:TC-INT-083:START traces=UXD-INT-006,REQ-INT-017,AC-INT-021,SCR-INT-003 -->
### TC-INT-083 — The uploaded documents render Document Access's values
Derived from : UXD-INT-006 (REQ-INT-017, AC-INT-021)
Exercises    : SCR-INT-003 `/checks/623/documents` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-003
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-007 answers one TRANSCRIPT `t.pdf` of 300 KB for Check 623; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 623.
Expected     : The entry renders the values API-INT-007 returned: TRANSCRIPT, "t.pdf", 300 KB.
Test data    : checkId 623 (AC-INT-021)
<!-- TC:TC-INT-083:END -->

<!-- TC:TC-INT-084:START traces=UXD-INT-006,REQ-INT-017,AC-INT-021,SCR-INT-003 -->
### TC-INT-084 — The uploaded list shows its error state and the form stays usable
Derived from : UXD-INT-006 (REQ-INT-017, AC-INT-021)
Exercises    : SCR-INT-003 `/checks/623/documents` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-003
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: Check 623 is AWAITING_DOCUMENTS; INT's read API-INT-007 answers HTTP 500 (INT-500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 623.
Expected     : The uploaded documents list shows its error state with a retry; the upload form is still offered; no blank screen.
Test data    : checkId 623; answer: HTTP 500
<!-- TC:TC-INT-084:END -->

<!-- TC:TC-INT-085:START traces=UXD-INT-007,REQ-INT-020,AC-INT-024,SCR-INT-004 -->
### TC-INT-085 — The confirmation lists Document Access's uploaded documents
Derived from : UXD-INT-007 (REQ-INT-020, AC-INT-024)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-004
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-007 answers one TRANSCRIPT for Check 626; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626.
Expected     : The uploaded documents list renders the TRANSCRIPT entry API-INT-007 returned (document type, file name, size).
Test data    : checkId 626 (AC-INT-024)
<!-- TC:TC-INT-085:END -->

<!-- TC:TC-INT-086:START traces=UXD-INT-007,REQ-INT-020,AC-INT-024,SCR-INT-004 -->
### TC-INT-086 — The confirmation shows its error state when the Document Access read fails
Derived from : UXD-INT-007 (REQ-INT-020, AC-INT-024)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-004
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: INT's read API-INT-007 answers HTTP 500 (INT-500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626.
Expected     : The screen's error state with a retry; "Confirm uploads" is disabled; no blank screen.
Test data    : checkId 626; answer: HTTP 500
<!-- TC:TC-INT-086:END -->
<!-- SUB:DOC:END -->

<!-- PHASE:INT-UXD:END -->

## TC TRACEABILITY INDEX

| TC | AC / UXD | REQ | SCR | RULE / code | Package |
|---|---|---|---|---|---|
| TC-INT-045 | AC-INT-020 | REQ-INT-016 | SCR-INT-003 | — | F3-SCR-INT-003 |
| TC-INT-046 | AC-INT-021 | REQ-INT-017 | SCR-INT-003 | — | F4-SCR-INT-003 |
| TC-INT-047 | AC-INT-024 | REQ-INT-020 | SCR-INT-004 | — | F4-SCR-INT-004 |
| TC-INT-048 | AC-INT-027 | REQ-INT-023 | SCR-INT-005 | — | F3-SCR-INT-005 |
| TC-INT-049 | AC-INT-045 | REQ-INT-040 | SCR-INT-001 | — | F4-SCR-INT-001 |
| TC-INT-050 | AC-INT-046 | REQ-INT-041 | SCR-INT-001 | RULE-INT-004 → — (frontend check, no catalog code) | F3-SCR-INT-001 |
| TC-INT-051 | AC-INT-047 | REQ-INT-042 | SCR-INT-001 | — | F4-SCR-INT-001 |
| TC-INT-052 | AC-INT-048 | REQ-INT-043 | SCR-INT-001 | — | F4-SCR-INT-001 |
| TC-INT-053 | AC-INT-050 | REQ-INT-045 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-054 | AC-INT-051 | REQ-INT-046 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-055 | AC-INT-052 | REQ-INT-047 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-056 | AC-INT-053 | REQ-INT-048 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-057 | AC-INT-054 | REQ-INT-049 | SCR-INT-002 | — | F3-SCR-INT-002 |
| TC-INT-058 | AC-INT-055 | REQ-INT-049 | SCR-INT-002 | — | F3-SCR-INT-002 |
| TC-INT-059 | AC-INT-056 | REQ-INT-050 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-060 | AC-INT-057 | REQ-INT-051 | SCR-INT-002 | — | F3-SCR-INT-002 |
| TC-INT-061 | AC-INT-058 | REQ-INT-052 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-062 | AC-INT-059 | REQ-INT-053 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-063 | AC-INT-060 | REQ-INT-054 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-064 | AC-INT-063 | REQ-INT-057 | SCR-INT-001 | — | F2-SCR-INT-001 |
| TC-INT-065 | AC-INT-041 | REQ-INT-036 | SCR-INT-005 | INT-502-APPROVAL-API-FAILED | F2-SCR-INT-005 |
| TC-INT-066 | AC-INT-049 | REQ-INT-044 | SCR-INT-001 | — | F2-SCR-INT-001 |
| TC-INT-067 | AC-INT-061 | REQ-INT-055 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-068 | AC-INT-062 | REQ-INT-056 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-069 | AC-INT-026 | REQ-INT-022 | SCR-INT-005 | — | F1-SCR-INT-005 |
| TC-INT-070 | AC-INT-043 | REQ-INT-038 | SCR-INT-005 | — | F4-SCR-INT-005 |
| TC-INT-071 | UXD-INT-001 (AC-INT-047) | REQ-INT-042 | SCR-INT-001 | — | F1-SCR-INT-001 |
| TC-INT-072 | UXD-INT-001 (AC-INT-045) | REQ-INT-040 | SCR-INT-001 | — | F2-SCR-INT-001 |
| TC-INT-073 | UXD-INT-002 (AC-INT-050) | REQ-INT-045 | SCR-INT-002 | — | F1-SCR-INT-002 |
| TC-INT-074 | UXD-INT-002 (AC-INT-050) | REQ-INT-045 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-075 | UXD-INT-003 (AC-INT-051) | REQ-INT-046 | SCR-INT-002 | — | F1-SCR-INT-002 |
| TC-INT-076 | UXD-INT-003 (AC-INT-051) | REQ-INT-046 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-077 | UXD-INT-004 (AC-INT-052) | REQ-INT-047 | SCR-INT-002 | — | F1-SCR-INT-002 |
| TC-INT-078 | UXD-INT-004 (AC-INT-053) | REQ-INT-048 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-079 | UXD-INT-005 (AC-INT-020) | REQ-INT-016 | SCR-INT-003 | — | F3-SCR-INT-003 |
| TC-INT-080 | UXD-INT-005 (AC-INT-020) | REQ-INT-016 | SCR-INT-003 | — | F2-SCR-INT-003 |
| TC-INT-081 | UXD-INT-008 (AC-INT-024) | REQ-INT-020 | SCR-INT-004 | — | F3-SCR-INT-004 |
| TC-INT-082 | UXD-INT-008 (AC-INT-024) | REQ-INT-020 | SCR-INT-004 | — | F2-SCR-INT-004 |
| TC-INT-083 | UXD-INT-006 (AC-INT-021) | REQ-INT-017 | SCR-INT-003 | — | F1-SCR-INT-003 |
| TC-INT-084 | UXD-INT-006 (AC-INT-021) | REQ-INT-017 | SCR-INT-003 | — | F2-SCR-INT-003 |
| TC-INT-085 | UXD-INT-007 (AC-INT-024) | REQ-INT-020 | SCR-INT-004 | — | F1-SCR-INT-004 |
| TC-INT-086 | UXD-INT-007 (AC-INT-024) | REQ-INT-020 | SCR-INT-004 | — | F2-SCR-INT-004 |

Package → TC: F1-SCR-INT-001: TC-INT-071 · F1-SCR-INT-002: TC-INT-073, TC-INT-075, TC-INT-077 · F1-SCR-INT-003: TC-INT-083 · F1-SCR-INT-004: TC-INT-085 · F1-SCR-INT-005: TC-INT-069 · F2-SCR-INT-001: TC-INT-064, TC-INT-066, TC-INT-072 · F2-SCR-INT-002: TC-INT-067, TC-INT-068, TC-INT-074, TC-INT-076, TC-INT-078 · F2-SCR-INT-003: TC-INT-080, TC-INT-084 · F2-SCR-INT-004: TC-INT-082, TC-INT-086 · F2-SCR-INT-005: TC-INT-065 · F3-SCR-INT-001: TC-INT-050 · F3-SCR-INT-002: TC-INT-057, TC-INT-058, TC-INT-060 · F3-SCR-INT-003: TC-INT-045, TC-INT-079 · F3-SCR-INT-004: TC-INT-081 · F3-SCR-INT-005: TC-INT-048 · F4-SCR-INT-001: TC-INT-049, TC-INT-051, TC-INT-052 · F4-SCR-INT-002: TC-INT-053, TC-INT-054, TC-INT-055, TC-INT-056, TC-INT-059, TC-INT-061, TC-INT-062, TC-INT-063 · F4-SCR-INT-003: TC-INT-046 · F4-SCR-INT-004: TC-INT-047 · F4-SCR-INT-005: TC-INT-070

## COVERAGE

AC covered (frontend track) 26 — AC-INT-020, AC-INT-021, AC-INT-024, AC-INT-026, AC-INT-027, AC-INT-041, AC-INT-043, AC-INT-045, AC-INT-046, AC-INT-047, AC-INT-048, AC-INT-049, AC-INT-050, AC-INT-051, AC-INT-052, AC-INT-053, AC-INT-054, AC-INT-055, AC-INT-056, AC-INT-057, AC-INT-058, AC-INT-059, AC-INT-060, AC-INT-061, AC-INT-062, AC-INT-063; of these AC-INT-026, AC-INT-041, AC-INT-043 are also covered in `backend-test-plan-int.md` (ADR-INT-019 (2)) · the remaining 51 ACs (incl. AC-INT-067 … AC-INT-074 of INT's reads) are covered there → module AC coverage 74/74, no gap ✗.
SCR covered 5/5 (SCR-INT-001, SCR-INT-002, SCR-INT-003, SCR-INT-004, SCR-INT-005) · UXD covered 8/8 (UXD-INT-001 … UXD-INT-008, 2 cases each) · frontend plan units with acceptance 20/20 (F1–F4 × 5 screens; ALIGN-FE is `no_tests`).
TC count 42 for 23 frontend ACs + 3 twins + 8 UXD (guard ~2×: 26 AC-derived cases for 23 ACs).
