<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND TEST PLAN — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module : CHK   Version : v1   Profile : aias   Track : frontend   Plan : test
Sources: _state/current-srs.md (v1) · current-registry-srs.md (v1) · current-frontend-execution-plan.md (v1 — 0 SCR, 0 UXD; F1–F3 bound to API-CHK-001, F4 empty) · current-api-spec.yaml (v1)
TCs    : 6 — TC-CHK-101 … TC-CHK-106 (the sequence continues from the backend plan)
Open ADRs : 0 BLOCKED — ADR-CHK-019 (no CHK screen), ADR-CHK-020 (derivation)
══════════════════════════════════════════════════════════════════

Framework: `profile.stack.testing.frontend` is agnostic. CHK has no screen (ADR-CHK-019), so no TC navigates a CHK
route. Five TCs drive the F2 hook ACTIVE-CHECK-QUERY (with the F1 types and F3 validators) against the mock server
of api-spec-chk.yaml; one structural F4 TC proves CHK registers no route and sends no write call (ADR-CHK-021). The six TCs are under the TEST-PLAN-FE threshold (> 8), so there is no UI-FLOWS / INT-FLOW
SUB. The INT-UXD phase is absent because CHK cites no UXD. No refusal text is asserted: the plan routes
CHK-404-ACTIVE-CHECK-NOT-FOUND to the ended state with no text, and the other codes to the generic error.




## TC TRACEABILITY INDEX

| AC | TC |
|---|---|
| AC-CHK-079 | TC-CHK-101 |
| AC-CHK-080 | TC-CHK-102 |
| AC-CHK-081 | TC-CHK-103, TC-CHK-106 |
| AC-CHK-083 | TC-CHK-104 |
| AC-CHK-085 | TC-CHK-105 |

| REQ | TC |
|---|---|
| REQ-CHK-076 | TC-CHK-101 |
| REQ-CHK-077 | TC-CHK-102 |
| REQ-CHK-078 | TC-CHK-103, TC-CHK-106 |
| REQ-CHK-080 | TC-CHK-104 |
| REQ-CHK-082 | TC-CHK-105 |

| API | TC |
|---|---|
| API-CHK-001 | TC-CHK-101, TC-CHK-102, TC-CHK-103, TC-CHK-104, TC-CHK-105, TC-CHK-106 |

| Rule / code | TC |
|---|---|
| RULE-CHK-009 | TC-CHK-105 |
| — (CHK-404-ACTIVE-CHECK-NOT-FOUND, PLATFORM-STD — routed to the ended state, no text) | TC-CHK-103 |

| Package | TC |
|---|---|
| F1 | TC-CHK-101 |
| F2 | TC-CHK-102, TC-CHK-103, TC-CHK-104 |
| F3 | TC-CHK-105 |
| F4 | TC-CHK-106 |

| SCR | TC |
|---|---|
| none — CHK has no screen (ADR-CHK-019) | — |

| UXD | TC |
|---|---|
| none | — |

## COVERAGE

- AC covered by frontend TCs: 5 (AC-CHK-079, AC-CHK-080, AC-CHK-081, AC-CHK-083, AC-CHK-085 — the ACs API-CHK-001 makes observable; 6 TCs). Module AC coverage is 85/85 with the backend plan, no gap ✗.
- SCR covered: 0/0. UXD covered: 0/0, so the INT-UXD phase is absent.
- Packages with acceptance: F1, F2, F3, F4. ALIGN-FE is `no_tests`. F4 holds no screen and no route (ADR-CHK-019); its acceptance is the structural TC proving exactly that: no CHK route and no write call, with API-CHK-001 read only through the F2 hook (ADR-CHK-021).
