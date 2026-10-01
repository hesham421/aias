<!-- source: PHASE:TEST-PLAN-FE -->
<!-- traces: AC-REG-008, AC-REG-014, AC-REG-015, AC-REG-016, AC-REG-018, AC-REG-081, API-REG-001, API-REG-002, API-REG-003, REQ-REG-008, REQ-REG-013, REQ-REG-014, REQ-REG-015, REQ-REG-017, RULE-REG-016 -->
<!-- PHASE:TEST-PLAN-FE:START traces=AC-REG-008,AC-REG-014,AC-REG-015,AC-REG-016,AC-REG-018,REQ-REG-008,REQ-REG-013,REQ-REG-014,REQ-REG-015,REQ-REG-017,API-REG-001,API-REG-002,API-REG-003,RULE-REG-016,AC-REG-081 -->
## PHASE TEST-PLAN-FE

8 TCs ≤ 8 → no SUB.

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
Expected     : `ServiceSummary` has exactly serviceCode, available, versionNumber, fetchMode, requiredDocumentTypes and approvalEnabled (`available` — ADR-REG-016); the list holds 2 rows; no type or row carries sqlText or a connection field.
Test data    : 2 mock services
<!-- TC:TC-REG-065:END -->

<!-- TC:TC-REG-066:START traces=AC-REG-008,REQ-REG-008,API-REG-003 -->
### TC-REG-066 — Load report rows typed with every field of the latest run, PACKAGE_DIRECTORY included
Derived from : AC-REG-008  (REQ-REG-008)
Exercises    : API-REG-003 GET /api/v1/load-results — F1 `LoadResult` type (no REG screen — ADR-REG-012)
Rule / code  : —
Package      : F1
Scenario     : HAPPY · data class VALID · language en
Preconditions: the mock server serving api-spec-reg.yaml returns a latest run (loadRunAt `2026-10-01T08:00:00Z`) with 3 results: 1 REGISTERED SERVICE_PACKAGE row (serviceCode `scholarship-request`, version 3), 1 REJECTED SERVICE_PACKAGE row, and 1 REJECTED row of subjectKind PACKAGE_DIRECTORY (subjectName `/srv/aias/packages`, serviceCode and versionNumber null — ADR-REG-018).
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. Read API-REG-003 from the mock through LOAD-REPORT-QUERY. 2. Map the response to the `LoadResult` type.
Expected     : The strict `LoadResult` validator accepts all 3 rows, including the PACKAGE_DIRECTORY one; 3 rows, each with loadResultId, loadRunAt (`2026-10-01T08:00:00Z`, the same on every row), subjectKind, subjectName, serviceCode, versionNumber, outcome and reason; the REGISTERED row has serviceCode `scholarship-request` and versionNumber 3; each REJECTED row keeps its reason; a null serviceCode, versionNumber or reason stays null.
Test data    : 1 REGISTERED, 1 REJECTED and 1 PACKAGE_DIRECTORY mock row
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
Expected     : The query returns 2 rows with serviceCode, available = true, versionNumber, fetchMode, requiredDocumentTypes and approvalEnabled under cache key ["reg-services"]; the second run is served from that key.
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
Expected     : The query returns serviceCode `scholarship-request`, available = true, versionNumber 3, fetchMode `path`, requiredDocumentTypes TRANSCRIPT and ID_CARD, approvalEnabled = false, under cache key ["reg-service", "scholarship-request"].
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

<!-- TC:TC-REG-100:START traces=AC-REG-081,REQ-REG-014,API-REG-002 -->
### TC-REG-100 — Read-one-service query keeps available = false for a withdrawn service
Derived from : AC-REG-081  (REQ-REG-014)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode} — F2 SERVICE-QUERY, reused by INT's SCR-REQ-INT-003 (no REG screen — ADR-REG-012)
Rule / code  : —
Package      : F2
Scenario     : EDGE · data class VALID · language en
Preconditions: the mock server serving api-spec-reg.yaml answers API-REG-002 for `vehicle-permit` with 200 and a withdrawn service: available = false, versionNumber 2 (ADR-REG-016).
Host data    : none — the mock server serves the responses of api-spec-reg.yaml; no catalogue value is created
Steps        : 1. Run SERVICE-QUERY with serviceCode `vehicle-permit`. 2. Read the cached entry under ["reg-service", "vehicle-permit"].
Expected     : The query succeeds (not an error) and returns serviceCode `vehicle-permit`, available = false and versionNumber 2; the cached entry under cache key ["reg-service", "vehicle-permit"] holds available = false unchanged.
Test data    : withdrawn service `vehicle-permit`, version 2
<!-- TC:TC-REG-100:END -->

<!-- PHASE:TEST-PLAN-FE:END -->
