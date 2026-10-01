<!-- source: PHASE:SVC-API / SUB:SVC-API-QUERY -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-INT-001, DBF-INT-002, DBF-INT-003, DBF-INT-004, DBF-INT-005, DBF-INT-006, DBF-INT-007, DBF-INT-008, REQ-INT-006, REQ-INT-007, REQ-INT-008, REQ-INT-016, REQ-INT-017, REQ-INT-020, REQ-INT-040, REQ-INT-042, REQ-INT-043, REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, REQ-INT-049, REQ-INT-050, REQ-INT-051, REQ-INT-052, REQ-INT-053, REQ-INT-054, REQ-INT-055, REQ-INT-056, REQ-INT-057, REQ-INT-061, REQ-INT-062, REQ-INT-063, REQ-INT-064, REQ-INT-066 -->
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
