<!-- source: PHASE:TEST-PLAN-FE / SUB:UI-FLOWS -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-020, AC-INT-021, AC-INT-024, AC-INT-027, AC-INT-041, AC-INT-045, AC-INT-046, AC-INT-047, AC-INT-048, AC-INT-050, AC-INT-051, AC-INT-052, AC-INT-053, AC-INT-054, AC-INT-055, AC-INT-056, AC-INT-057, AC-INT-058, AC-INT-059, AC-INT-060, AC-INT-063, AC-INT-075, AC-INT-076, AC-INT-077, REQ-INT-006, REQ-INT-016, REQ-INT-017, REQ-INT-020, REQ-INT-023, REQ-INT-036, REQ-INT-040, REQ-INT-041, REQ-INT-042, REQ-INT-043, REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, REQ-INT-049, REQ-INT-050, REQ-INT-051, REQ-INT-052, REQ-INT-053, REQ-INT-054, REQ-INT-057, REQ-INT-065, RULE-INT-004, SCR-INT-001, SCR-INT-002, SCR-INT-003, SCR-INT-004, SCR-INT-005 -->
<!-- SUB:UI-FLOWS:START traces=REQ-INT-016,REQ-INT-017,REQ-INT-020,REQ-INT-023,REQ-INT-036,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-057,AC-INT-020,AC-INT-021,AC-INT-024,AC-INT-027,AC-INT-041,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-063,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005,REQ-INT-006,REQ-INT-065,AC-INT-075,AC-INT-076,AC-INT-077 -->
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
<!-- TC:TC-INT-102:START traces=AC-INT-075,AC-INT-076,REQ-INT-006,SCR-INT-003 -->
### TC-INT-102 — Document Access's ended-Check and upload-limit refusals are shown as a form message on the upload form
Derived from : AC-INT-075, AC-INT-076  (REQ-INT-006)
Exercises    : SCR-INT-003 `/checks/650/documents` and `/checks/651/documents` (+ launch query string)
Rule / code  : PASS-THROUGH → DOC-409-CHECK-ENDED · DOC-422-UPLOAD-LIMIT-REACHED (ADR-INT-025)
Package      : F3-SCR-INT-003
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: Checks 650 and 651 of `manual-service` are AWAITING_DOCUMENTS; API-INT-002 answers HTTP 409 `DOC-409-CHECK-ENDED` for Check 650 (detail "The Check 650 has already ended; documents can no longer be uploaded for it. Start a new check to provide these documents.") and HTTP 422 `DOC-422-UPLOAD-LIMIT-REACHED` for Check 651 (detail "The Check 651 already has the maximum of 20 uploaded documents; no further file can be uploaded for it."); after the 409, API-INT-005 answers Check 650 as COMPLETED; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 650, choose TRANSCRIPT, choose a file, press "Upload". 2. Open the document upload of Check 651, choose ID_CARD, choose a file, press "Upload".
Expected     : 1 → the server's detail for Check 650 is shown as a form message on the upload form, not as an inline field error and not as the generic INT-500 message; once Check 650 is read again as COMPLETED the form is no longer offered. 2 → the server's detail for Check 651 is shown as a form message; the chosen type ID_CARD is kept and the form stays offered.
Test data    : checkIds 650, 651 (AC-INT-075, AC-INT-076)
<!-- TC:TC-INT-102:END -->

<!-- TC:TC-INT-103:START traces=AC-INT-077,REQ-INT-065,SCR-INT-003 -->
### TC-INT-103 — A document type that already has an upload is marked and stays selectable
Derived from : AC-INT-077  (REQ-INT-065)
Exercises    : SCR-INT-003 `/checks/653/documents` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-003
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Check 653 of `manual-service` is AWAITING_DOCUMENTS; API-INT-008 answers TRANSCRIPT and ID_CARD; API-INT-007 first answers one TRANSCRIPT `t.pdf`, then `t.pdf` and `t-v2.pdf` after the upload; API-INT-002 answers HTTP 201 for `t-v2.pdf`; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 653 and open the document type control. 2. Choose TRANSCRIPT, choose `t-v2.pdf`, press "Upload".
Expected     : 1 → TRANSCRIPT is marked "already uploaded" and can be selected; ID_CARD carries no marker. 2 → API-INT-002 receives exactly 1 request, with documentType TRANSCRIPT; the uploaded documents list shows 2 TRANSCRIPT entries, "t.pdf" first, then "t-v2.pdf".
Test data    : checkId 653; t-v2.pdf: placeholder (AC-INT-077)
<!-- TC:TC-INT-103:END -->
<!-- SUB:UI-FLOWS:END -->
