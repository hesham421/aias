<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND EXECUTION PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod)
Inputs : srs-rpt.md · prd-rpt.md · api-spec-rpt.yaml (the backend plan's document — served by the mock server) · registry-srs-rpt.md · registry-exec-be-rpt.md
Screens : 0 (SCR-REQ 0)   UXD : 0   Open ADRs : 0 BLOCKED — applied ADR-RPT-013, ADR-RPT-014
══════════════════════════════════════════════════════════════════

RPT has no screen of its own (ADR-RPT-014). This plan states RPT's frontend surface as what the embedded employee frontend consumes from RPT's API: the client models (F1), the read hooks (F2) and the parameter validators (F3) of API-RPT-001 and API-RPT-002. The screens and routes that compose them are Host Integration's, which binds its screens' hook tables to these operations. The sub-bearing phases therefore carry no per-screen SUB. No SEC-FE phase and no authentication: security is deferred (raw-idea A2).

## 3.0 Binding to the API document

```yaml name=api-surface
mock: api-spec-rpt.yaml
bindings:
  - {req: REQ-RPT-023, api: [API-RPT-001]}
  - {req: REQ-RPT-024, api: [API-RPT-001]}
  - {req: REQ-RPT-025, api: [API-RPT-001]}
  - {req: REQ-RPT-026, api: [API-RPT-001]}
  - {req: REQ-RPT-027, api: [API-RPT-001]}
  - {req: REQ-RPT-028, api: [API-RPT-002]}
  - {req: REQ-RPT-029, api: [API-RPT-002]}
  - {req: REQ-RPT-030, api: [API-RPT-002]}
  - {req: REQ-RPT-031, api: [API-RPT-002]}
  - {req: REQ-RPT-050, api: [API-RPT-002]}
unmapped:
  - "API-RPT-003 (REQ-RPT-040, REQ-RPT-041) — no employee-frontend consumer; the service administrator reads it directly, administration UI out of scope (ADR-RPT-014)"
codes:
  - {code: "RPT-400-REQUEST-KEYS-MISSING", rule: RULE-RPT-009}
  - {code: "RPT-400-SERVICE-CODE-MISSING", rule: RULE-RPT-015}
```

Reconciliation against the SRS: every REQ that the employee frontend needs has an operation (REQ-RPT-023 … REQ-RPT-031, REQ-RPT-050 → API-RPT-001 / API-RPT-002); every operation maps to a REQ; the result port and the decision are in-process (ADR-RPT-006) and have no HTTP operation — the decision the employee records goes through Host Integration's operation, not RPT's. The other catalog codes (RPT-400-CHECK-ID-INVALID, RPT-404-CHECK-NOT-FOUND, RPT-500) are PLATFORM-STD rows (ADR-RPT-013) and carry no RULE.











## Hand-off

Implementer reads F1 → F4 in order and api-spec-rpt.yaml for every shape (served by the mock server until the backend is delivered). It adds no RPT route, screen, component, permission or field; Host Integration's plan composes these hooks into its screens.
