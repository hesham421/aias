<!-- source: PHASE:F1 / SUB:F1-SCR-INT-005 -->
<!-- context: F1-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-025, AC-INT-026, AC-INT-027, AC-INT-038, AC-INT-039, AC-INT-040, AC-INT-041, AC-INT-042, AC-INT-043, API-INT-004, API-INT-005, REQ-INT-006, REQ-INT-021, REQ-INT-022, REQ-INT-023, REQ-INT-024, REQ-INT-025, REQ-INT-026, REQ-INT-027, REQ-INT-028, REQ-INT-034, REQ-INT-035, REQ-INT-036, REQ-INT-037, REQ-INT-038, REQ-INT-054, REQ-INT-061, SCR-INT-005 -->
<!-- SUB:F1-SCR-INT-005:START traces=REQ-INT-006,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-054,REQ-INT-061,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,API-INT-004,API-INT-005,SCR-INT-005 -->
### F1 — SCR-INT-005 Employee decision
- From the documents: API-INT-004 → `DecisionRequest` {employeeDecision, decidedBy}, `RecordedDecisionResponse`; API-INT-005 → `CheckReportResponse` (status, decision); `ProblemDetail`.
- Frontend-only: `DecisionFormValues` {employeeDecision: 'APPROVED' | 'REJECTED'} — `decidedBy` is never a form value, it is the launch identity (REQ-INT-023).
- Cross-module: none rendered.
<!-- SUB:F1-SCR-INT-005:END -->
