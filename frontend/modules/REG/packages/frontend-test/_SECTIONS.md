<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND TEST PLAN — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Stage : P4   Track : frontend
Sources: _state/current-srs.md (REG v1) · current-registry-srs.md · current-frontend-execution-plan.md (frontend-execution-plan-reg.md — 0 SCR · 0 UXD; units F1, F2, F3, F4) · current-api-spec.yaml (api-spec-reg.yaml, served by the mock server) · current-backend-execution-plan.md
Framework: agnostic — each TC below is the whole contract; the consumer repository chooses its tool. No framework, annotation or file layout is named.
Open ADRs: none BLOCKED — applied ADR-REG-011, ADR-REG-012, ADR-REG-016, ADR-REG-018, ADR-REG-022
══════════════════════════════════════════════════════════════════

Derivation notes
- REG has no screen (SRS PART B not applicable; the employee frontend's five screens are INT's; administration UI out of scope — ADR-REG-012), so no case navigates a REG screen and no UI flow is fabricated. Each case is derived from an AC whose behaviour the frontend plan carries: the client contract of the three reads (F1 types, F2 read queries, F3 response validators) and the absence of any REG route or write call (F4). Every case runs against the mock server serving api-spec-reg.yaml.
- Package: each case names one frontend split unit — the plan has no SUB, so its units are the phases F1, F2, F3, F4 (ALIGN-FE is `no_tests`).
- The one refusal asserted by its text (TC-REG-069) is bound in the plan: F2 SERVICE-QUERY routes `REG-404-SERVICE-NOT-FOUND` with `text: RULE-REG-016 message (SRS)`. Arabic is PENDING ADR-REG-011.
- 8 cases or fewer → no SUB (threshold > 8); the UI-FLOWS / INT-FLOW grouping is not used. Integration: the plan cites no `UXD-*` (0 UXD), so the INT-UXD phase is absent.
- The cases share the TC sequence with the backend plan (TC-REG-001 … TC-REG-064); this plan continues at TC-REG-065 (TC-REG-065 … TC-REG-071); the backend plan's revision cases continue at TC-REG-072 (TC-REG-072 … TC-REG-099). The round-2 frontend case continues after the highest TC id across both plans: TC-REG-100.



## TC TRACEABILITY INDEX

| TC | AC | REQ | API | SCR | RULE / code | UXD | Package |
|---|---|---|---|---|---|---|---|
| TC-REG-065 | AC-REG-014 | REQ-REG-013 | API-REG-001, API-REG-002 | — (no REG screen) | — | — | F1 |
| TC-REG-066 | AC-REG-008 | REQ-REG-008 | API-REG-003 | — (no REG screen) | — | — | F1 |
| TC-REG-067 | AC-REG-014 | REQ-REG-013 | API-REG-001 | — (no REG screen) | — | — | F2 |
| TC-REG-068 | AC-REG-015 | REQ-REG-014 | API-REG-002 | — (no REG screen) | — | — | F2 |
| TC-REG-069 | AC-REG-016 | REQ-REG-015 | API-REG-002 | — (no REG screen) | RULE-REG-016 → REG-404-SERVICE-NOT-FOUND | — | F2 |
| TC-REG-070 | AC-REG-014 | REQ-REG-013 | API-REG-001, API-REG-002 | — (no REG screen) | — | — | F3 |
| TC-REG-071 | AC-REG-018 | REQ-REG-017 | API-REG-001, API-REG-002, API-REG-003 | — (no REG screen) | — | — | F4 |
| TC-REG-100 | AC-REG-081 | REQ-REG-014 | API-REG-002 | — (no REG screen) | — | — | F2 |

### Package → TC
| Package | TCs |
|---|---|
| F1 | TC-REG-065, TC-REG-066 |
| F2 | TC-REG-067, TC-REG-068, TC-REG-069, TC-REG-100 |
| F3 | TC-REG-070 |
| F4 | TC-REG-071 |

### UXD → TC
None — the frontend plan cites no UXD.

## COVERAGE

| Measure | Covered | Note |
|---|---|---|
| AC (frontend-borne) | 6/6 ✓ | AC-REG-008, AC-REG-014, AC-REG-015, AC-REG-016, AC-REG-018, AC-REG-081 — the ACs the frontend plan carries; all 91 ACs are covered by the backend plan (TC-REG-001 … TC-REG-064, TC-REG-072 … TC-REG-099) |
| REQ | 5/5 ✓ | REQ-REG-008, REQ-REG-013, REQ-REG-014, REQ-REG-015, REQ-REG-017 |
| SCR | 0/0 | REG has no screen (ADR-REG-012) |
| UXD | 0/0 | none cited — INT-UXD absent |
| Packages | F1 2 · F2 4 · F3 1 · F4 1 ✓ | ALIGN-FE `no_tests` |

Track TC count 8 ≤ 2× the 6 frontend-borne ACs.
