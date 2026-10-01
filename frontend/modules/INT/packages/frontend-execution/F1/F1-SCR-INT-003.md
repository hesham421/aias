<!-- source: PHASE:F1 / SUB:F1-SCR-INT-003 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-013, AC-INT-015, AC-INT-016, AC-INT-017, AC-INT-018, AC-INT-020, AC-INT-021, AC-INT-071, AC-INT-072, AC-INT-073, AC-INT-074, AC-INT-075, AC-INT-076, AC-INT-077, API-INT-002, API-INT-005, API-INT-007, API-INT-008, REQ-INT-006, REQ-INT-009, REQ-INT-010, REQ-INT-011, REQ-INT-012, REQ-INT-013, REQ-INT-014, REQ-INT-015, REQ-INT-016, REQ-INT-017, REQ-INT-019, REQ-INT-061, REQ-INT-063, REQ-INT-064, REQ-INT-065, SCR-INT-003, UXD-INT-005, UXD-INT-006 -->
<!-- SUB:F1-SCR-INT-003:START traces=REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-002,API-INT-005,API-INT-007,API-INT-008,UXD-INT-005,UXD-INT-006,SCR-INT-003,REQ-INT-065,AC-INT-075,AC-INT-076,AC-INT-077 -->
### F1 — SCR-INT-003 Document upload
- From the documents: API-INT-002 → `UploadRequest` (multipart: documentType, file), `UploadReceiptResponse` (incl. optional `notice`); API-INT-008 → `RequiredDocumentTypesResponse` (`requiredDocumentTypes` of the Check's version); API-INT-007 → `UploadedDocumentResponse[]`; API-INT-005 → `CheckReportResponse` (status only is used); `ProblemDetail`.
- Frontend-only: `UploadFormValues` {documentType: string, file: File}.
- Cross-module: UXD-INT-005 (REG), UXD-INT-006 (DOC).
<!-- SUB:F1-SCR-INT-003:END -->
