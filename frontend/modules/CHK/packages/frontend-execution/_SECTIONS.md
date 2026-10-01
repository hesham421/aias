<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND EXECUTION PLAN — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module : CHK   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod; lazy chunk per screen)
Inputs : srs-chk.md · prd-chk.md · api-spec-chk.yaml (OPENAPI 3.1.0, 1 operation) · registry-srs-chk.md · registry-exec-be-chk.md
Screens: 0 (SRS PART B not applicable) · UXD 0 · API bound 1 (API-CHK-001)   Open ADRs : 0 BLOCKED — ADR-CHK-019 (ACCEPTED)
══════════════════════════════════════════════════════════════════

## 3.0 Binding to the API document

This plan is bound to `api-spec-chk.yaml`, the document the backend plan derived (ADR-CHK-017). The
frontend executor's mock server serves that document. Shapes are read there by `x-api-id` and are not
restated here.

```yaml name=api-surface
mock: api-spec-chk.yaml
bindings:
  - {req: REQ-CHK-076, api: [API-CHK-001]}
  - {req: REQ-CHK-077, api: [API-CHK-001]}
  - {req: REQ-CHK-080, api: [API-CHK-001]}
unmapped: []
codes: []
```

Reconciliation against the SRS:
- Every operation maps to a REQ. API-CHK-001 maps to REQ-CHK-076, REQ-CHK-077 and REQ-CHK-080 (its `x-traces`).
- No REQ of CHK needs an HTTP operation it lacks. The SRS gives CHK no HTTP operation (ADR-CHK-013): start and
  upload confirmation are in-process operations behind INT's endpoints, and the pipeline has no caller. So
  `unmapped` is empty.
- `codes` is empty. The three error codes of API-CHK-001 (`CHK-400-CHECK-ID-INVALID`, `CHK-404-ACTIVE-CHECK-NOT-FOUND`,
  `CHK-500`) are PLATFORM-STD and carry no RULE (ADR-CHK-018). Their routing is in F2.
- Screens: none. CHK's SRS has no screen entry, so no phase below has a per-screen SUB. Each F-phase states only what a
  consumer of API-CHK-001 needs. The one `screen-hooks` block (F2) is keyed to the consumer screen entry of INT
  that the read is offered to, because the block's schema needs a screen id. INT's screens (SCR-REQ-INT-001 … SCR-REQ-INT-005) are the consumers that may use it
  (ADR-CHK-019).
- Security: no permission model — screens open per the SRS. Caller authentication is deferred (raw-idea A2),
  and the profile has no SEC-FE phase (REQ-CHK-076, REQ-CHK-077, REQ-CHK-080 name no role).











## Hand-off

The plan and registry are split into `frontend-execution/` after the P4 verdict. The implementer builds F1–F3
(types, one read hook, two validators) against `api-spec-chk.yaml` served by its mock server. It builds no
route, screen or component for CHK (F4 is empty). Using the read on INT's screens is a decision of
INT's plan (ADR-CHK-019).
