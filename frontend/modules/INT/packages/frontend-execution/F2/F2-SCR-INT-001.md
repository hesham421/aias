<!-- source: PHASE:F2 / SUB:F2-SCR-INT-001 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-045, AC-INT-046, AC-INT-047, AC-INT-048, AC-INT-049, AC-INT-069, AC-INT-070, API-INT-001, API-INT-006, REQ-INT-001, REQ-INT-006, REQ-INT-040, REQ-INT-041, REQ-INT-042, REQ-INT-043, REQ-INT-044, REQ-INT-057, REQ-INT-062, SCR-INT-001, UXD-INT-001 -->
<!-- SUB:F2-SCR-INT-001:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-057,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-069,AC-INT-070,API-INT-001,API-INT-006,UXD-INT-001,SCR-INT-001 -->
### F2 — SCR-INT-001 Checks of a request

```yaml name=screen-hooks
screen: SCR-INT-001
hooks:
  - {hook: LAUNCH-CONTEXT-INIT, kind: init, errors: "launch context incomplete → RULE-INT-004 page message, no query enabled", loading: NONE, invalidation: "—"}
  - {hook: CHECKS-QUERY, kind: read, api: [API-INT-006], cache_key: "['checks-of-request', {serviceCode, requestNumber}]", errors: "RPT-400-REQUEST-KEYS-MISSING → page message; INT-500 → error state with retry", loading: LOCAL, invalidation: "—"}
  - {hook: START-CHECK-SAVE, kind: mutation, api: [API-INT-001], errors: "CHK-400-START-INCOMPLETE, CHK-422-SERVICE-NOT-AVAILABLE, CHK-422-CONNECTION-NOT-ACTIVATED, INT-400-REQUEST-INVALID, INT-500 → page message above the list", loading: LOCAL, invalidation: "['checks-of-request', {serviceCode, requestNumber}]"}
  - {hook: CHECKS-FACADE, kind: facade, api: [API-INT-006, API-INT-001], loading: LOCAL}
```

### LAUNCH-CONTEXT-INIT — SCR-INT-001            traces=REQ-INT-041
Reads serviceCode, requestNumber, employeeId from the route's query string (ADR-INT-018 (4)); validated by the F3
launch schema; incomplete → no query is enabled, the RULE-INT-004 message is shown. No permission read (no model).
### CHECKS-QUERY — API-INT-006            traces=API-INT-006,REQ-INT-040,REQ-INT-042,REQ-INT-043
Kind read query — the shape is the document's (API-INT-006 in `api-spec-int.yaml`).
Cache key    : ['checks-of-request', {serviceCode, requestNumber}] — both filters change the response; no page/size (at most 100, newest first, with `total`)
Errors       : RPT-400-REQUEST-KEYS-MISSING → page message (text bound in §3.0) · INT-500 → the screen's error state with retry
Loading      : LOCAL · Cache policy : defaults · Invalidation : —
### START-CHECK-SAVE — API-INT-001            traces=API-INT-001,REQ-INT-044,REQ-INT-001,REQ-INT-006
Kind mutation — body is the launch context as sent (REQ-INT-044, REQ-INT-003); 202 → the new Check appears first.
Errors       : CHK-400-START-INCOMPLETE · CHK-422-SERVICE-NOT-AVAILABLE · CHK-422-CONNECTION-NOT-ACTIVATED · INT-400-REQUEST-INVALID · INT-500 → page message above the list (texts bound in §3.0)
Invalidation : ['checks-of-request', {serviceCode, requestNumber}]
### CHECKS-FACADE — SCR-INT-001
Composes LAUNCH-CONTEXT-INIT, CHECKS-QUERY, START-CHECK-SAVE · owns: the list from query data, `total` and the
"more than listed" flag (`total` > `checks.length`), derived loading · operation: `startCheck()` (no argument —
the launch context is the body).
<!-- SUB:F2-SCR-INT-001:END -->
