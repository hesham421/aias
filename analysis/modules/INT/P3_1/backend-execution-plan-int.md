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

<!-- PHASE:CORE:START traces=REQ-INT-007,REQ-INT-008,REQ-INT-014,REQ-INT-037,REQ-INT-057,REQ-INT-059,REQ-INT-060 -->
## PHASE CORE — CORE

### R1 — Core configuration
- Type mapping (oracle19c → Java), column types only (for the bound values INT carries):

| oracle19c | Java |
|---|---|
| NUMBER(19) | Long |
| NUMBER(10) | Integer |
| VARCHAR2(n CHAR) | String |
| NUMBER(1) | Boolean (0/1) |
| TIMESTAMP WITH TIME ZONE | OffsetDateTime |

- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` — every error-catalog row below is an instance of it; INT's own rows start with `INT-`, the pass-through rows keep their owner's prefix (ADR-INT-003).
- Error envelope: ProblemDetail (RFC 9457) → {type, title, status, detail, code}. One `@RestControllerAdvice` — `IntegrationProblemAdvice` — maps: INT's own exceptions to their catalog codes; every typed refusal arriving from the Check Engine, Document Access or the Report Store to a ProblemDetail carrying that refusal's own code, HTTP status (the `{http}` part of its code) and message, unchanged (REQ-INT-006); `MaxUploadSizeExceededException` → `INT-413-UPLOAD-TOO-LARGE`; an unreadable body, a missing multipart part or a non-numeric `checkId` → `INT-400-REQUEST-INVALID` (REQ-INT-007); any other exception → `INT-500`, logged with the request path and the Check identifier, the answer carrying no stack trace (REQ-INT-008).
- Lookup values: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded. INT uses the enums the owners publish (`CheckStatus` of the Check Engine, `EmployeeDecision` of the Report Store) and owns none (ADR-INT-013); service codes and document types pass through as strings.
- Workflow engine: **forbidden**.
- Search contract: INT has no search endpoint; its two lists (API-INT-006, API-INT-007) take exact keys, are bounded by their owners (at most 100 Checks; the uploaded documents of one Check) and are not paginated.
- Languages: messages en (SRS); ar PENDING ADR-INT-017.
- Configuration properties (environment settings, bound with `@ConfigurationProperties`, validated at start-up — ADR-INT-012):
  - `aias.integration.approval.timeout` — Duration, default `10s`; connect + read timeout of the host Approval API call (REQ-INT-037).
  - `aias.integration.approval.base-address` — URI of the host Approval API per environment; the version's definition supplies only the method and the path (ADR-INT-009). Absent → an approval-path decision answers `INT-502-APPROVAL-API-FAILED` with `{status}` = "no answer — no address is configured" and records nothing.
  - `aias.integration.upload.request-limit` — DataSize, default `50MB`, never below `aias.check.max-file-size` (start-up fails if lower); bound to `spring.servlet.multipart.max-request-size` and `max-file-size` (REQ-INT-014).
- No state between requests (REQ-INT-059): no table, no cache, no static or session field holds a request, a file, a report or a decision; an uploaded part lives only for its request (the container discards its temporary part when the request ends). The only in-memory structure is the per-Check approval lock of API-INT-004, released at the end of the request.
- No host database (REQ-INT-060): INT declares no `DataSource`, JDBC template or MCP client; its only outbound host connection is the Approval API adapter (PORTS).
- One REST API (REQ-INT-057, REQ-INT-058): the employee frontend calls only INT's operations (API-INT-001 … API-INT-008 — ADR-INT-020); the owners' own reads stay published for hosts; no server-rendered page or view controller exists (`/api/v1/checks/{checkId}/view` is not mapped → 404).
<!-- PHASE:CORE:END -->

<!-- PHASE:DATA-DOM:START traces=REQ-INT-011,REQ-INT-034,REQ-INT-035,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-006,DBF-INT-007,DBF-INT-008,DBF-INT-009,DBF-INT-010 -->
## PHASE DATA-DOM — DATA+DOM

No entity block: INT declares no entity and creates no table (ADR-INT-007, ADR-INT-013, ADR-INT-015). The domain is a set of immutable value objects (Java records) and two guards.

### Value objects
| Record | Fields (DBF where bound) | Built from |
|---|---|---|
| `CheckSnapshot` | checkId (DBF-INT-001), status (DBF-INT-002), serviceCode (DBF-INT-003), versionNumber (DBF-INT-004), requestNumber (DBF-INT-005), employeeDecision (DBF-INT-007, nullable) | the Report Store's read of a Check (PORTS) |
| `ApprovalDefinition` | enabled (DBF-INT-009), method, pathTemplate (both parsed from DBF-INT-010) | XM-INT-001 |
| `StartCheckCommand` | serviceCode (DBF-INT-003), requestNumber (DBF-INT-005), employeeId (DBF-INT-006) — exactly as received | API-INT-001 body |
| `UploadCommand` | checkId (DBF-INT-001), documentType, fileName (text), bytes | API-INT-002 multipart |
| `DecisionCommand` | checkId (DBF-INT-001), employeeDecision (DBF-INT-007), decidedBy (DBF-INT-008) — exactly as received | API-INT-004 body |

### Domain rules (owner layer: domain classes `UploadGuard`, `ApprovalGuard`)
- RULE-INT-001 — Uploads only while the Check waits for documents · trigger: on upload · scope: CREATE (upload)
  - statement: The system shall prevent handing an upload to Document Access when the Check's status is not AWAITING_DOCUMENTS.
  - message (en): Documents can be uploaded only while Check {checkId} is waiting for documents; its status is {status}. · message (ar): PENDING ADR-INT-017
  - data source: DBF-INT-002 · enforcement: app-level (`UploadGuard`) → `INT-409-CHECK-NOT-AWAITING-DOCUMENTS`
- RULE-INT-002 — A decision is complete before any Approval API call · trigger: on record decision (before the Approval API call) · scope: CREATE (decision, approval path)
  - statement: The system shall prevent calling the Approval API when the decision request carries no decision code of EMPLOYEE_DECISION or no deciding employee.
  - message (en): The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. · message (ar): PENDING ADR-INT-017
  - data source: DBF-INT-007, DBF-INT-008 (request values) · enforcement: app-level (`ApprovalGuard`) → `RPT-400-DECISION-INCOMPLETE` (the Report Store's own code — ADR-INT-010)
- RULE-INT-003 — Approval only on a completed, undecided Check · trigger: on record decision (before the Approval API call) · scope: CREATE (decision, approval path)
  - statement: The system shall prevent calling the Approval API when the Check's status is not COMPLETED or the Check already holds an Employee Decision.
  - message (en): Check {checkId} is not completed; a decision can only be recorded on a completed Check. / Check {checkId} already has an Employee Decision. · message (ar): PENDING ADR-INT-017
  - data source: DBF-INT-002, DBF-INT-007 · enforcement: app-level (`ApprovalGuard`) → `RPT-409-CHECK-NOT-COMPLETED` / `RPT-409-DECISION-ALREADY-RECORDED` (ADR-INT-010)
- RULE-INT-004 — The frontend is opened for one request — enforced by the employee frontend (P3.2) before any call; no backend endpoint, no catalog row.

STATE MACHINE  none of INT's own; INT reads the Check status (the Check Engine's closed list) and never changes it.
CROSS-MODULE   XM-INT-001 (approval definition).
REPOSITORY OPS none (no QR — ADR-INT-017).
<!-- PHASE:DATA-DOM:END -->

<!-- PHASE:PORTS:START traces=REQ-INT-001,REQ-INT-009,REQ-INT-015,REQ-INT-018,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-021,REQ-INT-029,REQ-INT-031,REQ-INT-032,REQ-INT-033,REQ-INT-036,REQ-INT-037 -->
## PHASE PORTS — PORTS+ADAPTERS

Every dependency sits behind an INT port with a replaceable adapter (profile `layers`). The modules of the same deployable are injected by type (profile `module_interface: in_process`).

- `CheckEnginePort` → `ChkCheckEngineAdapter` — wraps the injected Check Engine interface `CheckEngine`: `start(command)` calls `startCheck(serviceCode, requestNumber, employeeId)` with the values exactly as received and returns `{checkId, status}`; `confirm(checkId)` calls `confirmUploads(checkId)`. The Check Engine's typed refusals (CHK-400-START-INCOMPLETE, CHK-422-SERVICE-NOT-AVAILABLE, CHK-422-CONNECTION-NOT-ACTIVATED, CHK-404-CHECK-NOT-FOUND, CHK-409-CHECK-NOT-AWAITING-DOCUMENTS) propagate unchanged to `IntegrationProblemAdvice`.
- `DocumentAccessPort` → `DocDocumentAccessAdapter` — wraps the injected Document Access interface `DocumentAccess`: `handOver(command, serviceCode, versionNumber)` calls `handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` and returns its receipt `{uploadedDocumentId, documentType, fileName, fileSize, oversized, notice}`. The file is passed as bytes and the file name as text; INT never builds or opens a file path (REQ-INT-015). Document Access's six upload refusals (DOC-400-INCOMPLETE-UPLOAD, DOC-404-SERVICE-VERSION-NOT-FOUND, DOC-422-FETCH-MODE-NOT-MANUAL, DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE, DOC-409-CHECK-ENDED, DOC-422-UPLOAD-LIMIT-REACHED — the last two passed through per ADR-INT-025) propagate unchanged to `IntegrationProblemAdvice`, which answers each as a ProblemDetail with DOC's code, status and message (ADR-INT-003); INT neither mirrors DOC's upload limit nor tracks ended Checks. Several uploads of one document type are each handed over; INT never removes, replaces or picks among them (REQ-INT-065). `listUploaded(checkId)` calls Document Access's published in-process operation `listUploadedDocuments(checkId)` (Document Access's listing operation — ADR-INT-023, closing ADR-INT-020 (4)) and returns its list of {uploadedDocumentId, documentType, fileName, fileSize, oversized, uploadedAt} unchanged, in upload order, never the content; a Check with no upload (unknown or ended included) yields an empty list; never against DOC's table. DOC-400-CHECK-ID-REQUIRED cannot arise (INT always passes a parsed numeric checkId) and, if ever raised, is an unexpected failure answered INT-500 (ADR-INT-023 (2)).
- `CheckRecordPort` → `RptCheckRecordAdapter` — wraps the injected Report Store interface `ReportStore`: `read(checkId)` calls `readCheck(checkId)` and maps checkId, status, serviceCode, versionNumber, requestNumber and the decision code into `CheckSnapshot`; `readReport(checkId)` calls the same `readCheck(checkId)` and returns the whole report unchanged as `CheckReportView`; `listOfRequest(serviceCode, requestNumber)` calls `listChecksOfRequest(serviceCode, requestNumber)` and returns `{total, checks}` unchanged; `handOverDecision(checkId, employeeDecision, decidedBy, approvalApiExecuted)` calls `recordDecision(...)` and returns `{checkId, employeeDecision, decidedBy, decidedAt, approvalApiExecuted}`. The Report Store's refusals (RPT-404-CHECK-NOT-FOUND, RPT-400-DECISION-INCOMPLETE, RPT-409-CHECK-NOT-COMPLETED, RPT-409-DECISION-ALREADY-RECORDED, RPT-422-APPROVAL-FLAG-ON-REJECTION) propagate unchanged.
- `ApprovalDefinitionPort` — implemented in the CROSS-MOD block of XM-INT-001; injected only into `DecisionService` (REQ-INT-029).
- `VersionDocumentsPort` — implemented in the CROSS-MOD block of XM-INT-001; injected only into `RequiredDocumentTypesService` (REQ-INT-064).
- `HostApprovalPort` → `HttpHostApprovalAdapter` — the only network call of INT (ADR-INT-009, ADR-INT-017 (5)). `approve(definition, requestNumber, checkId, decidedBy)`:
  1. builds the URI from `aias.integration.approval.base-address` and `definition.pathTemplate`, expanding its single `{…}` placeholder with the request number as ONE path-segment value, URL-encoded by `UriComponentsBuilder.buildAndExpand(...).encode()` — never by string concatenation (REQ-INT-031);
  2. sends `definition.method` with the JSON body `{"checkId": <checkId>, "decidedBy": "<decidedBy>"}` (REQ-INT-032) through a `RestClient` whose connect and read timeouts are `aias.integration.approval.timeout`;
  3. any 2xx → success; any other status, a connection failure or an unconfigured base address → `ApprovalApiFailedException(status)` (`INT-502-APPROVAL-API-FAILED`, REQ-INT-036); a timeout → `ApprovalApiTimedOutException(seconds)` (`INT-504-APPROVAL-API-TIMED-OUT`, REQ-INT-037);
  4. exactly one attempt — no retry interceptor, no retry template (REQ-INT-033); every outcome logged at INFO with checkId, request number and status.
  The adapter is a plain Spring bean, never registered as a model tool, and injected only into `DecisionService` (REQ-INT-029; AIAS-3, AIAS-4). INT has no query port, no document reader and no model port.
<!-- PHASE:PORTS:END -->

<!-- PHASE:SVC-API:START traces=REQ-INT-001,REQ-INT-002,REQ-INT-003,REQ-INT-004,REQ-INT-005,REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-029,REQ-INT-030,REQ-INT-031,REQ-INT-032,REQ-INT-033,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-039,REQ-INT-044,REQ-INT-053,REQ-INT-054,REQ-INT-057,REQ-INT-058,REQ-INT-059,REQ-INT-060,REQ-INT-040,REQ-INT-042,REQ-INT-043,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-055,REQ-INT-056,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-064,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-006,DBF-INT-007,DBF-INT-008,DBF-INT-009,DBF-INT-010,REQ-INT-065,REQ-INT-066 -->
## PHASE SVC-API — SVC+API

8 API blocks (≥ 8 — split COMMAND / QUERY; no VIEW: INT renders no view, REQ-INT-058). Controllers: `CheckIntakeController` (API-INT-001), `CheckUploadController` (API-INT-002, API-INT-003, API-INT-007), `DecisionController` (API-INT-004), `CheckReportController` (API-INT-005, API-INT-006), `RequiredDocumentTypesController` (API-INT-008); services: `CheckIntakeService`, `UploadService`, `UploadConfirmationService`, `DecisionService`, `CheckReportService`, `UploadedDocumentsService`, `RequiredDocumentTypesService`.

<!-- SUB:SVC-API-COMMAND:START traces=REQ-INT-001,REQ-INT-002,REQ-INT-003,REQ-INT-004,REQ-INT-005,REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-029,REQ-INT-030,REQ-INT-031,REQ-INT-032,REQ-INT-033,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-039,REQ-INT-044,REQ-INT-053,REQ-INT-054,REQ-INT-057,REQ-INT-058,REQ-INT-059,REQ-INT-060,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-006,DBF-INT-007,DBF-INT-008,DBF-INT-009,DBF-INT-010,REQ-INT-065 -->
### SVC-API-COMMAND — the four write operations

<!-- API:API-INT-001:START traces=REQ-INT-001,REQ-INT-002,REQ-INT-003,REQ-INT-004,REQ-INT-005,REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-044,REQ-INT-057,REQ-INT-058,REQ-INT-059,REQ-INT-060,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-005,DBF-INT-006 -->
### API-INT-001 — Start a Check
Entity       : the Report Store's Check Run (operation: create — starts a Check whose run the Report Store keeps)
Endpoint     : /api/v1/checks   verb: POST
Layers       : controller CheckIntakeController.start → service CheckIntakeService.start
Request      : body (application/json) StartCheckRequest {serviceCode (DBF-INT-003, string ≤ 100, required), requestNumber (DBF-INT-005, string ≤ 100, required), employeeId (DBF-INT-006, string ≤ 100, required)} — passed exactly as received, never trimmed or checked against a directory (REQ-INT-003, REQ-INT-004); presence is decided by the Check Engine (CHK-400-START-INCOMPLETE); no path or query parameter
Response     : 202 · StartedCheckResponse {checkId (DBF-INT-001, int64), status (DBF-INT-002 — RUNNING or AWAITING_DOCUMENTS), checkUrl (`/api/v1/checks/{checkId}`)} and header `Location: /api/v1/checks/{checkId}` (REQ-INT-005) · not paginated · no envelope
Validations  : body readable as JSON (PLATFORM-STD, ADR-INT-017)
Errors       : INT-400-REQUEST-INVALID (400) · CHK-400-START-INCOMPLETE (400, PASS-THROUGH) · CHK-422-SERVICE-NOT-AVAILABLE (422, PASS-THROUGH) · CHK-422-CONNECTION-NOT-ACTIVATED (422, PASS-THROUGH) · INT-500 (500)
Orchestration : parse → `CheckEnginePort.start` (PORTS) → answer 202 at once; the pipeline runs in the background inside the Check Engine (REQ-INT-002). INT writes no column: DBF-INT-001 and DBF-INT-002 come back from the call
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE — INT allocates nothing and decides on nothing it writes; the Check identifier is allocated by the Report Store through the Check Engine, and every start is a new, independent Check
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; REQ-INT-004)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-001
Covers       : REQ-INT-044 (the frontend's start uses this endpoint with its launch context); REQ-INT-057 … REQ-INT-060 hold for every INT endpoint (CORE).
<!-- API:API-INT-001:END -->

<!-- API:API-INT-002:START traces=REQ-INT-065,REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-053,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004 -->
### API-INT-002 — Hand over an uploaded document
Entity       : the Report Store's Check Run (operation: create — one uploaded document handed over for the Check)
Endpoint     : /api/v1/checks/{checkId}/documents   verb: POST
Layers       : controller CheckUploadController.upload → service UploadService.upload
Request      : path checkId (DBF-INT-001, integer int64, required); body (multipart/form-data) documentType (string ≤ 100, required — one of the service's required document types, checked by Document Access), file (binary, exactly one part, required); the service code and version are NOT accepted from the caller (REQ-INT-010)
Response     : 201 · UploadReceiptResponse {uploadedDocumentId (int64), documentType, fileName, fileSize (int64), oversized (boolean), notice (string, present only when oversized — REQ-INT-013)} · not paginated · no envelope
Validations  : request within `aias.integration.upload.request-limit` (REQ-INT-014, PLATFORM-STD) · RULE-INT-001 — Uploads only while the Check waits for documents · trigger: on upload · statement: The system shall prevent handing an upload to Document Access when the Check's status is not AWAITING_DOCUMENTS. · message (en): Documents can be uploaded only while Check {checkId} is waiting for documents; its status is {status}. · message (ar): PENDING ADR-INT-017
Errors       : INT-400-REQUEST-INVALID (400) · RPT-404-CHECK-NOT-FOUND (404, PASS-THROUGH) · INT-409-CHECK-NOT-AWAITING-DOCUMENTS (409, RULE-INT-001) · INT-413-UPLOAD-TOO-LARGE (413) · DOC-400-INCOMPLETE-UPLOAD (400, PASS-THROUGH) · DOC-404-SERVICE-VERSION-NOT-FOUND (404, PASS-THROUGH) · DOC-422-FETCH-MODE-NOT-MANUAL (422, PASS-THROUGH) · DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE (422, PASS-THROUGH) · DOC-409-CHECK-ENDED (409, PASS-THROUGH — ADR-INT-025) · DOC-422-UPLOAD-LIMIT-REACHED (422, PASS-THROUGH — ADR-INT-025) · INT-500 (500)
Orchestration : size limit (container, INT-413) → parse → load the Check: `CheckRecordPort.read(checkId)` (PORTS; unknown → RPT-404-CHECK-NOT-FOUND) → RULE-INT-001 on DBF-INT-002 → `DocumentAccessPort.handOver` with serviceCode DBF-INT-003 and versionNumber DBF-INT-004 of the Check → 201 with the receipt. INT writes no column; the Uploaded Document is Document Access's record. The upload never confirms the uploads (REQ-INT-019)
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE of INT's own allocation. Read-then-act: the status read and the hand-over are not atomic. If the Check leaves AWAITING_DOCUMENTS in between (a confirmation commits, or the Check Engine ends the Check and sends Document Access its end-of-Check notice), Document Access refuses the handover synchronously: its CheckEndedException becomes DOC-409-CHECK-ENDED, which INT passes through (ADR-INT-025). Document Access does not accept the file and delete it later. A handover that passed DOC's check just before the notice committed is swept by Document Access at the next end of any Check (Document Access's own safety net — ADR-INT-025). INT upholds its side of the ordering rule (PF-6) by handing over only after reading the Check as AWAITING_DOCUMENTS (RULE-INT-001). Concurrent uploads for one Check may exceed DOC's upload limit by at most the number running at once (Document Access's counting rule — ADR-INT-025); INT adds no guard
Security     : none — no permission model (raw-idea A2)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-002
Covers       : REQ-INT-065 (each same-type upload is its own handover). REQ-INT-016, REQ-INT-017 and REQ-INT-053 are the frontend's use of this endpoint with the owners' reads of the service and of the uploaded documents (P3.2).
<!-- API:API-INT-002:END -->

<!-- API:API-INT-003:START traces=REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-053,DBF-INT-001,DBF-INT-002 -->
### API-INT-003 — Confirm the uploads
Entity       : the Report Store's Check Run (operation: custom — confirm that the uploads of a `manual` Check are complete)
Endpoint     : /api/v1/checks/{checkId}/upload-confirmation   verb: POST
Layers       : controller CheckUploadController.confirm → service UploadConfirmationService.confirm
Request      : path checkId (DBF-INT-001, integer int64, required); body (application/json) UploadConfirmationRequest {} — an empty object, no field
Response     : 202 · ConfirmedCheckResponse {checkId (DBF-INT-001, int64), status (DBF-INT-002 — RUNNING)} · not paginated · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-INT-017)
Errors       : INT-400-REQUEST-INVALID (400) · CHK-404-CHECK-NOT-FOUND (404, PASS-THROUGH) · CHK-409-CHECK-NOT-AWAITING-DOCUMENTS (409, PASS-THROUGH) · INT-500 (500)
Orchestration : parse → `CheckEnginePort.confirm(checkId)` (PORTS) → 202; the pipeline continues in the background inside the Check Engine. INT writes no column
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE in INT — the Check Engine's locking read serialises a confirmation against the upload-window deadline and a second confirmation (its own guard); the loser is refused CHK-409-CHECK-NOT-AWAITING-DOCUMENTS
Security     : none — no permission model (raw-idea A2)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-003
Covers       : REQ-INT-020 is the confirmation screen's use of the uploaded-documents read before this call (P3.2).
<!-- API:API-INT-003:END -->

<!-- API:API-INT-004:START traces=REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-029,REQ-INT-030,REQ-INT-031,REQ-INT-032,REQ-INT-033,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-039,REQ-INT-054,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-007,DBF-INT-008,DBF-INT-009,DBF-INT-010 -->
### API-INT-004 — Record an Employee Decision
Entity       : the Report Store's Check Run (operation: create — the Employee Decision recorded beside the Check's result)
Endpoint     : /api/v1/checks/{checkId}/decision   verb: POST
Layers       : controller DecisionController.decide → service DecisionService.decide
Request      : path checkId (DBF-INT-001, integer int64, required); body (application/json) DecisionRequest {employeeDecision (DBF-INT-007, string, `APPROVED` | `REJECTED`, required), decidedBy (DBF-INT-008, string ≤ 100, required — exactly as the host sent it)}; approvalApiExecuted is never accepted from the caller — INT sets it (REQ-INT-026)
Response     : 201 · RecordedDecisionResponse {checkId (int64), employeeDecision, decidedBy, decidedAt (date-time), approvalApiExecuted (boolean)} · not paginated · no envelope
Validations  : body readable (PLATFORM-STD) · RULE-INT-002 — A decision is complete before any Approval API call · trigger: on record decision (before the Approval API call) · statement: The system shall prevent calling the Approval API when the decision request carries no decision code of EMPLOYEE_DECISION or no deciding employee. · message (en): The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. · message (ar): PENDING ADR-INT-017 · RULE-INT-003 — Approval only on a completed, undecided Check · trigger: on record decision (before the Approval API call) · statement: The system shall prevent calling the Approval API when the Check's status is not COMPLETED or the Check already holds an Employee Decision. · message (en): Check {checkId} is not completed; a decision can only be recorded on a completed Check. / Check {checkId} already has an Employee Decision. · message (ar): PENDING ADR-INT-017
Errors       : INT-400-REQUEST-INVALID (400) · RPT-404-CHECK-NOT-FOUND (404, PASS-THROUGH) · RPT-400-DECISION-INCOMPLETE (400, RULE-INT-002 / PASS-THROUGH) · RPT-409-CHECK-NOT-COMPLETED (409, RULE-INT-003 / PASS-THROUGH) · RPT-409-DECISION-ALREADY-RECORDED (409, RULE-INT-003 / PASS-THROUGH) · RPT-422-APPROVAL-FLAG-ON-REJECTION (422, PASS-THROUGH) · INT-502-APPROVAL-API-FAILED (502) · INT-504-APPROVAL-API-TIMED-OUT (504) · INT-500 (500)
Orchestration : 
  1. parse (INT-400-REQUEST-INVALID); load the Check: `CheckRecordPort.read(checkId)` (unknown → RPT-404-CHECK-NOT-FOUND).
  2. When employeeDecision (DBF-INT-007) is `APPROVED`: integrate (XM-INT-001) — the approval definition of the Check's own version, by serviceCode DBF-INT-003 and versionNumber DBF-INT-004 (REQ-INT-030): enabled DBF-INT-009, method + path DBF-INT-010.
  3. Approval path — APPROVED and enabled (REQ-INT-025): take the per-Check lock (below); re-read the Check; RULE-INT-002 on the request (DBF-INT-007, DBF-INT-008) → RPT-400-DECISION-INCOMPLETE; RULE-INT-003 on DBF-INT-002 and DBF-INT-007 → RPT-409-CHECK-NOT-COMPLETED / RPT-409-DECISION-ALREADY-RECORDED — in every refusal the host receives no call (REQ-INT-034, REQ-INT-035). Then `HostApprovalPort.approve(definition, requestNumber DBF-INT-005, checkId, decidedBy)` once (REQ-INT-031 … REQ-INT-033): failure → INT-502-APPROVAL-API-FAILED, timeout → INT-504-APPROVAL-API-TIMED-OUT, nothing recorded and the employee may submit again as a new request (REQ-INT-036 … REQ-INT-038). Success → `CheckRecordPort.handOverDecision(checkId, APPROVED, decidedBy, approvalApiExecuted = true)` (REQ-INT-026). A refusal of the hand-over after a successful call → answer that refusal unchanged and log at WARN "Approval executed but decision not recorded: Check {checkId}, request {requestNumber}" (REQ-INT-039). Release the lock.
  4. Every other case — REJECTED (REQ-INT-027), a version that does not enable the Approval API (REQ-INT-028), or a code that is not APPROVED: no Approval API call; `CheckRecordPort.handOverDecision(checkId, employeeDecision, decidedBy, approvalApiExecuted = false)` (REQ-INT-021); the Report Store's refusals pass through unchanged (REQ-INT-006).
  5. 201 with the recorded decision (REQ-INT-022). INT writes no column: DBF-INT-007 and DBF-INT-008 are written by the Report Store from the hand-over. The Approval API is called from this step 3 only (REQ-INT-029).
Repository   : none (no QR — ADR-INT-017)
Concurrency  : guard — a per-Check-identifier in-process lock (`ConcurrentHashMap<Long, ReentrantLock>`, removed when released) held across step 3 from the re-read to the hand-over: two simultaneous APPROVED decisions on one approval-enabled Check cannot both call the host; the second waits, re-reads, finds the decision and is refused RPT-409-DECISION-ALREADY-RECORDED before any call. The Report Store's conditional update stays the final guard for every path (one decision per Check). A multi-instance deployment is not covered by the in-process lock (ADR-INT-017 (6))
Security     : none — no permission model (raw-idea A2); `HostApprovalPort` and `ApprovalDefinitionPort` are injected into `DecisionService` only (AIAS-4)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-004
Covers       : REQ-INT-023 (the frontend sends its launch identity as decidedBy) and REQ-INT-054 (the frontend offers the decision only on a COMPLETED, undecided Check) are the frontend's use of this endpoint (P3.2); REQ-INT-024: no other endpoint carries a decision.
<!-- API:API-INT-004:END -->
<!-- SUB:SVC-API-COMMAND:END -->

<!-- SUB:SVC-API-QUERY:START traces=REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-016,REQ-INT-017,REQ-INT-020,REQ-INT-040,REQ-INT-042,REQ-INT-043,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-057,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-064,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-006,DBF-INT-007,DBF-INT-008,REQ-INT-066 -->
### SVC-API-QUERY — the four frontend-facing reads (ADR-INT-020)

Each read relays one owner operation and keeps nothing (REQ-INT-059); no read touches another module's table, and no read writes.

<!-- API:API-INT-005:START traces=REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-057,REQ-INT-061,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-006,DBF-INT-007,DBF-INT-008,REQ-INT-066 -->
### API-INT-005 — Read a Check and its report
Entity       : the Report Store's Check Run (operation: read — a Check and its report)
Endpoint     : /api/v1/check-reports/{checkId}   verb: GET
Layers       : controller CheckReportController.read → service CheckReportService.read
Request      : path checkId (DBF-INT-001, integer int64, required); no query parameter; no body
Response     : 200 · CheckReportResponse {checkId (DBF-INT-001, int64), status (DBF-INT-002), serviceCode (DBF-INT-003), versionNumber (DBF-INT-004), fetchMode, requestNumber (DBF-INT-005), employeeId (DBF-INT-006), startedAt, runningSince (nullable), endedAt (nullable), overallStatus (nullable, COMPLETED only), comparisonModel (nullable), failureReason (nullable, FAILED only), failureDetail (nullable), findings [position, condition, outcome, evidence, note], documents [position, documentType, sourceMode, readStatus, unreadableReason (nullable), detail (nullable)], unreadQueries [position, queryName, detail], decision {employeeDecision (DBF-INT-007), decidedBy (DBF-INT-008), decidedAt, approvalApiExecuted} or null} — the Report Store's read-a-Check answer, unchanged, every text as stored · not paginated · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-INT-017)
Errors       : INT-400-REQUEST-INVALID (400) · RPT-404-CHECK-NOT-FOUND (404, PASS-THROUGH) · INT-500 (500)
Orchestration : parse → `CheckRecordPort.readReport(checkId)` (PORTS; unknown → RPT-404-CHECK-NOT-FOUND) → 200. Writes nothing; INT derives nothing (the Overall Status is the stored one — ADR-INT-011 (2))
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (raw-idea A2)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-005
<!-- API:API-INT-005:END -->

<!-- API:API-INT-006:START traces=REQ-INT-006,REQ-INT-008,REQ-INT-040,REQ-INT-042,REQ-INT-043,REQ-INT-057,REQ-INT-062,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-005,DBF-INT-007 -->
### API-INT-006 — List the Checks of a request
Entity       : the Report Store's Check Run (operation: list — the Checks of a request)
Endpoint     : /api/v1/check-reports   verb: GET
Layers       : controller CheckReportController.list → service CheckReportService.list
Request      : query serviceCode (DBF-INT-003, string ≤ 100, required), query requestNumber (DBF-INT-005, string ≤ 100, required) — passed exactly as received; presence is decided by the Report Store (RPT-400-REQUEST-KEYS-MISSING); no body
Response     : 200 · ChecksOfRequestResponse {total (integer), checks [checkId (DBF-INT-001), status (DBF-INT-002), overallStatus (nullable), startedAt, endedAt (nullable), employeeDecision (DBF-INT-007, nullable)] — at most 100, newest first} — the Report Store's list answer, unchanged · not paginated (bounded by the owner) · no envelope
Validations  : none of INT's own — the keys are checked by the Report Store (pass-through)
Errors       : RPT-400-REQUEST-KEYS-MISSING (400, PASS-THROUGH) · INT-500 (500)
Orchestration : `CheckRecordPort.listOfRequest(serviceCode, requestNumber)` (PORTS) → 200. Writes nothing
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model (raw-idea A2)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-006
<!-- API:API-INT-006:END -->

<!-- API:API-INT-007:START traces=REQ-INT-007,REQ-INT-008,REQ-INT-017,REQ-INT-020,REQ-INT-057,REQ-INT-063,DBF-INT-001 -->
### API-INT-007 — List the uploaded documents of a Check
Entity       : the Report Store's Check Run (operation: list — the uploaded documents of a Check)
Endpoint     : /api/v1/checks/{checkId}/documents   verb: GET
Layers       : controller CheckUploadController.list → service UploadedDocumentsService.list
Request      : path checkId (DBF-INT-001, integer int64, required); no body
Response     : 200 · array of UploadedDocumentResponse {uploadedDocumentId (int64), documentType (string ≤ 100), fileName (string ≤ 255), fileSize (int64), oversized (boolean), uploadedAt (date-time)} in upload order; empty when none; never the content · not paginated (one Check's uploads) · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-INT-017)
Errors       : INT-400-REQUEST-INVALID (400) · INT-500 (500)
Orchestration : parse → `DocumentAccessPort.listUploaded(checkId)` (PORTS — Document Access's in-process `listUploadedDocuments(checkId)`, ADR-INT-023) → 200 with the list as received (empty for a Check with no upload). Writes nothing
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model (raw-idea A2)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-007
<!-- API:API-INT-007:END -->

<!-- API:API-INT-008:START traces=REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-016,REQ-INT-020,REQ-INT-057,REQ-INT-064,DBF-INT-001,DBF-INT-003,DBF-INT-004 -->
### API-INT-008 — Read the required document types of a Check's version
Entity       : the Report Store's Check Run (operation: read — the required document types of the version the Check runs on)
Endpoint     : /api/v1/checks/{checkId}/required-document-types   verb: GET
Layers       : controller RequiredDocumentTypesController.read → service RequiredDocumentTypesService.read
Request      : path checkId (DBF-INT-001, integer int64, required); no body
Response     : 200 · RequiredDocumentTypesResponse {checkId (DBF-INT-001), serviceCode (DBF-INT-003), versionNumber (DBF-INT-004), requiredDocumentTypes [string ≤ 100] in the version's order} · not paginated · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-INT-017)
Errors       : INT-400-REQUEST-INVALID (400) · RPT-404-CHECK-NOT-FOUND (404, PASS-THROUGH) · INT-500 (500)
Orchestration : parse → load the Check: `CheckRecordPort.read(checkId)` (PORTS; unknown → RPT-404-CHECK-NOT-FOUND) → integrate (XM-INT-001) — the required document types of the version by serviceCode DBF-INT-003 and versionNumber DBF-INT-004 (REQ-INT-064) → 200. Writes nothing
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model (raw-idea A2)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-008
<!-- API:API-INT-008:END -->
<!-- SUB:SVC-API-QUERY:END -->

<!-- PHASE:SVC-API:END -->

<!-- PHASE:ALIGN-BE:START traces=REQ-INT-006,REQ-INT-029 -->
## PHASE ALIGN-BE — ALIGN-BE

```
ALIGN — INT v1
row               backing check        assertion
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF
COVERED           orphans              every REQ is covered by ≥1 API or DBF
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares for it
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not
QRC (§5)          orphans              every catalogued query is reached by ≥1 API
API (R3)          code-format          every catalog code is an instance of the declared format and carries a status the platform can emit
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-int.yaml and every operation one block, agreeing on method and path
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation of api-spec-int.yaml with its status and code
DOCUMENT VALID    api-spec-valid       api-spec-int.yaml validates against OPENAPI 3.1.0 and reaches every required item
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from, or is deferred
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase, and nothing else names its target
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's own registry
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why it is not
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/INT/
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves to something that exists
COVERAGE          (the report)         as stamped by the orchestrator from the analyze report
```
```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C7.19
- C7.20
- C7.22
- C7.23
- C7.24
- C7.28
```
R5 — Security (backend half): no permission model — endpoints are open per the SRS (caller authentication deferred, raw-idea A2; REQ-INT-004 states the identity is not checked against any directory).
<!-- PHASE:ALIGN-BE:END -->

<!-- PHASE:CROSS-MOD:START traces=REQ-INT-025,REQ-INT-028,REQ-INT-030,REQ-INT-064 -->
## PHASE CROSS-MOD — CROSS-MODULE

<!-- XM:XM-INT-001:START traces=REQ-INT-025,REQ-INT-028,REQ-INT-030,REQ-INT-064 -->
### XM-INT-001 — Approval definition and required document types of the Check's service package version
target     : REG · ENT-REG-002
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-002
requires   : REG:DELIVERED
do         :
  adapter   : `RegApprovalDefinitionAdapter implements ApprovalDefinitionPort` (INT port) wraps the injected REG in-process interface `ApprovalApiRegistry` — the separate interface REG gives only to the Employee Decision operation — and calls `getApprovalApi(serviceCode, versionNumber)` (CON-REG-012) with the service code and version number of the Check; maps ENT-REG-002.approvalEnabled (DBF-INT-009) to `ApprovalDefinition.enabled` and splits ENT-REG-002.approvalApi (DBF-INT-010, e.g. `POST /requests/{requestId}/approve`) at its first space into `method` and `pathTemplate`. REG's not-found for the Check's own version (CON-REG-002 promises it never disappears) → `INT-500`, logged with the Check identifier. Nothing is kept beyond the call; no FK. The adapter is injected only into `DecisionService` (AIAS-4) and never exposed to a model. Second adapter (ADR-INT-020): `RegVersionDocumentsAdapter implements VersionDocumentsPort` wraps the injected REG in-process interface `ServiceRegistry` and calls `getServicePackageVersion(serviceCode, versionNumber)` (CON-REG-009) with the Check's service code and version number; maps the version's `requiredDocumentTypes` (ENT-REG-004 values of ENT-REG-002) into an unmodifiable list in the version's order; REG's `VersionNotFoundException` for the Check's own version (CON-REG-002 promises it never disappears) → `INT-500`, logged with the Check identifier. Injected only into `RequiredDocumentTypesService`; it never receives the approval interface.
  config    : none — the REG interface is a Spring bean of the same deployable, injected by type.
tests      : AC-INT-029, AC-INT-032, AC-INT-034, AC-INT-073
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INT-001:END -->

<!-- PHASE:CROSS-MOD:END -->

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
