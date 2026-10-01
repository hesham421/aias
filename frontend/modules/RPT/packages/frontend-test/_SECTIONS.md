<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND TEST PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Stage : P4   Framework : agnostic (the consumer repo chooses its tool; this plan names none)
Sources : _state/current-srs.md (v1) · current-frontend-execution-plan.md (SCR 0, UXD 0; units F1, F2, F3, F4; ALIGN-FE no_tests) · current-api-spec.yaml (served by the mock server)
Open ADRs : none BLOCKED — applied ADR-RPT-013, ADR-RPT-014, ADR-RPT-015
TCs : 10 (TC-RPT-065 … TC-RPT-074) — continuing the backend plan's sequence (TC-RPT-001 … TC-RPT-064) — UI-FLOWS 7 · INT-FLOW 3
══════════════════════════════════════════════════════════════════

RPT has no screen (ADR-RPT-014): its frontend cases exercise what the employee frontend consumes from RPT — the F1 models, the F2 read hooks, the F3 parameter validators and the F4 rendering obligations — against api-spec-rpt.yaml served by the mock server, for the ACs whose Given/When names the employee frontend or the report it renders. Navigation and screen flows are Host Integration's cases. No permission case: no permission model (raw-idea A2). INT-UXD is absent: RPT cites no UXD. Every refusal asserted by its text is bound in the frontend plan (`text:` on the F2/F3 rows routing RPT-404-CHECK-NOT-FOUND and RULE-RPT-009 / RPT-400-REQUEST-KEYS-MISSING); Arabic texts `PENDING ADR-RPT-013`.



## TC TRACEABILITY INDEX

| AC | REQ | TC | API (mock) | SCR | RULE → code | Package |
|---|---|---|---|---|---|---|
| AC-RPT-027 | REQ-RPT-023 | TC-RPT-065 | API-RPT-001 | none (ADR-RPT-014) | — | F1 |
| AC-RPT-028 | REQ-RPT-023 | TC-RPT-066 | API-RPT-001 | none (ADR-RPT-014) | — | F1 |
| AC-RPT-031 | REQ-RPT-026 | TC-RPT-067 | API-RPT-001 | none (ADR-RPT-014) | — | F1 |
| AC-RPT-030 | REQ-RPT-025 | TC-RPT-068 | API-RPT-001 | none (ADR-RPT-014) | REQ-RPT-025 → RPT-404-CHECK-NOT-FOUND | F2 |
| AC-RPT-033 | REQ-RPT-028 | TC-RPT-069 | API-RPT-002 | none (ADR-RPT-014) | — | F2 |
| AC-RPT-034 | REQ-RPT-028 | TC-RPT-070 | API-RPT-002 | none (ADR-RPT-014) | — | F2 |
| AC-RPT-035 | REQ-RPT-029 | TC-RPT-071 | API-RPT-002 | none (ADR-RPT-014) | RULE-RPT-009 → RPT-400-REQUEST-KEYS-MISSING | F3 |
| AC-RPT-058 | REQ-RPT-050 | TC-RPT-072 | API-RPT-002 | none (ADR-RPT-014) | — | F3 |
| AC-RPT-032 | REQ-RPT-027 | TC-RPT-073 | API-RPT-001 | none (ADR-RPT-014) | — | F4 |
| AC-RPT-029 | REQ-RPT-024 | TC-RPT-074 | API-RPT-001 | none (ADR-RPT-014) | — | F4 |

Package → TC: F1: TC-RPT-065, TC-RPT-066, TC-RPT-067 · F2: TC-RPT-068, TC-RPT-069, TC-RPT-070 · F3: TC-RPT-071, TC-RPT-072 · F4: TC-RPT-073, TC-RPT-074 · ALIGN-FE: no_tests (profile)
UXD → TC: none (0 UXD)

## COVERAGE

frontend cases cover 10 ACs (AC-RPT-027 … AC-RPT-035 AC-RPT-058 — as indexed); every AC 64/64 is covered across both plans (backend 64/64) ✓ · SCR covered 0/0 (no RPT screen) · UXD covered 0/0 (none cited) · frontend units covered 4/4 (F1, F2, F3, F4) ✓
