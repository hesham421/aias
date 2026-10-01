<!-- source: PHASE:TEST-PLAN-BE / SUB:API-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-001, AC-INT-002, AC-INT-003, AC-INT-004, AC-INT-005, AC-INT-006, AC-INT-013, AC-INT-014, AC-INT-017, AC-INT-022, AC-INT-025, AC-INT-026, AC-INT-029, AC-INT-030, AC-INT-034, AC-INT-035, AC-INT-036, AC-INT-037, AC-INT-043, AC-INT-067, AC-INT-069, AC-INT-071, AC-INT-072, AC-INT-073, AC-INT-077, API-INT-001, API-INT-002, API-INT-003, API-INT-004, API-INT-005, API-INT-006, API-INT-007, API-INT-008, REQ-INT-001, REQ-INT-002, REQ-INT-003, REQ-INT-004, REQ-INT-005, REQ-INT-009, REQ-INT-010, REQ-INT-013, REQ-INT-018, REQ-INT-021, REQ-INT-022, REQ-INT-025, REQ-INT-026, REQ-INT-030, REQ-INT-031, REQ-INT-032, REQ-INT-033, REQ-INT-038, REQ-INT-061, REQ-INT-062, REQ-INT-063, REQ-INT-064, REQ-INT-065 -->
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
