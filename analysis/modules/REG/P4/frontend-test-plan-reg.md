# FRONTEND TEST PLAN — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Stage : P4   Track : frontend
Sources: _state/current-srs.md (REG v1) · current-registry-srs.md · current-frontend-execution-plan.md (frontend-execution-plan-reg.md — 0 SCR · 0 UXD; units F1, F2, F3, F4) · current-api-spec.yaml (api-spec-reg.yaml, served by the mock server) · current-backend-execution-plan.md
Framework: agnostic — each TC below is the whole contract; the consumer repository chooses its tool. No framework, annotation or file layout is named.
Open ADRs: none BLOCKED — applied ADR-REG-011, ADR-REG-012
══════════════════════════════════════════════════════════════════

Derivation notes
- REG has no screen (SRS PART B not applicable; the employee frontend's five screens are INT's; administration UI out of scope — ADR-REG-012), so no case navigates a REG screen and no UI flow is fabricated. Each case is derived from an AC whose behaviour the frontend plan carries: the client contract of the three reads (F1 types, F2 read queries, F3 response validators) and the absence of any REG route or write call (F4). Every case runs against the mock server serving api-spec-reg.yaml.
- Package: each case names one frontend split unit — the plan has no SUB, so its units are the phases F1, F2, F3, F4 (ALIGN-FE is `no_tests`).
- The one refusal asserted by its text (TC-REG-069) is bound in the plan: F2 SERVICE-QUERY routes `REG-404-SERVICE-NOT-FOUND` with `text: RULE-REG-016 message (SRS)`. Arabic is PENDING ADR-REG-011.
- 8 cases or fewer → no SUB (threshold > 8); the UI-FLOWS / INT-FLOW grouping is not used. Integration: the plan cites no `UXD-*` (0 UXD), so the INT-UXD phase is absent.
- The cases share the TC sequence with the backend plan (TC-REG-001 … TC-REG-064); this plan continues at TC-REG-065.

<!-- PHASE:TEST-PLAN-FE:START traces=AC-REG-008,AC-REG-014,AC-REG-015,AC-REG-016,AC-REG-018,REQ-REG-008,REQ-REG-013,REQ-REG-014,REQ-REG-015,REQ-REG-017,API-REG-001,API-REG-002,API-REG-003,RULE-REG-016 -->
## PHASE TEST-PLAN-FE

7 TCs ≤ 8 → no SUB.

<!-- TC:TC-REG-065:START traces=AC-REG-014,REQ-REG-013,API-REG-001,API-REG-002 -->
### TC-REG-065 — Client types carry no SQL or connection field
Derived from : AC-REG-014  (REQ-REG-013)
Exercises    : API-REG-001 GET /api/v1/services — F1 client types (no REG screen — ADR-REG-012)
Rule / code  : —
Package      : F1
Scenario     : HAPPY · data class VALID · language en
Preconditions: The frontend's REG client types are generated from api-spec-reg.yaml; the mock server serving api-spec-reg.yaml returns 2 available services.
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. Generate the client types from api-spec-reg.yaml. 2. Inspect the `ServiceSummary` type and the list response of API-REG-001 from the mock.
Expected     : `ServiceSummary` has exactly serviceCode, versionNumber, fetchMode, requiredDocumentTypes and approvalEnabled; the list holds 2 rows; no type or row carries sqlText or a connection field.
Test data    : 2 mock services
<!-- TC:TC-REG-065:END -->

<!-- TC:TC-REG-066:START traces=AC-REG-008,REQ-REG-008,API-REG-003 -->
### TC-REG-066 — Load report rows typed with every field of the latest run
Derived from : AC-REG-008  (REQ-REG-008)
Exercises    : API-REG-003 GET /api/v1/load-results — F1 `LoadResult` type (no REG screen — ADR-REG-012)
Rule / code  : —
Package      : F1
Scenario     : HAPPY · data class VALID · language en
Preconditions: the mock server serving api-spec-reg.yaml returns a latest run with 1 REGISTERED and 1 REJECTED result.
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. Read API-REG-003 from the mock through LOAD-REPORT-QUERY. 2. Map the response to the `LoadResult` type.
Expected     : 2 rows, each with subjectKind, subjectName, versionNumber, outcome and reason; the REJECTED row keeps its reason, a null versionNumber or reason stays null.
Test data    : 1 REGISTERED and 1 REJECTED mock row
<!-- TC:TC-REG-066:END -->

<!-- TC:TC-REG-067:START traces=AC-REG-014,REQ-REG-013,API-REG-001 -->
### TC-REG-067 — List-services query returns the available services
Derived from : AC-REG-014  (REQ-REG-013)
Exercises    : API-REG-001 GET /api/v1/services — F2 SERVICES-QUERY (no REG screen — ADR-REG-012)
Rule / code  : —
Package      : F2
Scenario     : HAPPY · data class VALID · language en
Preconditions: the mock server serving api-spec-reg.yaml answers API-REG-001 with 2 available services (1 withdrawn service is not in the response).
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. Run SERVICES-QUERY. 2. Run it a second time within the cache lifetime.
Expected     : The query returns 2 rows with serviceCode, versionNumber, fetchMode, requiredDocumentTypes and approvalEnabled under cache key ["reg-services"]; the second run is served from that key.
Test data    : 2 mock services
<!-- TC:TC-REG-067:END -->

<!-- TC:TC-REG-068:START traces=AC-REG-015,REQ-REG-014,API-REG-002 -->
### TC-REG-068 — Read-one-service query returns the pilot's summary
Derived from : AC-REG-015  (REQ-REG-014)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode} — F2 SERVICE-QUERY, reused by INT's SCR-REQ-INT-003 (no REG screen — ADR-REG-012)
Rule / code  : —
Package      : F2
Scenario     : HAPPY · data class VALID · language en
Preconditions: the mock server serving api-spec-reg.yaml answers API-REG-002 for `scholarship-request` with current version 3.
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. Run SERVICE-QUERY with serviceCode `scholarship-request`.
Expected     : The query returns serviceCode `scholarship-request`, versionNumber 3, fetchMode `path`, requiredDocumentTypes TRANSCRIPT and ID_CARD, approvalEnabled = false, under cache key ["reg-service", "scholarship-request"].
Test data    : service code `scholarship-request`, version 3
<!-- TC:TC-REG-068:END -->

<!-- TC:TC-REG-069:START traces=AC-REG-016,REQ-REG-015,API-REG-002,RULE-REG-016 -->
### TC-REG-069 — Unknown service routes REG-404-SERVICE-NOT-FOUND to its message
Derived from : AC-REG-016  (REQ-REG-015)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode} — F2 SERVICE-QUERY error routing (no REG screen — ADR-REG-012)
Rule / code  : RULE-REG-016 → REG-404-SERVICE-NOT-FOUND
Package      : F2
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: the mock server serving api-spec-reg.yaml answers API-REG-002 for `unknown-service` with 404 and a ProblemDetail carrying code `REG-404-SERVICE-NOT-FOUND`.
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. Run SERVICE-QUERY with serviceCode `unknown-service`.
Expected     : The query ends in error with code `REG-404-SERVICE-NOT-FOUND`, routed to a user message (no field) whose text is the RULE-REG-016 message — en: "The service "unknown-service" is not available." · ar: PENDING ADR-REG-011.
Test data    : service code `unknown-service`
<!-- TC:TC-REG-069:END -->

<!-- TC:TC-REG-070:START traces=AC-REG-014,REQ-REG-013,API-REG-001,API-REG-002 -->
### TC-REG-070 — Strict response validator refuses a response exposing SQL or a connection setting
Derived from : AC-REG-014  (REQ-REG-013)
Exercises    : API-REG-001 GET /api/v1/services, API-REG-002 — F3 response validators (no REG screen — ADR-REG-012)
Rule / code  : —
Package      : F3
Scenario     : VIOLATION · data class ATTACK · language en
Preconditions: A mock response for API-REG-001 whose second row adds `sqlText` and `endpoint` properties the document does not declare.
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. Run SERVICES-QUERY against that response. 2. Run it against the document's own example response.
Expected     : The strict `ServiceSummary` validator rejects the tampered response and the query ends in the generic server error (nothing of it reaches a consumer); the document's own response passes.
Test data    : tampered properties `sqlText`, `endpoint` (attack fixture)
<!-- TC:TC-REG-070:END -->

<!-- TC:TC-REG-071:START traces=AC-REG-018,REQ-REG-017,API-REG-001,API-REG-002,API-REG-003 -->
### TC-REG-071 — The frontend holds no REG route and no REG write call
Derived from : AC-REG-018  (REQ-REG-017)
Exercises    : F4 — no REG screen, no route (ADR-REG-012); API-REG-001 … API-REG-003 are the only REG operations
Rule / code  : —
Package      : F4
Scenario     : PERMISSION · data class EDGE · language en
Preconditions: The frontend build with REG's client contract (F1–F3).
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. List the routes the frontend registers. 2. List every request the REG client contract can send.
Expected     : No route belongs to REG and no request creates or changes a Service Package, a version or a Connection: the REG client sends only the 3 GET operations API-REG-001, API-REG-002 and API-REG-003.
Test data    : none beyond the build
<!-- TC:TC-REG-071:END -->

<!-- PHASE:TEST-PLAN-FE:END -->

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

### Package → TC
| Package | TCs |
|---|---|
| F1 | TC-REG-065, TC-REG-066 |
| F2 | TC-REG-067, TC-REG-068, TC-REG-069 |
| F3 | TC-REG-070 |
| F4 | TC-REG-071 |

### UXD → TC
None — the frontend plan cites no UXD.

## COVERAGE

| Measure | Covered | Note |
|---|---|---|
| AC (frontend-borne) | 5/5 ✓ | AC-REG-008, AC-REG-014, AC-REG-015, AC-REG-016, AC-REG-018 — the ACs the frontend plan carries; all 64 ACs are covered by the backend plan (TC-REG-001 … TC-REG-064) |
| REQ | 5/5 ✓ | REQ-REG-008, REQ-REG-013, REQ-REG-014, REQ-REG-015, REQ-REG-017 |
| SCR | 0/0 | REG has no screen (ADR-REG-012) |
| UXD | 0/0 | none cited — INT-UXD absent |
| Packages | F1 2 · F2 3 · F3 1 · F4 1 ✓ | ALIGN-FE `no_tests` |

Track TC count 7 ≤ 2× the 5 frontend-borne ACs.
