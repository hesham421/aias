<!-- source: PHASE:F1 / SUB:F1-SCR-INT-004 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-022, AC-INT-023, AC-INT-024, AC-INT-071, AC-INT-073, API-INT-003, API-INT-005, API-INT-007, API-INT-008, REQ-INT-006, REQ-INT-018, REQ-INT-019, REQ-INT-020, REQ-INT-061, REQ-INT-063, REQ-INT-064, SCR-INT-004, UXD-INT-007, UXD-INT-008 -->
<!-- SUB:F1-SCR-INT-004:START traces=REQ-INT-006,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-071,AC-INT-073,API-INT-003,API-INT-005,API-INT-007,API-INT-008,UXD-INT-007,UXD-INT-008,SCR-INT-004 -->
### F1 — SCR-INT-004 Upload confirmation
- From the documents: API-INT-003 → `UploadConfirmationRequest` (empty object), `ConfirmedCheckResponse`; API-INT-007 → `UploadedDocumentResponse[]`; API-INT-008 → `RequiredDocumentTypesResponse`; API-INT-005 → `CheckReportResponse` (status); `ProblemDetail`.
- Frontend-only: `MissingTypes` = string[] (required types with no upload — derived in F3).
- Cross-module: UXD-INT-007 (DOC), UXD-INT-008 (REG).
<!-- SUB:F1-SCR-INT-004:END -->
