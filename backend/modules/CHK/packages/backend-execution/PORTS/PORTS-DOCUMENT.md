<!-- source: PHASE:PORTS / SUB:PORTS-DOCUMENT -->
<!-- context: PORTS-HEADER.md — phase-level preamble -->
<!-- traces: REQ-CHK-017, REQ-CHK-018, REQ-CHK-023, REQ-CHK-061, REQ-CHK-062 -->
<!-- SUB:PORTS-DOCUMENT:START traces=REQ-CHK-017,REQ-CHK-018,REQ-CHK-023,REQ-CHK-061,REQ-CHK-062 -->
### SUB PORTS-DOCUMENT
- `DocumentPort` (port) — `fetch(checkId, requestNumber, serviceCode, versionNumber) → List<DocumentOutcome>` and `endCheck(checkId)`.
- `DocumentAccessAdapter` (adapter) — wraps the injected Document Access in-process interface `DocumentAccess` (same deployable): `fetch` calls its `fetchDocuments` with the Check's identifier, request number, service code and version number (REQ-CHK-017) and maps each outcome to CHK's immutable `DocumentOutcome {documentType, sourceMode, readStatus, reason, detail, content}`; a failure of the call (version not found) → `DocumentFetchFailedException` → the Check ends FAILED / INTERNAL_ERROR with the failure text as detail (REQ-CHK-023). `endCheck` calls its `endCheck`; an exception is retried once and a second failure is logged with the Check identifier (REQ-CHK-062). This adapter is the only way CHK gets documents: CHK opens no host file, BLOB column or upload itself (REQ-CHK-018; storage root and maximum file size are applied inside Document Access).
<!-- SUB:PORTS-DOCUMENT:END -->
