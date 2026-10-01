<!-- source: PHASE:F4 / SUB:F4-SCR-INT-005 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-025, AC-INT-026, AC-INT-027, AC-INT-038, AC-INT-039, AC-INT-040, AC-INT-041, AC-INT-042, AC-INT-043, API-INT-004, API-INT-005, REQ-INT-006, REQ-INT-021, REQ-INT-022, REQ-INT-023, REQ-INT-024, REQ-INT-025, REQ-INT-026, REQ-INT-027, REQ-INT-028, REQ-INT-034, REQ-INT-035, REQ-INT-036, REQ-INT-037, REQ-INT-038, REQ-INT-054, REQ-INT-061, SCR-INT-005 -->
<!-- SUB:F4-SCR-INT-005:START traces=REQ-INT-006,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-054,REQ-INT-061,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,API-INT-004,API-INT-005,SCR-INT-005 -->
### DECISION-SCREEN — SCR-INT-005
Routes       : `/checks/:checkId/decision` (+ launch query string)
Guard        : launch-context check (RULE-INT-004) — no permission; the form is offered only while the Check is COMPLETED with no Employee Decision (REQ-INT-054)
Facade       : DECISION-FACADE
Cross-module : none rendered (the Check read decides `offered` only)
Composition  : container page · decision form (APPROVED / REJECTED; decided by = launch identity, read-only) · no secondary detail
Saves        : ONE — "Record decision" (API-INT-004), one call; on 201 → `/checks/:checkId`; on INT-502 / INT-504 the form stays and may be submitted again (REQ-INT-038)
<!-- SUB:F4-SCR-INT-005:END -->
