<!-- source: PHASE:F1 / SUB:F1-SCR-INT-001 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-045, AC-INT-046, AC-INT-047, AC-INT-048, AC-INT-049, AC-INT-069, AC-INT-070, API-INT-001, API-INT-006, REQ-INT-001, REQ-INT-006, REQ-INT-040, REQ-INT-041, REQ-INT-042, REQ-INT-043, REQ-INT-044, REQ-INT-057, REQ-INT-062, SCR-INT-001, UXD-INT-001 -->
<!-- SUB:F1-SCR-INT-001:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-057,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-069,AC-INT-070,API-INT-001,API-INT-006,UXD-INT-001,SCR-INT-001 -->
### F1 — SCR-INT-001 Checks of a request
- From the documents: API-INT-006 → `ChecksOfRequestResponse` (`total`, `checks[]`); API-INT-001 → `StartCheckRequest`, `StartedCheckResponse`; `ProblemDetail` (every error response).
- Frontend-only: `LaunchContext` {serviceCode, requestNumber, employeeId} (strings, as the host passed them — ADR-INT-018 (4), kept by ADR-INT-021 (6)).
- Cross-module: UXD-INT-001 (RPT fields of `checks[]` and `total`).
<!-- SUB:F1-SCR-INT-001:END -->
