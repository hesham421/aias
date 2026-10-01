<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND EXECUTION PLAN — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod)
Inputs : srs-reg.md · prd-reg.md · api-spec-reg.yaml · registry-srs-reg.md · registry-exec-be-reg.md
Screens : 0 SCR · 0 UXD — REG has no screen (ADR-REG-012)
Security : no permission model — screens open per the SRS; caller authentication deferred (raw idea A2), so the profile has no SEC-FE phase
Open ADRs : 0 BLOCKED — decisions applied: ADR-REG-005, ADR-REG-007, ADR-REG-011, ADR-REG-012, ADR-REG-016 (analysis/decisions/REG/; header regenerated from the ADRs this file's body cites — gate-analysis REVISE round 2)
══════════════════════════════════════════════════════════════════

## 3.0 Binding to the API document

REG's frontend surface is only what a frontend consumes from REG's read API: the three GET operations of `api-spec-reg.yaml`. Method, path, parameters and response schemas (`ServiceSummary`, `LoadResult`, `ProblemDetail`) are read in the document by `x-api-id`, never restated here. The document has no security scheme (raw idea A2).

```yaml name=api-surface
mock: api-spec-reg.yaml
bindings:
  - {req: REQ-REG-013, api: [API-REG-001]}
  - {req: REQ-REG-014, api: [API-REG-002]}
  - {req: REQ-REG-015, api: [API-REG-002]}
  - {req: REQ-REG-008, api: [API-REG-003]}
unmapped: []
codes:
  - {code: "REG-404-SERVICE-NOT-FOUND", rule: RULE-REG-016}
```

Reconciliation against the SRS: every REQ of the SRS "API expectations" table that has an HTTP operation is bound above (REQ-REG-013, REQ-REG-014, REQ-REG-015, REQ-REG-008); every operation of the document maps to a REQ. The in-process operations (supply service package, resolve version, supply connection, supply approval API) have no HTTP operation by design (ADR-REG-011) and are never called by a frontend. `REG-500` is PLATFORM-STD (no RULE) and is routed to the generic server error. No ADR is pending against the document.











## Hand-off

The implementer reads this plan in profile-phase order, `api-spec-reg.yaml` for shapes (served by its mock server until the backend is delivered), and adds no REG route, component, permission or field — none is traceable to an F-block. INT's frontend plan owns every screen and cites any REG field it renders as its own `UXD-*`.
