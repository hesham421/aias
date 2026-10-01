# BACKEND TEST PLAN — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Stage : P4   Track : backend
Sources: _state/current-srs.md (REG v1 — REQ 69 · AC 81 · RULE 24) · current-registry-srs.md · current-registry-db.md (0 XM) · current-backend-execution-plan.md (API-REG-001 … API-REG-003; units PORTS, SVC-API) · current-api-spec.yaml (api-spec-reg.yaml) · current-frontend-execution-plan.md · current-dependency-graph.md
Framework: agnostic — each TC below is the whole contract; the consumer repository chooses its tool. No framework, annotation or file layout is named.
Open ADRs: none BLOCKED — applied ADR-REG-003, ADR-REG-007, ADR-REG-009, ADR-REG-011, ADR-REG-012, ADR-REG-015, ADR-REG-016, ADR-REG-017, ADR-REG-018, ADR-REG-019
══════════════════════════════════════════════════════════════════

Derivation notes
- One TC per AC, mechanically (TC-REG-nnn ↔ AC-REG-nnn for nnn = 001 … 064; TC-REG-065 … TC-REG-071 are the frontend track's). The ACs added by the gate-analysis revision (AC-REG-065 … AC-REG-081) map to TC-REG-072 … TC-REG-088 in AC order (TC = AC + 7). No happy-path twin and no boundary case was added beyond what an AC states.
- REG has no HTTP write operation (ADR-REG-007): load-time outcomes are observed through the load report (API-REG-003) and the reads (API-REG-001, API-REG-002); in-process outcomes through the `ServiceRegistry` / `ApprovalApiRegistry` interfaces the SVC-API phase specifies (ADR-REG-011). Shapes are read in api-spec-reg.yaml by `API-*` id.
- Load-time refusals are Load Result reasons behind `REG-LOAD-*` codes, not HTTP errors (ADR-REG-011); the only HTTP refusal is `REG-404-SERVICE-NOT-FOUND` (RULE-REG-016). Messages are the SRS English text, placeholders filled with the AC's values; Arabic is PENDING ADR-REG-011 (the SRS carries English only).
- Package: every TC names the backend split unit that implements its REQ — `PORTS` for the package directory and activation sources (REQ-REG-005, REQ-REG-016, REQ-REG-049), `SVC-API` for the load run, the reads and the in-process interface. CORE, DATA-DOM and ALIGN-BE are `no_tests`; CROSS-MOD has no edge (0 XM).
- Integration: REG declares no `XM-*` (registry-db: 0 XM; CROSS-MOD "No edge"), so the INT-XM phase is absent.

<!-- PHASE:TEST-PLAN-BE:START traces=AC-REG-001,AC-REG-002,AC-REG-003,AC-REG-004,AC-REG-005,AC-REG-006,AC-REG-007,AC-REG-008,AC-REG-009,AC-REG-010,AC-REG-011,AC-REG-012,AC-REG-013,AC-REG-014,AC-REG-015,AC-REG-016,AC-REG-017,AC-REG-018,AC-REG-019,AC-REG-020,AC-REG-021,AC-REG-022,AC-REG-023,AC-REG-024,AC-REG-025,AC-REG-026,AC-REG-027,AC-REG-028,AC-REG-029,AC-REG-030,AC-REG-031,AC-REG-032,AC-REG-033,AC-REG-034,AC-REG-035,AC-REG-036,AC-REG-037,AC-REG-038,AC-REG-039,AC-REG-040,AC-REG-041,AC-REG-042,AC-REG-043,AC-REG-044,AC-REG-045,AC-REG-046,AC-REG-047,AC-REG-048,AC-REG-049,AC-REG-050,AC-REG-051,AC-REG-052,AC-REG-053,AC-REG-054,AC-REG-055,AC-REG-056,AC-REG-057,AC-REG-058,AC-REG-059,AC-REG-060,AC-REG-061,AC-REG-062,AC-REG-063,AC-REG-064,REQ-REG-001,REQ-REG-002,REQ-REG-003,REQ-REG-004,REQ-REG-005,REQ-REG-006,REQ-REG-007,REQ-REG-008,REQ-REG-009,REQ-REG-010,REQ-REG-011,REQ-REG-012,REQ-REG-013,REQ-REG-014,REQ-REG-015,REQ-REG-016,REQ-REG-017,REQ-REG-018,REQ-REG-019,REQ-REG-020,REQ-REG-021,REQ-REG-022,REQ-REG-023,REQ-REG-024,REQ-REG-025,REQ-REG-026,REQ-REG-027,REQ-REG-028,REQ-REG-029,REQ-REG-030,REQ-REG-031,REQ-REG-032,REQ-REG-033,REQ-REG-034,REQ-REG-035,REQ-REG-036,REQ-REG-037,REQ-REG-038,REQ-REG-039,REQ-REG-040,REQ-REG-041,REQ-REG-042,REQ-REG-043,REQ-REG-044,REQ-REG-045,REQ-REG-046,REQ-REG-047,REQ-REG-048,REQ-REG-049,REQ-REG-050,REQ-REG-051,REQ-REG-052,REQ-REG-053,REQ-REG-054,REQ-REG-055,REQ-REG-056,REQ-REG-057,REQ-REG-058,REQ-REG-059,REQ-REG-060,REQ-REG-061,REQ-REG-062,API-REG-001,API-REG-002,API-REG-003,RULE-REG-001,RULE-REG-002,RULE-REG-003,RULE-REG-004,RULE-REG-005,RULE-REG-006,RULE-REG-007,RULE-REG-008,RULE-REG-009,RULE-REG-010,RULE-REG-011,RULE-REG-012,RULE-REG-013,RULE-REG-014,RULE-REG-015,RULE-REG-016,RULE-REG-017,RULE-REG-018,RULE-REG-019,RULE-REG-020,AC-REG-065,REQ-REG-063,RULE-REG-021,AC-REG-066,REQ-REG-064,AC-REG-067,AC-REG-068,AC-REG-069,REQ-REG-065,RULE-REG-022,AC-REG-070,REQ-REG-066,RULE-REG-023,AC-REG-071,AC-REG-072,REQ-REG-067,AC-REG-073,REQ-REG-068,AC-REG-074,REQ-REG-069,RULE-REG-024,AC-REG-075,AC-REG-076,AC-REG-077,AC-REG-078,AC-REG-079,AC-REG-080,AC-REG-081 -->
## PHASE TEST-PLAN-BE

81 TCs > 12 → grouped RULE-SCENARIOS / API-SCENARIOS / MODEL-EVAL.

<!-- SUB:RULE-SCENARIOS:START traces=AC-REG-003,AC-REG-004,AC-REG-007,AC-REG-012,AC-REG-013,AC-REG-016,AC-REG-022,AC-REG-023,AC-REG-029,AC-REG-032,AC-REG-033,AC-REG-034,AC-REG-035,AC-REG-036,AC-REG-037,AC-REG-040,AC-REG-041,AC-REG-042,AC-REG-043,AC-REG-045,AC-REG-049,AC-REG-054,AC-REG-055,AC-REG-057,AC-REG-063,AC-REG-064,REQ-REG-003,REQ-REG-004,REQ-REG-007,REQ-REG-012,REQ-REG-015,REQ-REG-021,REQ-REG-022,REQ-REG-028,REQ-REG-031,REQ-REG-032,REQ-REG-033,REQ-REG-034,REQ-REG-035,REQ-REG-038,REQ-REG-039,REQ-REG-040,REQ-REG-041,REQ-REG-043,REQ-REG-047,REQ-REG-052,REQ-REG-053,REQ-REG-055,REQ-REG-061,REQ-REG-062,API-REG-002,API-REG-003,RULE-REG-001,RULE-REG-002,RULE-REG-003,RULE-REG-004,RULE-REG-005,RULE-REG-006,RULE-REG-007,RULE-REG-008,RULE-REG-009,RULE-REG-010,RULE-REG-011,RULE-REG-012,RULE-REG-013,RULE-REG-014,RULE-REG-015,RULE-REG-016,RULE-REG-017,RULE-REG-018,RULE-REG-019,RULE-REG-020,AC-REG-065,REQ-REG-063,RULE-REG-021,AC-REG-067,REQ-REG-064,AC-REG-069,REQ-REG-065,RULE-REG-022,AC-REG-070,REQ-REG-066,API-REG-001,RULE-REG-023,AC-REG-071,AC-REG-074,REQ-REG-069,RULE-REG-024,AC-REG-075,AC-REG-076,AC-REG-077,AC-REG-078,AC-REG-079,AC-REG-080 -->
## SUB RULE-SCENARIOS

Rule-driven acceptance — every load-time and read-time refusal, asserted through the load report (API-REG-003), API-REG-002 or the in-process interface. (38 TCs)

<!-- TC:TC-REG-003:START traces=AC-REG-003,REQ-REG-003,API-REG-003,RULE-REG-001 -->
### TC-REG-003 — Folder with only a service definition is rejected
Derived from : AC-REG-003  (REQ-REG-003)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-001 → REG-LOAD-INCOMPLETE-PACKAGE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The package directory holds the folder `demo-service` with only a service definition file.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected and no version is stored for it (API-REG-002 GET /api/v1/services/demo-service → 404); the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-001 message (en) «The service package "demo-service" is incomplete: it needs both its service knowledge and its service definition.» (template: «The service package "{folder}" is incomplete: it needs both its service knowledge and its service definition.»; ar: PENDING ADR-REG-011).
Test data    : folder `demo-service` (definition file only)
<!-- TC:TC-REG-003:END -->

<!-- TC:TC-REG-004:START traces=AC-REG-004,REQ-REG-004,API-REG-003,API-REG-002,RULE-REG-002 -->
### TC-REG-004 — Two folders declaring one service code are both rejected
Derived from : AC-REG-004  (REQ-REG-004)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-002 → REG-LOAD-DUPLICATE-SERVICE-CODE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: Version 3 of `scholarship-request` is stored and current. Folders `a` and `b` both declare service code `scholarship-request` and version 4.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Read the service with API-REG-002 GET /api/v1/services/scholarship-request.
Expected     : Both packages are rejected: the load report holds 2 REJECTED rows (subjects `a` and `b`), each with the reason RULE-REG-002 (en) «The service code "scholarship-request" is declared by more than one package folder; keep one folder per service.» (ar: PENDING ADR-REG-011); API-REG-002 returns versionNumber 3 (version 3 stays current).
Test data    : folders `a`, `b`; service code `scholarship-request`; versions 3 and 4
<!-- TC:TC-REG-004:END -->

<!-- TC:TC-REG-007:START traces=AC-REG-007,REQ-REG-007,API-REG-003,RULE-REG-008 -->
### TC-REG-007 — A rejected folder is reported and loading continues
Derived from : AC-REG-007  (REQ-REG-007)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-008 → REG-LOAD-UNKNOWN-FETCH-MODE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The package directory holds 2 valid folders and 1 folder whose service definition names fetch mode `fax`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : 2 packages are stored and the third is rejected; the load report records 3 rows: 2 REGISTERED and 1 REJECTED with the reason RULE-REG-008 (en) «The fetch mode "fax" is not supported; use path, blob or manual.» (ar: PENDING ADR-REG-011).
Test data    : 2 valid fixture folders; 1 folder with `fetch: fax`
<!-- TC:TC-REG-007:END -->

<!-- TC:TC-REG-012:START traces=AC-REG-012,REQ-REG-012,RULE-REG-016 -->
### TC-REG-012 — No package is supplied for a withdrawn service
Derived from : AC-REG-012  (REQ-REG-012)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : RULE-REG-016 → ServiceNotAvailableException (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: Service Package `vehicle-permit` is withdrawn.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package of `vehicle-permit` through the in-process `getCurrentServicePackage("vehicle-permit")`.
Expected     : No package is supplied; the refusal carries the RULE-REG-016 message (en) «The service "vehicle-permit" is not available.» (ar: PENDING ADR-REG-011).
Test data    : service code `vehicle-permit`
<!-- TC:TC-REG-012:END -->

<!-- TC:TC-REG-013:START traces=AC-REG-013,REQ-REG-012,RULE-REG-016 -->
### TC-REG-013 — No package is supplied for an unknown service
Derived from : AC-REG-013  (REQ-REG-012)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : RULE-REG-016 → ServiceNotAvailableException (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The registry holds no service code `unknown-service`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package of `unknown-service` through the in-process `getCurrentServicePackage("unknown-service")`.
Expected     : No package is supplied; the refusal carries the RULE-REG-016 message (en) «The service "unknown-service" is not available.» (ar: PENDING ADR-REG-011).
Test data    : service code `unknown-service`
<!-- TC:TC-REG-013:END -->

<!-- TC:TC-REG-016:START traces=AC-REG-016,REQ-REG-015,API-REG-002,RULE-REG-016 -->
### TC-REG-016 — Read of an unknown service returns 404
Derived from : AC-REG-016  (REQ-REG-015)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : RULE-REG-016 → REG-404-SERVICE-NOT-FOUND
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The registry holds no service code `unknown-service`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Read the service with API-REG-002 GET /api/v1/services/unknown-service.
Expected     : 404 with a ProblemDetail body {type, title, status: 404, detail, code: `REG-404-SERVICE-NOT-FOUND`}; detail carries the RULE-REG-016 message (en) «The service "unknown-service" is not available.» (ar: PENDING ADR-REG-011).
Test data    : service code `unknown-service`
<!-- TC:TC-REG-016:END -->

<!-- TC:TC-REG-022:START traces=AC-REG-022,REQ-REG-021,API-REG-003,RULE-REG-003 -->
### TC-REG-022 — Same version number with changed content is rejected (never edited in place)
Derived from : AC-REG-022  (REQ-REG-021)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-003 → REG-LOAD-VERSION-EDITED-IN-PLACE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: Version 3 of `scholarship-request` is stored with contentHash `h1`; the folder declares version 3 with content whose hash is `h2`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Resolve version 3 through the in-process `getServicePackageVersion("scholarship-request", 3)`.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-003 message (en) «Version 3 of "scholarship-request" already exists with different content; publish the change as a new version.» (template: «Version {versionNumber} of "{serviceCode}" already exists with different content; publish the change as a new version.»; ar: PENDING ADR-REG-011); version 3 keeps contentHash `h1` and its stored texts unchanged.
Test data    : contentHash `h1` / `h2`
<!-- TC:TC-REG-022:END -->

<!-- TC:TC-REG-023:START traces=AC-REG-023,REQ-REG-022,API-REG-003,API-REG-002,RULE-REG-004 -->
### TC-REG-023 — A lower, unstored version is rejected
Derived from : AC-REG-023  (REQ-REG-022)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-004 → REG-LOAD-VERSION-OLDER-THAN-CURRENT (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: `scholarship-request` has current version 3 and no stored version 2; the folder declares version 2.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Read the service with API-REG-002 GET /api/v1/services/scholarship-request.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-004 message (en) «Version 2 of "scholarship-request" is older than the current version 3.» (template: «Version {versionNumber} of "{serviceCode}" is older than the current version {currentVersion}.»; ar: PENDING ADR-REG-011); API-REG-002 returns versionNumber 3.
Test data    : versions 2 and 3 of `scholarship-request`
<!-- TC:TC-REG-023:END -->

<!-- TC:TC-REG-029:START traces=AC-REG-029,REQ-REG-028,API-REG-003,RULE-REG-018 -->
### TC-REG-029 — Empty service knowledge is rejected
Derived from : AC-REG-029  (REQ-REG-028)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-018 → REG-LOAD-EMPTY-SERVICE-KNOWLEDGE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class BOUNDARY · language en
Preconditions: A package folder whose knowledge file has 0 characters (service code `demo-service`).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-018 message (en) «The service knowledge of "demo-service" is empty.» (template: «The service knowledge of "{serviceCode}" is empty.»; ar: PENDING ADR-REG-011).
Test data    : knowledge file of 0 characters
<!-- TC:TC-REG-029:END -->

<!-- TC:TC-REG-032:START traces=AC-REG-032,REQ-REG-031,API-REG-003,RULE-REG-005 -->
### TC-REG-032 — A query naming an unregistered connection is rejected
Derived from : AC-REG-032  (REQ-REG-031)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-005 → REG-LOAD-CONNECTION-NOT-ACTIVATED (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The environment registers connection `main-db` only; a folder's query `request_details` names connection `archive-db`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-005 message (en) «The query "request_details" uses the connection "archive-db", which is not activated in this environment.» (template: «The query "{queryName}" uses the connection "{connectionName}", which is not activated in this environment.»; ar: PENDING ADR-REG-011).
Test data    : connections `main-db`, `archive-db`
<!-- TC:TC-REG-032:END -->

<!-- TC:TC-REG-033:START traces=AC-REG-033,REQ-REG-032,API-REG-003,RULE-REG-006 -->
### TC-REG-033 — A query using another parameter than the declared input is rejected
Derived from : AC-REG-033  (REQ-REG-032)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-006 → REG-LOAD-UNBOUND-PARAMETER (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class ATTACK · language en
Preconditions: A service definition declares input `requestId`; its query `request_details` contains `:studentId`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-006 message (en) «The query "request_details" uses ":studentId"; queries take only the bind parameter ":requestId".» (template: «The query "{queryName}" uses "{marker}"; queries take only the bind parameter ":{inputName}".»; ar: PENDING ADR-REG-011).
Test data    : input `requestId`; marker `:studentId`
<!-- TC:TC-REG-033:END -->

<!-- TC:TC-REG-034:START traces=AC-REG-034,REQ-REG-032,API-REG-003,RULE-REG-006 -->
### TC-REG-034 — A query using a substitution marker is rejected
Derived from : AC-REG-034  (REQ-REG-032)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-006 → REG-LOAD-UNBOUND-PARAMETER (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class ATTACK · language en
Preconditions: A service definition declares input `requestId`; its query `request_details` contains `${requestId}`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-006 message (en) «The query "request_details" uses "${requestId}"; queries take only the bind parameter ":requestId".» (template: «The query "{queryName}" uses "{marker}"; queries take only the bind parameter ":{inputName}".»; ar: PENDING ADR-REG-011).
Test data    : input `requestId`; marker `${requestId}`
<!-- TC:TC-REG-034:END -->

<!-- TC:TC-REG-035:START traces=AC-REG-035,REQ-REG-033,API-REG-003,RULE-REG-007 -->
### TC-REG-035 — A query that is not a single SELECT is rejected
Derived from : AC-REG-035  (REQ-REG-033)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-007 → REG-LOAD-NOT-SINGLE-SELECT (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class ATTACK · language en
Preconditions: A folder whose query `request_details` text is `UPDATE requests SET status = 'A'`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-007 message (en) «The query "request_details" must be one SELECT statement; it cannot change data or run several statements.» (template: «The query "{queryName}" must be one SELECT statement; it cannot change data or run several statements.»; ar: PENDING ADR-REG-011).
Test data    : sql `UPDATE requests SET status = 'A'`
<!-- TC:TC-REG-035:END -->

<!-- TC:TC-REG-036:START traces=AC-REG-036,REQ-REG-034,API-REG-003,RULE-REG-012 -->
### TC-REG-036 — A service definition declaring a check limit is rejected
Derived from : AC-REG-036  (REQ-REG-034)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-012 → REG-LOAD-ELEMENT-NOT-ALLOWED (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder (service code `demo-service`) whose service definition declares `max_rows: 5000`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-012 message (en) «The service definition of "demo-service" contains "max_rows", which a service definition cannot set.» (template: «The service definition of "{serviceCode}" contains "{element}", which a service definition cannot set.»; ar: PENDING ADR-REG-011).
Test data    : `max_rows: 5000`
<!-- TC:TC-REG-036:END -->

<!-- TC:TC-REG-037:START traces=AC-REG-037,REQ-REG-035,API-REG-003,RULE-REG-019 -->
### TC-REG-037 — Two queries with one name are rejected
Derived from : AC-REG-037  (REQ-REG-035)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-019 → REG-LOAD-DUPLICATE-QUERY-NAME (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A service definition (service code `demo-service`) with 2 queries named `attachments`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-019 message (en) «The query name "attachments" is used twice in "demo-service".» (template: «The query name "{queryName}" is used twice in "{serviceCode}".»; ar: PENDING ADR-REG-011).
Test data    : 2 queries named `attachments`
<!-- TC:TC-REG-037:END -->

<!-- TC:TC-REG-040:START traces=AC-REG-040,REQ-REG-038,API-REG-003,RULE-REG-008 -->
### TC-REG-040 — An unknown fetch mode is rejected
Derived from : AC-REG-040  (REQ-REG-038)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-008 → REG-LOAD-UNKNOWN-FETCH-MODE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder whose service definition declares `fetch: fax`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-008 message (en) «The fetch mode "fax" is not supported; use path, blob or manual.» (template: «The fetch mode "{fetchMode}" is not supported; use path, blob or manual.»; ar: PENDING ADR-REG-011).
Test data    : `fetch: fax`
<!-- TC:TC-REG-040:END -->

<!-- TC:TC-REG-041:START traces=AC-REG-041,REQ-REG-039,API-REG-003,RULE-REG-009 -->
### TC-REG-041 — Fetch mode path without a path column is rejected
Derived from : AC-REG-041  (REQ-REG-039)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-009 → REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder (service code `demo-service`) with `fetch: path` and no `path_column`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-009 message (en) «The documents of "demo-service" cannot be fetched: the document source is incomplete.» (template: «The documents of "{serviceCode}" cannot be fetched: the document source is incomplete.»; ar: PENDING ADR-REG-011).
Test data    : `fetch: path`, no `path_column`
<!-- TC:TC-REG-041:END -->

<!-- TC:TC-REG-042:START traces=AC-REG-042,REQ-REG-040,API-REG-003,RULE-REG-010 -->
### TC-REG-042 — Blob documents over an mcp connection are rejected
Derived from : AC-REG-042  (REQ-REG-040)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-010 → REG-LOAD-BLOB-NOT-JDBC (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: Connection `main-db` is of type `mcp`; a folder with `fetch: blob` names a document source query on `main-db`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-010 message (en) «Documents stored in the database are read over a read-only jdbc connection; "main-db" is not one.» (template: «Documents stored in the database are read over a read-only jdbc connection; "{connectionName}" is not one.»; ar: PENDING ADR-REG-011).
Test data    : connection `main-db` (mcp); `fetch: blob`
<!-- TC:TC-REG-042:END -->

<!-- TC:TC-REG-043:START traces=AC-REG-043,REQ-REG-041,API-REG-003,RULE-REG-012 -->
### TC-REG-043 — A service definition declaring a file location is rejected
Derived from : AC-REG-043  (REQ-REG-041)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-012 → REG-LOAD-ELEMENT-NOT-ALLOWED (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class ATTACK · language en
Preconditions: A folder (service code `demo-service`) whose service definition declares `storage_root: /data/files`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected and no file location is stored: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-012 message (en) «The service definition of "demo-service" contains "storage_root", which a service definition cannot set.» (template: «The service definition of "{serviceCode}" contains "{element}", which a service definition cannot set.»; ar: PENDING ADR-REG-011).
Test data    : `storage_root: /data/files`
<!-- TC:TC-REG-043:END -->

<!-- TC:TC-REG-045:START traces=AC-REG-045,REQ-REG-043,API-REG-003,RULE-REG-011 -->
### TC-REG-045 — Enabled approval without a definition is rejected
Derived from : AC-REG-045  (REQ-REG-043)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-011 → REG-LOAD-APPROVAL-API-UNDEFINED (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder (service code `demo-service`) with `approval.enabled: true` and no `api`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-011 message (en) «The approval API of "demo-service" is enabled but not defined.» (template: «The approval API of "{serviceCode}" is enabled but not defined.»; ar: PENDING ADR-REG-011).
Test data    : `approval.enabled: true`, no `api`
<!-- TC:TC-REG-045:END -->

<!-- TC:TC-REG-049:START traces=AC-REG-049,REQ-REG-047,API-REG-003,RULE-REG-013 -->
### TC-REG-049 — A connection name listed twice is refused
Derived from : AC-REG-049  (REQ-REG-047)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-013 → REG-LOAD-DUPLICATE-CONNECTION-NAME (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The activation configuration lists `main-db` twice.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : 0 Connections named `main-db` are registered and the load report records 2 REJECTED CONNECTION rows, each with the reason RULE-REG-013 (en) «The connection name "main-db" is listed more than once.» (ar: PENDING ADR-REG-011).
Test data    : `main-db` listed twice
<!-- TC:TC-REG-049:END -->

<!-- TC:TC-REG-054:START traces=AC-REG-054,REQ-REG-052,API-REG-003,RULE-REG-014 -->
### TC-REG-054 — An unknown connection type is refused
Derived from : AC-REG-054  (REQ-REG-052)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-014 → REG-LOAD-UNKNOWN-CONNECTION-TYPE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The activation configuration lists `ftp-db` of type `ftp`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : `ftp-db` is not registered and the load report records REJECTED with the reason RULE-REG-014 (en) «The connection "ftp-db" has the type "ftp"; use mcp or jdbc.» (ar: PENDING ADR-REG-011).
Test data    : connection `ftp-db`, type `ftp`
<!-- TC:TC-REG-054:END -->

<!-- TC:TC-REG-055:START traces=AC-REG-055,REQ-REG-053,RULE-REG-017 -->
### TC-REG-055 — A version naming a removed connection is not supplied
Derived from : AC-REG-055  (REQ-REG-053)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : RULE-REG-017 → ServiceConnectionNotActivatedException (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The current version of `scholarship-request` names `main-db` in a query and `main-db` was removed at activation.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : No package is supplied; the refusal carries the RULE-REG-017 message (en) «The service "scholarship-request" uses the connection "main-db", which is not activated in this environment.» (ar: PENDING ADR-REG-011).
Test data    : service code `scholarship-request`; connection `main-db` removed
<!-- TC:TC-REG-055:END -->

<!-- TC:TC-REG-057:START traces=AC-REG-057,REQ-REG-055,API-REG-003,RULE-REG-015 -->
### TC-REG-057 — A connection not declared read-only is refused
Derived from : AC-REG-057  (REQ-REG-055)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-015 → REG-LOAD-CONNECTION-NOT-READ-ONLY (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The activation configuration lists `main-db` with readOnly = false.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : `main-db` is not registered and the load report records REJECTED with the reason RULE-REG-015 (en) «The connection "main-db" must use a read-only database user.» (ar: PENDING ADR-REG-011).
Test data    : `main-db` readOnly = false
<!-- TC:TC-REG-057:END -->

<!-- TC:TC-REG-063:START traces=AC-REG-063,REQ-REG-061,API-REG-003,RULE-REG-016 -->
### TC-REG-063 — A failing pilot package is reported and never supplied
Derived from : AC-REG-063  (REQ-REG-061)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-016 → ServiceNotAvailableException (in-process)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The delivered `scholarship-request` folder declares `fetch: fax` and no version of it is stored.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The load report records outcome REJECTED for `scholarship-request` (reason: the RULE-REG-008 message), and the Check receives the RULE-REG-016 message (en) «The service "scholarship-request" is not available.» (ar: PENDING ADR-REG-011).
Test data    : `scholarship-request` folder with `fetch: fax`
<!-- TC:TC-REG-063:END -->

<!-- TC:TC-REG-064:START traces=AC-REG-064,REQ-REG-062,API-REG-003,RULE-REG-020 -->
### TC-REG-064 — A package folder holding another file is rejected
Derived from : AC-REG-064  (REQ-REG-062)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-020 → REG-LOAD-FOREIGN-FILE-IN-PACKAGE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class ATTACK · language en
Preconditions: A package folder `demo-service` that also holds `request-4711.pdf`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected: the load report records one Load Result row for the folder with outcome REJECTED and reason equal to the RULE-REG-020 message (en) «The package folder "demo-service" holds "request-4711.pdf"; a package folder holds only its service knowledge and its service definition.» (template: «The package folder "{folder}" holds "{file}"; a package folder holds only its service knowledge and its service definition.»; ar: PENDING ADR-REG-011).
Test data    : folder `demo-service`; file `request-4711.pdf`
<!-- TC:TC-REG-064:END -->

<!-- TC:TC-REG-072:START traces=AC-REG-065,REQ-REG-063,API-REG-003,API-REG-002,RULE-REG-021 -->
### TC-REG-072 — A required document type declared twice is rejected
Derived from : AC-REG-065  (REQ-REG-063)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-021 → REG-LOAD-DUPLICATE-DOCUMENT-TYPE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder `demo-service` whose service definition declares `required: [TRANSCRIPT, ID_CARD, TRANSCRIPT]`; no version of `demo-service` is stored.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected and no version is stored (API-REG-002 GET /api/v1/services/demo-service → 404); the load report records one row for the folder with outcome REJECTED and reason equal to the RULE-REG-021 message (en) «The document type "TRANSCRIPT" is required more than once in "demo-service".» (template: «The document type "{documentType}" is required more than once in "{serviceCode}".»; ar: PENDING ADR-REG-011). No database constraint error surfaces.
Test data    : folder `demo-service`; required TRANSCRIPT, ID_CARD, TRANSCRIPT
<!-- TC:TC-REG-072:END -->

<!-- TC:TC-REG-074:START traces=AC-REG-067,REQ-REG-064,API-REG-003,RULE-REG-002 -->
### TC-REG-074 — Two folders whose codes differ only in case are both rejected
Derived from : AC-REG-067  (REQ-REG-064)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-002 → REG-LOAD-DUPLICATE-SERVICE-CODE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: Folder `a` declares service code `Scholarship-Request`, folder `b` declares `scholarship-request`; both otherwise valid.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : Both packages are rejected: 2 REJECTED rows (subjects `a` and `b`), each with the RULE-REG-002 message (en) «The service code "scholarship-request" is declared by more than one package folder; keep one folder per service.» (ar: PENDING ADR-REG-011).
Test data    : folders `a`, `b`; codes `Scholarship-Request`, `scholarship-request`
<!-- TC:TC-REG-074:END -->

<!-- TC:TC-REG-076:START traces=AC-REG-069,REQ-REG-065,API-REG-003,RULE-REG-022 -->
### TC-REG-076 — An invalid service code is rejected
Derived from : AC-REG-069  (REQ-REG-065)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-022 → REG-LOAD-INVALID-SERVICE-CODE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder whose service definition declares service code `scholarship request!`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected and no Service Package is created; the load report records one row with outcome REJECTED and reason equal to the RULE-REG-022 message (en) «The service code "scholarship request!" is not valid; use lower-case letters, digits and single hyphens, at most 100 characters.» (ar: PENDING ADR-REG-011).
Test data    : service code `scholarship request!` (also per Test-Hint: `a_b`, `-a`, `a--b`, a 101-character code)
<!-- TC:TC-REG-076:END -->

<!-- TC:TC-REG-077:START traces=AC-REG-070,REQ-REG-066,API-REG-003,API-REG-001,RULE-REG-023 -->
### TC-REG-077 — A missing package directory withdraws nothing
Derived from : AC-REG-070  (REQ-REG-066)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-023 → REG-LOAD-PACKAGE-DIRECTORY-UNAVAILABLE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The registry holds 2 available Service Packages (`scholarship-request`, `vehicle-permit`); `aias.registry.package-directory` = `/srv/aias/packages`, which does not exist.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : Both Service Packages stay available (API-REG-001 returns 2 rows, each available = true); 0 versions are stored; the load report holds 1 package row: subjectKind PACKAGE_DIRECTORY, subjectName `/srv/aias/packages`, outcome REJECTED, reason equal to the RULE-REG-023 message (en) «The package directory "/srv/aias/packages" is missing, unreadable or empty; no service was loaded or withdrawn.» (ar: PENDING ADR-REG-011); no WITHDRAWN row.
Test data    : directory `/srv/aias/packages` (absent); 2 stored services
<!-- TC:TC-REG-077:END -->

<!-- TC:TC-REG-078:START traces=AC-REG-071,REQ-REG-066,API-REG-003,API-REG-001,RULE-REG-023 -->
### TC-REG-078 — An empty package directory withdraws nothing while services are available
Derived from : AC-REG-071  (REQ-REG-066)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-023 → REG-LOAD-PACKAGE-DIRECTORY-UNAVAILABLE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The registry holds 2 available Service Packages; `aias.registry.package-directory` = `/srv/aias/packages` exists and holds no folder.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : Both Service Packages stay available (API-REG-001 returns 2 rows); the load report holds 1 package row: subjectKind PACKAGE_DIRECTORY, outcome REJECTED, reason the RULE-REG-023 message with {directory} = `/srv/aias/packages`; no WITHDRAWN row.
Test data    : empty directory `/srv/aias/packages`; 2 stored services
<!-- TC:TC-REG-078:END -->

<!-- TC:TC-REG-081:START traces=AC-REG-074,REQ-REG-069,API-REG-003,API-REG-002,RULE-REG-024 -->
### TC-REG-081 — A package file that changes while it is read is rejected
Derived from : AC-REG-074  (REQ-REG-069)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-024 → REG-LOAD-PACKAGE-CHANGED-DURING-READ (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The registry holds version 3 of `scholarship-request`; the test `PackageSource` adapter stub reports the service definition file of folder `scholarship-request` as 2048 bytes before and 4096 bytes after its read (declared version 4).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected; no version 4 is stored (API-REG-002 returns versionNumber 3); the load report records outcome REJECTED with the RULE-REG-024 message (en) «The package folder "scholarship-request" changed while it was being read; publish it again and restart.» (ar: PENDING ADR-REG-011).
Test data    : sizes 2048 → 4096 bytes
<!-- TC:TC-REG-081:END -->

<!-- TC:TC-REG-082:START traces=AC-REG-075,REQ-REG-039,API-REG-003,RULE-REG-009 -->
### TC-REG-082 — Fetch mode blob without a content column is rejected
Derived from : AC-REG-075  (REQ-REG-039)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-009 → REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder (service code `demo-service`) with `fetch: blob`, a document source query on the `jdbc` connection `docs-jdbc` and no `content_column`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected with the RULE-REG-009 message (en) «The documents of "demo-service" cannot be fetched: the document source is incomplete.» (ar: PENDING ADR-REG-011).
Test data    : `fetch: blob`, no `content_column`
<!-- TC:TC-REG-082:END -->

<!-- TC:TC-REG-083:START traces=AC-REG-076,REQ-REG-039,API-REG-003,RULE-REG-009 -->
### TC-REG-083 — Fetch mode path without a type column is rejected
Derived from : AC-REG-076  (REQ-REG-039)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-009 → REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder (service code `demo-service`) with `fetch: path`, a `path_column` and no `type_column`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected with the RULE-REG-009 message (en) «The documents of "demo-service" cannot be fetched: the document source is incomplete.» (ar: PENDING ADR-REG-011).
Test data    : `fetch: path`, no `type_column`
<!-- TC:TC-REG-083:END -->

<!-- TC:TC-REG-084:START traces=AC-REG-077,REQ-REG-039,API-REG-003,RULE-REG-009 -->
### TC-REG-084 — A document source naming an undeclared query is rejected
Derived from : AC-REG-077  (REQ-REG-039)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-009 → REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder (service code `demo-service`) with `fetch: path` whose `documents.source` names the query `files`, while its service definition declares only the query `request_details`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected with the RULE-REG-009 message (en) «The documents of "demo-service" cannot be fetched: the document source is incomplete.» (ar: PENDING ADR-REG-011).
Test data    : source `files`; declared query `request_details`
<!-- TC:TC-REG-084:END -->

<!-- TC:TC-REG-085:START traces=AC-REG-078,REQ-REG-007,API-REG-003,API-REG-002,RULE-REG-015,RULE-REG-008 -->
### TC-REG-085 — Failing and succeeding items in one load run each get their own outcome
Derived from : AC-REG-078  (REQ-REG-007)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-015 → REG-LOAD-CONNECTION-NOT-READ-ONLY (load reason code — ADR-REG-011) · RULE-REG-008 → REG-LOAD-UNKNOWN-FETCH-MODE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The activation configuration lists `main-db` (mcp, read-only) and `bad-db` (readOnly = false); the package directory holds `svc-ok` (valid, queries on `main-db`) and `svc-fax` (`fetch: fax`).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : One load run records 4 rows: `main-db` ACTIVATED, `bad-db` REJECTED with the RULE-REG-015 message «The connection "bad-db" must use a read-only database user.», `svc-ok` REGISTERED, `svc-fax` REJECTED with the RULE-REG-008 message «The fetch mode "fax" is not supported; use path, blob or manual.» (ar: PENDING ADR-REG-011); `svc-ok` is readable with API-REG-002.
Test data    : connections `main-db`, `bad-db`; folders `svc-ok`, `svc-fax`
<!-- TC:TC-REG-085:END -->

<!-- TC:TC-REG-086:START traces=AC-REG-079,REQ-REG-034,API-REG-003,RULE-REG-012 -->
### TC-REG-086 — A timeout in a service definition is rejected
Derived from : AC-REG-079  (REQ-REG-034)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-012 → REG-LOAD-ELEMENT-NOT-ALLOWED (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder (service code `demo-service`) whose service definition declares `timeout: 30`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected with the RULE-REG-012 message (en) «The service definition of "demo-service" contains "timeout", which a service definition cannot set.» (ar: PENDING ADR-REG-011).
Test data    : `timeout: 30`
<!-- TC:TC-REG-086:END -->

<!-- TC:TC-REG-087:START traces=AC-REG-080,REQ-REG-034,API-REG-003,RULE-REG-012 -->
### TC-REG-087 — A maximum file size in a service definition is rejected
Derived from : AC-REG-080  (REQ-REG-034)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-012 → REG-LOAD-ELEMENT-NOT-ALLOWED (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: A folder (service code `demo-service`) whose service definition declares `max_file_size: 50MB`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The package is rejected with the RULE-REG-012 message (en) «The service definition of "demo-service" contains "max_file_size", which a service definition cannot set.» (ar: PENDING ADR-REG-011).
Test data    : `max_file_size: 50MB`
<!-- TC:TC-REG-087:END -->

<!-- SUB:RULE-SCENARIOS:END -->

<!-- SUB:API-SCENARIOS:START traces=AC-REG-001,AC-REG-002,AC-REG-005,AC-REG-006,AC-REG-008,AC-REG-009,AC-REG-010,AC-REG-011,AC-REG-014,AC-REG-015,AC-REG-017,AC-REG-018,AC-REG-020,AC-REG-021,AC-REG-024,AC-REG-025,AC-REG-026,AC-REG-027,AC-REG-030,AC-REG-038,AC-REG-039,AC-REG-044,AC-REG-046,AC-REG-047,AC-REG-048,AC-REG-050,AC-REG-051,AC-REG-052,AC-REG-053,AC-REG-056,AC-REG-058,AC-REG-059,AC-REG-061,AC-REG-062,REQ-REG-001,REQ-REG-002,REQ-REG-005,REQ-REG-006,REQ-REG-008,REQ-REG-009,REQ-REG-010,REQ-REG-011,REQ-REG-013,REQ-REG-014,REQ-REG-016,REQ-REG-017,REQ-REG-019,REQ-REG-020,REQ-REG-023,REQ-REG-024,REQ-REG-025,REQ-REG-026,REQ-REG-029,REQ-REG-036,REQ-REG-037,REQ-REG-042,REQ-REG-044,REQ-REG-045,REQ-REG-046,REQ-REG-048,REQ-REG-049,REQ-REG-050,REQ-REG-051,REQ-REG-054,REQ-REG-056,REQ-REG-057,REQ-REG-059,REQ-REG-060,API-REG-001,API-REG-002,API-REG-003,AC-REG-066,REQ-REG-064,AC-REG-068,AC-REG-072,REQ-REG-067,AC-REG-073,REQ-REG-068,AC-REG-081 -->
## SUB API-SCENARIOS

Endpoint- and state-driven acceptance — the three reads, the load run's outcomes, version history, connections and the in-process supply. (34 TCs)

<!-- TC:TC-REG-001:START traces=AC-REG-001,REQ-REG-001,API-REG-003,API-REG-002 -->
### TC-REG-001 — One Service Package per service code at first load
Derived from : AC-REG-001  (REQ-REG-001)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The registry holds no service. The package directory holds the valid folder `scholarship-request`; connection `main-db` is activated.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Read the service with API-REG-002 GET /api/v1/services/scholarship-request.
Expected     : The registry stores exactly 1 Service Package with serviceCode `scholarship-request`: API-REG-002 returns 200 with serviceCode `scholarship-request`; the load report holds 1 SERVICE_PACKAGE row for it with outcome REGISTERED.
Test data    : folder `scholarship-request`; connection `main-db`
<!-- TC:TC-REG-001:END -->

<!-- TC:TC-REG-002:START traces=AC-REG-002,REQ-REG-002,API-REG-003 -->
### TC-REG-002 — Version stored as two separate parts
Derived from : AC-REG-002  (REQ-REG-002)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: A valid package folder with a service knowledge file and a service definition file (texts known to the test).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Resolve the stored version through the in-process `getServicePackageVersion(serviceCode, versionNumber)`.
Expected     : The stored version holds serviceKnowledge equal to the knowledge file text and serviceDefinition equal to the definition file text, as 2 separate fields.
Test data    : the two file texts of the fixture folder
<!-- TC:TC-REG-002:END -->

<!-- TC:TC-REG-005:START traces=AC-REG-005,REQ-REG-005,API-REG-003 -->
### TC-REG-005 — Every folder of the package directory is loaded at start
Derived from : AC-REG-005  (REQ-REG-005)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language en
Preconditions: The configured package directory (`aias.registry.package-directory`) holds 3 package folders.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The load report contains 3 Load Result rows of subjectKind SERVICE_PACKAGE, one per folder.
Test data    : 3 fixture folders
<!-- TC:TC-REG-005:END -->

<!-- TC:TC-REG-006:START traces=AC-REG-006,REQ-REG-006,API-REG-003,API-REG-002 -->
### TC-REG-006 — A new service code is registered with its first version
Derived from : AC-REG-006  (REQ-REG-006)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The registry holds no service code `vehicle-permit`; a valid folder declares `vehicle-permit` version 1.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Read the service with API-REG-002 GET /api/v1/services/vehicle-permit.
Expected     : The registry creates Service Package `vehicle-permit` with available = true and stores version 1 as its current version (API-REG-002 → 200, versionNumber 1; API-REG-001 lists it); the load report records outcome REGISTERED for it.
Test data    : service code `vehicle-permit`, version 1
<!-- TC:TC-REG-006:END -->

<!-- TC:TC-REG-008:START traces=AC-REG-008,REQ-REG-008,API-REG-003 -->
### TC-REG-008 — The load report returns every row of the latest run
Derived from : AC-REG-008  (REQ-REG-008)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The most recent load run recorded 1 REGISTERED and 1 REJECTED result.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : 200 with an array of 2 LoadResult rows, each with subjectKind, subjectName, versionNumber, outcome and reason (shape per api-spec-reg.yaml `LoadResult`).
Test data    : one valid folder and one invalid folder in the latest run
<!-- TC:TC-REG-008:END -->

<!-- TC:TC-REG-009:START traces=AC-REG-009,REQ-REG-009,API-REG-003 -->
### TC-REG-009 — Earlier load results are removed at a new run
Derived from : AC-REG-009  (REQ-REG-009)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: The previous load run left 4 Load Result rows; the package directory now holds 2 folders and the activation configuration 1 connection.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The load report contains exactly 3 rows, all with the loadRunAt of the new run.
Test data    : 2 folders, 1 connection
<!-- TC:TC-REG-009:END -->

<!-- TC:TC-REG-010:START traces=AC-REG-010,REQ-REG-010,API-REG-003,API-REG-001,API-REG-002 -->
### TC-REG-010 — A service whose folder is gone is withdrawn
Derived from : AC-REG-010  (REQ-REG-010)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: Service Package `vehicle-permit` is available with stored versions; its folder was removed from the package directory.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. List the services with API-REG-001 GET /api/v1/services. 4. Read the service with API-REG-002 GET /api/v1/services/vehicle-permit.
Expected     : The registry sets available = false and withdrawnAt on `vehicle-permit` (API-REG-001 no longer lists it; API-REG-002 still returns its current version summary — a read refuses only an unknown code, RULE-REG-016), keeps its stored versions, and the load report records outcome WITHDRAWN.
Test data    : service code `vehicle-permit`
<!-- TC:TC-REG-010:END -->

<!-- TC:TC-REG-011:START traces=AC-REG-011,REQ-REG-011,API-REG-001,API-REG-003 -->
### TC-REG-011 — A returning service is available again
Derived from : AC-REG-011  (REQ-REG-011)
Exercises    : API-REG-001 GET /api/v1/services
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: Service Package `vehicle-permit` is withdrawn and its valid folder is back in the package directory.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. List the services with API-REG-001 GET /api/v1/services.
Expected     : The registry sets available = true and clears withdrawnAt on `vehicle-permit`: API-REG-001 lists `vehicle-permit` again.
Test data    : service code `vehicle-permit`
<!-- TC:TC-REG-011:END -->

<!-- TC:TC-REG-014:START traces=AC-REG-014,REQ-REG-013,API-REG-001 -->
### TC-REG-014 — List of services returns available services only, without SQL or connection
Derived from : AC-REG-014  (REQ-REG-013)
Exercises    : API-REG-001 GET /api/v1/services
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: 2 Service Packages are available and 1 is withdrawn.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. List the services with API-REG-001 GET /api/v1/services.
Expected     : 200 with exactly 2 ServiceSummary rows, each with serviceCode, available = true, versionNumber, fetchMode, requiredDocumentTypes (the documentType values) and approvalEnabled; no row carries sqlText or any connection field (connectionName, endpoint, queryTool, dialect, credentialReference) — the body has only the properties api-spec-reg.yaml declares.
Test data    : 2 available fixture services, 1 withdrawn
<!-- TC:TC-REG-014:END -->

<!-- TC:TC-REG-015:START traces=AC-REG-015,REQ-REG-014,API-REG-002 -->
### TC-REG-015 — Read one service returns its current version summary
Derived from : AC-REG-015  (REQ-REG-014)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Service Package `scholarship-request` is available with current version 3 (fetch `path`, required TRANSCRIPT and ID_CARD, approval disabled).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Read the service with API-REG-002 GET /api/v1/services/scholarship-request.
Expected     : 200 with serviceCode `scholarship-request`, available = true, versionNumber 3, fetchMode `path`, requiredDocumentTypes TRANSCRIPT and ID_CARD, and approvalEnabled = false.
Test data    : service code `scholarship-request`, version 3
<!-- TC:TC-REG-015:END -->

<!-- TC:TC-REG-017:START traces=AC-REG-017,REQ-REG-016,API-REG-003 -->
### TC-REG-017 — A folder outside the package directory is never loaded
Derived from : AC-REG-017  (REQ-REG-016)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : PORTS
Scenario     : VIOLATION · data class ATTACK · language en
Preconditions: A valid package folder exists outside the configured package directory (a sibling directory, and a symbolic link inside the directory pointing outside it).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The registry stores no version from that folder and the load report contains no row for it.
Test data    : fixture folder outside `aias.registry.package-directory`
<!-- TC:TC-REG-017:END -->

<!-- TC:TC-REG-018:START traces=AC-REG-018,REQ-REG-017,API-REG-001,API-REG-002,API-REG-003 -->
### TC-REG-018 — The registry exposes read operations only
Derived from : AC-REG-018  (REQ-REG-017)
Exercises    : API-REG-001 GET /api/v1/services
Rule / code  : —
Package      : SVC-API
Scenario     : PERMISSION · data class EDGE · language en
Preconditions: The service is running.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. List the operations of the service registry from api-spec-reg.yaml and the running service. 2. Send POST, PUT, PATCH and DELETE to /api/v1/services, /api/v1/services/scholarship-request and /api/v1/load-results.
Expected     : Every listed operation is a read operation (3 GET: API-REG-001, API-REG-002, API-REG-003) and 0 operations create or change a Service Package, a version or a Connection; every write verb is refused with no change to the registry.
Test data    : none beyond the running service
<!-- TC:TC-REG-018:END -->

<!-- TC:TC-REG-020:START traces=AC-REG-020,REQ-REG-019,API-REG-002 -->
### TC-REG-020 — Every version carries its version number
Derived from : AC-REG-020  (REQ-REG-019)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: A valid folder declares version 3 of a new service code.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the service with API-REG-002.
Expected     : The stored version has versionNumber = 3.
Test data    : declared `version: 3`
<!-- TC:TC-REG-020:END -->

<!-- TC:TC-REG-021:START traces=AC-REG-021,REQ-REG-020,API-REG-002,API-REG-003 -->
### TC-REG-021 — A higher version becomes current; the earlier stays stored
Derived from : AC-REG-021  (REQ-REG-020)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: `scholarship-request` has current version 3; its folder now declares version 4.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Read the service with API-REG-002 GET /api/v1/services/scholarship-request. 4. Resolve version 3 through the in-process `getServicePackageVersion("scholarship-request", 3)`.
Expected     : Version 4 is stored and current (API-REG-002 → versionNumber 4), version 3 stays stored and resolvable, and the load report records outcome REGISTERED.
Test data    : versions 3 and 4 of `scholarship-request`
<!-- TC:TC-REG-021:END -->

<!-- TC:TC-REG-024:START traces=AC-REG-024,REQ-REG-023,API-REG-003 -->
### TC-REG-024 — An unchanged package registers no new version
Derived from : AC-REG-024  (REQ-REG-023)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` is stored with contentHash `h1`; the folder declares version 3 with contentHash `h1`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The registry stores 0 new versions and the load report records outcome UNCHANGED.
Test data    : contentHash `h1`
<!-- TC:TC-REG-024:END -->

<!-- TC:TC-REG-025:START traces=AC-REG-025,REQ-REG-024 -->
### TC-REG-025 — The current version is supplied to a Check
Derived from : AC-REG-025  (REQ-REG-024)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: `scholarship-request` has stored versions 2 and 3, version 3 current.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The system supplies version 3 and versionNumber = 3.
Test data    : versions 2 and 3
<!-- TC:TC-REG-025:END -->

<!-- TC:TC-REG-026:START traces=AC-REG-026,REQ-REG-025 -->
### TC-REG-026 — Any stored version resolves
Derived from : AC-REG-026  (REQ-REG-025)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: `scholarship-request` version 2 is stored and version 3 is current.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Request version 2 through the in-process `getServicePackageVersion("scholarship-request", 2)`.
Expected     : The system returns version 2 with its serviceKnowledge, serviceDefinition, queries and documentType values as stored.
Test data    : versions 2 and 3
<!-- TC:TC-REG-026:END -->

<!-- TC:TC-REG-027:START traces=AC-REG-027,REQ-REG-026,API-REG-003 -->
### TC-REG-027 — No stored version is deleted when a service is withdrawn
Derived from : AC-REG-027  (REQ-REG-026)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: `vehicle-permit` with stored versions 1 and 2 is withdrawn (its folder is absent).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Resolve versions 1 and 2 through the in-process `getServicePackageVersion("vehicle-permit", n)`.
Expected     : Versions 1 and 2 of `vehicle-permit` still exist and resolve.
Test data    : versions 1 and 2 of `vehicle-permit`
<!-- TC:TC-REG-027:END -->

<!-- TC:TC-REG-030:START traces=AC-REG-030,REQ-REG-029 -->
### TC-REG-030 — Only the queries of the service definition are supplied, unaltered
Derived from : AC-REG-030  (REQ-REG-029)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` defines queries `request_details` and `attachments`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The system supplies exactly 2 queries, each sqlText equal to the stored text and each with its connectionName.
Test data    : queries `request_details`, `attachments`
<!-- TC:TC-REG-030:END -->

<!-- TC:TC-REG-038:START traces=AC-REG-038,REQ-REG-036,API-REG-002 -->
### TC-REG-038 — One fetch mode recorded per version
Derived from : AC-REG-038  (REQ-REG-036)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: A valid folder whose service definition declares `fetch: path`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the service with API-REG-002.
Expected     : The stored version has fetchMode = path (API-REG-002 → fetchMode `path`).
Test data    : `fetch: path`
<!-- TC:TC-REG-038:END -->

<!-- TC:TC-REG-039:START traces=AC-REG-039,REQ-REG-037,API-REG-002 -->
### TC-REG-039 — Required document types recorded per version
Derived from : AC-REG-039  (REQ-REG-037)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: A valid folder whose service definition declares `required: [TRANSCRIPT, ID_CARD]`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the service with API-REG-002.
Expected     : The version stores 2 Required Document rows with documentType TRANSCRIPT and ID_CARD (API-REG-002 → requiredDocumentTypes TRANSCRIPT, ID_CARD).
Test data    : `required: [TRANSCRIPT, ID_CARD]`
<!-- TC:TC-REG-039:END -->

<!-- TC:TC-REG-044:START traces=AC-REG-044,REQ-REG-042 -->
### TC-REG-044 — An enabled approval API definition is recorded
Derived from : AC-REG-044  (REQ-REG-042)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: A folder (service code `vehicle-permit`, version 1) with `approval.enabled: true` and `api: POST /requests/{requestId}/approve`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the approval API through the in-process `ApprovalApiRegistry.getApprovalApi("vehicle-permit", 1)`.
Expected     : The stored version has approvalEnabled = true and approvalApi = `POST /requests/{requestId}/approve`.
Test data    : `approval.enabled: true`, `api: POST /requests/{requestId}/approve`
<!-- TC:TC-REG-044:END -->

<!-- TC:TC-REG-046:START traces=AC-REG-046,REQ-REG-044 -->
### TC-REG-046 — The approval API definition reaches only the Employee Decision path
Derived from : AC-REG-046  (REQ-REG-044)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : PERMISSION · data class VALID · language en
Preconditions: Version 1 of `vehicle-permit` enables the approval API.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("vehicle-permit")`. 2. The Employee Decision path requests `ApprovalApiRegistry.getApprovalApi("vehicle-permit", 1)`.
Expected     : The supplied package contains 0 approval API definitions, and the Employee Decision path request returns approvalApi.
Test data    : service code `vehicle-permit`, version 1
<!-- TC:TC-REG-046:END -->

<!-- TC:TC-REG-047:START traces=AC-REG-047,REQ-REG-045 -->
### TC-REG-047 — Disabled approval is reported to the Employee Decision path
Derived from : AC-REG-047  (REQ-REG-045)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` has `approval.enabled: false`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. The Employee Decision path requests `ApprovalApiRegistry.getApprovalApi("scholarship-request", 3)`.
Expected     : The system returns approvalEnabled = false and no approvalApi.
Test data    : `approval.enabled: false`
<!-- TC:TC-REG-047:END -->

<!-- TC:TC-REG-048:START traces=AC-REG-048,REQ-REG-046,API-REG-003 -->
### TC-REG-048 — One connection defined once and shared by two packages
Derived from : AC-REG-048  (REQ-REG-046)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The activation configuration lists connection `main-db`; 2 service definitions name `main-db`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Request connection `main-db` through the in-process `getConnection("main-db")`.
Expected     : The registry stores 1 Connection named `main-db` and both packages are stored (2 REGISTERED SERVICE_PACKAGE rows).
Test data    : connection `main-db`; 2 fixture folders
<!-- TC:TC-REG-048:END -->

<!-- TC:TC-REG-050:START traces=AC-REG-050,REQ-REG-048 -->
### TC-REG-050 — A connection is supplied by name
Derived from : AC-REG-050  (REQ-REG-048)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Connection `main-db` of type `mcp` is registered.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests Connection `main-db` through the in-process `getConnection("main-db")`.
Expected     : The system returns connectionType `mcp`, endpoint, queryTool, dialect and credentialReference of `main-db`.
Test data    : connection `main-db` (mcp)
<!-- TC:TC-REG-050:END -->

<!-- TC:TC-REG-051:START traces=AC-REG-051,REQ-REG-049,API-REG-003 -->
### TC-REG-051 — Activation registers the environment's connections
Derived from : AC-REG-051  (REQ-REG-049)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class VALID · language en
Preconditions: The activation configuration of environment `test` lists `main-db` and `blob-db` (`aias.registry.environment-name` = `test`).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service in `test`. 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The registry stores 2 Connections with environmentName `test` and the load report records 2 ACTIVATED rows.
Test data    : environment `test`; connections `main-db`, `blob-db`
<!-- TC:TC-REG-051:END -->

<!-- TC:TC-REG-052:START traces=AC-REG-052,REQ-REG-050,API-REG-003 -->
### TC-REG-052 — Changed connection settings update the connection, not the versions
Derived from : AC-REG-052  (REQ-REG-050)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: Connection `main-db` is registered with endpoint `e1`; the activation configuration now gives `main-db` endpoint `e2`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Request `main-db` through the in-process `getConnection("main-db")`.
Expected     : The registry sets endpoint = `e2` on `main-db`, records outcome UPDATED, and stores 0 new service package versions.
Test data    : endpoints `e1`, `e2`
<!-- TC:TC-REG-052:END -->

<!-- TC:TC-REG-053:START traces=AC-REG-053,REQ-REG-051,API-REG-003 -->
### TC-REG-053 — A connection no longer listed is removed
Derived from : AC-REG-053  (REQ-REG-051)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: Connection `archive-db` is registered and absent from the activation configuration.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The registry deletes `archive-db` and the load report records outcome REMOVED.
Test data    : connection `archive-db`
<!-- TC:TC-REG-053:END -->

<!-- TC:TC-REG-056:START traces=AC-REG-056,REQ-REG-054,API-REG-001 -->
### TC-REG-056 — Only the credential reference is stored
Derived from : AC-REG-056  (REQ-REG-054)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class ATTACK · language en
Preconditions: The activation configuration lists `main-db` with credentialReference `aias/main-db`; the secret store holds the password under that name.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Request `main-db` through the in-process `getConnection("main-db")`. 3. Read every stored Connection field.
Expected     : The stored Connection has credentialReference = `aias/main-db` and 0 stored fields contain the password.
Test data    : credentialReference `aias/main-db`; a known test password
<!-- TC:TC-REG-056:END -->

<!-- TC:TC-REG-058:START traces=AC-REG-058,REQ-REG-056 -->
### TC-REG-058 — The limited-to-views declaration is recorded
Derived from : AC-REG-058  (REQ-REG-056)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The activation configuration lists `main-db` with limitedToViews = true.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Request `main-db` through the in-process `getConnection("main-db")`.
Expected     : The stored Connection `main-db` has limitedToViews = true.
Test data    : `main-db` limitedToViews = true
<!-- TC:TC-REG-058:END -->

<!-- TC:TC-REG-059:START traces=AC-REG-059,REQ-REG-057,API-REG-002,API-REG-003 -->
### TC-REG-059 — The scholarship-request pilot package loads
Derived from : AC-REG-059  (REQ-REG-057)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: A fresh deployment with the delivered package directory and connection `main-db` activated.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the service with API-REG-002 GET /api/v1/services/scholarship-request.
Expected     : The registry returns Service Package `scholarship-request` with fetchMode = path and requiredDocumentTypes TRANSCRIPT and ID_CARD.
Test data    : the delivered package directory; connection `main-db`
<!-- TC:TC-REG-059:END -->

<!-- TC:TC-REG-061:START traces=AC-REG-061,REQ-REG-059,API-REG-001,API-REG-002,API-REG-003 -->
### TC-REG-061 — No request data is stored in the registry
Derived from : AC-REG-061  (REQ-REG-059)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: 100 Checks of `scholarship-request` have run.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Read every stored field of the REG tables. 2. Read API-REG-001, API-REG-002 and API-REG-003.
Expected     : 0 stored fields contain a request number, an employee identity, a query result or a document content; no response carries one.
Test data    : request numbers and employee identities of the 100 Checks (synthetic)
<!-- TC:TC-REG-061:END -->

<!-- TC:TC-REG-062:START traces=AC-REG-062,REQ-REG-060 -->
### TC-REG-062 — Supplied package content is read-only
Derived from : AC-REG-062  (REQ-REG-060)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : STATE · data class ATTACK · language en
Preconditions: A Check received version 3 of `scholarship-request`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. That Check attempts to change the supplied query text (and the query list). 2. A next Check requests the service package.
Expected     : The change is refused and the next Check receives the stored sqlText unchanged.
Test data    : service code `scholarship-request`, version 3
<!-- TC:TC-REG-062:END -->

<!-- TC:TC-REG-073:START traces=AC-REG-066,REQ-REG-064,API-REG-003,API-REG-001 -->
### TC-REG-073 — A case and space variant of a stored service code is the same service
Derived from : AC-REG-066  (REQ-REG-064)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : EDGE · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` is stored with contentHash `h1`; its folder now declares service code ` Scholarship-Request ` (leading/trailing space, mixed case) with version 3 and otherwise identical content (contentHash `h1`).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. List the services with API-REG-001 GET /api/v1/services.
Expected     : The load report records outcome UNCHANGED for the folder with serviceCode `scholarship-request`; 0 new versions are stored; API-REG-001 returns exactly 1 row for `scholarship-request` (no second Service Package).
Test data    : service code ` Scholarship-Request `; version 3; contentHash `h1`
<!-- TC:TC-REG-073:END -->

<!-- TC:TC-REG-075:START traces=AC-REG-068,REQ-REG-064,API-REG-002 -->
### TC-REG-075 — Read one service matches the code case-insensitively
Derived from : AC-REG-068  (REQ-REG-064)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : —
Package      : SVC-API
Scenario     : EDGE · data class VALID · language en
Preconditions: Service Package `scholarship-request` is available.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Read the service with API-REG-002 GET /api/v1/services/SCHOLARSHIP-REQUEST.
Expected     : 200 with serviceCode `scholarship-request` (canonical lower case) and available = true.
Test data    : path code `SCHOLARSHIP-REQUEST`
<!-- TC:TC-REG-075:END -->

<!-- TC:TC-REG-079:START traces=AC-REG-072,REQ-REG-067,API-REG-003,API-REG-001 -->
### TC-REG-079 — Two instances starting together produce one complete load report
Derived from : AC-REG-072  (REQ-REG-067)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : —
Package      : SVC-API
Scenario     : CONCURRENCY · data class VALID · language en
Preconditions: Two instances share one schema; the package directory holds 2 valid folders (`svc-one` v1, `svc-two` v1) and the activation configuration 1 connection `main-db`; the registry is empty.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start both instances at the same moment. 2. When both accept traffic, read the load report with API-REG-003. 3. List the services with API-REG-001.
Expected     : The load report holds exactly 3 rows, all with one loadRunAt (the run that committed last: either the 2 REGISTERED + 1 ACTIVATED rows of the first run, or the 2 UNCHANGED + 1 UPDATED/ACTIVATED rows of the idempotent second run — never a mix of two runs); API-REG-001 returns 2 services, each with versionNumber 1 stored once; 1 Connection `main-db` exists; neither instance logs a constraint violation.
Test data    : 2 instances; folders `svc-one`, `svc-two`; connection `main-db`
<!-- TC:TC-REG-079:END -->

<!-- TC:TC-REG-080:START traces=AC-REG-073,REQ-REG-068,API-REG-003 -->
### TC-REG-080 — An instance that cannot take the load lock serves the stored registry
Derived from : AC-REG-073  (REQ-REG-068)
Exercises    : in-process `ServiceRegistry.getCurrentServicePackage` and API-REG-003
Rule / code  : —
Package      : SVC-API
Scenario     : CONCURRENCY · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` is stored; another session holds `LOCK TABLE REG_LOAD_RESULT IN EXCLUSIVE MODE` for 60 seconds; `aias.registry.load-lock-timeout` = PT30S.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service. 2. After 30 seconds call getCurrentServicePackage(`scholarship-request`). 3. Release the lock and read API-REG-003.
Expected     : The starting instance writes 0 Load Result rows (API-REG-003 still returns the previous run's rows) and getCurrentServicePackage returns versionNumber 3.
Test data    : lock held 60 s; timeout PT30S
<!-- TC:TC-REG-080:END -->

<!-- TC:TC-REG-088:START traces=AC-REG-081,REQ-REG-014,API-REG-002 -->
### TC-REG-088 — Read of a withdrawn service reports available = false
Derived from : AC-REG-081  (REQ-REG-014)
Exercises    : API-REG-002 GET /api/v1/services/{serviceCode}
Rule / code  : —
Package      : SVC-API
Scenario     : EDGE · data class VALID · language en
Preconditions: Service Package `vehicle-permit` is withdrawn; its stored current version is 2.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Read the service with API-REG-002 GET /api/v1/services/vehicle-permit.
Expected     : 200 with serviceCode `vehicle-permit`, available = false and versionNumber 2.
Test data    : withdrawn service `vehicle-permit`, version 2
<!-- TC:TC-REG-088:END -->

<!-- SUB:API-SCENARIOS:END -->

<!-- SUB:MODEL-EVAL:START traces=AC-REG-019,AC-REG-028,AC-REG-031,AC-REG-060,REQ-REG-018,REQ-REG-027,REQ-REG-030,REQ-REG-058,API-REG-001 -->
## SUB MODEL-EVAL

What the model is given — the service knowledge a Check hands the LLM: whole, apart from every query and document reference, and with the pilot's thresholds as digits (the known-result request set of raw idea §10 runs against these). (4 TCs)

<!-- TC:TC-REG-019:START traces=AC-REG-019,REQ-REG-018 -->
### TC-REG-019 — Service knowledge supplied apart from queries and document references
Derived from : AC-REG-019  (REQ-REG-018)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` is current.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The supplied package returns the service knowledge in its own field, and that field contains 0 query texts and 0 document references.
Test data    : service code `scholarship-request`, version 3
<!-- TC:TC-REG-019:END -->

<!-- TC:TC-REG-028:START traces=AC-REG-028,REQ-REG-027 -->
### TC-REG-028 — The whole service knowledge is supplied unaltered
Derived from : AC-REG-028  (REQ-REG-027)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language en
Preconditions: The knowledge file of version 3 of `scholarship-request` has 12000 characters.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The supplied service knowledge has 12000 characters and equals the stored serviceKnowledge.
Test data    : knowledge file of 12000 characters
<!-- TC:TC-REG-028:END -->

<!-- TC:TC-REG-031:START traces=AC-REG-031,REQ-REG-030,API-REG-001 -->
### TC-REG-031 — Queries only through the query part; no SQL in the service knowledge
Derived from : AC-REG-031  (REQ-REG-030)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` is current.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The query part returns 2 queries and the serviceKnowledge field contains 0 SQL texts.
Test data    : service code `scholarship-request`, version 3
<!-- TC:TC-REG-031:END -->

<!-- TC:TC-REG-060:START traces=AC-REG-060,REQ-REG-058 -->
### TC-REG-060 — Pilot numeric conditions are written as digits
Derived from : AC-REG-060  (REQ-REG-058)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The delivered service knowledge of `scholarship-request`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Read the delivered service knowledge (in-process `getCurrentServicePackage("scholarship-request").serviceKnowledge`).
Expected     : Each numeric condition contains its threshold as digits and 0 numeric conditions state their threshold in words.
Test data    : the delivered knowledge file
<!-- TC:TC-REG-060:END -->

<!-- SUB:MODEL-EVAL:END -->

<!-- PHASE:TEST-PLAN-BE:END -->

## TC TRACEABILITY INDEX

| TC | AC | REQ | API | RULE / code | Package | Group |
|---|---|---|---|---|---|---|
| TC-REG-001 | AC-REG-001 | REQ-REG-001 | API-REG-003, API-REG-002 | — | SVC-API | API-SCENARIOS |
| TC-REG-002 | AC-REG-002 | REQ-REG-002 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-003 | AC-REG-003 | REQ-REG-003 | API-REG-003 | RULE-REG-001 → REG-LOAD-INCOMPLETE-PACKAGE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-004 | AC-REG-004 | REQ-REG-004 | API-REG-003, API-REG-002 | RULE-REG-002 → REG-LOAD-DUPLICATE-SERVICE-CODE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-005 | AC-REG-005 | REQ-REG-005 | API-REG-003 | — | PORTS | API-SCENARIOS |
| TC-REG-006 | AC-REG-006 | REQ-REG-006 | API-REG-003, API-REG-002 | — | SVC-API | API-SCENARIOS |
| TC-REG-007 | AC-REG-007 | REQ-REG-007 | API-REG-003 | RULE-REG-008 → REG-LOAD-UNKNOWN-FETCH-MODE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-008 | AC-REG-008 | REQ-REG-008 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-009 | AC-REG-009 | REQ-REG-009 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-010 | AC-REG-010 | REQ-REG-010 | API-REG-003, API-REG-001, API-REG-002 | — | SVC-API | API-SCENARIOS |
| TC-REG-011 | AC-REG-011 | REQ-REG-011 | API-REG-001, API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-012 | AC-REG-012 | REQ-REG-012 | in-process | RULE-REG-016 → ServiceNotAvailableException (in-process) | SVC-API | RULE-SCENARIOS |
| TC-REG-013 | AC-REG-013 | REQ-REG-012 | in-process | RULE-REG-016 → ServiceNotAvailableException (in-process) | SVC-API | RULE-SCENARIOS |
| TC-REG-014 | AC-REG-014 | REQ-REG-013 | API-REG-001 | — | SVC-API | API-SCENARIOS |
| TC-REG-015 | AC-REG-015 | REQ-REG-014 | API-REG-002 | — | SVC-API | API-SCENARIOS |
| TC-REG-016 | AC-REG-016 | REQ-REG-015 | API-REG-002 | RULE-REG-016 → REG-404-SERVICE-NOT-FOUND | SVC-API | RULE-SCENARIOS |
| TC-REG-017 | AC-REG-017 | REQ-REG-016 | API-REG-003 | — | PORTS | API-SCENARIOS |
| TC-REG-018 | AC-REG-018 | REQ-REG-017 | API-REG-001, API-REG-002, API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-019 | AC-REG-019 | REQ-REG-018 | in-process | — | SVC-API | MODEL-EVAL |
| TC-REG-020 | AC-REG-020 | REQ-REG-019 | API-REG-002 | — | SVC-API | API-SCENARIOS |
| TC-REG-021 | AC-REG-021 | REQ-REG-020 | API-REG-002, API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-022 | AC-REG-022 | REQ-REG-021 | API-REG-003 | RULE-REG-003 → REG-LOAD-VERSION-EDITED-IN-PLACE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-023 | AC-REG-023 | REQ-REG-022 | API-REG-003, API-REG-002 | RULE-REG-004 → REG-LOAD-VERSION-OLDER-THAN-CURRENT (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-024 | AC-REG-024 | REQ-REG-023 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-025 | AC-REG-025 | REQ-REG-024 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-026 | AC-REG-026 | REQ-REG-025 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-027 | AC-REG-027 | REQ-REG-026 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-028 | AC-REG-028 | REQ-REG-027 | in-process | — | SVC-API | MODEL-EVAL |
| TC-REG-029 | AC-REG-029 | REQ-REG-028 | API-REG-003 | RULE-REG-018 → REG-LOAD-EMPTY-SERVICE-KNOWLEDGE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-030 | AC-REG-030 | REQ-REG-029 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-031 | AC-REG-031 | REQ-REG-030 | API-REG-001 | — | SVC-API | MODEL-EVAL |
| TC-REG-032 | AC-REG-032 | REQ-REG-031 | API-REG-003 | RULE-REG-005 → REG-LOAD-CONNECTION-NOT-ACTIVATED (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-033 | AC-REG-033 | REQ-REG-032 | API-REG-003 | RULE-REG-006 → REG-LOAD-UNBOUND-PARAMETER (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-034 | AC-REG-034 | REQ-REG-032 | API-REG-003 | RULE-REG-006 → REG-LOAD-UNBOUND-PARAMETER (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-035 | AC-REG-035 | REQ-REG-033 | API-REG-003 | RULE-REG-007 → REG-LOAD-NOT-SINGLE-SELECT (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-036 | AC-REG-036 | REQ-REG-034 | API-REG-003 | RULE-REG-012 → REG-LOAD-ELEMENT-NOT-ALLOWED (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-037 | AC-REG-037 | REQ-REG-035 | API-REG-003 | RULE-REG-019 → REG-LOAD-DUPLICATE-QUERY-NAME (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-038 | AC-REG-038 | REQ-REG-036 | API-REG-002 | — | SVC-API | API-SCENARIOS |
| TC-REG-039 | AC-REG-039 | REQ-REG-037 | API-REG-002 | — | SVC-API | API-SCENARIOS |
| TC-REG-040 | AC-REG-040 | REQ-REG-038 | API-REG-003 | RULE-REG-008 → REG-LOAD-UNKNOWN-FETCH-MODE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-041 | AC-REG-041 | REQ-REG-039 | API-REG-003 | RULE-REG-009 → REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-042 | AC-REG-042 | REQ-REG-040 | API-REG-003 | RULE-REG-010 → REG-LOAD-BLOB-NOT-JDBC (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-043 | AC-REG-043 | REQ-REG-041 | API-REG-003 | RULE-REG-012 → REG-LOAD-ELEMENT-NOT-ALLOWED (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-044 | AC-REG-044 | REQ-REG-042 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-045 | AC-REG-045 | REQ-REG-043 | API-REG-003 | RULE-REG-011 → REG-LOAD-APPROVAL-API-UNDEFINED (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-046 | AC-REG-046 | REQ-REG-044 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-047 | AC-REG-047 | REQ-REG-045 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-048 | AC-REG-048 | REQ-REG-046 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-049 | AC-REG-049 | REQ-REG-047 | API-REG-003 | RULE-REG-013 → REG-LOAD-DUPLICATE-CONNECTION-NAME (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-050 | AC-REG-050 | REQ-REG-048 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-051 | AC-REG-051 | REQ-REG-049 | API-REG-003 | — | PORTS | API-SCENARIOS |
| TC-REG-052 | AC-REG-052 | REQ-REG-050 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-053 | AC-REG-053 | REQ-REG-051 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-054 | AC-REG-054 | REQ-REG-052 | API-REG-003 | RULE-REG-014 → REG-LOAD-UNKNOWN-CONNECTION-TYPE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-055 | AC-REG-055 | REQ-REG-053 | in-process | RULE-REG-017 → ServiceConnectionNotActivatedException (in-process) | SVC-API | RULE-SCENARIOS |
| TC-REG-056 | AC-REG-056 | REQ-REG-054 | API-REG-001 | — | SVC-API | API-SCENARIOS |
| TC-REG-057 | AC-REG-057 | REQ-REG-055 | API-REG-003 | RULE-REG-015 → REG-LOAD-CONNECTION-NOT-READ-ONLY (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-058 | AC-REG-058 | REQ-REG-056 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-059 | AC-REG-059 | REQ-REG-057 | API-REG-002, API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-060 | AC-REG-060 | REQ-REG-058 | in-process | — | SVC-API | MODEL-EVAL |
| TC-REG-061 | AC-REG-061 | REQ-REG-059 | API-REG-001, API-REG-002, API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-062 | AC-REG-062 | REQ-REG-060 | in-process | — | SVC-API | API-SCENARIOS |
| TC-REG-063 | AC-REG-063 | REQ-REG-061 | API-REG-003 | RULE-REG-016 → ServiceNotAvailableException (in-process) | SVC-API | RULE-SCENARIOS |
| TC-REG-064 | AC-REG-064 | REQ-REG-062 | API-REG-003 | RULE-REG-020 → REG-LOAD-FOREIGN-FILE-IN-PACKAGE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-072 | AC-REG-065 | REQ-REG-063 | API-REG-003, API-REG-002 | RULE-REG-021 → REG-LOAD-DUPLICATE-DOCUMENT-TYPE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-073 | AC-REG-066 | REQ-REG-064 | API-REG-003, API-REG-001 | — | SVC-API | API-SCENARIOS |
| TC-REG-074 | AC-REG-067 | REQ-REG-064 | API-REG-003 | RULE-REG-002 → REG-LOAD-DUPLICATE-SERVICE-CODE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-075 | AC-REG-068 | REQ-REG-064 | API-REG-002 | — | SVC-API | API-SCENARIOS |
| TC-REG-076 | AC-REG-069 | REQ-REG-065 | API-REG-003 | RULE-REG-022 → REG-LOAD-INVALID-SERVICE-CODE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-077 | AC-REG-070 | REQ-REG-066 | API-REG-003, API-REG-001 | RULE-REG-023 → REG-LOAD-PACKAGE-DIRECTORY-UNAVAILABLE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-078 | AC-REG-071 | REQ-REG-066 | API-REG-003, API-REG-001 | RULE-REG-023 → REG-LOAD-PACKAGE-DIRECTORY-UNAVAILABLE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-079 | AC-REG-072 | REQ-REG-067 | API-REG-003, API-REG-001 | — | SVC-API | API-SCENARIOS |
| TC-REG-080 | AC-REG-073 | REQ-REG-068 | API-REG-003 | — | SVC-API | API-SCENARIOS |
| TC-REG-081 | AC-REG-074 | REQ-REG-069 | API-REG-003, API-REG-002 | RULE-REG-024 → REG-LOAD-PACKAGE-CHANGED-DURING-READ (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-082 | AC-REG-075 | REQ-REG-039 | API-REG-003 | RULE-REG-009 → REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-083 | AC-REG-076 | REQ-REG-039 | API-REG-003 | RULE-REG-009 → REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-084 | AC-REG-077 | REQ-REG-039 | API-REG-003 | RULE-REG-009 → REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-085 | AC-REG-078 | REQ-REG-007 | API-REG-003, API-REG-002 | RULE-REG-015 → REG-LOAD-CONNECTION-NOT-READ-ONLY (load reason code — ADR-REG-011) · RULE-REG-008 → REG-LOAD-UNKNOWN-FETCH-MODE (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-086 | AC-REG-079 | REQ-REG-034 | API-REG-003 | RULE-REG-012 → REG-LOAD-ELEMENT-NOT-ALLOWED (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-087 | AC-REG-080 | REQ-REG-034 | API-REG-003 | RULE-REG-012 → REG-LOAD-ELEMENT-NOT-ALLOWED (load reason code — ADR-REG-011) | SVC-API | RULE-SCENARIOS |
| TC-REG-088 | AC-REG-081 | REQ-REG-014 | API-REG-002 | — | SVC-API | API-SCENARIOS |

### API → TC
| API | TCs |
|---|---|
| API-REG-001 | TC-REG-010, TC-REG-011, TC-REG-014, TC-REG-018, TC-REG-031, TC-REG-056, TC-REG-061, TC-REG-073, TC-REG-077, TC-REG-078, TC-REG-079  |
| API-REG-002 | TC-REG-001, TC-REG-004, TC-REG-006, TC-REG-010, TC-REG-015, TC-REG-016, TC-REG-018, TC-REG-020, TC-REG-021, TC-REG-023, TC-REG-038, TC-REG-039, TC-REG-059, TC-REG-061, TC-REG-072, TC-REG-075, TC-REG-081, TC-REG-085, TC-REG-088  |
| API-REG-003 | TC-REG-001, TC-REG-002, TC-REG-003, TC-REG-004, TC-REG-005, TC-REG-006, TC-REG-007, TC-REG-008, TC-REG-009, TC-REG-010, TC-REG-011, TC-REG-017, TC-REG-018, TC-REG-021, TC-REG-022, TC-REG-023, TC-REG-024, TC-REG-027, TC-REG-029, TC-REG-032, TC-REG-033, TC-REG-034, TC-REG-035, TC-REG-036, TC-REG-037, TC-REG-040, TC-REG-041, TC-REG-042, TC-REG-043, TC-REG-045, TC-REG-048, TC-REG-049, TC-REG-051, TC-REG-052, TC-REG-053, TC-REG-054, TC-REG-057, TC-REG-059, TC-REG-061, TC-REG-063, TC-REG-064, TC-REG-072, TC-REG-073, TC-REG-074, TC-REG-076, TC-REG-077, TC-REG-078, TC-REG-079, TC-REG-080, TC-REG-081, TC-REG-082, TC-REG-083, TC-REG-084, TC-REG-085, TC-REG-086, TC-REG-087  |

### Package → TC
| Package | TCs |
|---|---|
| PORTS | TC-REG-005, TC-REG-017, TC-REG-051 |
| SVC-API | TC-REG-001, TC-REG-002, TC-REG-003, TC-REG-004, TC-REG-006, TC-REG-007, TC-REG-008, TC-REG-009, TC-REG-010, TC-REG-011, TC-REG-012, TC-REG-013, TC-REG-014, TC-REG-015, TC-REG-016, TC-REG-018, TC-REG-019, TC-REG-020, TC-REG-021, TC-REG-022, TC-REG-023, TC-REG-024, TC-REG-025, TC-REG-026, TC-REG-027, TC-REG-028, TC-REG-029, TC-REG-030, TC-REG-031, TC-REG-032, TC-REG-033, TC-REG-034, TC-REG-035, TC-REG-036, TC-REG-037, TC-REG-038, TC-REG-039, TC-REG-040, TC-REG-041, TC-REG-042, TC-REG-043, TC-REG-044, TC-REG-045, TC-REG-046, TC-REG-047, TC-REG-048, TC-REG-049, TC-REG-050, TC-REG-052, TC-REG-053, TC-REG-054, TC-REG-055, TC-REG-056, TC-REG-057, TC-REG-058, TC-REG-059, TC-REG-060, TC-REG-061, TC-REG-062, TC-REG-063, TC-REG-064, TC-REG-072, TC-REG-073, TC-REG-074, TC-REG-075, TC-REG-076, TC-REG-077, TC-REG-078, TC-REG-079, TC-REG-080, TC-REG-081, TC-REG-082, TC-REG-083, TC-REG-084, TC-REG-085, TC-REG-086, TC-REG-087, TC-REG-088  |

### XM → TC
None — REG declares no XM edge.

## COVERAGE

| Measure | Covered | Note |
|---|---|---|
| AC | 81/81 ✓ | one TC per AC (TC-REG-001 … TC-REG-064, TC-REG-072 … TC-REG-088) |
| REQ | 69/69 ✓ | every REQ through its AC |
| API | 3/3 ✓ | API-REG-001 (11 TCs), API-REG-002 (19), API-REG-003 (56) |
| RULE | 24/24 ✓ | every RULE-REG-001 … RULE-REG-024 asserted by ≥1 violation TC |
| XM edges | 0/0 | no edge — INT-XM absent |
| Packages | PORTS 3 TCs · SVC-API 78 TCs | CORE, DATA-DOM, ALIGN-BE `no_tests`; CROSS-MOD carries no edge unit |

Track TC count 81 = AC count 81 (≤ 2× guard).

Cover map of the stage brief's focus: package loading at start-up TC-REG-001, TC-REG-005, TC-REG-006, TC-REG-007, TC-REG-009 · strict validation (single SELECT TC-REG-035; only the declared input as a named bind TC-REG-033, TC-REG-034; no limits / locations in definitions TC-REG-036, TC-REG-043; no extra files TC-REG-064) · versions never edited in place TC-REG-022 (with TC-REG-021, TC-REG-023, TC-REG-024, TC-REG-027) · withdrawn service refuses new checks TC-REG-010, TC-REG-012 · connections read-only, mcp and jdbc TC-REG-042, TC-REG-054, TC-REG-057, TC-REG-058 · per-environment activation TC-REG-051, TC-REG-052, TC-REG-053, TC-REG-055 · responses never expose SQL or connection settings TC-REG-014 (and the frontend TC-REG-065, TC-REG-070) · scholarship-request pilot package loads TC-REG-059 (with TC-REG-060, TC-REG-063) · gate-analysis revision: duplicate document type TC-REG-072 · service-code equality TC-REG-073 … TC-REG-076 · unreachable / empty package directory TC-REG-077, TC-REG-078 · concurrent start and load lock TC-REG-079, TC-REG-080 · torn read TC-REG-081 · document-source branches TC-REG-082 … TC-REG-084 · mixed failing / succeeding items TC-REG-085 · timeout / max_file_size rejected TC-REG-086, TC-REG-087 · withdrawn read available = false TC-REG-088.
