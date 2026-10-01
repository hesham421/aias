<!-- source: PHASE:F4 / SUB:F4-SCR-INT-003 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-013, AC-INT-015, AC-INT-016, AC-INT-017, AC-INT-018, AC-INT-020, AC-INT-021, AC-INT-071, AC-INT-072, AC-INT-073, AC-INT-074, AC-INT-075, AC-INT-076, AC-INT-077, API-INT-002, API-INT-005, API-INT-007, API-INT-008, REQ-INT-006, REQ-INT-009, REQ-INT-010, REQ-INT-011, REQ-INT-012, REQ-INT-013, REQ-INT-014, REQ-INT-015, REQ-INT-016, REQ-INT-017, REQ-INT-019, REQ-INT-061, REQ-INT-063, REQ-INT-064, REQ-INT-065, SCR-INT-003, UXD-INT-005, UXD-INT-006 -->
<!-- SUB:F4-SCR-INT-003:START traces=REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-002,API-INT-005,API-INT-007,API-INT-008,UXD-INT-005,UXD-INT-006,SCR-INT-003,REQ-INT-065,AC-INT-075,AC-INT-076,AC-INT-077 -->
### UPLOAD-SCREEN — SCR-INT-003
Routes       : `/checks/:checkId/documents` (+ launch query string)
Guard        : launch-context check (RULE-INT-004) — no permission; the form is offered only while the Check is AWAITING_DOCUMENTS
Facade       : UPLOAD-FACADE
Cross-module : UXD-INT-005 (REG — API-INT-008), UXD-INT-006 (DOC — API-INT-007)
Composition  : container page · upload form (document type — a type already uploaded is marked "already uploaded", still selectable, REQ-INT-065; file) · uploaded documents collection → inline pane beneath the form, read-only · "Confirm uploads" is a link to `/checks/:checkId/upload-confirmation`, not a submit
Saves        : ONE — "Upload" (API-INT-002); it never confirms (REQ-INT-019)
<!-- SUB:F4-SCR-INT-003:END -->
