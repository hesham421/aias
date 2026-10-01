# BACKEND TEST PLAN — Host Integration (INT)
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias   Stage : P4 (test-gen)   Framework : agnostic (profile.stack.testing.backend)
Sources: _state/current-srs.md (REQ-INT-001 … REQ-INT-066, AC-INT-001 … AC-INT-079, RULE-INT-001 … RULE-INT-004) · current-registry-srs.md · current-registry-db.md (XM-INT-001) · current-backend-execution-plan.md (API-INT-001 … API-INT-008, CROSS-MOD XM-INT-001) · current-frontend-execution-plan.md · current-api-spec.yaml (api-spec-int.yaml — every endpoint shape asserted here) — all v1
Open ADRs: ADR-INT-017 (Arabic messages PENDING) · ADR-INT-019 / ADR-INT-022 (derivation choices) · ADR-INT-020 (INT reads) · ADR-INT-021 (frontend binding) · ADR-INT-023 (API-INT-007 binds Document Access's listing operation; closes the DOC listing gap) · ADR-INT-025 (DOC-409-CHECK-ENDED, DOC-422-UPLOAD-LIMIT-REACHED passed through) · ADR-INT-026 (gate round 1 clarifications) — 0 BLOCKED
══════════════════════════════════════════════════════════════════

Framework note: the plan is framework-neutral — each TC block below is the whole contract; the consumer repository
chooses its tool and turns each TC into a test. Endpoint shapes (method, path, request/response schema, the error
responses' codes) are read in `api-spec-int.yaml` by the `API-*` id cited on the `Exercises` line. Every refusal is
asserted in the error envelope ProblemDetail (RFC 9457) → {type, title, status, detail, code}; message texts are
copied from the SRS / error catalog (en); ar is `PENDING ADR-INT-017`. The host Approval API is a stub
(ADR-INT-019 (5)). MODEL-EVAL is not populated: INT calls no model — the known-result request set belongs to the
module that runs the comparison (ADR-INT-019 (4)). 55 backend ACs (ADR-INT-019 (1), ADR-INT-022, ADR-INT-025, ADR-INT-026) — the other 24 ACs are
derived in `frontend-test-plan-int.md`. TC-INT-087 … TC-INT-094 cover INT's reads (ADR-INT-020).

<!-- PHASE:TEST-PLAN-BE:START traces=REQ-INT-001,REQ-INT-002,REQ-INT-003,REQ-INT-004,REQ-INT-005,REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-018,REQ-INT-019,REQ-INT-021,REQ-INT-022,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-029,REQ-INT-030,REQ-INT-031,REQ-INT-032,REQ-INT-033,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-039,REQ-INT-058,REQ-INT-059,REQ-INT-060,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-064,AC-INT-001,AC-INT-002,AC-INT-003,AC-INT-004,AC-INT-005,AC-INT-006,AC-INT-007,AC-INT-008,AC-INT-009,AC-INT-010,AC-INT-011,AC-INT-012,AC-INT-013,AC-INT-014,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-019,AC-INT-022,AC-INT-023,AC-INT-025,AC-INT-026,AC-INT-028,AC-INT-029,AC-INT-030,AC-INT-031,AC-INT-032,AC-INT-033,AC-INT-034,AC-INT-035,AC-INT-036,AC-INT-037,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,AC-INT-044,AC-INT-064,AC-INT-065,AC-INT-066,AC-INT-067,AC-INT-068,AC-INT-069,AC-INT-070,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,REQ-INT-065,AC-INT-075,AC-INT-076,AC-INT-077,AC-INT-079 -->
## PHASE TEST-PLAN-BE

<!-- SUB:RULE-SCENARIOS:START traces=REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-011,REQ-INT-012,REQ-INT-014,REQ-INT-015,REQ-INT-019,REQ-INT-024,REQ-INT-027,REQ-INT-028,REQ-INT-029,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-039,REQ-INT-058,REQ-INT-059,REQ-INT-060,REQ-INT-061,REQ-INT-062,REQ-INT-064,AC-INT-007,AC-INT-008,AC-INT-009,AC-INT-010,AC-INT-011,AC-INT-012,AC-INT-015,AC-INT-016,AC-INT-018,AC-INT-019,AC-INT-023,AC-INT-028,AC-INT-031,AC-INT-032,AC-INT-033,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-044,AC-INT-064,AC-INT-065,AC-INT-066,AC-INT-068,AC-INT-070,AC-INT-074,REQ-INT-033,AC-INT-075,AC-INT-076,AC-INT-079 -->
### RULE-SCENARIOS — refusals, rule violations and state guarantees

<!-- TC:TC-INT-001:START traces=AC-INT-007,REQ-INT-006,API-INT-001 -->
### TC-INT-001 — A start for a withdrawn service passes the Check Engine's refusal
Derived from : AC-INT-007  (REQ-INT-006)
Exercises    : API-INT-001 POST /api/v1/checks
Rule / code  : PASS-THROUGH → CHK-422-SERVICE-NOT-AVAILABLE
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: service `old-service` is withdrawn in the Service Registry
Host data    : none
Steps        : 1. POST /api/v1/checks with serviceCode "old-service", requestNumber <any>, employeeId <any>. 2. List the Checks of that service code and request number (GET /api/v1/checks — the Report Store's read).
Expected     : 1 → HTTP 422, application/problem+json, code `CHK-422-SERVICE-NOT-AVAILABLE`, detail en: "The service "old-service" is not available for Checks." · ar: PENDING ADR-INT-017. 2 → no Check is listed (no Check was created).
Test data    : serviceCode `old-service`; requestNumber, employeeId: placeholders
<!-- TC:TC-INT-001:END -->

<!-- TC:TC-INT-002:START traces=AC-INT-008,REQ-INT-006,API-INT-002 -->
### TC-INT-002 — An upload of a type the service does not require passes Document Access's refusal
Derived from : AC-INT-008  (REQ-INT-006)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : PASS-THROUGH → DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 612 of `manual-service` is AWAITING_DOCUMENTS; `manual-service` requires TRANSCRIPT and ID_CARD
Host data    : none
Steps        : 1. POST /api/v1/checks/612/documents (multipart) with documentType "PASSPORT" and one non-empty file.
Expected     : HTTP 422, code `DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE`, detail en: ""PASSPORT" is not a document type of the service "manual-service"; choose one of: TRANSCRIPT, ID_CARD." · ar: PENDING ADR-INT-017.
Test data    : checkId 612; documentType PASSPORT; file: placeholder (non-empty)
<!-- TC:TC-INT-002:END -->

<!-- TC:TC-INT-003:START traces=AC-INT-009,REQ-INT-006,API-INT-004 -->
### TC-INT-003 — A second decision on a decided Check passes the Report Store's refusal
Derived from : AC-INT-009  (REQ-INT-006)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : RULE-INT-003 → RPT-409-DECISION-ALREADY-RECORDED (raised by the Report Store — no Approval API on this version)
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 613 is COMPLETED with decision REJECTED; its version does not enable the Approval API
Host data    : none
Steps        : 1. POST /api/v1/checks/613/decision with employeeDecision "APPROVED", decidedBy <any>.
Expected     : HTTP 409, code `RPT-409-DECISION-ALREADY-RECORDED`, detail en: "Check 613 already has an Employee Decision." · ar: PENDING ADR-INT-017.
Test data    : checkId 613; decidedBy: placeholder
<!-- TC:TC-INT-003:END -->

<!-- TC:TC-INT-004:START traces=AC-INT-010,REQ-INT-006,API-INT-003 -->
### TC-INT-004 — A confirmation of a running Check passes the Check Engine's refusal
Derived from : AC-INT-010  (REQ-INT-006)
Exercises    : API-INT-003 POST /api/v1/checks/{checkId}/upload-confirmation
Rule / code  : PASS-THROUGH → CHK-409-CHECK-NOT-AWAITING-DOCUMENTS
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 614 is RUNNING
Host data    : none
Steps        : 1. POST /api/v1/checks/614/upload-confirmation with body {}.
Expected     : HTTP 409, code `CHK-409-CHECK-NOT-AWAITING-DOCUMENTS`, detail en: "Check 614 is not waiting for documents; its status is RUNNING." · ar: PENDING ADR-INT-017.
Test data    : checkId 614
<!-- TC:TC-INT-004:END -->

<!-- TC:TC-INT-005:START traces=AC-INT-011,REQ-INT-007,API-INT-003 -->
### TC-INT-005 — A non-numeric Check identifier is refused before any module is called
Derived from : AC-INT-011  (REQ-INT-007)
Exercises    : API-INT-003 POST /api/v1/checks/{checkId}/upload-confirmation
Rule / code  : PLATFORM-STD → INT-400-REQUEST-INVALID
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: any state; the Check Engine's in-process interface is observed for calls
Host data    : none
Steps        : 1. POST /api/v1/checks/abc/upload-confirmation with body {}.
Expected     : HTTP 400, code `INT-400-REQUEST-INVALID`, detail en: "The request could not be read: checkId must be a number." · ar: PENDING ADR-INT-017; the Check Engine receives no call.
Test data    : checkId `abc`
<!-- TC:TC-INT-005:END -->

<!-- TC:TC-INT-006:START traces=AC-INT-012,REQ-INT-008,API-INT-004 -->
### TC-INT-006 — An unexpected failure is answered without internals
Derived from : AC-INT-012  (REQ-INT-008)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : PLATFORM-STD → INT-500
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: the Report Store is unreachable because of a database outage (its datasource is stopped); Check 615 exists
Host data    : none
Steps        : 1. POST /api/v1/checks/615/decision with employeeDecision <APPROVED or REJECTED>, decidedBy <any>.
Expected     : HTTP 500, code `INT-500`, detail en: "The request could not be completed because of an unexpected error." · ar: PENDING ADR-INT-017; the body contains no stack trace.
Test data    : checkId 615; decision values: placeholders
<!-- TC:TC-INT-006:END -->

<!-- TC:TC-INT-007:START traces=AC-INT-015,REQ-INT-011,API-INT-002 -->
### TC-INT-007 — An upload for a running Check is refused and Document Access receives nothing
Derived from : AC-INT-015  (REQ-INT-011)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : RULE-INT-001 → INT-409-CHECK-NOT-AWAITING-DOCUMENTS
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 618 of `manual-service` is RUNNING
Host data    : none
Steps        : 1. POST /api/v1/checks/618/documents with documentType "TRANSCRIPT" and one non-empty file. 2. List the uploaded documents of Check 618 (GET /api/v1/uploaded-documents?checkId=618 — Document Access's read).
Expected     : 1 → HTTP 409, code `INT-409-CHECK-NOT-AWAITING-DOCUMENTS`, detail en: "Documents can be uploaded only while Check 618 is waiting for documents; its status is RUNNING." · ar: PENDING ADR-INT-017. 2 → no document listed (Document Access received nothing).
Test data    : checkId 618; documentType TRANSCRIPT; file: placeholder
<!-- TC:TC-INT-007:END -->

<!-- TC:TC-INT-008:START traces=AC-INT-016,REQ-INT-012,API-INT-002 -->
### TC-INT-008 — An upload for an unknown Check passes the Report Store's not-found refusal
Derived from : AC-INT-016  (REQ-INT-012)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : PASS-THROUGH → RPT-404-CHECK-NOT-FOUND
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check 99999 exists
Host data    : none
Steps        : 1. POST /api/v1/checks/99999/documents with documentType "TRANSCRIPT" and one non-empty file.
Expected     : HTTP 404, code `RPT-404-CHECK-NOT-FOUND`, detail en: "Check 99999 was not found." · ar: PENDING ADR-INT-017; Document Access receives nothing.
Test data    : checkId 99999
<!-- TC:TC-INT-008:END -->

<!-- TC:TC-INT-009:START traces=AC-INT-018,REQ-INT-014,API-INT-002 -->
### TC-INT-009 — An upload request above the request limit is refused
Derived from : AC-INT-018  (REQ-INT-014)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : PLATFORM-STD → INT-413-UPLOAD-TOO-LARGE
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: `aias.integration.upload.request-limit` = 50 MB; Check 620 is AWAITING_DOCUMENTS
Host data    : none
Steps        : 1. POST /api/v1/checks/620/documents with documentType <a required type> and a 60 MB file.
Expected     : HTTP 413, code `INT-413-UPLOAD-TOO-LARGE`, detail en: "The upload is larger than the 50 MB the service accepts in one request." · ar: PENDING ADR-INT-017; Document Access receives nothing.
Test data    : upload request limit 50 MB; file size 60 MB; checkId 620
<!-- TC:TC-INT-009:END -->

<!-- TC:TC-INT-010:START traces=AC-INT-019,REQ-INT-015,API-INT-002 -->
### TC-INT-010 — A file name shaped like a path is passed as text and opens no file
Derived from : AC-INT-019  (REQ-INT-015)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : PORTS
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: Check 621 is AWAITING_DOCUMENTS; file-system access by Host Integration is observed
Host data    : none
Steps        : 1. POST /api/v1/checks/621/documents with documentType "TRANSCRIPT" and a file named `../../etc/passwd`.
Expected     : Document Access receives the file's bytes with file name "../../etc/passwd" as text; no file of the server's file system is opened by Host Integration.
Test data    : checkId 621; file name `../../etc/passwd`
<!-- TC:TC-INT-010:END -->

<!-- TC:TC-INT-011:START traces=AC-INT-023,REQ-INT-019,API-INT-002 -->
### TC-INT-011 — Uploads alone never continue the Check
Derived from : AC-INT-023  (REQ-INT-019)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 625 of `manual-service`, which requires TRANSCRIPT and ID_CARD, is AWAITING_DOCUMENTS
Host data    : none
Steps        : 1. POST /api/v1/checks/625/documents with a TRANSCRIPT. 2. POST /api/v1/checks/625/documents with an ID_CARD. 3. Do not call the upload confirmation; read Check 625 (GET /api/v1/checks/625 — the Report Store's read).
Expected     : 3 → status AWAITING_DOCUMENTS.
Test data    : checkId 625; files: placeholders
<!-- TC:TC-INT-011:END -->

<!-- TC:TC-INT-012:START traces=AC-INT-028,REQ-INT-024,API-INT-002,API-INT-003 -->
### TC-INT-012 — Neither an upload nor a confirmation records a decision
Derived from : AC-INT-028  (REQ-INT-024)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents; API-INT-003 POST /api/v1/checks/{checkId}/upload-confirmation
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 630 of `manual-service` is AWAITING_DOCUMENTS
Host data    : none
Steps        : 1. POST /api/v1/checks/630/documents with a TRANSCRIPT. 2. POST /api/v1/checks/630/upload-confirmation with body {}. 3. Read Check 630 (GET /api/v1/checks/630).
Expected     : 3 → `decision` is null: Check 630 holds no Employee Decision.
Test data    : checkId 630; file: placeholder
<!-- TC:TC-INT-012:END -->

<!-- TC:TC-INT-013:START traces=AC-INT-031,REQ-INT-027,API-INT-004 -->
### TC-INT-013 — A rejection never calls the Approval API
Derived from : AC-INT-031  (REQ-INT-027)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 632 of `approve-service` version 2 (Approval API enabled) is COMPLETED and undecided; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/632/decision with employeeDecision "REJECTED", decidedBy <any>.
Expected     : The host receives no call; Check 632 holds decision REJECTED, executed through the Approval API false (`approvalApiExecuted` false).
Test data    : checkId 632; decidedBy: placeholder
<!-- TC:TC-INT-013:END -->

<!-- TC:TC-INT-014:START traces=AC-INT-032,REQ-INT-028,API-INT-004 -->
### TC-INT-014 — No call where the version does not enable the Approval API
Derived from : AC-INT-032  (REQ-INT-028)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 633 of `scholarship-request` version 3 (Approval API not enabled) is COMPLETED and undecided; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/633/decision with employeeDecision "APPROVED", decidedBy <any>.
Expected     : No Approval API is called; Check 633 holds decision APPROVED, executed through the Approval API false.
Test data    : checkId 633; decidedBy: placeholder
<!-- TC:TC-INT-014:END -->

<!-- TC:TC-INT-015:START traces=AC-INT-033,REQ-INT-029,API-INT-001 -->
### TC-INT-015 — A completed compliant Check without a decision triggers no approval
Derived from : AC-INT-033  (REQ-INT-029)
Exercises    : API-INT-001 POST /api/v1/checks
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: `approve-service` version 2 enables the Approval API; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks for `approve-service` with requestNumber <any>, employeeId <any>. 2. Let the Check run to COMPLETED with Overall Status COMPLIANT; record no decision.
Expected     : The host receives no Approval API call (0 calls recorded by the stub).
Test data    : serviceCode `approve-service`; requestNumber, employeeId: placeholders
<!-- TC:TC-INT-015:END -->

<!-- TC:TC-INT-016:START traces=AC-INT-038,REQ-INT-034,API-INT-004 -->
### TC-INT-016 — An approval without a deciding employee is refused before any call
Derived from : AC-INT-038  (REQ-INT-034)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : RULE-INT-002 → RPT-400-DECISION-INCOMPLETE
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 638 of `approve-service` version 2 is COMPLETED and undecided; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/638/decision with employeeDecision "APPROVED" and no decidedBy.
Expected     : HTTP 400, code `RPT-400-DECISION-INCOMPLETE`, detail en: "The decision was not recorded: the deciding employee is missing." · ar: PENDING ADR-INT-017; the host receives no call.
Test data    : checkId 638
<!-- TC:TC-INT-016:END -->

<!-- TC:TC-INT-017:START traces=AC-INT-039,REQ-INT-035,API-INT-004 -->
### TC-INT-017 — An approval on a running Check is refused before any call
Derived from : AC-INT-039  (REQ-INT-035)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : RULE-INT-003 → RPT-409-CHECK-NOT-COMPLETED
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 639 of `approve-service` version 2 is RUNNING; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/639/decision with employeeDecision "APPROVED", decidedBy <any>.
Expected     : HTTP 409, code `RPT-409-CHECK-NOT-COMPLETED`, detail en: "Check 639 is not completed; a decision can only be recorded on a completed Check." · ar: PENDING ADR-INT-017; the host receives no call.
Test data    : checkId 639; decidedBy: placeholder
<!-- TC:TC-INT-017:END -->

<!-- TC:TC-INT-018:START traces=AC-INT-040,REQ-INT-035,API-INT-004 -->
### TC-INT-018 — An approval on an already decided Check is refused before any call
Derived from : AC-INT-040  (REQ-INT-035)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : RULE-INT-003 → RPT-409-DECISION-ALREADY-RECORDED
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 640 of `approve-service` version 2 is COMPLETED with decision REJECTED; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/640/decision with employeeDecision "APPROVED", decidedBy <any>.
Expected     : HTTP 409, code `RPT-409-DECISION-ALREADY-RECORDED`, detail en: "Check 640 already has an Employee Decision." · ar: PENDING ADR-INT-017; the host receives no call.
Test data    : checkId 640; decidedBy: placeholder
<!-- TC:TC-INT-018:END -->

<!-- TC:TC-INT-019:START traces=AC-INT-041,REQ-INT-036,API-INT-004 -->
### TC-INT-019 — A failed approval records nothing
Derived from : AC-INT-041  (REQ-INT-036)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : PLATFORM-STD → INT-502-APPROVAL-API-FAILED
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: Check 641 of `approve-service` version 2 is COMPLETED and undecided; the stub host Approval API answers HTTP 500
Host data    : none
Steps        : 1. POST /api/v1/checks/641/decision with employeeDecision "APPROVED", decidedBy <any>. 2. Read Check 641 (GET /api/v1/checks/641).
Expected     : 1 → HTTP 502, code `INT-502-APPROVAL-API-FAILED`, detail en: "The approval was not executed: the host Approval API answered 500. Nothing was recorded; you can try again." · ar: PENDING ADR-INT-017. 2 → `decision` is null: Check 641 holds no decision.
Test data    : checkId 641; stub answer 500; decidedBy: placeholder
<!-- TC:TC-INT-019:END -->

<!-- TC:TC-INT-020:START traces=AC-INT-042,REQ-INT-037,API-INT-004 -->
### TC-INT-020 — A timed-out approval records nothing
Derived from : AC-INT-042  (REQ-INT-037)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : PLATFORM-STD → INT-504-APPROVAL-API-TIMED-OUT
Package      : PORTS
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: `aias.integration.approval.timeout` = 10 s; Check 642 of `approve-service` version 2 is COMPLETED and undecided; the stub host Approval API does not answer
Host data    : none
Steps        : 1. POST /api/v1/checks/642/decision with employeeDecision "APPROVED", decidedBy <any>; measure the time to the answer. 2. Read Check 642.
Expected     : 1 → after 10 seconds, HTTP 504, code `INT-504-APPROVAL-API-TIMED-OUT`, detail en: "The approval was not executed: the host Approval API did not answer within 10 seconds. Nothing was recorded; you can try again." · ar: PENDING ADR-INT-017. 2 → Check 642 holds no decision.
Test data    : approval timeout 10 s; checkId 642; decidedBy: placeholder
<!-- TC:TC-INT-020:END -->

<!-- TC:TC-INT-021:START traces=AC-INT-044,REQ-INT-039,API-INT-004 -->
### TC-INT-021 — A refusal after an executed approval is answered and logged
Derived from : AC-INT-044  (REQ-INT-039)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : PASS-THROUGH → RPT-409-DECISION-ALREADY-RECORDED
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: Check 643 of `approve-service` version 2, request `R-643`, is COMPLETED and undecided; the stub host Approval API answers HTTP 200 after a delay; the service log is captured
Host data    : none
Steps        : 1. POST /api/v1/checks/643/decision with employeeDecision "APPROVED", decidedBy <any> (the stub holds its answer). 2. While the stub holds, POST /api/v1/checks/643/decision with employeeDecision "REJECTED", decidedBy <any> (recorded by the Report Store). 3. Release the stub's HTTP 200.
Expected     : Request 1 → HTTP 409, code `RPT-409-DECISION-ALREADY-RECORDED`, detail en: "Check 643 already has an Employee Decision." · ar: PENDING ADR-INT-017; the service log holds an entry naming Check 643, request "R-643" and the executed approval.
Test data    : checkId 643; requestNumber `R-643`; decidedBy: placeholders
<!-- TC:TC-INT-021:END -->

<!-- TC:TC-INT-022:START traces=AC-INT-064,REQ-INT-058 -->
### TC-INT-022 — No server-rendered report page exists
Derived from : AC-INT-064  (REQ-INT-058)
Exercises    : no operation — GET /api/v1/checks/{checkId}/view is mapped by no API document
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 717 exists
Host data    : none
Steps        : 1. GET /api/v1/checks/717/view.
Expected     : HTTP 404; no HTML report is returned.
Test data    : checkId 717
<!-- TC:TC-INT-022:END -->

<!-- TC:TC-INT-023:START traces=AC-INT-065,REQ-INT-059,API-INT-002,API-INT-004 -->
### TC-INT-023 — Host Integration keeps nothing after answering
Derived from : AC-INT-065  (REQ-INT-059)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents; API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 718 is AWAITING_DOCUMENTS; Check 719 is COMPLETED and undecided
Host data    : none
Steps        : 1. POST /api/v1/checks/718/documents with a TRANSCRIPT. 2. POST /api/v1/checks/719/decision with a decision. 3. After both answers, inspect Host Integration: its tables (none by design), its temporary multipart storage and its in-memory state.
Expected     : Host Integration holds no copy of the file, the request data or the decision; the file is listed only by Document Access (GET /api/v1/uploaded-documents?checkId=718) and the decision is held only by the Report Store (GET /api/v1/checks/719).
Test data    : checkIds 718, 719; file and decision: placeholders
<!-- TC:TC-INT-023:END -->

<!-- TC:TC-INT-024:START traces=AC-INT-066,REQ-INT-060,API-INT-004 -->
### TC-INT-024 — The only host connection is the Approval API call
Derived from : AC-INT-066  (REQ-INT-060)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 720 of an approval-enabled version is COMPLETED and undecided; outbound connections opened by Host Integration are recorded
Host data    : none
Steps        : 1. POST /api/v1/checks/720/decision with employeeDecision "APPROVED", decidedBy <any>. 2. List the outbound connections opened by Host Integration.
Expected     : The only host connection is the HTTP call to the Approval API; no database connection is opened by Host Integration.
Test data    : checkId 720; decidedBy: placeholder
<!-- TC:TC-INT-024:END -->

<!-- TC:TC-INT-088:START traces=AC-INT-068,REQ-INT-061,API-INT-005 -->
### TC-INT-088 — An unknown Check is refused on the report read
Derived from : AC-INT-068  (REQ-INT-061)
Exercises    : API-INT-005 GET /api/v1/check-reports/{checkId}
Rule / code  : PASS-THROUGH → RPT-404-CHECK-NOT-FOUND
Package      : SVC-API-QUERY
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check 99998 exists
Host data    : none
Steps        : 1. GET /api/v1/check-reports/99998.
Expected     : HTTP 404, code `RPT-404-CHECK-NOT-FOUND`, detail en: "Check 99998 was not found." · ar: PENDING ADR-INT-017.
Test data    : checkId 99998
<!-- TC:TC-INT-088:END -->

<!-- TC:TC-INT-090:START traces=AC-INT-070,REQ-INT-062,API-INT-006 -->
### TC-INT-090 — A list of Checks without a service code passes the Report Store's refusal
Derived from : AC-INT-070  (REQ-INT-062)
Exercises    : API-INT-006 GET /api/v1/check-reports
Rule / code  : PASS-THROUGH → RPT-400-REQUEST-KEYS-MISSING
Package      : SVC-API-QUERY
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Checks exist for request `1001`
Host data    : none
Steps        : 1. GET /api/v1/check-reports?requestNumber=1001 (no serviceCode).
Expected     : HTTP 400, code `RPT-400-REQUEST-KEYS-MISSING`, detail en: "Both a service code and a request number are needed to list Checks." · ar: PENDING ADR-INT-017; no Check is listed.
Test data    : requestNumber 1001; no serviceCode
<!-- TC:TC-INT-090:END -->

<!-- TC:TC-INT-094:START traces=AC-INT-074,REQ-INT-064,API-INT-008 -->
### TC-INT-094 — An unknown Check is refused on the required-document-types read
Derived from : AC-INT-074  (REQ-INT-064)
Exercises    : API-INT-008 GET /api/v1/checks/{checkId}/required-document-types
Rule / code  : PASS-THROUGH → RPT-404-CHECK-NOT-FOUND
Package      : SVC-API-QUERY
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: no Check 99997 exists; the Service Registry's version read is observed
Host data    : none
Steps        : 1. GET /api/v1/checks/99997/required-document-types.
Expected     : HTTP 404, code `RPT-404-CHECK-NOT-FOUND`, detail en: "Check 99997 was not found." · ar: PENDING ADR-INT-017; the Service Registry is not asked for any version.
Test data    : checkId 99997
<!-- TC:TC-INT-094:END -->
<!-- TC:TC-INT-096:START traces=AC-INT-075,REQ-INT-006,API-INT-002 -->
### TC-INT-096 — An upload reaching Document Access after the Check ended passes Document Access's ended-Check refusal
Derived from : AC-INT-075  (REQ-INT-006)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : PASS-THROUGH → DOC-409-CHECK-ENDED (ADR-INT-025)
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: Check 650 of `manual-service` version 1 is AWAITING_DOCUMENTS in the Report Store; Document Access has recorded Check 650 as ended (its end-of-Check notice arrived after INT's status read — the race of ADR-INT-025), so its handover refuses Check 650
Host data    : none
Steps        : 1. POST /api/v1/checks/650/documents (multipart) with documentType "TRANSCRIPT" and one non-empty file. 2. GET /api/v1/checks/650/documents.
Expected     : 1 → HTTP 409, application/problem+json, code `DOC-409-CHECK-ENDED`, detail en: "The Check 650 has already ended; documents can no longer be uploaded for it. Start a new check to provide these documents." · ar: PENDING ADR-INT-017 (not INT-500). 2 → HTTP 200 with 0 entries (no Uploaded Document was created).
Test data    : checkId 650; documentType TRANSCRIPT; file: placeholder (non-empty)
<!-- TC:TC-INT-096:END -->

<!-- TC:TC-INT-097:START traces=AC-INT-076,REQ-INT-006,API-INT-002 -->
### TC-INT-097 — An upload above the maximum uploads per Check passes Document Access's upload-limit refusal
Derived from : AC-INT-076  (REQ-INT-006)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : PASS-THROUGH → DOC-422-UPLOAD-LIMIT-REACHED (ADR-INT-025)
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class BOUNDARY · language ALL
Preconditions: the maximum uploads per Check is 20 (platform configuration); Check 651 of `manual-service` version 1 is AWAITING_DOCUMENTS and already holds 20 uploaded documents in Document Access
Host data    : none
Steps        : 1. POST /api/v1/checks/651/documents (multipart) with documentType "ID_CARD" and one non-empty file. 2. GET /api/v1/checks/651/documents.
Expected     : 1 → HTTP 422, application/problem+json, code `DOC-422-UPLOAD-LIMIT-REACHED`, detail en: "The Check 651 already has the maximum of 20 uploaded documents; no further file can be uploaded for it." · ar: PENDING ADR-INT-017 (not INT-500). 2 → HTTP 200 with exactly 20 entries.
Test data    : checkId 651; maximum uploads per Check 20; documentType ID_CARD; file: placeholder (non-empty)
<!-- TC:TC-INT-097:END -->

<!-- TC:TC-INT-098:START traces=AC-INT-040,REQ-INT-035,REQ-INT-033,API-INT-004 -->
### TC-INT-098 — Two simultaneous APPROVED decisions on one approval-enabled Check are serialised by the per-Check lock
Derived from : AC-INT-040  (REQ-INT-035, REQ-INT-033)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : RULE-INT-003 → RPT-409-DECISION-ALREADY-RECORDED (raised by INT before any Approval API call — the per-Check lock of API-INT-004)
Package      : SVC-API-COMMAND
Scenario     : STATE · data class EDGE · language ALL
Preconditions: Check 654 of `approve-service` version 2 (Approval API enabled), request `R-654`, is COMPLETED and undecided; the stub host Approval API holds its first answer (HTTP 200 after a delay released by the test) and counts the calls it receives
Host data    : none
Steps        : 1. Send request A: POST /api/v1/checks/654/decision with employeeDecision "APPROVED", decidedBy "E-1001"; wait until the stub has received A's call and holds it. 2. Send request B, the same body with decidedBy "E-1002", while A is held. 3. Release the stub's HTTP 200. 4. Read the Check (GET /api/v1/check-reports/654).
Expected     : the stub receives exactly 1 call in total (A's). B waits for A's lock and gets no answer before step 3. A → HTTP 201 with employeeDecision APPROVED, decidedBy "E-1001", approvalApiExecuted true. B → HTTP 409, code `RPT-409-DECISION-ALREADY-RECORDED`, detail en: "Check 654 already has an Employee Decision." · ar: PENDING ADR-INT-017, and B never reaches the stub. 4 → the decision is APPROVED by "E-1001" with approvalApiExecuted true.
Test data    : checkId 654; requestNumber `R-654`; decidedBy E-1001, E-1002
<!-- TC:TC-INT-098:END -->

<!-- TC:TC-INT-099:START traces=AC-INT-079,REQ-INT-034,API-INT-004 -->
### TC-INT-099 — A decision code outside APPROVED and REJECTED is refused before any call
Derived from : AC-INT-079  (REQ-INT-034)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : RULE-INT-002 → RPT-400-DECISION-INCOMPLETE (invalid-code half)
Package      : SVC-API-COMMAND
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 652 of `approve-service` version 2 (Approval API enabled) is COMPLETED and undecided; the stub host Approval API counts its calls
Host data    : none
Steps        : 1. POST /api/v1/checks/652/decision with employeeDecision "MAYBE", decidedBy "E-1001". 2. Read the Check (GET /api/v1/check-reports/652).
Expected     : 1 → HTTP 400, code `RPT-400-DECISION-INCOMPLETE`, detail en: "The decision was not recorded: `MAYBE` is not APPROVED or REJECTED." · ar: PENDING ADR-INT-017; the stub receives 0 calls. 2 → the Check holds no decision.
Test data    : checkId 652; employeeDecision MAYBE; decidedBy E-1001
<!-- TC:TC-INT-099:END -->

<!-- SUB:RULE-SCENARIOS:END -->

<!-- SUB:API-SCENARIOS:START traces=REQ-INT-001,REQ-INT-002,REQ-INT-003,REQ-INT-004,REQ-INT-005,REQ-INT-009,REQ-INT-010,REQ-INT-013,REQ-INT-018,REQ-INT-021,REQ-INT-022,REQ-INT-025,REQ-INT-026,REQ-INT-030,REQ-INT-031,REQ-INT-032,REQ-INT-033,REQ-INT-038,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-064,AC-INT-001,AC-INT-002,AC-INT-003,AC-INT-004,AC-INT-005,AC-INT-006,AC-INT-013,AC-INT-014,AC-INT-017,AC-INT-022,AC-INT-025,AC-INT-026,AC-INT-029,AC-INT-030,AC-INT-034,AC-INT-035,AC-INT-036,AC-INT-037,AC-INT-043,AC-INT-067,AC-INT-069,AC-INT-071,AC-INT-072,AC-INT-073,REQ-INT-065,AC-INT-077 -->
### API-SCENARIOS — endpoint behaviour on valid requests

<!-- TC:TC-INT-025:START traces=AC-INT-001,REQ-INT-001,API-INT-001 -->
### TC-INT-025 — Start a Check of a path service
Derived from : AC-INT-001  (REQ-INT-001)
Exercises    : API-INT-001 POST /api/v1/checks
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` is available with fetch mode `path`
Host data    : none
Steps        : 1. POST /api/v1/checks with serviceCode "scholarship-request", requestNumber "REQ-2026-0042", employeeId "E-3307".
Expected     : HTTP 202 with a new `checkId` and `status` RUNNING.
Test data    : scholarship-request · REQ-2026-0042 · E-3307
<!-- TC:TC-INT-025:END -->

<!-- TC:TC-INT-026:START traces=AC-INT-002,REQ-INT-001,API-INT-001 -->
### TC-INT-026 — Start a Check of a manual service
Derived from : AC-INT-002  (REQ-INT-001)
Exercises    : API-INT-001 POST /api/v1/checks
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `manual-service` is available with fetch mode `manual`
Host data    : none
Steps        : 1. POST /api/v1/checks with serviceCode "manual-service", requestNumber "M-77", employeeId "E-3307".
Expected     : HTTP 202 with a new `checkId` and `status` AWAITING_DOCUMENTS.
Test data    : manual-service · M-77 · E-3307
<!-- TC:TC-INT-026:END -->

<!-- TC:TC-INT-027:START traces=AC-INT-003,REQ-INT-002,API-INT-001 -->
### TC-INT-027 — The start is answered before the report exists
Derived from : AC-INT-003  (REQ-INT-002)
Exercises    : API-INT-001 POST /api/v1/checks
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: a `path` service whose pipeline takes 40 seconds
Host data    : none
Steps        : 1. POST /api/v1/checks for that service. 2. Right after the answer, read the Check (GET /api/v1/checks/{checkId}).
Expected     : 1 → the answer arrives with `status` RUNNING before the Check's report exists. 2 → `overallStatus` is null (no Overall Status).
Test data    : pipeline duration 40 s; serviceCode, requestNumber, employeeId: placeholders
<!-- TC:TC-INT-027:END -->

<!-- TC:TC-INT-028:START traces=AC-INT-004,REQ-INT-003,API-INT-001 -->
### TC-INT-028 — Host identifiers are handed on unchanged
Derived from : AC-INT-004  (REQ-INT-003)
Exercises    : API-INT-001 POST /api/v1/checks
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: service `scholarship-request` is available
Host data    : none
Steps        : 1. POST /api/v1/checks with serviceCode "scholarship-request", requestNumber "0042/B", employeeId "e.ahmed@moe". 2. Read the Check.
Expected     : 2 → request number "0042/B" and employee "e.ahmed@moe", unchanged.
Test data    : 0042/B · e.ahmed@moe
<!-- TC:TC-INT-028:END -->

<!-- TC:TC-INT-029:START traces=AC-INT-005,REQ-INT-004,API-INT-001 -->
### TC-INT-029 — An employee unknown to any directory is accepted
Derived from : AC-INT-005  (REQ-INT-004)
Exercises    : API-INT-001 POST /api/v1/checks
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: employee identity `X-999` is known to no directory of the service; an available service
Host data    : none
Steps        : 1. POST /api/v1/checks with that service, requestNumber <any>, employeeId "X-999". 2. Read the Check.
Expected     : 1 → HTTP 202. 2 → employee "X-999".
Test data    : employeeId X-999; serviceCode, requestNumber: placeholders
<!-- TC:TC-INT-029:END -->

<!-- TC:TC-INT-030:START traces=AC-INT-006,REQ-INT-005,API-INT-001 -->
### TC-INT-030 — The accepted start names the address of the Check's read
Derived from : AC-INT-006  (REQ-INT-005)
Exercises    : API-INT-001 POST /api/v1/checks
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the next Check receives identifier 611
Host data    : none
Steps        : 1. POST /api/v1/checks with an available service, requestNumber <any>, employeeId <any>.
Expected     : The answer names `/api/v1/checks/611` as the address of the Check's read (`checkUrl` and the `Location` header).
Test data    : checkId 611
<!-- TC:TC-INT-030:END -->

<!-- TC:TC-INT-031:START traces=AC-INT-013,REQ-INT-009,API-INT-002 -->
### TC-INT-031 — An upload is handed to Document Access
Derived from : AC-INT-013  (REQ-INT-009)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 616 of `manual-service` version 1 is AWAITING_DOCUMENTS
Host data    : none
Steps        : 1. POST /api/v1/checks/616/documents with documentType "TRANSCRIPT" and `transcript.pdf` (300 KB).
Expected     : HTTP 201 with the uploaded document's identifier, `documentType` TRANSCRIPT, `fileName` "transcript.pdf", `fileSize` 307200 and `oversized` false.
Test data    : checkId 616; transcript.pdf, 300 KB
<!-- TC:TC-INT-031:END -->

<!-- TC:TC-INT-032:START traces=AC-INT-014,REQ-INT-010,API-INT-002 -->
### TC-INT-032 — The upload carries the Check's own service version
Derived from : AC-INT-014  (REQ-INT-010)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 617 runs `manual-service` version 1 and version 2 is now current; Document Access's in-process hand-over is observed
Host data    : none
Steps        : 1. POST /api/v1/checks/617/documents with documentType "ID_CARD" and a file.
Expected     : Document Access receives service `manual-service` and version 1 with the file.
Test data    : checkId 617; versions 1 and 2
<!-- TC:TC-INT-032:END -->

<!-- TC:TC-INT-033:START traces=AC-INT-017,REQ-INT-013,API-INT-002 -->
### TC-INT-033 — An oversized file is accepted with Document Access's notice
Derived from : AC-INT-017  (REQ-INT-013)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class BOUNDARY · language ALL
Preconditions: the maximum file size is 10 MB; Check 619 of `manual-service` is AWAITING_DOCUMENTS
Host data    : none
Steps        : 1. POST /api/v1/checks/619/documents with documentType "ID_CARD" and a 12 MB file.
Expected     : HTTP 201 with `oversized` true and Document Access's notice text in `notice` (as Document Access gives it).
Test data    : maximum file size 10 MB; file 12 MB; checkId 619
<!-- TC:TC-INT-033:END -->

<!-- TC:TC-INT-034:START traces=AC-INT-022,REQ-INT-018,API-INT-003 -->
### TC-INT-034 — Confirmed uploads continue the Check
Derived from : AC-INT-022  (REQ-INT-018)
Exercises    : API-INT-003 POST /api/v1/checks/{checkId}/upload-confirmation
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 624 of `manual-service` is AWAITING_DOCUMENTS with a TRANSCRIPT and an ID_CARD uploaded
Host data    : none
Steps        : 1. POST /api/v1/checks/624/upload-confirmation with body {}.
Expected     : HTTP 202 with `checkId` 624 and `status` RUNNING.
Test data    : checkId 624
<!-- TC:TC-INT-034:END -->

<!-- TC:TC-INT-035:START traces=AC-INT-025,REQ-INT-021,API-INT-004 -->
### TC-INT-035 — A rejection without the Approval API is recorded
Derived from : AC-INT-025  (REQ-INT-021)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 627 is COMPLETED, NOT_COMPLIANT, undecided, and its version does not enable the Approval API
Host data    : none
Steps        : 1. POST /api/v1/checks/627/decision with employeeDecision "REJECTED", decidedBy "E-3307". 2. Read Check 627.
Expected     : 1 → HTTP 201. 2 → decision REJECTED, decided by "E-3307", executed through the Approval API false.
Test data    : checkId 627; E-3307
<!-- TC:TC-INT-035:END -->

<!-- TC:TC-INT-036:START traces=AC-INT-026,REQ-INT-022,API-INT-004 -->
### TC-INT-036 — The recorded decision is answered
Derived from : AC-INT-026  (REQ-INT-022)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 628 is COMPLETED and undecided, without the Approval API
Host data    : none
Steps        : 1. POST /api/v1/checks/628/decision with employeeDecision "APPROVED", decidedBy "E-4410".
Expected     : The answer carries `checkId` 628, `employeeDecision` APPROVED, `decidedBy` "E-4410", a `decidedAt` recording time and `approvalApiExecuted` false.
Test data    : checkId 628; E-4410
<!-- TC:TC-INT-036:END -->

<!-- TC:TC-INT-037:START traces=AC-INT-029,REQ-INT-025,API-INT-004 -->
### TC-INT-037 — The Approval API is called before the Report Store
Derived from : AC-INT-029  (REQ-INT-025)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 631 of `approve-service` version 2 is COMPLETED, COMPLIANT, undecided, request `R-631`; version 2 enables the Approval API `POST /requests/{requestId}/approve`; the stub host Approval API records every call (ADR-INT-019 (5)); the Report Store's hand-over is observed
Host data    : none
Steps        : 1. POST /api/v1/checks/631/decision with employeeDecision "APPROVED", decidedBy "E-3307".
Expected     : The host receives one `POST /requests/R-631/approve` before the Report Store receives the decision.
Test data    : checkId 631; R-631; E-3307
<!-- TC:TC-INT-037:END -->

<!-- TC:TC-INT-038:START traces=AC-INT-030,REQ-INT-026,API-INT-004 -->
### TC-INT-038 — An executed approval is recorded as executed
Derived from : AC-INT-030  (REQ-INT-026)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: TC-INT-037's setup; the stub host Approval API answers HTTP 200
Host data    : none
Steps        : 1. POST /api/v1/checks/631/decision with employeeDecision "APPROVED", decidedBy "E-3307". 2. Read Check 631.
Expected     : 1 → HTTP 201. 2 → decision APPROVED, decided by "E-3307", executed through the Approval API true.
Test data    : checkId 631; E-3307
<!-- TC:TC-INT-038:END -->

<!-- TC:TC-INT-039:START traces=AC-INT-034,REQ-INT-030,API-INT-004 -->
### TC-INT-039 — The approval definition of the Check's own version is used
Derived from : AC-INT-034  (REQ-INT-030)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 634 ran on `approve-service` version 2 (Approval API enabled); version 3, now current, does not enable it; Check 634 is COMPLETED and undecided; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/634/decision with employeeDecision "APPROVED", decidedBy <any>.
Expected     : The Approval API of version 2 is called (one call recorded by the stub).
Test data    : checkId 634; versions 2 and 3
<!-- TC:TC-INT-039:END -->

<!-- TC:TC-INT-040:START traces=AC-INT-035,REQ-INT-031,API-INT-004 -->
### TC-INT-040 — The request number is placed in the path as one encoded value
Derived from : AC-INT-035  (REQ-INT-031)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Check 635 of `approve-service` version 2 has request number `2026/77 A` and is COMPLETED and undecided; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/635/decision with employeeDecision "APPROVED", decidedBy <any>.
Expected     : The host receives `POST /requests/2026%2F77%20A/approve`.
Test data    : checkId 635; request number `2026/77 A`
<!-- TC:TC-INT-040:END -->

<!-- TC:TC-INT-041:START traces=AC-INT-036,REQ-INT-032,API-INT-004 -->
### TC-INT-041 — The call carries the Check and the deciding employee
Derived from : AC-INT-036  (REQ-INT-032)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 636 of `approve-service` version 2 is COMPLETED and undecided; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/636/decision with employeeDecision "APPROVED", decidedBy "E-3307".
Expected     : The Approval API call carries Check identifier 636 and deciding employee "E-3307".
Test data    : checkId 636; E-3307
<!-- TC:TC-INT-041:END -->

<!-- TC:TC-INT-042:START traces=AC-INT-037,REQ-INT-033,API-INT-004 -->
### TC-INT-042 — One call, never retried
Derived from : AC-INT-037  (REQ-INT-033)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: the stub host Approval API of `approve-service` answers HTTP 503; Check 637 is COMPLETED and undecided
Host data    : none
Steps        : 1. POST /api/v1/checks/637/decision with employeeDecision "APPROVED", decidedBy <any>, once.
Expected     : The host receives exactly one call.
Test data    : checkId 637; stub answer 503
<!-- TC:TC-INT-042:END -->

<!-- TC:TC-INT-043:START traces=AC-INT-043,REQ-INT-038,API-INT-004 -->
### TC-INT-043 — A retry after a failed approval is a new decision request
Derived from : AC-INT-043  (REQ-INT-038)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: TC-INT-019 happened (Check 641 holds no decision); the stub host Approval API now answers HTTP 200
Host data    : none
Steps        : 1. POST /api/v1/checks/641/decision with employeeDecision "APPROVED", decidedBy <any>. 2. Read Check 641.
Expected     : 1 → the host receives one call. 2 → decision APPROVED, executed through the Approval API true.
Test data    : checkId 641; stub answer 200
<!-- TC:TC-INT-043:END -->

<!-- TC:TC-INT-087:START traces=AC-INT-067,REQ-INT-061,API-INT-005 -->
### TC-INT-087 — A Check and its report are read through Host Integration
Derived from : AC-INT-067  (REQ-INT-061)
Exercises    : API-INT-005 GET /api/v1/check-reports/{checkId}
Rule / code  : —
Package      : SVC-API-QUERY
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 705 is COMPLETED on `scholarship-request` version 3 with Overall Status NOT_COMPLIANT, one finding "GPA at least 3.0", NOT_SATISFIED, evidence "2.7", note "Below the minimum", and a TRANSCRIPT document READ
Host data    : none
Steps        : 1. GET /api/v1/check-reports/705.
Expected     : HTTP 200 with `status` COMPLETED, `serviceCode` "scholarship-request", `versionNumber` 3, `overallStatus` NOT_COMPLIANT, 1 finding with `evidence` "2.7" and 1 document TRANSCRIPT with `readStatus` READ.
Test data    : Check 705
<!-- TC:TC-INT-087:END -->

<!-- TC:TC-INT-089:START traces=AC-INT-069,REQ-INT-062,API-INT-006 -->
### TC-INT-089 — The Checks of a request are listed through Host Integration
Derived from : AC-INT-069  (REQ-INT-062)
Exercises    : API-INT-006 GET /api/v1/check-reports
Rule / code  : —
Package      : SVC-API-QUERY
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: request `REQ-2026-0042` of `scholarship-request` has Checks 701 (started 09:00) and 702 (started 10:30)
Host data    : none
Steps        : 1. GET /api/v1/check-reports?serviceCode=scholarship-request&requestNumber=REQ-2026-0042.
Expected     : HTTP 200 with `total` 2 and `checks` 702 then 701.
Test data    : scholarship-request · REQ-2026-0042 · Checks 701, 702
<!-- TC:TC-INT-089:END -->

<!-- TC:TC-INT-091:START traces=AC-INT-071,REQ-INT-063,API-INT-007 -->
### TC-INT-091 — The uploaded documents are listed without content
Derived from : AC-INT-071  (REQ-INT-063)
Exercises    : API-INT-007 GET /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : SVC-API-QUERY
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 623 has one uploaded TRANSCRIPT `t.pdf` of 300 KB (Document Access's listing operation published — ADR-INT-023)
Host data    : none
Steps        : 1. GET /api/v1/checks/623/documents.
Expected     : HTTP 200 with 1 entry: `documentType` TRANSCRIPT, `fileName` "t.pdf", `fileSize` 307200, `oversized` false, and no content field.
Test data    : checkId 623; t.pdf; 300 KB
<!-- TC:TC-INT-091:END -->

<!-- TC:TC-INT-092:START traces=AC-INT-072,REQ-INT-063,API-INT-007 -->
### TC-INT-092 — A Check without uploads lists none
Derived from : AC-INT-072  (REQ-INT-063)
Exercises    : API-INT-007 GET /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : SVC-API-QUERY
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Check 631 exists and no document was uploaded for it
Host data    : none
Steps        : 1. GET /api/v1/checks/631/documents.
Expected     : HTTP 200 with 0 entries.
Test data    : checkId 631
<!-- TC:TC-INT-092:END -->

<!-- TC:TC-INT-095:START traces=AC-INT-071,REQ-INT-063,API-INT-007 -->
### TC-INT-095 — The uploaded-documents read relays Document Access's listing operation unchanged
Derived from : AC-INT-071  (REQ-INT-063)
Exercises    : API-INT-007 GET /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 623 has two uploads in Document Access — TRANSCRIPT `t.pdf` of 300 KB uploaded first, ID_CARD `id.png` of 60 MB (oversized) uploaded second; calls to Document Access's in-process listing operation `listUploadedDocuments` are observed (ADR-INT-023)
Host data    : none
Steps        : 1. GET /api/v1/checks/623/documents.
Expected     : 1 → Document Access's `listUploadedDocuments` receives exactly 1 call, with checkId 623. 2 → HTTP 200 with exactly 2 entries in upload order: TRANSCRIPT "t.pdf" 307200 oversized false, then ID_CARD "id.png" 62914560 oversized true, each with its `uploadedDocumentId` and `uploadedAt` as Document Access returned them, and no content field.
Test data    : checkId 623; t.pdf 300 KB; id.png 60 MB
<!-- TC:TC-INT-095:END -->

<!-- TC:TC-INT-093:START traces=AC-INT-073,REQ-INT-064,API-INT-008 -->
### TC-INT-093 — The required document types are those of the Check's own version
Derived from : AC-INT-073  (REQ-INT-064)
Exercises    : API-INT-008 GET /api/v1/checks/{checkId}/required-document-types
Rule / code  : —
Package      : SVC-API-QUERY
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 617 runs `manual-service` version 1, which requires TRANSCRIPT and ID_CARD; version 2, now current, requires only TRANSCRIPT
Host data    : none
Steps        : 1. GET /api/v1/checks/617/required-document-types.
Expected     : HTTP 200 with exactly 2 types: TRANSCRIPT and ID_CARD (`versionNumber` 1).
Test data    : checkId 617; versions 1 and 2
<!-- TC:TC-INT-093:END -->
<!-- TC:TC-INT-100:START traces=AC-INT-077,REQ-INT-065,API-INT-002,API-INT-007 -->
### TC-INT-100 — A second upload of the same document type is handed over separately and both are listed
Derived from : AC-INT-077  (REQ-INT-065)
Exercises    : API-INT-002 POST /api/v1/checks/{checkId}/documents · API-INT-007 GET /api/v1/checks/{checkId}/documents
Rule / code  : —
Package      : SVC-API-COMMAND
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: Check 653 of `manual-service` version 1 is AWAITING_DOCUMENTS and has one uploaded TRANSCRIPT `t.pdf`; calls to Document Access's handover are observed
Host data    : none
Steps        : 1. POST /api/v1/checks/653/documents (multipart) with documentType "TRANSCRIPT" and file `t-v2.pdf`. 2. GET /api/v1/checks/653/documents.
Expected     : 1 → HTTP 201 with documentType TRANSCRIPT and fileName "t-v2.pdf"; Document Access's handover receives exactly 1 call; INT makes no call that removes or replaces `t.pdf`. 2 → HTTP 200 with exactly 2 TRANSCRIPT entries in upload order: "t.pdf", then "t-v2.pdf".
Test data    : checkId 653; t.pdf, t-v2.pdf: placeholders (non-empty)
<!-- TC:TC-INT-100:END -->
<!-- SUB:API-SCENARIOS:END -->
<!-- PHASE:TEST-PLAN-BE:END -->

<!-- PHASE:INT-XM:START traces=REQ-INT-025,REQ-INT-028,REQ-INT-030,XM-INT-001 -->
## PHASE INT-XM

Target module REG (one edge — 1 TC, no SUB).

<!-- TC:TC-INT-044:START traces=XM-INT-001,REQ-INT-025,REQ-INT-028,REQ-INT-030,API-INT-004 -->
### TC-INT-044 — The decision path answers in the standard form when the approval definition cannot be read
Derived from : XM-INT-001 (REQ-INT-025, REQ-INT-028, REQ-INT-030)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : PLATFORM-STD → INT-500
Package      : XM-INT-001
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: the target read fails: the Service Registry's approval definition read (CON-REG-012, ENT-REG-002) answers not-found for the Check's service code and version; a Check of that version is COMPLETED and undecided; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/{checkId}/decision with employeeDecision "APPROVED", decidedBy <any>. 2. Read the Check (GET /api/v1/checks/{checkId}).
Expected     : 1 → a defined result, not an unhandled failure: HTTP 500, application/problem+json, code `INT-500`, detail en: "The request could not be completed because of an unexpected error." · ar: PENDING ADR-INT-017; the host receives no call; the service log names the Check identifier. 2 → the Check holds no decision.
Test data    : checkId, decidedBy: placeholders (GRACEFUL-DEGRADATION — XM-INT-001 SOFT-READ)
<!-- TC:TC-INT-044:END -->
<!-- PHASE:INT-XM:END -->

## TC TRACEABILITY INDEX

| TC | AC / XM | REQ | API | RULE / code | Package |
|---|---|---|---|---|---|
| TC-INT-001 | AC-INT-007 | REQ-INT-006 | API-INT-001 | PASS-THROUGH → CHK-422-SERVICE-NOT-AVAILABLE | SVC-API-COMMAND |
| TC-INT-002 | AC-INT-008 | REQ-INT-006 | API-INT-002 | PASS-THROUGH → DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE | SVC-API-COMMAND |
| TC-INT-003 | AC-INT-009 | REQ-INT-006 | API-INT-004 | RULE-INT-003 → RPT-409-DECISION-ALREADY-RECORDED (raised by the Report Store — no Approval API on this version) | SVC-API-COMMAND |
| TC-INT-004 | AC-INT-010 | REQ-INT-006 | API-INT-003 | PASS-THROUGH → CHK-409-CHECK-NOT-AWAITING-DOCUMENTS | SVC-API-COMMAND |
| TC-INT-005 | AC-INT-011 | REQ-INT-007 | API-INT-003 | PLATFORM-STD → INT-400-REQUEST-INVALID | SVC-API-COMMAND |
| TC-INT-006 | AC-INT-012 | REQ-INT-008 | API-INT-004 | PLATFORM-STD → INT-500 | SVC-API-COMMAND |
| TC-INT-007 | AC-INT-015 | REQ-INT-011 | API-INT-002 | RULE-INT-001 → INT-409-CHECK-NOT-AWAITING-DOCUMENTS | SVC-API-COMMAND |
| TC-INT-008 | AC-INT-016 | REQ-INT-012 | API-INT-002 | PASS-THROUGH → RPT-404-CHECK-NOT-FOUND | SVC-API-COMMAND |
| TC-INT-009 | AC-INT-018 | REQ-INT-014 | API-INT-002 | PLATFORM-STD → INT-413-UPLOAD-TOO-LARGE | SVC-API-COMMAND |
| TC-INT-010 | AC-INT-019 | REQ-INT-015 | API-INT-002 | — | PORTS |
| TC-INT-011 | AC-INT-023 | REQ-INT-019 | API-INT-002 | — | SVC-API-COMMAND |
| TC-INT-012 | AC-INT-028 | REQ-INT-024 | API-INT-002, API-INT-003 | — | SVC-API-COMMAND |
| TC-INT-013 | AC-INT-031 | REQ-INT-027 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-014 | AC-INT-032 | REQ-INT-028 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-015 | AC-INT-033 | REQ-INT-029 | API-INT-001 | — | SVC-API-COMMAND |
| TC-INT-016 | AC-INT-038 | REQ-INT-034 | API-INT-004 | RULE-INT-002 → RPT-400-DECISION-INCOMPLETE | SVC-API-COMMAND |
| TC-INT-017 | AC-INT-039 | REQ-INT-035 | API-INT-004 | RULE-INT-003 → RPT-409-CHECK-NOT-COMPLETED | SVC-API-COMMAND |
| TC-INT-018 | AC-INT-040 | REQ-INT-035 | API-INT-004 | RULE-INT-003 → RPT-409-DECISION-ALREADY-RECORDED | SVC-API-COMMAND |
| TC-INT-019 | AC-INT-041 | REQ-INT-036 | API-INT-004 | PLATFORM-STD → INT-502-APPROVAL-API-FAILED | SVC-API-COMMAND |
| TC-INT-020 | AC-INT-042 | REQ-INT-037 | API-INT-004 | PLATFORM-STD → INT-504-APPROVAL-API-TIMED-OUT | PORTS |
| TC-INT-021 | AC-INT-044 | REQ-INT-039 | API-INT-004 | PASS-THROUGH → RPT-409-DECISION-ALREADY-RECORDED | SVC-API-COMMAND |
| TC-INT-022 | AC-INT-064 | REQ-INT-058 | — | — | SVC-API-COMMAND |
| TC-INT-023 | AC-INT-065 | REQ-INT-059 | API-INT-002, API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-024 | AC-INT-066 | REQ-INT-060 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-088 | AC-INT-068 | REQ-INT-061 | API-INT-005 | PASS-THROUGH → RPT-404-CHECK-NOT-FOUND | SVC-API-QUERY |
| TC-INT-090 | AC-INT-070 | REQ-INT-062 | API-INT-006 | PASS-THROUGH → RPT-400-REQUEST-KEYS-MISSING | SVC-API-QUERY |
| TC-INT-094 | AC-INT-074 | REQ-INT-064 | API-INT-008 | PASS-THROUGH → RPT-404-CHECK-NOT-FOUND | SVC-API-QUERY |
| TC-INT-096 | AC-INT-075 | REQ-INT-006 | API-INT-002 | PASS-THROUGH → DOC-409-CHECK-ENDED | SVC-API-COMMAND |
| TC-INT-097 | AC-INT-076 | REQ-INT-006 | API-INT-002 | PASS-THROUGH → DOC-422-UPLOAD-LIMIT-REACHED | SVC-API-COMMAND |
| TC-INT-098 | AC-INT-040 | REQ-INT-035, REQ-INT-033 | API-INT-004 | RULE-INT-003 → RPT-409-DECISION-ALREADY-RECORDED (per-Check lock) | SVC-API-COMMAND |
| TC-INT-099 | AC-INT-079 | REQ-INT-034 | API-INT-004 | RULE-INT-002 → RPT-400-DECISION-INCOMPLETE | SVC-API-COMMAND |
| TC-INT-025 | AC-INT-001 | REQ-INT-001 | API-INT-001 | — | SVC-API-COMMAND |
| TC-INT-026 | AC-INT-002 | REQ-INT-001 | API-INT-001 | — | SVC-API-COMMAND |
| TC-INT-027 | AC-INT-003 | REQ-INT-002 | API-INT-001 | — | SVC-API-COMMAND |
| TC-INT-028 | AC-INT-004 | REQ-INT-003 | API-INT-001 | — | SVC-API-COMMAND |
| TC-INT-029 | AC-INT-005 | REQ-INT-004 | API-INT-001 | — | SVC-API-COMMAND |
| TC-INT-030 | AC-INT-006 | REQ-INT-005 | API-INT-001 | — | SVC-API-COMMAND |
| TC-INT-031 | AC-INT-013 | REQ-INT-009 | API-INT-002 | — | SVC-API-COMMAND |
| TC-INT-032 | AC-INT-014 | REQ-INT-010 | API-INT-002 | — | SVC-API-COMMAND |
| TC-INT-033 | AC-INT-017 | REQ-INT-013 | API-INT-002 | — | SVC-API-COMMAND |
| TC-INT-034 | AC-INT-022 | REQ-INT-018 | API-INT-003 | — | SVC-API-COMMAND |
| TC-INT-035 | AC-INT-025 | REQ-INT-021 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-036 | AC-INT-026 | REQ-INT-022 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-037 | AC-INT-029 | REQ-INT-025 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-038 | AC-INT-030 | REQ-INT-026 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-039 | AC-INT-034 | REQ-INT-030 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-040 | AC-INT-035 | REQ-INT-031 | API-INT-004 | — | PORTS |
| TC-INT-041 | AC-INT-036 | REQ-INT-032 | API-INT-004 | — | PORTS |
| TC-INT-042 | AC-INT-037 | REQ-INT-033 | API-INT-004 | — | PORTS |
| TC-INT-043 | AC-INT-043 | REQ-INT-038 | API-INT-004 | — | SVC-API-COMMAND |
| TC-INT-087 | AC-INT-067 | REQ-INT-061 | API-INT-005 | — | SVC-API-QUERY |
| TC-INT-089 | AC-INT-069 | REQ-INT-062 | API-INT-006 | — | SVC-API-QUERY |
| TC-INT-091 | AC-INT-071 | REQ-INT-063 | API-INT-007 | — | SVC-API-QUERY |
| TC-INT-092 | AC-INT-072 | REQ-INT-063 | API-INT-007 | — | SVC-API-QUERY |
| TC-INT-095 | AC-INT-071 | REQ-INT-063 | API-INT-007 | — | PORTS |
| TC-INT-093 | AC-INT-073 | REQ-INT-064 | API-INT-008 | — | SVC-API-QUERY |
| TC-INT-100 | AC-INT-077 | REQ-INT-065 | API-INT-002, API-INT-007 | — | SVC-API-COMMAND |
| TC-INT-044 | XM-INT-001 | REQ-INT-025, REQ-INT-028, REQ-INT-030 | API-INT-004 | PLATFORM-STD → INT-500 | XM-INT-001 |

API → TC: API-INT-001: 8 (TC-INT-001 …) · API-INT-002: 14 (TC-INT-002 …) · API-INT-003: 4 (TC-INT-004 …) · API-INT-004: 24 (TC-INT-003 …) · API-INT-005: 2 (TC-INT-088 …) · API-INT-006: 2 (TC-INT-090 …) · API-INT-007: 4 (TC-INT-091 …) · API-INT-008: 2 (TC-INT-094 …)

Package → TC: PORTS: TC-INT-010, TC-INT-020, TC-INT-040, TC-INT-041, TC-INT-042, TC-INT-095 · SVC-API-COMMAND: TC-INT-001, TC-INT-002, TC-INT-003, TC-INT-004, TC-INT-005, TC-INT-006, TC-INT-007, TC-INT-008, TC-INT-009, TC-INT-011, TC-INT-012, TC-INT-013, TC-INT-014, TC-INT-015, TC-INT-016, TC-INT-017, TC-INT-018, TC-INT-019, TC-INT-021, TC-INT-022, TC-INT-023, TC-INT-024, TC-INT-025, TC-INT-026, TC-INT-027, TC-INT-028, TC-INT-029, TC-INT-030, TC-INT-031, TC-INT-032, TC-INT-033, TC-INT-034, TC-INT-035, TC-INT-036, TC-INT-037, TC-INT-038, TC-INT-039, TC-INT-043, TC-INT-096, TC-INT-097, TC-INT-098, TC-INT-099, TC-INT-100 · SVC-API-QUERY: TC-INT-088, TC-INT-090, TC-INT-094, TC-INT-087, TC-INT-089, TC-INT-091, TC-INT-092, TC-INT-093 · XM-INT-001: TC-INT-044

## COVERAGE

AC covered (backend track) 55/55 — AC-INT-001, AC-INT-002, AC-INT-003, AC-INT-004, AC-INT-005, AC-INT-006, AC-INT-007, AC-INT-008, AC-INT-009, AC-INT-010, AC-INT-011, AC-INT-012, AC-INT-013, AC-INT-014, AC-INT-015, AC-INT-016, AC-INT-017, AC-INT-018, AC-INT-019, AC-INT-022, AC-INT-023, AC-INT-025, AC-INT-026, AC-INT-028, AC-INT-029, AC-INT-030, AC-INT-031, AC-INT-032, AC-INT-033, AC-INT-034, AC-INT-035, AC-INT-036, AC-INT-037, AC-INT-038, AC-INT-039, AC-INT-040, AC-INT-041, AC-INT-042, AC-INT-043, AC-INT-044, AC-INT-064, AC-INT-065, AC-INT-066, AC-INT-067, AC-INT-068, AC-INT-069, AC-INT-070, AC-INT-071, AC-INT-072, AC-INT-073, AC-INT-074, AC-INT-075, AC-INT-076, AC-INT-077, AC-INT-079 · the other 24 ACs (AC-INT-020, AC-INT-021, AC-INT-024, AC-INT-027, AC-INT-045 … AC-INT-063, AC-INT-078) are covered in `frontend-test-plan-int.md` → module AC coverage 79/79, no gap ✗.
REQ covered (backend) 43 · API covered 8/8 (API-INT-001 … API-INT-008) · XM edges covered 1/1 (XM-INT-001 → TC-INT-044).
Units of the backend execution plan with acceptance: PORTS, SVC-API-COMMAND, SVC-API-QUERY, XM-INT-001 (CORE, DATA-DOM, ALIGN-BE are `no_tests`).
TC count 58 for 55 ACs + 1 XM (TC-INT-095 — ADR-INT-023; TC-INT-096 … TC-INT-100 — ADR-INT-025, ADR-INT-026 and the per-Check lock case TC-INT-098) (guard ~2× not exceeded).
