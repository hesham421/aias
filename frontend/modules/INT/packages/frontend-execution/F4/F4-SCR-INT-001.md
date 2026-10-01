<!-- source: PHASE:F4 / SUB:F4-SCR-INT-001 -->
<!-- context: F4-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-045, AC-INT-046, AC-INT-047, AC-INT-048, AC-INT-049, AC-INT-069, AC-INT-070, API-INT-001, API-INT-006, REQ-INT-001, REQ-INT-006, REQ-INT-040, REQ-INT-041, REQ-INT-042, REQ-INT-043, REQ-INT-044, REQ-INT-057, REQ-INT-062, SCR-INT-001, UXD-INT-001 -->
<!-- SUB:F4-SCR-INT-001:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-057,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-069,AC-INT-070,API-INT-001,API-INT-006,UXD-INT-001,SCR-INT-001 -->
### CHECKS-SCREEN — SCR-INT-001
Routes       : `/?serviceCode=&requestNumber=&employeeId=` (the host's launch address)
Guard        : launch-context check (RULE-INT-004) — no permission
Facade       : CHECKS-FACADE
Cross-module : UXD-INT-001 (RPT — API-INT-006)
Composition  : container page · Checks collection → inline list (newest first as received; "This request has {total} Checks." when `total` exceeds the listed count — REQ-INT-043) · a row opens `/checks/:checkId` seeding that ONE checkId
Saves        : ONE — "Start a Check" (API-INT-001); on 202 the list is refreshed and the new Check is first (REQ-INT-044)
<!-- SUB:F4-SCR-INT-001:END -->
