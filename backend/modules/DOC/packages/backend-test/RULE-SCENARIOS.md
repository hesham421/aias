<!-- source: PHASE:TEST-PLAN-BE / SUB:RULE-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-DOC-003, AC-DOC-007, AC-DOC-008, AC-DOC-009, AC-DOC-010, AC-DOC-011, AC-DOC-012, AC-DOC-013, AC-DOC-016, AC-DOC-017, AC-DOC-020, AC-DOC-022, AC-DOC-023, AC-DOC-024, AC-DOC-025, AC-DOC-030, AC-DOC-031, AC-DOC-038, AC-DOC-039, AC-DOC-041, AC-DOC-042, AC-DOC-043, AC-DOC-044, AC-DOC-045, AC-DOC-046, AC-DOC-047, AC-DOC-055, AC-DOC-059, AC-DOC-065, AC-DOC-067, AC-DOC-068, API-DOC-001, REQ-DOC-003, REQ-DOC-007, REQ-DOC-008, REQ-DOC-009, REQ-DOC-010, REQ-DOC-011, REQ-DOC-014, REQ-DOC-015, REQ-DOC-018, REQ-DOC-020, REQ-DOC-021, REQ-DOC-022, REQ-DOC-023, REQ-DOC-028, REQ-DOC-029, REQ-DOC-036, REQ-DOC-037, REQ-DOC-039, REQ-DOC-040, REQ-DOC-041, REQ-DOC-042, REQ-DOC-043, REQ-DOC-044, REQ-DOC-052, REQ-DOC-056, REQ-DOC-061, REQ-DOC-063, RULE-DOC-001, RULE-DOC-002, RULE-DOC-003, RULE-DOC-004, RULE-DOC-005, RULE-DOC-006, RULE-DOC-007, RULE-DOC-008, RULE-DOC-009, RULE-DOC-010 -->
<!-- SUB:RULE-SCENARIOS:START traces=AC-DOC-003,AC-DOC-007,AC-DOC-008,AC-DOC-009,AC-DOC-010,AC-DOC-011,AC-DOC-012,AC-DOC-013,AC-DOC-016,AC-DOC-017,AC-DOC-020,AC-DOC-022,AC-DOC-023,AC-DOC-024,AC-DOC-025,AC-DOC-030,AC-DOC-031,AC-DOC-038,AC-DOC-039,AC-DOC-041,AC-DOC-042,AC-DOC-043,AC-DOC-044,AC-DOC-045,AC-DOC-046,AC-DOC-047,AC-DOC-055,AC-DOC-059,REQ-DOC-003,REQ-DOC-007,REQ-DOC-008,REQ-DOC-009,REQ-DOC-010,REQ-DOC-011,REQ-DOC-014,REQ-DOC-015,REQ-DOC-018,REQ-DOC-020,REQ-DOC-021,REQ-DOC-022,REQ-DOC-023,REQ-DOC-028,REQ-DOC-029,REQ-DOC-036,REQ-DOC-037,REQ-DOC-039,REQ-DOC-040,REQ-DOC-041,REQ-DOC-042,REQ-DOC-043,REQ-DOC-044,REQ-DOC-052,REQ-DOC-056,AC-DOC-065,AC-DOC-067,AC-DOC-068,REQ-DOC-061,REQ-DOC-063 -->
### SUB RULE-SCENARIOS
<!-- TC:TC-DOC-001:START traces=AC-DOC-003,REQ-DOC-003 -->
### TC-DOC-001 — Unresolvable service package version refused before any document is fetched
Derived from : AC-DOC-003  (REQ-DOC-003)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : REQ-DOC-003 → DOC-404-SERVICE-VERSION-NOT-FOUND (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: REG holds service `scholarship-request` with no version 9.
Host data    : SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check naming `scholarship-request` version 9.
Expected     : `ServiceVersionNotFoundException` with code DOC-404-SERVICE-VERSION-NOT-FOUND; message en: "service package version not found" · ar: PENDING ADR-DOC-012. 0 files opened, 0 document source queries run (MCP query channel 0 calls, `jdbc` 0 sessions), 0 Uploaded Document reads.
Test data    : service `scholarship-request`, version number 9.
<!-- TC:TC-DOC-001:END -->
<!-- TC:TC-DOC-002:START traces=AC-DOC-007,REQ-DOC-007 -->
### TC-DOC-002 — `path` document with no file at its path reported NOT_FOUND
Derived from : AC-DOC-007  (REQ-DOC-007)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the query `attachments` returns for request 1001 the row doc_type = ID_CARD, file_path = `2026/1001/id.png`; no such file exists.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check of request 1001 on version 3.
Expected     : The outcome for ID_CARD has read status UNREADABLE, reason NOT_FOUND and a detail naming `2026/1001/id.png`.
Test data    : request 1001, path `2026/1001/id.png`.
<!-- TC:TC-DOC-002:END -->
<!-- TC:TC-DOC-003:START traces=AC-DOC-008,REQ-DOC-008 -->
### TC-DOC-003 — `..` segments resolved before the storage-root check
Derived from : AC-DOC-008  (REQ-DOC-008)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011); `HostFilePort` boundary observed
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : BOUNDARY · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the path column holds `2026/../2026/1001/transcript.pdf` and `/data/docs/2026/1001/transcript.pdf` exists.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Record the location the `HostFilePort` compares with the storage root before it opens the file.
Expected     : The location checked against the storage root equals `/data/docs/2026/1001/transcript.pdf`.
Test data    : storage root `/data/docs`, path `2026/../2026/1001/transcript.pdf`.
<!-- TC:TC-DOC-003:END -->
<!-- TC:TC-DOC-004:START traces=AC-DOC-009,REQ-DOC-009 -->
### TC-DOC-004 — Absolute path outside the storage root refused unopened
Derived from : AC-DOC-009  (REQ-DOC-009)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the path column holds `/srv/hr/salaries.pdf` (an existing file outside the root).
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Inspect the file-open calls of the `HostFilePort`.
Expected     : The outcome has read status UNREADABLE with reason OUTSIDE_STORAGE_ROOT; 0 open calls on `/srv/hr/salaries.pdf`.
Test data    : path `/srv/hr/salaries.pdf`.
<!-- TC:TC-DOC-004:END -->
<!-- TC:TC-DOC-005:START traces=AC-DOC-010,REQ-DOC-009 -->
### TC-DOC-005 — Relative traversal out of the storage root refused unopened
Derived from : AC-DOC-010  (REQ-DOC-009)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the path column holds `../../srv/hr/salaries.pdf`.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Inspect the file-open calls of the `HostFilePort`.
Expected     : The outcome has read status UNREADABLE with reason OUTSIDE_STORAGE_ROOT; 0 files opened.
Test data    : path `../../srv/hr/salaries.pdf`.
<!-- TC:TC-DOC-005:END -->
<!-- TC:TC-DOC-006:START traces=AC-DOC-011,REQ-DOC-009 -->
### TC-DOC-006 — Symbolic link pointing outside the storage root refused unopened
Derived from : AC-DOC-011  (REQ-DOC-009)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; `/data/docs/2026/link.pdf` is a symbolic link to `/srv/private/a.pdf`; the path column holds `2026/link.pdf`.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Inspect the file-open calls of the `HostFilePort`.
Expected     : The outcome has read status UNREADABLE with reason OUTSIDE_STORAGE_ROOT; 0 open calls on `/srv/private/a.pdf`.
Test data    : link `/data/docs/2026/link.pdf` → `/srv/private/a.pdf`.
<!-- TC:TC-DOC-006:END -->
<!-- TC:TC-DOC-007:START traces=AC-DOC-012,REQ-DOC-010 -->
### TC-DOC-007 — Storage root taken only from the environment setting
Derived from : AC-DOC-012  (REQ-DOC-010)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; a host row holds the path `/data/other/x.pdf` (an existing file).
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : The outcome has read status UNREADABLE with reason OUTSIDE_STORAGE_ROOT — only `/data/docs` is the storage root; `/data/other/x.pdf` is not opened.
Test data    : path `/data/other/x.pdf`.
<!-- TC:TC-DOC-007:END -->
<!-- TC:TC-DOC-008:START traces=AC-DOC-013,REQ-DOC-011 -->
### TC-DOC-008 — No storage root set closes every `path` document
Derived from : AC-DOC-013  (REQ-DOC-011)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; `aias.documents.storage-root` is not set; the host lists 2 `path` documents for the request.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Inspect the file-open calls of the `HostFilePort`.
Expected     : 2 outcomes with read status UNREADABLE and reason OUTSIDE_STORAGE_ROOT; 0 files opened.
Test data    : 2 listed `path` documents (placeholders `<path-1>`, `<path-2>`).
<!-- TC:TC-DOC-008:END -->
<!-- TC:TC-DOC-009:START traces=AC-DOC-016,REQ-DOC-014,RULE-DOC-006 -->
### TC-DOC-009 — `blob` query naming a non-jdbc connection refused without running
Derived from : AC-DOC-016  (REQ-DOC-014)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : RULE-DOC-006 → UNREADABLE / SOURCE_QUERY_FAILED outcome
Package      : PORTS-QUERY
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: A `blob` version of service `<blob-service>` requiring TRANSCRIPT and ID_CARD whose document source query names a connection `<mcp-connection>` of type `mcp`.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE <blob-service> (placeholder) — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check on that version. 2. Count the calls on the MCP query channel and the `jdbc` sessions opened.
Expected     : 2 outcomes with read status UNREADABLE and reason SOURCE_QUERY_FAILED; detail = RULE-DOC-006 message en: "The documents of "{serviceCode}" could not be fetched: connection "{connectionName}" is not a JDBC connection." with {serviceCode} = `<blob-service>`, {connectionName} = `<mcp-connection>` · ar: PENDING ADR-DOC-012; the query is not run (0 MCP calls, 0 `jdbc` sessions).
Test data    : service `<blob-service>` and connection `<mcp-connection>` are placeholders — the AC names neither.
<!-- TC:TC-DOC-009:END -->
<!-- TC:TC-DOC-010:START traces=AC-DOC-017,REQ-DOC-015 -->
### TC-DOC-010 — Empty BLOB content column reported NOT_FOUND
Derived from : AC-DOC-017  (REQ-DOC-015)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: A `blob` version of service `<blob-service>` requiring ID_CARD over the read-only `jdbc` connection `docs-jdbc`; the query returns the row doc_type = ID_CARD with a null content column.
Host data    : DOCUMENT_TYPE ID_CARD — present (required document types of the version) · SERVICE_CODE <blob-service> (placeholder) — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check on that version.
Expected     : The outcome for ID_CARD has read status UNREADABLE with reason NOT_FOUND.
Test data    : null content column; service `<blob-service>` is a placeholder.
<!-- TC:TC-DOC-010:END -->
<!-- TC:TC-DOC-011:START traces=AC-DOC-020,REQ-DOC-018,RULE-DOC-008 -->
### TC-DOC-011 — `manual` fetch reads only the Check's own uploads
Derived from : AC-DOC-020  (REQ-DOC-018)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : RULE-DOC-008 → internal log
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Uploaded Documents exist for Check 501 (TRANSCRIPT) and Check 502 (ID_CARD), both on `manual-service` version 1.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for Check 501.
Expected     : 1 outcome with document type TRANSCRIPT and source mode `manual`, plus 1 MISSING outcome for ID_CARD; Check 502's upload is not among the outcomes.
Test data    : Checks 501 and 502.
<!-- TC:TC-DOC-011:END -->
<!-- TC:TC-DOC-012:START traces=AC-DOC-022,REQ-DOC-020,API-DOC-001,RULE-DOC-001 -->
### TC-DOC-012 — Upload refused for a service whose fetch mode is not `manual`
Derived from : AC-DOC-022  (REQ-DOC-020)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-001 → DOC-422-FETCH-MODE-NOT-MANUAL (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: Check 600 runs `scholarship-request` version 3 (service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD).
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 600 with `id.png` and document type ID_CARD. 2. Call GET /api/v1/uploaded-documents?checkId=600.
Expected     : `FetchModeNotManualException` with code DOC-422-FETCH-MODE-NOT-MANUAL; message en: "Documents can be uploaded only for a service whose documents are provided by the employee; the service "{serviceCode}" obtains its documents by "{fetchMode}"." with {serviceCode} = `scholarship-request`, {fetchMode} = `path` · ar: PENDING ADR-DOC-012. The GET returns 200 with an empty array (0 Uploaded Documents created).
Test data    : Check 600, file `id.png`.
<!-- TC:TC-DOC-012:END -->
<!-- TC:TC-DOC-013:START traces=AC-DOC-023,REQ-DOC-021,API-DOC-001,RULE-DOC-002 -->
### TC-DOC-013 — Upload refused for a document type outside the version's list
Derived from : AC-DOC-023  (REQ-DOC-021)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-002 → DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 runs it.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with a file and document type PASSPORT. 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : `DocumentTypeNotOfServiceException` with code DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE; message en: ""{documentType}" is not a document type of the service "{serviceCode}"; choose one of: {requiredDocumentTypes}." with {documentType} = `PASSPORT`, {serviceCode} = `manual-service`, {requiredDocumentTypes} = the version's TRANSCRIPT and ID_CARD · ar: PENDING ADR-DOC-012. The GET returns 200 with an empty array.
Test data    : document type PASSPORT is not a required type of the version (named by the AC); file placeholder `<file>`.
<!-- TC:TC-DOC-013:END -->
<!-- TC:TC-DOC-014:START traces=AC-DOC-024,REQ-DOC-022,API-DOC-001,RULE-DOC-003 -->
### TC-DOC-014 — Upload of a 0-byte file refused
Derived from : AC-DOC-024  (REQ-DOC-022)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-003 → DOC-400-INCOMPLETE-UPLOAD (in-process)
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 runs it.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with an empty file of 0 bytes and document type TRANSCRIPT. 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : `IncompleteUploadException` with code DOC-400-INCOMPLETE-UPLOAD; message en: "The upload needs a Check, a document type and a file that is not empty." · ar: PENDING ADR-DOC-012. The GET returns 200 with an empty array.
Test data    : file of 0 bytes.
<!-- TC:TC-DOC-014:END -->
<!-- TC:TC-DOC-015:START traces=AC-DOC-025,REQ-DOC-023,API-DOC-001,RULE-DOC-004 -->
### TC-DOC-015 — A second upload never changes the first
Derived from : AC-DOC-025  (REQ-DOC-023)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-004 → — (no update path exists)
Package      : SVC-API
Scenario     : STATE · data class VALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; an Uploaded Document `transcript.pdf` of type TRANSCRIPT exists for Check 501.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with `transcript-v2.pdf` and document type TRANSCRIPT. 2. Call GET /api/v1/uploaded-documents?checkId=501. 3. Call fetchDocuments for Check 501 and compare the first upload's content with the original file.
Expected     : The GET returns 2 items in upload order; the first has fileName `transcript.pdf` and its original fileSize; the first upload's content equals the original bytes of `transcript.pdf`.
Test data    : files `transcript.pdf`, `transcript-v2.pdf`.
<!-- TC:TC-DOC-015:END -->
<!-- TC:TC-DOC-016:START traces=AC-DOC-030,REQ-DOC-028 -->
### TC-DOC-016 — Unsupported format reported UNSUPPORTED_FORMAT
Derived from : AC-DOC-030  (REQ-DOC-028)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; a listed document inside the root whose content is a `.docx` file.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : The outcome has read status UNREADABLE with reason UNSUPPORTED_FORMAT.
Test data    : a `.docx` file (placeholder `<file>.docx`).
<!-- TC:TC-DOC-016:END -->
<!-- TC:TC-DOC-017:START traces=AC-DOC-031,REQ-DOC-029 -->
### TC-DOC-017 — Password-protected PDF reported READING_FAILED
Derived from : AC-DOC-031  (REQ-DOC-029)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; a password-protected PDF inside the root is listed as TRANSCRIPT.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : The outcome for TRANSCRIPT has read status UNREADABLE with reason READING_FAILED.
Test data    : a password-protected PDF.
<!-- TC:TC-DOC-017:END -->
<!-- TC:TC-DOC-018:START traces=AC-DOC-038,REQ-DOC-036 -->
### TC-DOC-018 — TOO_LARGE outcome names the file size and the maximum file size
Derived from : AC-DOC-038  (REQ-DOC-036)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; `aias.check.max-file-size` = `<max>` (set low, RULE-DOC-005 test hint); a listed document inside the root of `<max>` + 1 byte.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : Its outcome has read status UNREADABLE, reason TOO_LARGE and a detail text containing the file size and the maximum file size.
Test data    : `<max>` is a placeholder for the configured limit.
<!-- TC:TC-DOC-018:END -->
<!-- TC:TC-DOC-019:START traces=AC-DOC-039,REQ-DOC-037 -->
### TC-DOC-019 — One document outside the storage root does not stop the others
Derived from : AC-DOC-039  (REQ-DOC-037)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the host lists 3 documents: the first at `/srv/hr/salaries.pdf` (outside the root), the second and third readable text PDFs inside the root.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : 3 outcomes: the first UNREADABLE / OUTSIDE_STORAGE_ROOT, the second and third READ.
Test data    : 3 listed documents (placeholders for the two readable PDFs).
<!-- TC:TC-DOC-019:END -->
<!-- TC:TC-DOC-020:START traces=AC-DOC-041,REQ-DOC-039 -->
### TC-DOC-020 — Document source query over the maximum rows fails every required type
Derived from : AC-DOC-041  (REQ-DOC-039)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; `aias.check.max-rows` = 100; the document source query returns 101 rows.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Inspect the file-open calls of the `HostFilePort`.
Expected     : 2 outcomes with read status UNREADABLE and reason SOURCE_QUERY_FAILED (TRANSCRIPT, ID_CARD); 0 files opened.
Test data    : maximum rows 100; 101 returned rows.
<!-- TC:TC-DOC-020:END -->
<!-- TC:TC-DOC-021:START traces=AC-DOC-041,REQ-DOC-039 -->
### TC-DOC-021 — Document source query at exactly the maximum rows is accepted (boundary)
Derived from : AC-DOC-041  (REQ-DOC-039)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; `aias.check.max-rows` = 100; the document source query returns exactly 100 rows, each with a doc_type and a path inside the root.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : 0 outcomes with reason SOURCE_QUERY_FAILED; 100 document outcomes are returned (one per row), as REQ-DOC-039 fails only more rows than the maximum.
Test data    : maximum rows 100; 100 returned rows (boundary twin of AC-DOC-041 — ADR-DOC-014).
<!-- TC:TC-DOC-021:END -->
<!-- TC:TC-DOC-022:START traces=AC-DOC-042,REQ-DOC-039 -->
### TC-DOC-022 — MCP error on the document source query fails every required type
Derived from : AC-DOC-042  (REQ-DOC-039)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; the MCP query channel answers the document source query with an error `<mcp-error>`.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : 2 outcomes with read status UNREADABLE and reason SOURCE_QUERY_FAILED, each detail text containing `<mcp-error>`.
Test data    : `<mcp-error>` is a placeholder for the channel's error text.
<!-- TC:TC-DOC-022:END -->
<!-- TC:TC-DOC-023:START traces=AC-DOC-043,REQ-DOC-040 -->
### TC-DOC-023 — Check timeout reached during reading marks unread documents OUT_OF_TIME
Derived from : AC-DOC-043  (REQ-DOC-040)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011); the document-reading model port (`DocumentReadingModelPort`) observed
Rule / code  : —
Package      : SVC-API
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: A Check with 2 image documents; environment data class SYNTHETIC and synthetic documents only (US-DOC-013); the document-reading model is stubbed to answer after the Check's deadline.
Host data    : none
Steps        : 1. Call fetchDocuments with a deadline that passes while the first document is in the document-reading step.
Expected     : Both outcomes have read status UNREADABLE with reason OUT_OF_TIME.
Test data    : 2 synthetic images; deadline placeholder `<deadline>`.
<!-- TC:TC-DOC-023:END -->
<!-- TC:TC-DOC-024:START traces=AC-DOC-044,REQ-DOC-041 -->
### TC-DOC-024 — Oversized BLOB measured before any byte is read
Derived from : AC-DOC-044  (REQ-DOC-041)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `aias.check.max-file-size` = 10 MB; a `blob` version of service `<blob-service>` over the read-only `jdbc` connection `docs-jdbc`; a row holds a 25 MB content column.
Host data    : SERVICE_CODE <blob-service> (placeholder) — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Count the content bytes streamed from the column.
Expected     : The size 25 MB is recorded in the outcome detail; 0 bytes of the content are read.
Test data    : maximum file size 10 MB; content 25 MB.
<!-- TC:TC-DOC-024:END -->
<!-- TC:TC-DOC-025:START traces=AC-DOC-045,REQ-DOC-042 -->
### TC-DOC-025 — Oversized `path` document reported TOO_LARGE
Derived from : AC-DOC-045  (REQ-DOC-042)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; `aias.check.max-file-size` = 10 MB; a `path` document of 12 MB inside the storage root.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : Its outcome has read status UNREADABLE with reason TOO_LARGE.
Test data    : maximum file size 10 MB; file 12 MB.
<!-- TC:TC-DOC-025:END -->
<!-- TC:TC-DOC-026:START traces=AC-DOC-045,REQ-DOC-042 -->
### TC-DOC-026 — `path` document of exactly the maximum file size is read (boundary)
Derived from : AC-DOC-045  (REQ-DOC-042)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; `aias.check.max-file-size` = 10 MB; a readable text PDF of exactly 10 MB inside the storage root.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : Its outcome is not TOO_LARGE: read status READ, as REQ-DOC-042 refuses only a document larger than the maximum.
Test data    : maximum file size 10 MB; file exactly 10 MB (boundary twin of AC-DOC-045 — ADR-DOC-014).
<!-- TC:TC-DOC-026:END -->
<!-- TC:TC-DOC-027:START traces=AC-DOC-046,REQ-DOC-043,API-DOC-001,RULE-DOC-005 -->
### TC-DOC-027 — Oversized upload kept without content, with the RULE-DOC-005 notice
Derived from : AC-DOC-046  (REQ-DOC-043)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-005 → notice (not an error)
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `aias.check.max-file-size` = 10 MB; service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 runs it.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with a 15 MB file `<file>` of type TRANSCRIPT. 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : 1 Uploaded Document created with oversized = true and no content; the receipt's notice = RULE-DOC-005 message en: "The file "{fileName}" is larger than the maximum file size of {maxFileSize}; it will not be read and will be reported as unreadable." with {fileName} = `<file>`, {maxFileSize} = 10 MB · ar: PENDING ADR-DOC-012. The GET returns 1 item with oversized = true and no content member.
Test data    : maximum file size 10 MB; upload 15 MB; file name placeholder `<file>`.
<!-- TC:TC-DOC-027:END -->
<!-- TC:TC-DOC-028:START traces=AC-DOC-046,REQ-DOC-043,API-DOC-001,RULE-DOC-005 -->
### TC-DOC-028 — Upload of exactly the maximum file size keeps its content (boundary)
Derived from : AC-DOC-046  (REQ-DOC-043)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-005 → no notice
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: `aias.check.max-file-size` = 10 MB; service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 runs it.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with a file `<file>` of exactly 10 MB of type TRANSCRIPT. 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : 1 Uploaded Document created with oversized = false and its content kept; the receipt carries no notice. The GET returns 1 item with oversized = false.
Test data    : maximum file size 10 MB; upload exactly 10 MB (boundary twin of AC-DOC-046 — ADR-DOC-014).
<!-- TC:TC-DOC-028:END -->
<!-- TC:TC-DOC-029:START traces=AC-DOC-047,REQ-DOC-044,RULE-DOC-005 -->
### TC-DOC-029 — Oversized upload reported TOO_LARGE at fetch
Derived from : AC-DOC-047  (REQ-DOC-044)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : RULE-DOC-005 → UNREADABLE / TOO_LARGE outcome
Package      : SVC-API
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 has 1 Uploaded Document of type TRANSCRIPT marked oversized.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for Check 501.
Expected     : The outcome for TRANSCRIPT has read status UNREADABLE with reason TOO_LARGE.
Test data    : Check 501.
<!-- TC:TC-DOC-029:END -->
<!-- TC:TC-DOC-030:START traces=AC-DOC-055,REQ-DOC-052,RULE-DOC-007 -->
### TC-DOC-030 — Connection not declared read-only refused without running the query
Derived from : AC-DOC-055  (REQ-DOC-052)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : RULE-DOC-007 → UNREADABLE / SOURCE_QUERY_FAILED outcome
Package      : PORTS-QUERY
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: A `blob` version of service `<blob-service>` requiring TRANSCRIPT whose `jdbc` connection `<jdbc-connection>` is declared readOnly = false.
Host data    : DOCUMENT_TYPE TRANSCRIPT — present (required document types of the version) · SERVICE_CODE <blob-service> (placeholder) — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check on that version. 2. Count the `jdbc` sessions opened.
Expected     : 1 outcome with read status UNREADABLE and reason SOURCE_QUERY_FAILED; detail = RULE-DOC-007 message en: "The documents of "{serviceCode}" could not be fetched: connection "{connectionName}" is not declared read-only." with {serviceCode} = `<blob-service>`, {connectionName} = `<jdbc-connection>` · ar: PENDING ADR-DOC-012; the query is not run (0 sessions).
Test data    : service and connection names are placeholders — the AC names neither.
<!-- TC:TC-DOC-030:END -->
<!-- TC:TC-DOC-031:START traces=AC-DOC-059,REQ-DOC-056,RULE-DOC-008 -->
### TC-DOC-031 — Another Check's upload never supplied
Derived from : AC-DOC-059  (REQ-DOC-056)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : RULE-DOC-008 → internal log
Package      : SVC-API
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Checks 501 and 502 run it; the only Uploaded Document of type ID_CARD carries checkId = 502.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for Check 501.
Expected     : The outcome for ID_CARD is MISSING and the content of Check 502's document is not returned in any outcome.
Test data    : Checks 501 and 502.
<!-- TC:TC-DOC-031:END -->
<!-- TC:TC-DOC-070:START traces=AC-DOC-065,REQ-DOC-061,API-DOC-001,RULE-DOC-009 -->
### TC-DOC-070 — Upload refused for a Check already ended
Derived from : AC-DOC-065  (REQ-DOC-061)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-009 → DOC-409-CHECK-ENDED (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 runs it and has 0 Uploaded Documents; endCheck(501) has been called once, so Check 501 is recorded as an Ended Check (ADR-DOC-015).
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with `transcript.pdf` (81920 bytes) and document type TRANSCRIPT. 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : `CheckEndedException` with code DOC-409-CHECK-ENDED; message en: "The Check {checkId} has already ended; documents can no longer be uploaded for it. Start a new check to provide these documents." with {checkId} = 501 · ar: PENDING ADR-DOC-012. The GET returns 200 with an empty array (0 Uploaded Documents exist for Check 501).
Test data    : Check 501, file `transcript.pdf` 81920 bytes.
<!-- TC:TC-DOC-070:END -->
<!-- TC:TC-DOC-071:START traces=AC-DOC-067,REQ-DOC-063,API-DOC-001,RULE-DOC-010 -->
### TC-DOC-071 — Upload refused once the Check holds the maximum uploads
Derived from : AC-DOC-067  (REQ-DOC-063)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-010 → DOC-422-UPLOAD-LIMIT-REACHED (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class EDGE · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; platform configuration `aias.check.max-uploads` = 20; Check 501 already has 20 Uploaded Documents, created by 20 handOverUpload calls of 1 KB `t01.pdf` … `t20.pdf` with document type TRANSCRIPT.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with `id.png` (1 KB) and document type ID_CARD. 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : `UploadLimitReachedException` with code DOC-422-UPLOAD-LIMIT-REACHED; message en: "The Check {checkId} already has the maximum of {maxUploads} uploaded documents; no further file can be uploaded for it." with {checkId} = 501, {maxUploads} = 20 · ar: PENDING ADR-DOC-012. The GET returns 200 with 20 items, none named `id.png`.
Test data    : Check 501, 20 existing uploads, file `id.png`.
<!-- TC:TC-DOC-071:END -->
<!-- TC:TC-DOC-072:START traces=AC-DOC-068,REQ-DOC-063,API-DOC-001,RULE-DOC-010 -->
### TC-DOC-072 — Upload accepted one below the maximum uploads (boundary)
Derived from : AC-DOC-068  (REQ-DOC-063)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : RULE-DOC-010 → not raised
Package      : SVC-API
Scenario     : BOUNDARY · data class EDGE · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; platform configuration `aias.check.max-uploads` = 20; Check 501 already has 19 Uploaded Documents, created by 19 handOverUpload calls of 1 KB `t01.pdf` … `t19.pdf` with document type TRANSCRIPT.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with `id.png` (1 KB) and document type ID_CARD. 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : handOverUpload returns an UploadReceipt with documentType ID_CARD, fileName `id.png`, oversized = false and no notice. The GET returns 200 with 20 items, the last named `id.png`.
Test data    : Check 501, 19 existing uploads, file `id.png`.
<!-- TC:TC-DOC-072:END -->
<!-- SUB:RULE-SCENARIOS:END -->
