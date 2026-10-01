<!-- source: PHASE:TEST-PLAN-BE / SUB:API-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-REG-001, AC-REG-002, AC-REG-005, AC-REG-006, AC-REG-008, AC-REG-009, AC-REG-010, AC-REG-011, AC-REG-014, AC-REG-015, AC-REG-017, AC-REG-018, AC-REG-020, AC-REG-021, AC-REG-024, AC-REG-025, AC-REG-026, AC-REG-027, AC-REG-030, AC-REG-038, AC-REG-039, AC-REG-044, AC-REG-046, AC-REG-047, AC-REG-048, AC-REG-050, AC-REG-051, AC-REG-052, AC-REG-053, AC-REG-056, AC-REG-058, AC-REG-059, AC-REG-061, AC-REG-062, AC-REG-066, AC-REG-068, AC-REG-072, AC-REG-073, AC-REG-081, AC-REG-082, API-REG-001, API-REG-002, API-REG-003, REQ-REG-001, REQ-REG-002, REQ-REG-005, REQ-REG-006, REQ-REG-008, REQ-REG-009, REQ-REG-010, REQ-REG-011, REQ-REG-013, REQ-REG-014, REQ-REG-016, REQ-REG-017, REQ-REG-019, REQ-REG-020, REQ-REG-023, REQ-REG-024, REQ-REG-025, REQ-REG-026, REQ-REG-029, REQ-REG-036, REQ-REG-037, REQ-REG-042, REQ-REG-044, REQ-REG-045, REQ-REG-046, REQ-REG-048, REQ-REG-049, REQ-REG-050, REQ-REG-051, REQ-REG-054, REQ-REG-056, REQ-REG-057, REQ-REG-059, REQ-REG-060, REQ-REG-064, REQ-REG-067, REQ-REG-068, REQ-REG-070 -->
<!-- SUB:API-SCENARIOS:START traces=AC-REG-001,AC-REG-002,AC-REG-005,AC-REG-006,AC-REG-008,AC-REG-009,AC-REG-010,AC-REG-011,AC-REG-014,AC-REG-015,AC-REG-017,AC-REG-018,AC-REG-020,AC-REG-021,AC-REG-024,AC-REG-025,AC-REG-026,AC-REG-027,AC-REG-030,AC-REG-038,AC-REG-039,AC-REG-044,AC-REG-046,AC-REG-047,AC-REG-048,AC-REG-050,AC-REG-051,AC-REG-052,AC-REG-053,AC-REG-056,AC-REG-058,AC-REG-059,AC-REG-061,AC-REG-062,REQ-REG-001,REQ-REG-002,REQ-REG-005,REQ-REG-006,REQ-REG-008,REQ-REG-009,REQ-REG-010,REQ-REG-011,REQ-REG-013,REQ-REG-014,REQ-REG-016,REQ-REG-017,REQ-REG-019,REQ-REG-020,REQ-REG-023,REQ-REG-024,REQ-REG-025,REQ-REG-026,REQ-REG-029,REQ-REG-036,REQ-REG-037,REQ-REG-042,REQ-REG-044,REQ-REG-045,REQ-REG-046,REQ-REG-048,REQ-REG-049,REQ-REG-050,REQ-REG-051,REQ-REG-054,REQ-REG-056,REQ-REG-057,REQ-REG-059,REQ-REG-060,API-REG-001,API-REG-002,API-REG-003,AC-REG-066,REQ-REG-064,AC-REG-068,AC-REG-072,REQ-REG-067,AC-REG-073,REQ-REG-068,AC-REG-081,AC-REG-082,REQ-REG-070 -->
## SUB API-SCENARIOS

Endpoint- and state-driven acceptance — the three reads, the load run's outcomes, version history, connections and the in-process supply. (35 TCs)

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

<!-- TC:TC-REG-089:START traces=AC-REG-082,REQ-REG-070 -->
### TC-REG-089 — A Check's pinned version is supplied after a newer version became current
Derived from : AC-REG-082  (REQ-REG-070)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : EDGE · data class VALID · language en
Preconditions: Start 1 stores `scholarship-request` version 3 (fetch `path`, document source query `documents` with type column `doc_type` and path column `file_path`, required TRANSCRIPT and ID_CARD). A Check resolves it through `getCurrentServicePackage("scholarship-request")` and pins versionNumber 3. Start 2 then stores version 4 (fetch `manual`, required TRANSCRIPT) as current.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. After start 2, request `getServicePackageVersion("scholarship-request", 3)` — the read DOC's document fetch of that Check makes (ADR-REG-020). 2. Request `getCurrentServicePackage("scholarship-request")`.
Expected     : Step 1 returns versionNumber 3, fetchMode `path`, document source {documentSourceQueryName `documents`, documentTypeColumn `doc_type`, documentPathColumn `file_path`} and requiredDocumentTypes TRANSCRIPT, ID_CARD — unchanged by version 4; step 2 returns version 4 (the resolution step of a new Check only).
Test data    : versions 3 and 4 of `scholarship-request`
<!-- TC:TC-REG-089:END -->

<!-- SUB:API-SCENARIOS:END -->
