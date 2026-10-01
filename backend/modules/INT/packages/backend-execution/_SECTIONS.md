<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# BACKEND EXECUTION PLAN — Host Integration (INT)
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias   Dialect : oracle19c   Framework : spring-boot-4-java-21
Inputs : srs-int.md · db-script-int.md · registry-srs-int.md · registry-db-int.md · contract-int.md · the published contracts of CHK, DOC and RPT (platform edges) and of the module reached through XM-INT-001
Governance : FULL (db-script present — no table by design, ADR-INT-015)   Open ADRs : 0 BLOCKED — decisions applied: ADR-INT-001 … ADR-INT-017, ADR-INT-020, ADR-INT-023 (analysis/decisions/INT/)
══════════════════════════════════════════════════════════════════

## EXECUTION PLAN INDEX — INT v1

### Entity registry
| ENT | Name | Table | Business code | Operations |
|---|---|---|---|---|
| — | INT declares no entity (ADR-INT-007, ADR-INT-013) | — | — | — |
| — (Report Store, consumed by value) | Check Run — the subject of every INT endpoint | owned by the Report Store | — | create (start a Check — API-INT-001), create (hand over an upload — API-INT-002), custom (confirm the uploads — API-INT-003), create (record a decision — API-INT-004), read (a Check and its report — API-INT-005), list (the Checks of a request — API-INT-006), list (the uploaded documents of a Check — API-INT-007), read (the required document types of the Check's version — API-INT-008) |

### API registry
| API | Operation | Verb | Path | Traces |
|---|---|---|---|---|
| API-INT-001 | Start a Check | POST | /api/v1/checks | REQ-INT-001 … REQ-INT-008, REQ-INT-044, REQ-INT-057 … REQ-INT-060 |
| API-INT-002 | Hand over an uploaded document | POST | /api/v1/checks/{checkId}/documents | REQ-INT-009 … REQ-INT-017, REQ-INT-053, REQ-INT-065 |
| API-INT-003 | Confirm the uploads | POST | /api/v1/checks/{checkId}/upload-confirmation | REQ-INT-018 … REQ-INT-020, REQ-INT-053 |
| API-INT-004 | Record an Employee Decision | POST | /api/v1/checks/{checkId}/decision | REQ-INT-021 … REQ-INT-039, REQ-INT-054 |
| API-INT-005 | Read a Check and its report | GET | /api/v1/check-reports/{checkId} | REQ-INT-045 … REQ-INT-056, REQ-INT-061, REQ-INT-066 |
| API-INT-006 | List the Checks of a request | GET | /api/v1/check-reports | REQ-INT-040, REQ-INT-042, REQ-INT-043, REQ-INT-062 |
| API-INT-007 | List the uploaded documents of a Check | GET | /api/v1/checks/{checkId}/documents | REQ-INT-017, REQ-INT-020, REQ-INT-063 |
| API-INT-008 | Read the required document types of a Check's version | GET | /api/v1/checks/{checkId}/required-document-types | REQ-INT-016, REQ-INT-020, REQ-INT-064 |

### Rule registry
| RULE | Name | Scope | Enforced where | Message en / ar |
|---|---|---|---|---|
| RULE-INT-001 | Uploads only while the Check waits for documents | Check Run (read, Report Store) | API-INT-002 | ✓ / PENDING ADR-INT-017 |
| RULE-INT-002 | A decision is complete before any Approval API call | decision request | API-INT-004 (approval path) | ✓ (Report Store's text) / PENDING ADR-INT-017 |
| RULE-INT-003 | Approval only on a completed, undecided Check | Check Run (read, Report Store) | API-INT-004 (approval path) | ✓ (Report Store's text) / PENDING ADR-INT-017 |
| RULE-INT-004 | The frontend is opened for one request | launch context | employee frontend (P3.2) — no backend endpoint, no catalog row | ✓ / PENDING ADR-INT-017 |

### Screen registry
| Screen | Type | ENT | Permission names |
|---|---|---|---|
| SCR-REQ-INT-001 — Checks of a request | list + start | — (Report Store) | none (no permission model — raw-idea A2) |
| SCR-REQ-INT-002 — Check report | read | — (Report Store) | none |
| SCR-REQ-INT-003 — Document upload | create + list | — (Report Store) | none |
| SCR-REQ-INT-004 — Upload confirmation | custom | — (Report Store) | none |
| SCR-REQ-INT-005 — Employee decision | create | — (Report Store) | none |

### Screen demand resolution (SRS `Operations` lines — ADR-INT-017 (4))
| Screen | Operation | Resolution |
|---|---|---|
| SCR-REQ-INT-001 | create | built — API-INT-001 (Start a Check) on the Report Store's Check Run |
| SCR-REQ-INT-001 | list | built — API-INT-006 (List the Checks of a request) on the Report Store's Check Run, relayed from the Report Store's list of the Checks of a request (ADR-INT-020) |
| SCR-REQ-INT-002 | read | built — API-INT-005 (Read a Check and its report) on the Report Store's Check Run, relayed from the Report Store's read of a Check (ADR-INT-020) |
| SCR-REQ-INT-003 | create | built — API-INT-002 (Hand over an uploaded document) on the Report Store's Check Run |
| SCR-REQ-INT-003 | list | built — API-INT-007 (List the uploaded documents of a Check) and API-INT-008 (the required document types of the Check's version) on the Report Store's Check Run (ADR-INT-020) |
| SCR-REQ-INT-004 | custom | built — API-INT-003 (Confirm the uploads) on the Report Store's Check Run |
| SCR-REQ-INT-005 | create | built — API-INT-004 (Record an Employee Decision) on the Report Store's Check Run |

### QRC summary
None — INT has no repository; every read and write is another module's in-process operation (ADR-INT-017 (2)).

DB ALIGNMENT: see manifest — ALIGNED ✓ / issues: 0 · INTEGRATION: 1 edge (XM-INT-001), one block in CROSS-MOD · SECURITY: 5 screens × 1 role (Employee), no permission model (caller authentication deferred — raw-idea A2)

```yaml name=totals
DBF: 10
XM: 1
API: 8
QR: 0
```

## DB ALIGNMENT MANIFEST — INT v1

Columns, types and SRS references are read from db-script-int.md (dbf-matrix) by DBF id. Every row is a READ BINDING (ADR-INT-015): the value reaches INT through the owner's in-process operation; INT writes none of them. Writer: why no INT endpoint writes the column.

| DBF | ENT | Plan property | Plan type | XM | Status | Writer |
|---|---|---|---|---|---|---|
| DBF-INT-001 | — (Report Store) | checkId | Long | — | ✓ | derived — the Report Store generates it when the Check Engine creates the Check run; INT passes it by value |
| DBF-INT-002 | — (Report Store) | status | CheckStatus (enum) | — | ✓ | derived — written by the Check Engine through the Report Store; INT only reads it |
| DBF-INT-003 | — (Report Store) | serviceCode | String | — | ✓ | derived — written at Check start from API-INT-001's request by the Check Engine and the Report Store |
| DBF-INT-004 | — (Report Store) | versionNumber | Integer | — | ✓ | derived — written at Check start by the Check Engine and the Report Store |
| DBF-INT-005 | — (Report Store) | requestNumber | String | — | ✓ | derived — written at Check start from API-INT-001's request by the Check Engine and the Report Store |
| DBF-INT-006 | — (Report Store) | employeeId | String | — | ✓ | derived — written at Check start from API-INT-001's request by the Check Engine and the Report Store |
| DBF-INT-007 | — (Report Store) | employeeDecision | EmployeeDecision (enum) | — | ✓ | derived — written by the Report Store from API-INT-004's hand-over |
| DBF-INT-008 | — (Report Store) | decidedBy | String | — | ✓ | derived — written by the Report Store from API-INT-004's hand-over |
| DBF-INT-009 | — (via XM-INT-001) | approvalEnabled | Boolean | XM-INT-001 | ✓ | derived — written by the target's load run; read only through XM-INT-001 |
| DBF-INT-010 | — (via XM-INT-001) | approvalApi | String (method + path template) | XM-INT-001 | ✓ | derived — written by the target's load run; read only through XM-INT-001 |

Legend ✓ aligned. No property of INT's own is stored.













## QUERY REFERENCE CATALOG — INT v1

None — INT owns no table and no repository (ADR-INT-015, ADR-INT-017 (2)). Every read is the Report Store's read of a Check (PORTS) or the approval definition (XM-INT-001); every write is the Check Engine's, Document Access's or the Report Store's own operation.

## ERROR CATALOG — INT v1

```yaml name=error-catalog
rows:
  - {code: "INT-400-REQUEST-INVALID", rule: PLATFORM-STD, api: [API-INT-001, API-INT-002, API-INT-003, API-INT-004, API-INT-005, API-INT-007, API-INT-008], http: 400, trigger: "the body, a multipart part or the checkId cannot be read (REQ-INT-007)", messages: {en: "The request could not be read: {detail}.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-017"}
  - {code: "INT-409-CHECK-NOT-AWAITING-DOCUMENTS", rule: RULE-INT-001, api: [API-INT-002], http: 409, trigger: "a file is uploaded for a Check whose status is not AWAITING_DOCUMENTS (REQ-INT-011)", messages: {en: "Documents can be uploaded only while Check {checkId} is waiting for documents; its status is {status}.", ar: "PENDING ADR-INT-017"}}
  - {code: "INT-413-UPLOAD-TOO-LARGE", rule: PLATFORM-STD, api: [API-INT-002], http: 413, trigger: "the upload request exceeds the upload request limit (REQ-INT-014)", messages: {en: "The upload is larger than the {limit} the service accepts in one request.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-017"}
  - {code: "INT-502-APPROVAL-API-FAILED", rule: PLATFORM-STD, api: [API-INT-004], http: 502, trigger: "the host Approval API answers outside 2xx, cannot be reached or has no configured address (REQ-INT-036)", messages: {en: "The approval was not executed: the host Approval API answered {status}. Nothing was recorded; you can try again.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-017"}
  - {code: "INT-504-APPROVAL-API-TIMED-OUT", rule: PLATFORM-STD, api: [API-INT-004], http: 504, trigger: "the host Approval API does not answer within the approval timeout (REQ-INT-037)", messages: {en: "The approval was not executed: the host Approval API did not answer within {timeout} seconds. Nothing was recorded; you can try again.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-017"}
  - {code: "INT-500", rule: PLATFORM-STD, api: [API-INT-001, API-INT-002, API-INT-003, API-INT-004, API-INT-005, API-INT-006, API-INT-007, API-INT-008], http: 500, trigger: "an unexpected server failure (REQ-INT-008)", messages: {en: "The request could not be completed because of an unexpected error.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-017"}
  - {code: "CHK-400-START-INCOMPLETE", rule: PASS-THROUGH, api: [API-INT-001], http: 400, trigger: "the Check Engine refuses a start with a value absent or blank", messages: {en: "A Check needs a service code, a request number and the employee's identity.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "CHK-422-SERVICE-NOT-AVAILABLE", rule: PASS-THROUGH, api: [API-INT-001], http: 422, trigger: "the Check Engine refuses a start for an unknown or withdrawn service", messages: {en: "The service \"{serviceCode}\" is not available for Checks.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "CHK-422-CONNECTION-NOT-ACTIVATED", rule: PASS-THROUGH, api: [API-INT-001], http: 422, trigger: "the Check Engine refuses a start whose connection is not activated", messages: {en: "The service \"{serviceCode}\" cannot be checked: connection \"{connectionName}\" is not activated in this environment.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "CHK-404-CHECK-NOT-FOUND", rule: PASS-THROUGH, api: [API-INT-003], http: 404, trigger: "the Check Engine knows no Check with this identifier on confirmation", messages: {en: "Check {checkId} does not exist.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "CHK-409-CHECK-NOT-AWAITING-DOCUMENTS", rule: PASS-THROUGH, api: [API-INT-003], http: 409, trigger: "the Check Engine refuses a confirmation of a Check not waiting for documents", messages: {en: "Check {checkId} is not waiting for documents; its status is {status}.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "DOC-400-INCOMPLETE-UPLOAD", rule: PASS-THROUGH, api: [API-INT-002], http: 400, trigger: "Document Access refuses an upload without a document type or with an empty file", messages: {en: "The upload needs a Check, a document type and a file that is not empty.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "DOC-404-SERVICE-VERSION-NOT-FOUND", rule: PASS-THROUGH, api: [API-INT-002], http: 404, trigger: "Document Access cannot resolve the Check's service package version", messages: {en: "service package version not found", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "DOC-422-FETCH-MODE-NOT-MANUAL", rule: PASS-THROUGH, api: [API-INT-002], http: 422, trigger: "Document Access refuses an upload for a service whose fetch mode is not manual", messages: {en: "Documents can be uploaded only for a service whose documents are provided by the employee; the service \"{serviceCode}\" obtains its documents by \"{fetchMode}\".", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE", rule: PASS-THROUGH, api: [API-INT-002], http: 422, trigger: "Document Access refuses a document type the version does not require", messages: {en: "\"{documentType}\" is not a document type of the service \"{serviceCode}\"; choose one of: {requiredDocumentTypes}.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "DOC-409-CHECK-ENDED", rule: PASS-THROUGH, api: [API-INT-002], http: 409, trigger: "Document Access refuses an upload for a Check it has recorded as ended — the race in which the Check ends between INT's status read and the handover (ADR-INT-025)", messages: {en: "The Check {checkId} has already ended; documents can no longer be uploaded for it. Start a new check to provide these documents.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-025"}
  - {code: "DOC-422-UPLOAD-LIMIT-REACHED", rule: PASS-THROUGH, api: [API-INT-002], http: 422, trigger: "Document Access refuses an upload when the Check already holds the maximum uploads per Check (ADR-INT-025)", messages: {en: "The Check {checkId} already has the maximum of {maxUploads} uploaded documents; no further file can be uploaded for it.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-025"}
  - {code: "RPT-404-CHECK-NOT-FOUND", rule: PASS-THROUGH, api: [API-INT-002, API-INT-004, API-INT-005, API-INT-008], http: 404, trigger: "the Report Store holds no Check with this identifier", messages: {en: "Check {checkId} was not found.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
  - {code: "RPT-400-DECISION-INCOMPLETE", rule: RULE-INT-002, api: [API-INT-004], http: 400, trigger: "the decision code is not APPROVED or REJECTED or the deciding employee is missing — raised by INT before an Approval API call, otherwise by the Report Store", messages: {en: "The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-010"}
  - {code: "RPT-409-CHECK-NOT-COMPLETED", rule: RULE-INT-003, api: [API-INT-004], http: 409, trigger: "the Check is not COMPLETED — raised by INT before an Approval API call, otherwise by the Report Store", messages: {en: "Check {checkId} is not completed; a decision can only be recorded on a completed Check.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-010"}
  - {code: "RPT-409-DECISION-ALREADY-RECORDED", rule: RULE-INT-003, api: [API-INT-004], http: 409, trigger: "the Check already holds an Employee Decision — raised by INT before an Approval API call, otherwise by the Report Store", messages: {en: "Check {checkId} already has an Employee Decision.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-010"}
  - {code: "RPT-400-REQUEST-KEYS-MISSING", rule: PASS-THROUGH, api: [API-INT-006], http: 400, trigger: "the Report Store refuses a list of the Checks of a request without a service code or a request number", messages: {en: "Both a service code and a request number are needed to list Checks.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-020"}
  - {code: "RPT-422-APPROVAL-FLAG-ON-REJECTION", rule: PASS-THROUGH, api: [API-INT-004], http: 422, trigger: "the Report Store refuses an executed rejection (INT never sends one — REQ-INT-027)", messages: {en: "The decision was not recorded: only an APPROVED decision is executed through the Approval API.", ar: "PENDING ADR-INT-017"}, adr: "ADR-INT-003"}
```

## Coverage
| RULE | Where enforced | Catalog code |
|---|---|---|
| RULE-INT-001 | API-INT-002 (`UploadGuard`) | INT-409-CHECK-NOT-AWAITING-DOCUMENTS |
| RULE-INT-002 | API-INT-004 step 3 (`ApprovalGuard`) | RPT-400-DECISION-INCOMPLETE |
| RULE-INT-003 | API-INT-004 step 3 (`ApprovalGuard`) | RPT-409-CHECK-NOT-COMPLETED, RPT-409-DECISION-ALREADY-RECORDED |
| RULE-INT-004 | employee frontend (P3.2) | — (no backend path) |

| DBF | Phases | QR | XM |
|---|---|---|---|
| DBF-INT-001 … DBF-INT-008 | DATA-DOM, PORTS, SVC-API | — | — (platform edge INT → RPT) |
| DBF-INT-009, DBF-INT-010 | DATA-DOM, SVC-API, CROSS-MOD | — | XM-INT-001 |

| XM | requires | tests |
|---|---|---|
| XM-INT-001 | REG:DELIVERED | AC-INT-029, AC-INT-032, AC-INT-034, AC-INT-073 |

Frontend-served requirements (REQ-INT-040 … REQ-INT-043, REQ-INT-045 … REQ-INT-052, REQ-INT-055, REQ-INT-056, REQ-INT-066) are built by P3.2 on INT's reads API-INT-005 … API-INT-008 (ADR-INT-020); presentation (order, MISSING safeguard, plain text, polling) is the frontend's (ADR-INT-011).

ADRs cited: ADR-INT-001, ADR-INT-003, ADR-INT-009, ADR-INT-010, ADR-INT-011, ADR-INT-012, ADR-INT-013, ADR-INT-015, ADR-INT-016, ADR-INT-017, ADR-INT-020, ADR-INT-023.
