<!-- source: PHASE:F3 / SUB:F3-SCR-INT-004 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-022, AC-INT-023, AC-INT-024, AC-INT-071, AC-INT-073, API-INT-003, API-INT-005, API-INT-007, API-INT-008, REQ-INT-006, REQ-INT-018, REQ-INT-019, REQ-INT-020, REQ-INT-061, REQ-INT-063, REQ-INT-064, SCR-INT-004, UXD-INT-007, UXD-INT-008 -->
<!-- SUB:F3-SCR-INT-004:START traces=REQ-INT-006,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-071,AC-INT-073,API-INT-003,API-INT-005,API-INT-007,API-INT-008,UXD-INT-007,UXD-INT-008,SCR-INT-004 -->
### F3 — SCR-INT-004 Upload confirmation
- `confirmFormSchema` — the empty object of UploadConfirmationRequest (API-INT-003); no field.
- `missingTypes(required, uploaded)` — the required document types (REQUIRED-TYPES-QUERY) with no uploaded document of that type (UPLOADED-DOCUMENTS-QUERY), in the order the version lists them (REQ-INT-020).
- One submit ("Confirm uploads").
<!-- SUB:F3-SCR-INT-004:END -->
