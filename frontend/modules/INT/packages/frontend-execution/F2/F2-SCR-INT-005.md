<!-- source: PHASE:F2 / SUB:F2-SCR-INT-005 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-025, AC-INT-026, AC-INT-027, AC-INT-038, AC-INT-039, AC-INT-040, AC-INT-041, AC-INT-042, AC-INT-043, API-INT-004, API-INT-005, REQ-INT-006, REQ-INT-021, REQ-INT-022, REQ-INT-023, REQ-INT-024, REQ-INT-025, REQ-INT-026, REQ-INT-027, REQ-INT-028, REQ-INT-034, REQ-INT-035, REQ-INT-036, REQ-INT-037, REQ-INT-038, REQ-INT-054, REQ-INT-061, SCR-INT-005 -->
<!-- SUB:F2-SCR-INT-005:START traces=REQ-INT-006,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-054,REQ-INT-061,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,API-INT-004,API-INT-005,SCR-INT-005 -->
### F2 — SCR-INT-005 Employee decision

```yaml name=screen-hooks
screen: SCR-INT-005
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-INT-005], cache_key: "['check', checkId]", errors: "RPT-404-CHECK-NOT-FOUND → page message; INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: DECISION-SAVE, kind: mutation, api: [API-INT-004], errors: "RPT-400-DECISION-INCOMPLETE, RPT-409-CHECK-NOT-COMPLETED, RPT-409-DECISION-ALREADY-RECORDED, RPT-422-APPROVAL-FLAG-ON-REJECTION, RPT-404-CHECK-NOT-FOUND, INT-502-APPROVAL-API-FAILED, INT-504-APPROVAL-API-TIMED-OUT, INT-400-REQUEST-INVALID, INT-500 → per §3.0", loading: LOCAL, invalidation: "['check', checkId], ['checks-of-request', {serviceCode, requestNumber}]"}
  - {hook: DECISION-FACADE, kind: facade, api: [API-INT-005, API-INT-004], loading: LOCAL}
```

### DECISION-SAVE — API-INT-004            traces=API-INT-004,REQ-INT-021,REQ-INT-023,REQ-INT-036,REQ-INT-037,REQ-INT-038
Kind mutation — body {employeeDecision, decidedBy = launch employeeId} (REQ-INT-023). 201 → invalidate and navigate to
SCR-INT-002, which shows the recorded decision (REQ-INT-022, REQ-INT-052). INT-502 / INT-504 → nothing was recorded:
no invalidation, the form keeps its value and the submit is enabled again — a new submit is a new request
(REQ-INT-038); the hook never retries on its own (REQ-INT-033).
Invalidation : ['check', checkId], ['checks-of-request', {serviceCode, requestNumber}] (on 201 only)
### DECISION-FACADE — SCR-INT-005
Composes CHECK-QUERY, DECISION-SAVE · owns: `offered` (status COMPLETED and `decision` null — REQ-INT-054), the last
refusal · operation: `decide(values)`.
<!-- SUB:F2-SCR-INT-005:END -->
