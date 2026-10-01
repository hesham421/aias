<!-- source: PHASE:SVC-API -->
<!-- traces: DBF-DOC-001, DBF-DOC-002, DBF-DOC-005, DBF-DOC-006, DBF-DOC-007, DBF-DOC-009, DBF-DOC-010, DBF-DOC-013, REQ-DOC-001, REQ-DOC-002, REQ-DOC-003, REQ-DOC-017, REQ-DOC-018, REQ-DOC-034, REQ-DOC-035, REQ-DOC-043, REQ-DOC-054, REQ-DOC-056, REQ-DOC-060, REQ-DOC-061, REQ-DOC-062, REQ-DOC-063, REQ-DOC-064 -->
<!-- PHASE:SVC-API:START traces=DBF-DOC-001,DBF-DOC-002,DBF-DOC-005,DBF-DOC-006,DBF-DOC-007,DBF-DOC-009,DBF-DOC-010,DBF-DOC-013,REQ-DOC-001,REQ-DOC-002,REQ-DOC-003,REQ-DOC-017,REQ-DOC-018,REQ-DOC-034,REQ-DOC-035,REQ-DOC-054,REQ-DOC-060,REQ-DOC-061,REQ-DOC-062,REQ-DOC-063,REQ-DOC-064 -->
## PHASE SVC-API — SVC+API

### Service layer — the in-process interface `DocumentAccess` (contract-doc.md)
Injected into CHK and INT (profile `module_interface: in_process`). Value objects are Java records with unmodifiable lists. Service classes: `UploadHandoverService`, `DocumentFetchService`, `CheckEndService`, `UploadedDocumentQueryService`.

**`handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` → UploadReceipt {uploadedDocumentId, documentType, fileName, fileSize, oversized, notice}** — READ_WRITE, one transaction.
  - Honours: CON-DOC-003
  1. RULE-DOC-003: checkId absent, documentType blank or `bytes.length == 0` → `IncompleteUploadException` (DOC-400-INCOMPLETE-UPLOAD) (REQ-DOC-022).
  1a. RULE-DOC-009: SELECT 1 FROM DOC_ENDED_CHECK WHERE CHECK_ID = :checkId (DBF-DOC-013) finds a row → `CheckEndedException` (DOC-409-CHECK-ENDED), nothing stored (REQ-DOC-061, ADR-DOC-015).
  2. integrate (XM-DOC-001) — resolve the version by serviceCode + versionNumber; unknown → `ServiceVersionNotFoundException` (DOC-404-SERVICE-VERSION-NOT-FOUND) (REQ-DOC-003).
  3. RULE-DOC-001: version fetch mode ≠ `manual` → `FetchModeNotManualException` (DOC-422-FETCH-MODE-NOT-MANUAL) (REQ-DOC-020).
  4. RULE-DOC-002 — integrate (XM-DOC-003): documentType not among the version's required document types → `DocumentTypeNotOfServiceException` (DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE) (REQ-DOC-021).
  5. RULE-DOC-005: `bytes.length > aias.check.max-file-size` → oversized = true, content = null, notice = the RULE-DOC-005 message (REQ-DOC-043).
  5a. RULE-DOC-010: SELECT COUNT(*) FROM DOC_UPLOADED_DOC WHERE CHECK_ID = :checkId (DBF-DOC-002) ≥ `aias.check.max-uploads` → `UploadLimitReachedException` (DOC-422-UPLOAD-LIMIT-REACHED), nothing stored (REQ-DOC-063, ADR-DOC-016); oversized rows count.
  6. Persist: INSERT INTO DOC_UPLOADED_DOC (CHECK_ID DBF-DOC-002, SERVICE_CODE DBF-DOC-003, VERSION_NUMBER DBF-DOC-004, DOCUMENT_TYPE DBF-DOC-005, FILE_NAME DBF-DOC-006, FILE_SIZE DBF-DOC-007, CONTENT DBF-DOC-008, OVERSIZED DBF-DOC-009); UPLOADED_DOCUMENT_ID by the identity clause; CREATED_AT / UPDATED_AT by DEFAULT SYSTIMESTAMP (REQ-DOC-017). Every handover is a new row — an earlier upload is never changed (REQ-DOC-023).
  - Concurrency: OPTIMISTIC-BY-DESIGN — each handover inserts its own row and allocates no unique value. Steps 1a and 5a read committed rows to decide the write: a handover racing an endCheck of the same Check can commit after its delete, and is removed by the next end-of-Check sweep (REQ-DOC-062, ADR-DOC-015); concurrent handovers of one Check can exceed `aias.check.max-uploads` by at most the number running at once (ADR-DOC-016).

**`fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` → List<DocumentOutcome {documentType, sourceMode, readStatus, reason, detail, content}>** — READ_ONLY.
  - Honours: CON-DOC-004
  1. integrate (XM-DOC-001) — resolve the version; unknown → `ServiceVersionNotFoundException` (DOC-404-SERVICE-VERSION-NOT-FOUND), no document fetched (REQ-DOC-002, REQ-DOC-003). Read its fetch mode, document source and required document types (integrate XM-DOC-002, XM-DOC-003). Only that fetch mode's branch runs (REQ-DOC-001).
  2. `path`: integrate (XM-DOC-004) for the query's connection settings → RULE-DOC-007 → `McpDocumentSourceAdapter` (REQ-DOC-004) → per row: document type from the type column (REQ-DOC-006), `GuardedFileSystemAdapter` (REQ-DOC-005, REQ-DOC-007 … REQ-DOC-011, REQ-DOC-041, REQ-DOC-042).
  3. `blob`: integrate (XM-DOC-004) → RULE-DOC-006, RULE-DOC-007 → `JdbcDocumentSourceAdapter` (REQ-DOC-012 … REQ-DOC-016).
  4. `manual`: no query, no host file, no JDBC (REQ-DOC-019) → SELECT UPLOADED_DOCUMENT_ID, DOCUMENT_TYPE, FILE_NAME, FILE_SIZE, CONTENT, OVERSIZED FROM DOC_UPLOADED_DOC WHERE CHECK_ID = :checkId ORDER BY CREATED_AT (RULE-DOC-008; REQ-DOC-018, REQ-DOC-056); OVERSIZED = 1 → UNREADABLE / TOO_LARGE (REQ-DOC-044).
  5. Per fetched document, independently and in a try/catch per document (REQ-DOC-037): `FormatDetector` → reader (REQ-DOC-024 … REQ-DOC-029) → READ outcome with content (REQ-DOC-038), or UNREADABLE with one `UnreadableReason` and a detail (REQ-DOC-036). Each step checks the deadline first; once passed, every document not yet read → UNREADABLE / OUT_OF_TIME (REQ-DOC-040).
  6. Every required document type that no fetched or uploaded document carries → one MISSING outcome (REQ-DOC-035) — except after a SOURCE_QUERY_FAILED, where every required type is UNREADABLE instead (REQ-DOC-039).
  7. Return exactly one outcome per document plus the MISSING ones (REQ-DOC-034). Nothing is stored and no reference to the bytes or content is kept after return (REQ-DOC-055). DOC calls no host endpoint and holds no approval definition (REQ-DOC-053; AIAS-4). Whether an outcome blocks COMPLIANT is decided by the caller (AIAS-8 is satisfied by every MISSING / UNREADABLE outcome reaching it, never dropped).
  - Concurrency: NONE — reads only.

**`endCheck(checkId)` → deletedCount** — READ_WRITE.
  - Honours: CON-DOC-005
  1. Record the end (REQ-DOC-060, ADR-DOC-015): MERGE INTO DOC_ENDED_CHECK t USING (SELECT :checkId AS CHECK_ID FROM DUAL) s ON (t.CHECK_ID = s.CHECK_ID) WHEN NOT MATCHED THEN INSERT (CHECK_ID) VALUES (s.CHECK_ID) (DBF-DOC-013); ENDED_CHECK_ID by the identity clause; CREATED_AT / UPDATED_AT by DEFAULT SYSTIMESTAMP. A concurrent duplicate insert raising the UQ_DOC_ENDED_CHECK_CHECK_ID violation is treated as already recorded.
  2. DELETE FROM DOC_UPLOADED_DOC WHERE CHECK_ID = :checkId (DBF-DOC-002) — hard delete (REQ-DOC-054, ADR-DOC-008).
  3. Sweep (REQ-DOC-062, ADR-DOC-015): DELETE FROM DOC_UPLOADED_DOC u WHERE EXISTS (SELECT 1 FROM DOC_ENDED_CHECK e WHERE e.CHECK_ID = u.CHECK_ID) — removes an upload left by a handover that committed after an earlier end.
  4. deletedCount = rows deleted by steps 2 and 3. Idempotent: a second call records nothing new and deletes 0 rows.
  - Concurrency: one transaction; step 1 commits the marker with the deletes. A handover that read no marker before this commit and commits after it leaves a row that step 3 of the next endCheck removes (ADR-DOC-015). Ordering guarantee upheld by the callers (CON-DOC-003, CON-DOC-005): CHK calls endCheck only once the Check accepts no more documents.

**`listUploadedDocuments(checkId)` → List<UploadedDocumentSummary {uploadedDocumentId, documentType, fileName, fileSize, oversized, uploadedAt}>** — READ_ONLY (`UploadedDocumentQueryService`).
  - Honours: CON-DOC-006
  1. checkId absent → `CheckIdRequiredException` (DOC-400-CHECK-ID-REQUIRED — the same code as API-DOC-001's catalog row).
  2. QR-DOC-001 — SELECT UPLOADED_DOCUMENT_ID, DOCUMENT_TYPE, FILE_NAME, FILE_SIZE, OVERSIZED, CREATED_AT FROM DOC_UPLOADED_DOC WHERE CHECK_ID = :checkId ORDER BY CREATED_AT (RULE-DOC-008); CONTENT is never selected (REQ-DOC-064). uploadedAt = CREATED_AT (DBF-DOC-010).
  3. No row → empty list (an unknown or ended Check — AC-DOC-070). DOC holds no read status; `oversized` is the only reading-related field (ADR-DOC-017).
  - Called in-process by the host integration (its frontend read) and by API-DOC-001 below, which delegates to it.
  - Concurrency: NONE — reads only.

### In-process rejection codes (typed exceptions of `DocumentAccess` — mapped by INT to ProblemDetail, ADR-DOC-012)
| Code | Rule / REQ | Exception | Message en | Message ar |
|---|---|---|---|---|
| DOC-400-INCOMPLETE-UPLOAD | RULE-DOC-003 | IncompleteUploadException | The upload needs a Check, a document type and a file that is not empty. | PENDING ADR-DOC-012 |
| DOC-404-SERVICE-VERSION-NOT-FOUND | REQ-DOC-003 | ServiceVersionNotFoundException | service package version not found | PENDING ADR-DOC-012 |
| DOC-422-FETCH-MODE-NOT-MANUAL | RULE-DOC-001 | FetchModeNotManualException | Documents can be uploaded only for a service whose documents are provided by the employee; the service "{serviceCode}" obtains its documents by "{fetchMode}". | PENDING ADR-DOC-012 |
| DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE | RULE-DOC-002 | DocumentTypeNotOfServiceException | "{documentType}" is not a document type of the service "{serviceCode}"; choose one of: {requiredDocumentTypes}. | PENDING ADR-DOC-012 |
| DOC-409-CHECK-ENDED | RULE-DOC-009 | CheckEndedException | The Check {checkId} has already ended; documents can no longer be uploaded for it. Start a new check to provide these documents. | PENDING ADR-DOC-012 |
| DOC-422-UPLOAD-LIMIT-REACHED | RULE-DOC-010 | UploadLimitReachedException | The Check {checkId} already has the maximum of {maxUploads} uploaded documents; no further file can be uploaded for it. | PENDING ADR-DOC-012 |
| (notice, not an error) | RULE-DOC-005 | UploadReceipt.notice | The file "{fileName}" is larger than the maximum file size of {maxFileSize}; it will not be read and will be reported as unreadable. | PENDING ADR-DOC-012 |

### HTTP endpoint
Controller `UploadedDocumentController` → service `UploadedDocumentQueryService` (the same `listUploadedDocuments` procedure INT calls in-process — ADR-DOC-017). DOC exposes no POST, PUT or DELETE (ADR-DOC-011).

<!-- API:API-DOC-001:START traces=REQ-DOC-017,REQ-DOC-018,REQ-DOC-043,REQ-DOC-056,REQ-DOC-064,DBF-DOC-001,DBF-DOC-002,DBF-DOC-005,DBF-DOC-006,DBF-DOC-007,DBF-DOC-009,DBF-DOC-010 -->
### API-DOC-001 — List the Uploaded Documents of a Check
Entity       : ENT-DOC-001
Endpoint     : /api/v1/uploaded-documents   verb: GET
Layers       : controller UploadedDocumentController → service UploadedDocumentQueryService
Request      : query checkId (DBF-DOC-002, integer int64, required); no path parameter; no body
Response     : 200 · array of UploadedDocumentSummary {uploadedDocumentId (DBF-DOC-001), documentType (DBF-DOC-005), fileName (DBF-DOC-006), fileSize (DBF-DOC-007), oversized (DBF-DOC-009), createdAt (DBF-DOC-010)} ordered by createdAt · not paginated · no envelope; never the file content (DBF-DOC-008)
Validations  : checkId present and numeric (PLATFORM-STD, ADR-DOC-012)
Errors       : DOC-400-CHECK-ID-REQUIRED (400, PLATFORM-STD) · DOC-500 (500, PLATFORM-STD)
Orchestration : validate checkId → delegate to `listUploadedDocuments(checkId)` (CON-DOC-006, REQ-DOC-064), which loads the Check's rows (QR-DOC-001, filter on DBF-DOC-002 — RULE-DOC-008) → map to UploadedDocumentSummary (createdAt = uploadedAt); writes nothing
Repository   : QR-DOC-001 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; REQ-DOC-017, REQ-DOC-018 name no role check)
Localization : messages en per SRS; ar PENDING ADR-DOC-012
Covers       : DOC's only HTTP surface (ADR-DOC-011). It shows the result of the handover (REQ-DOC-017, REQ-DOC-020 … REQ-DOC-023, REQ-DOC-043) and of the end of a Check (REQ-DOC-054). The in-process procedures of this phase implement, and are listed here for the coverage check: REQ-DOC-001, REQ-DOC-002, REQ-DOC-003, REQ-DOC-004, REQ-DOC-005, REQ-DOC-006, REQ-DOC-007, REQ-DOC-008, REQ-DOC-009, REQ-DOC-010, REQ-DOC-011, REQ-DOC-012, REQ-DOC-013, REQ-DOC-014, REQ-DOC-015, REQ-DOC-016, REQ-DOC-019, REQ-DOC-024, REQ-DOC-025, REQ-DOC-026, REQ-DOC-027, REQ-DOC-028, REQ-DOC-029, REQ-DOC-030, REQ-DOC-031, REQ-DOC-032, REQ-DOC-033, REQ-DOC-034, REQ-DOC-035, REQ-DOC-036, REQ-DOC-037, REQ-DOC-038, REQ-DOC-039, REQ-DOC-040, REQ-DOC-041, REQ-DOC-042, REQ-DOC-044, REQ-DOC-045, REQ-DOC-046, REQ-DOC-047, REQ-DOC-048, REQ-DOC-049, REQ-DOC-050, REQ-DOC-051, REQ-DOC-052, REQ-DOC-053, REQ-DOC-055, REQ-DOC-057, REQ-DOC-058, REQ-DOC-059, REQ-DOC-060, REQ-DOC-061, REQ-DOC-062, REQ-DOC-063, REQ-DOC-064
<!-- API:API-DOC-001:END -->

<!-- PHASE:SVC-API:END -->
