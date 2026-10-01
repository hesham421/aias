<!-- source: PHASE:F3 / SUB:F3-SCR-INT-001 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-045, AC-INT-046, AC-INT-047, AC-INT-048, AC-INT-049, AC-INT-069, AC-INT-070, API-INT-001, API-INT-006, REQ-INT-001, REQ-INT-006, REQ-INT-040, REQ-INT-041, REQ-INT-042, REQ-INT-043, REQ-INT-044, REQ-INT-057, REQ-INT-062, SCR-INT-001, UXD-INT-001 -->
<!-- SUB:F3-SCR-INT-001:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-057,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-069,AC-INT-070,API-INT-001,API-INT-006,UXD-INT-001,SCR-INT-001 -->
### F3 — SCR-INT-001 Checks of a request
- `launchContextSchema` — serviceCode, requestNumber, employeeId each present and not blank; any one missing → RULE-INT-004 (REQ-INT-041), text: RULE-INT-004 message (SRS) "Open this screen from the host system for one request." Values are passed on unchanged — never trimmed or reformatted (REQ-INT-003).
- Start form — no field; its submit sends the validated launch context (StartCheckRequest of API-INT-001). Submit disabled while pending (one start per press).
<!-- SUB:F3-SCR-INT-001:END -->
