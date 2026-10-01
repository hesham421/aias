<!-- source: PHASE:F4 / SUB:F4-SCR-INT-002 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-050, AC-INT-051, AC-INT-052, AC-INT-053, AC-INT-054, AC-INT-055, AC-INT-056, AC-INT-057, AC-INT-058, AC-INT-059, AC-INT-060, AC-INT-061, AC-INT-062, AC-INT-067, AC-INT-068, AC-INT-078, API-INT-005, REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, REQ-INT-049, REQ-INT-050, REQ-INT-051, REQ-INT-052, REQ-INT-053, REQ-INT-054, REQ-INT-055, REQ-INT-056, REQ-INT-061, REQ-INT-066, SCR-INT-002, UXD-INT-002, UXD-INT-003, UXD-INT-004 -->
<!-- SUB:F4-SCR-INT-002:START traces=REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-061,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,API-INT-005,UXD-INT-002,UXD-INT-003,UXD-INT-004,SCR-INT-002,REQ-INT-066,AC-INT-078 -->
### REPORT-SCREEN — SCR-INT-002
Routes       : `/checks/:checkId` (+ launch query string)
Guard        : launch-context check (RULE-INT-004) — no permission
Facade       : REPORT-FACADE
Cross-module : UXD-INT-002, UXD-INT-003, UXD-INT-004 (RPT — API-INT-005)
Composition  : container page · header · findings collection → inline pane (each Finding one entry: condition, outcome, evidence, note side by side — REQ-INT-046) · documents collection → inline pane (UNREADABLE with reason and detail — REQ-INT-047) · service queries not read → inline pane (REQ-INT-048) · decision pane when recorded (REQ-INT-052) · failure pane when FAILED, no Overall Status (REQ-INT-050) · actions as links: "Upload documents" → `/checks/:checkId/documents`, "Confirm uploads" → `/checks/:checkId/upload-confirmation` (AWAITING_DOCUMENTS only — REQ-INT-053), "Record decision" → `/checks/:checkId/decision` (COMPLETED and undecided only — REQ-INT-054)
Saves        : none — the screen reads and navigates; it follows the Check every 5 seconds until it ends (REQ-INT-055, REQ-INT-056); a failed background read keeps the shown Check with the refresh-failed notice (REQ-INT-066)
<!-- SUB:F4-SCR-INT-002:END -->
