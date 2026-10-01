<!-- source: PHASE:TEST-PLAN-BE / SUB:API-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-DOC-001, AC-DOC-002, AC-DOC-004, AC-DOC-005, AC-DOC-006, AC-DOC-014, AC-DOC-015, AC-DOC-018, AC-DOC-019, AC-DOC-021, AC-DOC-027, AC-DOC-028, AC-DOC-036, AC-DOC-037, AC-DOC-040, AC-DOC-048, AC-DOC-052, AC-DOC-053, AC-DOC-054, AC-DOC-056, AC-DOC-057, AC-DOC-058, AC-DOC-063, AC-DOC-064, AC-DOC-066, AC-DOC-069, AC-DOC-070, API-DOC-001, REQ-DOC-001, REQ-DOC-002, REQ-DOC-004, REQ-DOC-005, REQ-DOC-006, REQ-DOC-012, REQ-DOC-013, REQ-DOC-016, REQ-DOC-017, REQ-DOC-019, REQ-DOC-025, REQ-DOC-026, REQ-DOC-034, REQ-DOC-035, REQ-DOC-038, REQ-DOC-045, REQ-DOC-049, REQ-DOC-050, REQ-DOC-051, REQ-DOC-053, REQ-DOC-054, REQ-DOC-055, REQ-DOC-060, REQ-DOC-062, REQ-DOC-064 -->
<!-- SUB:API-SCENARIOS:START traces=AC-DOC-001,AC-DOC-002,AC-DOC-004,AC-DOC-005,AC-DOC-006,AC-DOC-014,AC-DOC-015,AC-DOC-018,AC-DOC-019,AC-DOC-021,AC-DOC-027,AC-DOC-028,AC-DOC-036,AC-DOC-037,AC-DOC-040,AC-DOC-048,AC-DOC-052,AC-DOC-053,AC-DOC-054,AC-DOC-056,AC-DOC-057,AC-DOC-058,REQ-DOC-001,REQ-DOC-002,REQ-DOC-004,REQ-DOC-005,REQ-DOC-006,REQ-DOC-012,REQ-DOC-013,REQ-DOC-016,REQ-DOC-017,REQ-DOC-019,REQ-DOC-025,REQ-DOC-026,REQ-DOC-034,REQ-DOC-035,REQ-DOC-038,REQ-DOC-045,REQ-DOC-049,REQ-DOC-050,REQ-DOC-051,REQ-DOC-053,REQ-DOC-054,REQ-DOC-055,AC-DOC-063,AC-DOC-064,AC-DOC-066,AC-DOC-069,AC-DOC-070,REQ-DOC-060,REQ-DOC-062,REQ-DOC-064 -->
### SUB API-SCENARIOS
<!-- TC:TC-DOC-032:START traces=AC-DOC-001,REQ-DOC-001 -->
### TC-DOC-032 — Documents obtained only by the version's fetch mode (`path`)
Derived from : AC-DOC-001  (REQ-DOC-001)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the host lists 2 readable documents inside the root for request 1001.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check of request 1001 on version 3. 2. Inspect the Uploaded Document reads and the `jdbc` sessions.
Expected     : 2 document outcomes, each with source mode `path`; 0 Uploaded Document reads; 0 BLOB columns read (0 `jdbc` sessions).
Test data    : request 1001; 2 listed documents (placeholders).
<!-- TC:TC-DOC-032:END -->
<!-- TC:TC-DOC-033:START traces=AC-DOC-002,REQ-DOC-002 -->
### TC-DOC-033 — Document settings read from the version the Check names
Derived from : AC-DOC-002  (REQ-DOC-002)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the host lists one readable document of each required type.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check naming `scholarship-request` version 3. 2. Inspect the REG interface calls.
Expected     : The REG interface is asked for version 3 of `scholarship-request`; outcomes are returned for document types TRANSCRIPT and ID_CARD.
Test data    : service `scholarship-request`, version 3.
<!-- TC:TC-DOC-033:END -->
<!-- TC:TC-DOC-034:START traces=AC-DOC-004,REQ-DOC-004 -->
### TC-DOC-034 — `path` document source query sent once through the MCP channel with the request number bound
Derived from : AC-DOC-004  (REQ-DOC-004)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check of request 1001. 2. Capture the calls on the MCP query channel.
Expected     : The query `attachments` is sent once through the MCP query channel with `requestId` = 1001 bound and its SQL text equal to the stored text.
Test data    : request 1001; query `attachments`.
<!-- TC:TC-DOC-034:END -->
<!-- TC:TC-DOC-035:START traces=AC-DOC-005,REQ-DOC-005 -->
### TC-DOC-035 — `path` document read at the returned path
Derived from : AC-DOC-005  (REQ-DOC-005)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the query `attachments` returns the row doc_type = TRANSCRIPT, file_path = `2026/1001/transcript.pdf` and that text PDF exists inside the root.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : The outcome for TRANSCRIPT has read status READ and its content equals the text of `2026/1001/transcript.pdf`.
Test data    : path `2026/1001/transcript.pdf` (synthetic text PDF).
<!-- TC:TC-DOC-035:END -->
<!-- TC:TC-DOC-036:START traces=AC-DOC-006,REQ-DOC-006 -->
### TC-DOC-036 — Document type taken from the type column
Derived from : AC-DOC-006  (REQ-DOC-006)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the query returns 2 rows with doc_type ID_CARD and TRANSCRIPT, each with a readable file.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : 2 outcomes are returned, with document types ID_CARD and TRANSCRIPT.
Test data    : 2 rows.
<!-- TC:TC-DOC-036:END -->
<!-- TC:TC-DOC-037:START traces=AC-DOC-014,REQ-DOC-012 -->
### TC-DOC-037 — `blob` document source query run over the read-only jdbc connection, not MCP
Derived from : AC-DOC-014  (REQ-DOC-012)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: A `blob` version of service `<blob-service>` whose document source query names the connection `docs-jdbc` of type `jdbc`, declared read-only.
Host data    : SERVICE_CODE <blob-service> (placeholder) — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check of request 1002. 2. Capture the `jdbc` statements and the MCP query channel calls.
Expected     : The query is run once over `docs-jdbc` with the request number 1002 bound; the MCP query channel receives 0 calls for it.
Test data    : request 1002; connection `docs-jdbc`; service placeholder `<blob-service>`.
<!-- TC:TC-DOC-037:END -->
<!-- TC:TC-DOC-038:START traces=AC-DOC-015,REQ-DOC-013 -->
### TC-DOC-038 — `blob` content read from the content column
Derived from : AC-DOC-015  (REQ-DOC-013)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: A `blob` version of service `<blob-service>` requiring TRANSCRIPT over `docs-jdbc`; the query returns the row doc_type = TRANSCRIPT with a text PDF of 120 KB in the content column.
Host data    : DOCUMENT_TYPE TRANSCRIPT — present (required document types of the version) · SERVICE_CODE <blob-service> (placeholder) — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : The outcome for TRANSCRIPT has read status READ and source mode `blob`.
Test data    : PDF of 120 KB (synthetic).
<!-- TC:TC-DOC-038:END -->
<!-- TC:TC-DOC-039:START traces=AC-DOC-018,REQ-DOC-016 -->
### TC-DOC-039 — BLOB content never through the MCP query channel
Derived from : AC-DOC-018  (REQ-DOC-016)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: A `blob` version of service `<blob-service>` and service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD, each with 1 listed document; environment setting `aias.documents.storage-root` = `/data/docs`.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for one Check of each version. 2. Capture every call on the MCP query channel.
Expected     : The MCP query channel receives 1 call in total (the `path` document source query) and no call carries a content column.
Test data    : 1 listed document per version.
<!-- TC:TC-DOC-039:END -->
<!-- TC:TC-DOC-040:START traces=AC-DOC-019,REQ-DOC-017,API-DOC-001 -->
### TC-DOC-040 — Upload handed over by INT creates an Uploaded Document
Derived from : AC-DOC-019  (REQ-DOC-017)
Exercises    : in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003); stored rows read back through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 runs it.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call handOverUpload for Check 501 with `transcript.pdf` (80 KB) and document type TRANSCRIPT. 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : 1 Uploaded Document created with checkId = 501, documentType = TRANSCRIPT, fileName = `transcript.pdf`, fileSize = 81920 and oversized = false; the GET returns 200 with exactly that 1 item.
Test data    : file `transcript.pdf`, 81920 bytes.
<!-- TC:TC-DOC-040:END -->
<!-- TC:TC-DOC-041:START traces=AC-DOC-021,REQ-DOC-019 -->
### TC-DOC-041 — `manual` mode touches no host document
Derived from : AC-DOC-021  (REQ-DOC-019)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check on `manual-service` version 1. 2. Inspect the MCP query channel, the `HostFilePort` and the `jdbc` sessions.
Expected     : 0 document source queries run, 0 host files opened, 0 `jdbc` connections used.
Test data    : a Check on `manual-service` version 1 (placeholder checkId `<checkId>`).
<!-- TC:TC-DOC-041:END -->
<!-- TC:TC-DOC-042:START traces=AC-DOC-027,REQ-DOC-025 -->
### TC-DOC-042 — PDF with a text layer read by text extraction
Derived from : AC-DOC-027  (REQ-DOC-025)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011); the document-reading model port (`DocumentReadingModelPort`) observed
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; a TRANSCRIPT PDF whose text layer contains "GPA 3.6".
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Count the document-reading model calls.
Expected     : The outcome has read status READ and its content contains "GPA 3.6"; the document-reading model receives 0 calls.
Test data    : synthetic text PDF with "GPA 3.6".
<!-- TC:TC-DOC-042:END -->
<!-- TC:TC-DOC-043:START traces=AC-DOC-028,REQ-DOC-026 -->
### TC-DOC-043 — `.xlsx` read by table extraction
Derived from : AC-DOC-028  (REQ-DOC-026)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; a listed `.xlsx` document with 1 sheet of 3 rows and 2 columns.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : The outcome has read status READ and its content holds 1 table of 3 rows and 2 columns with the cell values.
Test data    : synthetic `.xlsx` (1 sheet, 3 × 2 cells).
<!-- TC:TC-DOC-043:END -->
<!-- TC:TC-DOC-044:START traces=AC-DOC-036,REQ-DOC-034 -->
### TC-DOC-044 — Exactly one outcome per document
Derived from : AC-DOC-036  (REQ-DOC-034)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the host lists 3 documents: 1 readable PDF, 1 path with no file, 1 `.docx`.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : 3 outcomes: 1 READ, 1 UNREADABLE with reason NOT_FOUND and 1 UNREADABLE with reason UNSUPPORTED_FORMAT.
Test data    : 3 listed documents.
<!-- TC:TC-DOC-044:END -->
<!-- TC:TC-DOC-045:START traces=AC-DOC-037,REQ-DOC-035 -->
### TC-DOC-045 — MISSING outcome for a required type with no document
Derived from : AC-DOC-037  (REQ-DOC-035)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the host lists only a TRANSCRIPT for the request.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : The outcomes include 1 MISSING outcome with document type ID_CARD.
Test data    : 1 listed TRANSCRIPT.
<!-- TC:TC-DOC-045:END -->
<!-- TC:TC-DOC-046:START traces=AC-DOC-040,REQ-DOC-038 -->
### TC-DOC-046 — Read content handed to the Check Engine with its outcome
Derived from : AC-DOC-040  (REQ-DOC-038)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; a TRANSCRIPT PDF fetched in `path` mode whose text contains "GPA 3.6".
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check.
Expected     : 1 outcome with documentType = TRANSCRIPT, source mode `path`, read status READ and content containing "GPA 3.6".
Test data    : synthetic text PDF.
<!-- TC:TC-DOC-046:END -->
<!-- TC:TC-DOC-047:START traces=AC-DOC-048,REQ-DOC-045 -->
### TC-DOC-047 — Content handed over only as data, with no instruction field
Derived from : AC-DOC-048  (REQ-DOC-045)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-MODEL
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; a readable TRANSCRIPT text PDF.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Inspect the members of the returned `DocumentOutcome`.
Expected     : The content is carried in the outcome's content field; the outcome has exactly the members documentType, sourceMode, readStatus, reason, detail, content — no instruction field.
Test data    : synthetic text PDF.
<!-- TC:TC-DOC-047:END -->
<!-- TC:TC-DOC-048:START traces=AC-DOC-052,REQ-DOC-049 -->
### TC-DOC-048 — Host file opened for reading only
Derived from : AC-DOC-052  (REQ-DOC-049)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; a `path` document inside the root; its modification time `<mtime>` is recorded.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for the Check. 2. Capture the open options and read the file's modification time.
Expected     : The file is opened in read mode only; its modification time equals `<mtime>`.
Test data    : `<mtime>` recorded before the fetch.
<!-- TC:TC-DOC-048:END -->
<!-- TC:TC-DOC-049:START traces=AC-DOC-053,REQ-DOC-050 -->
### TC-DOC-049 — Host documents unchanged after a Check
Derived from : AC-DOC-053  (REQ-DOC-050)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011); then the Check ends
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the root contains 2 files for request 1001; their names, sizes and modification times are recorded.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check of request 1001. 2. End the Check (`endCheck`). 3. List the storage root.
Expected     : The storage root contains the same 2 files with the same names, sizes and modification times.
Test data    : 2 files for request 1001.
<!-- TC:TC-DOC-049:END -->
<!-- TC:TC-DOC-050:START traces=AC-DOC-054,REQ-DOC-051 -->
### TC-DOC-050 — Document source query sent exactly as written; request number bound, never concatenated
Derived from : AC-DOC-054  (REQ-DOC-051)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : HAPPY · data class ATTACK · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments with the request number `1001' OR '1'='1`. 2. Capture the call on the MCP query channel.
Expected     : The SQL text sent equals the stored query text; the request number is sent as 1 bound value.
Test data    : request number `1001' OR '1'='1`.
<!-- TC:TC-DOC-050:END -->
<!-- TC:TC-DOC-051:START traces=AC-DOC-056,REQ-DOC-053 -->
### TC-DOC-051 — No host endpoint called, the Approval API included
Derived from : AC-DOC-056  (REQ-DOC-053)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD whose Approval API is enabled; environment setting `aias.documents.storage-root` = `/data/docs`; an HTTP recorder on the host.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check of that service. 2. Count the HTTP calls DOC makes to the host.
Expected     : 0 HTTP calls to the host are made by DOC.
Test data    : Approval API enabled on the version (placeholder endpoint `<approval-api>`).
<!-- TC:TC-DOC-051:END -->
<!-- TC:TC-DOC-052:START traces=AC-DOC-057,REQ-DOC-054,API-DOC-001 -->
### TC-DOC-052 — Uploads deleted when their Check ends
Derived from : AC-DOC-057  (REQ-DOC-054)
Exercises    : in-process `DocumentAccess.endCheck(checkId)` (CON-DOC-005); remaining rows read through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 has 2 Uploaded Documents and Check 502 has 1.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call endCheck(501). 2. Call GET /api/v1/uploaded-documents?checkId=501. 3. Call GET /api/v1/uploaded-documents?checkId=502.
Expected     : endCheck returns 2; the first GET returns 200 with an empty array (0 Uploaded Documents remain for Check 501); the second returns 1 item.
Test data    : Checks 501 and 502.
<!-- TC:TC-DOC-052:END -->
<!-- TC:TC-DOC-053:START traces=AC-DOC-058,REQ-DOC-055 -->
### TC-DOC-053 — No fetched content kept between Checks
Derived from : AC-DOC-058  (REQ-DOC-055)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language ALL
Preconditions: service `scholarship-request` version 3 registered in REG with fetch mode `path`, document source query `attachments` (bind parameter `requestId`, type column `doc_type`, path column `file_path`) and required document types TRANSCRIPT and ID_CARD; environment setting `aias.documents.storage-root` = `/data/docs`; the documents of a `path` Check of request 1001 have been handed to the Check Engine.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE scholarship-request — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call fetchDocuments for a Check of request 2002 on the same service. 2. Query the service schema for request 1001's document content.
Expected     : The outcomes contain only documents listed for request 2002; the service schema has 0 rows of request 1001's document content.
Test data    : requests 1001 and 2002.
<!-- TC:TC-DOC-053:END -->
<!-- TC:TC-DOC-073:START traces=AC-DOC-063,REQ-DOC-060 -->
### TC-DOC-073 — End of a Check recorded as an Ended Check
Derived from : AC-DOC-063  (REQ-DOC-060)
Exercises    : in-process `DocumentAccess.endCheck(checkId)` (CON-DOC-005); the record is observed by a read of DOC_ENDED_CHECK in the test schema and by RULE-DOC-009 refusing a following handover
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; no Ended Check exists for Check 501 (DOC_ENDED_CHECK holds 0 rows with CHECK_ID = 501).
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call endCheck(501). 2. Read DOC_ENDED_CHECK rows with CHECK_ID = 501. 3. Call handOverUpload for Check 501 with `transcript.pdf` and document type TRANSCRIPT.
Expected     : endCheck returns 0 (Check 501 had no upload); exactly 1 row with CHECK_ID = 501 exists; step 3 raises `CheckEndedException` with code DOC-409-CHECK-ENDED.
Test data    : Check 501.
<!-- TC:TC-DOC-073:END -->
<!-- TC:TC-DOC-074:START traces=AC-DOC-064,REQ-DOC-060 -->
### TC-DOC-074 — Repeated end of a Check keeps one Ended Check and raises no error
Derived from : AC-DOC-064  (REQ-DOC-060)
Exercises    : in-process `DocumentAccess.endCheck(checkId)` (CON-DOC-005); the record is observed by a read of DOC_ENDED_CHECK in the test schema
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class EDGE · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; endCheck(501) has been called once, so 1 Ended Check exists for Check 501.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call endCheck(501) a second time. 2. Read DOC_ENDED_CHECK rows with CHECK_ID = 501.
Expected     : endCheck returns 0 and raises no exception (no UQ_DOC_ENDED_CHECK_CHECK_ID violation escapes); exactly 1 row with CHECK_ID = 501 exists.
Test data    : Check 501.
<!-- TC:TC-DOC-074:END -->
<!-- TC:TC-DOC-075:START traces=AC-DOC-066,REQ-DOC-062,API-DOC-001 -->
### TC-DOC-075 — Late upload of an ended Check swept at the next end of a Check
Derived from : AC-DOC-066  (REQ-DOC-062)
Exercises    : in-process `DocumentAccess.endCheck(checkId)` (CON-DOC-005); remaining rows read through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class EDGE · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 501 is recorded as an Ended Check; 1 Uploaded Document with CHECK_ID = 501 (`late.pdf`, TRANSCRIPT) is inserted directly into DOC_UPLOADED_DOC by the test fixture, standing for a handover that committed after the end of Check 501 (the race of ADR-DOC-015 — no operation can create it once the Check is recorded ended); Check 502 has 1 Uploaded Document created by handOverUpload; Check 503 has none.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call endCheck(503). 2. Call GET /api/v1/uploaded-documents?checkId=501. 3. Call GET /api/v1/uploaded-documents?checkId=502.
Expected     : endCheck returns 1 (the swept row of Check 501); the first GET returns 200 with an empty array (0 Uploaded Documents remain for Check 501); the second returns 1 item.
Test data    : Checks 501, 502, 503; fixture row `late.pdf`.
<!-- TC:TC-DOC-075:END -->
<!-- TC:TC-DOC-076:START traces=AC-DOC-069,REQ-DOC-064,API-DOC-001 -->
### TC-DOC-076 — Uploaded Documents of a Check listed in upload order without content
Derived from : AC-DOC-069  (REQ-DOC-064)
Exercises    : in-process `DocumentAccess.listUploadedDocuments(checkId)` (CON-DOC-006); the same list read through API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId}, which delegates to it
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; platform configuration `aias.check.max-file-size` = 1 MB; handOverUpload created, in this order, `transcript.pdf` (TRANSCRIPT, 81920 bytes) and `id.png` (ID_CARD, 2 MB, oversized) for Check 501, and 1 Uploaded Document for Check 502.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call listUploadedDocuments(501). 2. Call GET /api/v1/uploaded-documents?checkId=501.
Expected     : Step 1 returns 2 summaries in the order `transcript.pdf`, `id.png`, each with uploadedDocumentId, documentType (TRANSCRIPT, ID_CARD), fileName, fileSize (81920, 2097152), oversized (false, true) and uploadedAt; no summary has a content member. Step 2 returns 200 with the same 2 items in the same order and no content field.
Test data    : Checks 501 and 502; files `transcript.pdf`, `id.png`.
<!-- TC:TC-DOC-076:END -->
<!-- TC:TC-DOC-077:START traces=AC-DOC-070,REQ-DOC-064 -->
### TC-DOC-077 — Listing the Uploaded Documents of a Check with none returns an empty list
Derived from : AC-DOC-070  (REQ-DOC-064)
Exercises    : in-process `DocumentAccess.listUploadedDocuments(checkId)` (CON-DOC-006)
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class EDGE · language ALL
Preconditions: service `manual-service` version 1 registered in REG with fetch mode `manual` and required document types TRANSCRIPT and ID_CARD; Check 503 has 0 Uploaded Documents.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Call listUploadedDocuments(503).
Expected     : An empty list (0 entries) is returned and no exception is raised.
Test data    : Check 503.
<!-- TC:TC-DOC-077:END -->
<!-- SUB:API-SCENARIOS:END -->
