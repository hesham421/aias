<!-- source: PHASE:F4 / SUB:F4-SCR-INT-004 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-022, AC-INT-023, AC-INT-024, AC-INT-071, AC-INT-073, API-INT-003, API-INT-005, API-INT-007, API-INT-008, REQ-INT-006, REQ-INT-018, REQ-INT-019, REQ-INT-020, REQ-INT-061, REQ-INT-063, REQ-INT-064, SCR-INT-004, UXD-INT-007, UXD-INT-008 -->
<!-- SUB:F4-SCR-INT-004:START traces=REQ-INT-006,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-071,AC-INT-073,API-INT-003,API-INT-005,API-INT-007,API-INT-008,UXD-INT-007,UXD-INT-008,SCR-INT-004 -->
### CONFIRMATION-SCREEN — SCR-INT-004
Routes       : `/checks/:checkId/upload-confirmation` (+ launch query string)
Guard        : launch-context check (RULE-INT-004) — no permission
Facade       : CONFIRMATION-FACADE
Cross-module : UXD-INT-007 (DOC — API-INT-007), UXD-INT-008 (REG — API-INT-008)
Composition  : container page · uploaded documents collection → inline pane · missing types collection → inline pane (read-only, shown before the submit — REQ-INT-020)
Saves        : ONE — "Confirm uploads" (API-INT-003); on 202 → `/checks/:checkId`
<!-- SUB:F4-SCR-INT-004:END -->
