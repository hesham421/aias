<!-- source: PHASE:F2 / SUB:F2-SCR-INT-002 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-050, AC-INT-051, AC-INT-052, AC-INT-053, AC-INT-054, AC-INT-055, AC-INT-056, AC-INT-057, AC-INT-058, AC-INT-059, AC-INT-060, AC-INT-061, AC-INT-062, AC-INT-067, AC-INT-068, AC-INT-078, API-INT-005, REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, REQ-INT-049, REQ-INT-050, REQ-INT-051, REQ-INT-052, REQ-INT-053, REQ-INT-054, REQ-INT-055, REQ-INT-056, REQ-INT-061, REQ-INT-066, SCR-INT-002, UXD-INT-002, UXD-INT-003, UXD-INT-004 -->
<!-- SUB:F2-SCR-INT-002:START traces=REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-061,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,API-INT-005,UXD-INT-002,UXD-INT-003,UXD-INT-004,SCR-INT-002,REQ-INT-066,AC-INT-078 -->
### F2 — SCR-INT-002 Check report

```yaml name=screen-hooks
screen: SCR-INT-002
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-INT-005], cache_key: "['check', checkId]", errors: "RPT-404-CHECK-NOT-FOUND → page message with a way back; INT-400-REQUEST-INVALID, INT-500 on the first read → error state with retry; any failure of a later background read → last good Check kept with the refresh-failed notice (REQ-INT-066)", loading: LOCAL, invalidation: "—"}
  - {hook: REPORT-FACADE, kind: facade, api: [API-INT-005], loading: LOCAL}
```

### CHECK-QUERY — API-INT-005            traces=API-INT-005,REQ-INT-045,REQ-INT-055,REQ-INT-056,REQ-INT-066
Kind read query — the shape is the document's (API-INT-005 in `api-spec-int.yaml`).
Cache key    : ['check', checkId]
Errors       : RPT-404-CHECK-NOT-FOUND → page message, text bound in §3.0, link back to SCR-INT-001 · INT-400-REQUEST-INVALID / INT-500 on the first read (no data yet) → error state with retry · a failed background refetch while data is shown → the last successful data stays rendered, `refreshFailed` is set and the notice "Refresh failed; retrying" is shown (non-blocking); the refetch interval keeps running and the next success clears it — the error state is never shown over a Check already on screen (REQ-INT-066, ADR-INT-026 (2))
Loading      : LOCAL on the first read only; the repeated reads are background refetches (no placeholder)
Cache policy : refetch interval 5 000 ms while `status` is AWAITING_DOCUMENTS or RUNNING; no interval once COMPLETED or FAILED (REQ-INT-055, REQ-INT-056 — polling interval is frontend configuration, ADR-INT-011 (4)); deviation from defaults recorded in ADR-INT-021 (1)
Invalidation : —
### REPORT-FACADE — SCR-INT-002
Composes CHECK-QUERY · owns: `presentedOverallStatus` (F3 presenter), the panes from query data in `position`
order, `canUpload` (status AWAITING_DOCUMENTS — REQ-INT-053), `canDecide` (status COMPLETED and `decision` null —
REQ-INT-054), `isFollowing` (AWAITING_DOCUMENTS or RUNNING), `refreshFailed` (last background read failed while data is shown — REQ-INT-066) · no imperative operation (the screen navigates only).
<!-- SUB:F2-SCR-INT-002:END -->
