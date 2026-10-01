<!-- source: PHASE:TEST-PLAN-BE / SUB:RULE-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-REG-003, AC-REG-004, AC-REG-007, AC-REG-012, AC-REG-013, AC-REG-016, AC-REG-022, AC-REG-023, AC-REG-029, AC-REG-032, AC-REG-033, AC-REG-034, AC-REG-035, AC-REG-036, AC-REG-037, AC-REG-040, AC-REG-041, AC-REG-042, AC-REG-043, AC-REG-045, AC-REG-049, AC-REG-054, AC-REG-055, AC-REG-057, AC-REG-063, AC-REG-064, AC-REG-065, AC-REG-067, AC-REG-069, AC-REG-070, AC-REG-071, AC-REG-074, AC-REG-075, AC-REG-076, AC-REG-077, AC-REG-078, AC-REG-079, AC-REG-080, AC-REG-083, AC-REG-084, AC-REG-085, AC-REG-086, AC-REG-087, AC-REG-088, AC-REG-089, AC-REG-090, AC-REG-091, API-REG-001, API-REG-002, API-REG-003, REQ-REG-003, REQ-REG-004, REQ-REG-007, REQ-REG-012, REQ-REG-015, REQ-REG-021, REQ-REG-022, REQ-REG-028, REQ-REG-031, REQ-REG-032, REQ-REG-033, REQ-REG-034, REQ-REG-035, REQ-REG-038, REQ-REG-039, REQ-REG-040, REQ-REG-041, REQ-REG-043, REQ-REG-047, REQ-REG-052, REQ-REG-053, REQ-REG-055, REQ-REG-061, REQ-REG-062, REQ-REG-063, REQ-REG-064, REQ-REG-065, REQ-REG-066, REQ-REG-069, REQ-REG-071, REQ-REG-072, REQ-REG-073, REQ-REG-074, RULE-REG-001, RULE-REG-002, RULE-REG-003, RULE-REG-004, RULE-REG-005, RULE-REG-006, RULE-REG-007, RULE-REG-008, RULE-REG-009, RULE-REG-010, RULE-REG-011, RULE-REG-012, RULE-REG-013, RULE-REG-014, RULE-REG-015, RULE-REG-016, RULE-REG-017, RULE-REG-018, RULE-REG-019, RULE-REG-020, RULE-REG-021, RULE-REG-022, RULE-REG-023, RULE-REG-024, RULE-REG-025, RULE-REG-026, RULE-REG-027, RULE-REG-028 -->
<!-- SUB:RULE-SCENARIOS:START traces=AC-REG-003,AC-REG-004,AC-REG-007,AC-REG-012,AC-REG-013,AC-REG-016,AC-REG-022,AC-REG-023,AC-REG-029,AC-REG-032,AC-REG-033,AC-REG-034,AC-REG-035,AC-REG-036,AC-REG-037,AC-REG-040,AC-REG-041,AC-REG-042,AC-REG-043,AC-REG-045,AC-REG-049,AC-REG-054,AC-REG-055,AC-REG-057,AC-REG-063,AC-REG-064,REQ-REG-003,REQ-REG-004,REQ-REG-007,REQ-REG-012,REQ-REG-015,REQ-REG-021,REQ-REG-022,REQ-REG-028,REQ-REG-031,REQ-REG-032,REQ-REG-033,REQ-REG-034,REQ-REG-035,REQ-REG-038,REQ-REG-039,REQ-REG-040,REQ-REG-041,REQ-REG-043,REQ-REG-047,REQ-REG-052,REQ-REG-053,REQ-REG-055,REQ-REG-061,REQ-REG-062,API-REG-002,API-REG-003,RULE-REG-001,RULE-REG-002,RULE-REG-003,RULE-REG-004,RULE-REG-005,RULE-REG-006,RULE-REG-007,RULE-REG-008,RULE-REG-009,RULE-REG-010,RULE-REG-011,RULE-REG-012,RULE-REG-013,RULE-REG-014,RULE-REG-015,RULE-REG-016,RULE-REG-017,RULE-REG-018,RULE-REG-019,RULE-REG-020,AC-REG-065,REQ-REG-063,RULE-REG-021,AC-REG-067,REQ-REG-064,AC-REG-069,REQ-REG-065,RULE-REG-022,AC-REG-070,REQ-REG-066,API-REG-001,RULE-REG-023,AC-REG-071,AC-REG-074,REQ-REG-069,RULE-REG-024,AC-REG-075,AC-REG-076,AC-REG-077,AC-REG-078,AC-REG-079,AC-REG-080,AC-REG-083,REQ-REG-071,RULE-REG-026,AC-REG-084,AC-REG-085,REQ-REG-072,RULE-REG-025,AC-REG-086,AC-REG-087,AC-REG-088,REQ-REG-073,RULE-REG-027,AC-REG-089,AC-REG-090,REQ-REG-074,RULE-REG-028,AC-REG-091 -->
## SUB RULE-SCENARIOS

Rule-driven acceptance — every load-time and read-time refusal, asserted through the load report (API-REG-003), API-REG-002 or the in-process interface. (48 TCs)

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
Rule / code  : RULE-REG-017 → ServiceConnectionNotActivatedException, in-process code REG-SERVE-CONNECTION-NOT-ACTIVATED (never a load reason)
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
Expected     : The package is rejected and no Service Package is created; the load report records one row with outcome REJECTED and reason equal to the RULE-REG-022 message (en) «The service code "scholarship request!" is not valid; use lower-case letters, digits and single hyphens, at most 100 characters.» (ar: PENDING ADR-REG-011). Sub-cases, one folder each, each run separately and each asserted the same way — REJECTED, the RULE-REG-022 message with the code filled in, 0 Service Packages for it: (b) `a_b` (underscore); (c) `-a` (leading hyphen); (d) `a-` (trailing hyphen); (e) `a--b` (double hyphen); (f) a 101-character code (`a` × 101); (g) an empty code (`""` or only spaces). The boundary pass at exactly 100 characters is TC-REG-099.
Test data    : (a) `scholarship request!` · (b) `a_b` · (c) `-a` · (d) `a-` · (e) `a--b` · (f) `a` × 101 · (g) `""`
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

<!-- TC:TC-REG-090:START traces=AC-REG-083,REQ-REG-071,API-REG-003,API-REG-002,RULE-REG-026 -->
### TC-REG-090 — A present package folder with an unreadable file is rejected and the run continues
Derived from : AC-REG-083  (REQ-REG-071)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-026 → REG-LOAD-PACKAGE-FILE-UNREADABLE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The package directory holds 3 otherwise valid folders `scholarship-request`, `vehicle-permit` and `demo-service`; the file `service.yaml` of `demo-service` exists but reading it fails with a permission error (file mode 000 for the service account).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The run completes (no aborted load run): 3 SERVICE_PACKAGE rows — `scholarship-request` and `vehicle-permit` REGISTERED, `demo-service` REJECTED with the RULE-REG-026 message (en) «The file "service.yaml" of the package folder "demo-service" cannot be read; check its permissions and restart.» (ar: PENDING ADR-REG-011); API-REG-002 GET /api/v1/services/demo-service → 404.
Test data    : folder `demo-service`, unreadable `service.yaml`
<!-- TC:TC-REG-090:END -->

<!-- TC:TC-REG-091:START traces=AC-REG-084,REQ-REG-072,API-REG-003,RULE-REG-025 -->
### TC-REG-091 — A re-activation that turns a blob version's connection into mcp is refused
Derived from : AC-REG-084  (REQ-REG-072)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-025 → REG-LOAD-CONNECTION-TYPE-BREAKS-BLOB (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: Start 1: the activation configuration lists `main-db` (jdbc, endpoint `jdbc:oracle:thin:@db1:1521/APP`, read-only) and the package directory holds `archive-request` version 1 with `fetch: blob` and its document source query on `main-db`; start 1 stores version 1. Start 2: the activation configuration lists `main-db` with type `mcp` and endpoint `http://mcp1:8080`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Run start 1, then start 2. 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Request `getConnection("main-db")` and `getCurrentServicePackage("archive-request")` in-process.
Expected     : After start 2: 1 CONNECTION row for `main-db`, outcome REJECTED, reason equal to the RULE-REG-025 message (en) «The connection "main-db" must stay of type jdbc: version 1 of "archive-request" reads its documents through it.» (ar: PENDING ADR-REG-011); `getConnection("main-db")` returns connectionType `jdbc` and endpoint `jdbc:oracle:thin:@db1:1521/APP`; `getCurrentServicePackage("archive-request")` supplies version 1.
Test data    : connection `main-db` jdbc → mcp; service `archive-request` v1 blob
<!-- TC:TC-REG-091:END -->

<!-- TC:TC-REG-092:START traces=AC-REG-085,REQ-REG-072,API-REG-003,RULE-REG-025 -->
### TC-REG-092 — A removed connection is not re-registered as mcp while a blob version reads through it
Derived from : AC-REG-085  (REQ-REG-072)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-025 → REG-LOAD-CONNECTION-TYPE-BREAKS-BLOB (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: Stored version 1 of `archive-request` has `fetch: blob` with its document source query on `main-db`; `main-db` was removed at an earlier start (absent from REG_CONNECTION); the activation configuration now lists `main-db` with type `mcp`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Request `getConnection("main-db")` in-process.
Expected     : 1 CONNECTION row for `main-db`, outcome REJECTED, reason equal to the RULE-REG-025 message (en) «The connection "main-db" must stay of type jdbc: version 1 of "archive-request" reads its documents through it.»; `getConnection("main-db")` → ConnectionNotFoundException (no Connection `main-db` registered).
Test data    : connection `main-db` (removed, relisted as mcp)
<!-- TC:TC-REG-092:END -->

<!-- TC:TC-REG-093:START traces=AC-REG-086,REQ-REG-073,API-REG-003,API-REG-002,RULE-REG-027 -->
### TC-REG-093 — A folder whose name exceeds 200 characters is rejected; the run continues
Derived from : AC-REG-086  (REQ-REG-073)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-027 → REG-LOAD-VALUE-TOO-LONG (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : BOUNDARY · data class INVALID · language en
Preconditions: The package directory holds 2 valid folders and 1 otherwise valid folder whose name is 201 characters (`p` × 201) declaring `long-folder-service` version 1.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The run completes: 3 SERVICE_PACKAGE rows — 2 REGISTERED and 1 REJECTED; the REJECTED row's reason is the RULE-REG-027 message with field `folder name`, length 201 and limit 200, i.e. (en) «The folder name of "pppp…" has 201 characters; at most 200 are allowed.» shortened if needed to 1000 characters; its subjectName has exactly 200 characters (199 × `p` followed by «…» — RULE-REG-028); API-REG-002 GET /api/v1/services/long-folder-service → 404.
Test data    : folder name of 201 characters
<!-- TC:TC-REG-093:END -->

<!-- TC:TC-REG-094:START traces=AC-REG-087,REQ-REG-073,API-REG-003,RULE-REG-027 -->
### TC-REG-094 — A connection name longer than 100 characters is refused
Derived from : AC-REG-087  (REQ-REG-073)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-027 → REG-LOAD-VALUE-TOO-LONG (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : BOUNDARY · data class INVALID · language en
Preconditions: The activation configuration lists `main-db` (mcp, read-only) and a read-only mcp connection whose name is 101 characters (`c` × 101).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The run completes: `main-db` ACTIVATED; the 101-character connection is not registered and has one CONNECTION row, outcome REJECTED, reason the RULE-REG-027 message (en) «The connection name of "ccc…" has 101 characters; at most 100 are allowed.»; the row's subjectName is the full 101-character name (it fits the 200-character subject field).
Test data    : connection name of 101 characters
<!-- TC:TC-REG-094:END -->

<!-- TC:TC-REG-095:START traces=AC-REG-088,REQ-REG-073,API-REG-003,RULE-REG-027 -->
### TC-REG-095 — A connection endpoint longer than 500 characters is refused
Derived from : AC-REG-088  (REQ-REG-073)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-027 → REG-LOAD-VALUE-TOO-LONG (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : BOUNDARY · data class INVALID · language en
Preconditions: The activation configuration lists `blob-db` (jdbc, read-only) with an endpoint of 501 characters.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : `blob-db` is not registered; 1 CONNECTION row `blob-db`, outcome REJECTED, reason the RULE-REG-027 message (en) «The endpoint of "blob-db" has 501 characters; at most 500 are allowed.» (ar: PENDING ADR-REG-011).
Test data    : endpoint of 501 characters
<!-- TC:TC-REG-095:END -->

<!-- TC:TC-REG-096:START traces=AC-REG-089,REQ-REG-074,API-REG-003,RULE-REG-023,RULE-REG-028 -->
### TC-REG-096 — An over-length package directory path is shortened on its Load Result row, never a load-run failure
Derived from : AC-REG-089  (REQ-REG-074)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-023 → REG-LOAD-PACKAGE-DIRECTORY-UNAVAILABLE (load reason code — ADR-REG-011) · RULE-REG-028 (shortening, no code)
Package      : SVC-API
Scenario     : BOUNDARY · data class EDGE · language en
Preconditions: The registry holds 2 available Service Packages; `aias.registry.package-directory` is a 250-character path (`/srv/` followed by 245 × `d`) that does not exist.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The run completes; 1 row subjectKind PACKAGE_DIRECTORY, outcome REJECTED, subjectName of exactly 200 characters = the first 199 characters of the path followed by «…»; reason = the RULE-REG-023 message with the full path, at most 1000 characters; both Service Packages stay available.
Test data    : 250-character directory path (absent)
<!-- TC:TC-REG-096:END -->

<!-- TC:TC-REG-097:START traces=AC-REG-090,REQ-REG-074,API-REG-003,RULE-REG-022,RULE-REG-028 -->
### TC-REG-097 — An over-length invalid service code is shortened on its Load Result row
Derived from : AC-REG-090  (REQ-REG-074)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-022 → REG-LOAD-INVALID-SERVICE-CODE (load reason code — ADR-REG-011) · RULE-REG-028 (shortening, no code)
Package      : SVC-API
Scenario     : BOUNDARY · data class INVALID · language en
Preconditions: A folder `long-code` whose service definition declares a service code of 150 lower-case letters (`a` × 150).
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : The run completes; the package is rejected with the RULE-REG-022 message (reason at most 1000 characters); the row's serviceCode has exactly 100 characters = 99 × `a` followed by «…»; no Service Package is created.
Test data    : service code `a` × 150
<!-- TC:TC-REG-097:END -->

<!-- TC:TC-REG-098:START traces=AC-REG-091,REQ-REG-004,API-REG-003,API-REG-002,RULE-REG-002,RULE-REG-008 -->
### TC-REG-098 — A valid folder is rejected as a duplicate even when its twin fails another rule
Derived from : AC-REG-091  (REQ-REG-004)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-002 → REG-LOAD-DUPLICATE-SERVICE-CODE (load reason code — ADR-REG-011) · RULE-REG-008 → REG-LOAD-UNKNOWN-FETCH-MODE (load reason code — ADR-REG-011)
Package      : SVC-API
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: The registry holds no `vehicle-permit`; folder `a` is valid and declares `vehicle-permit` version 1; folder `b` declares `Vehicle-Permit` version 1 with `fetch: fax`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results.
Expected     : 2 SERVICE_PACKAGE rows: `a` REJECTED with the RULE-REG-002 message (en) «The service code "vehicle-permit" is declared by more than one package folder; keep one folder per service.»; `b` REJECTED with the RULE-REG-008 message (en) «The fetch mode "fax" is not supported; use path, blob or manual.»; API-REG-002 GET /api/v1/services/vehicle-permit → 404.
Test data    : folders `a`, `b`; codes `vehicle-permit`, `Vehicle-Permit`
<!-- TC:TC-REG-098:END -->

<!-- TC:TC-REG-099:START traces=AC-REG-069,REQ-REG-065,API-REG-003,API-REG-002,RULE-REG-022 -->
### TC-REG-099 — A 100-character service code with single hyphens is accepted (boundary pass)
Derived from : AC-REG-069  (REQ-REG-065)
Exercises    : API-REG-003 GET /api/v1/load-results
Rule / code  : RULE-REG-022 (boundary pass — no reason)
Package      : SVC-API
Scenario     : BOUNDARY · data class VALID · language en
Preconditions: A valid folder whose service definition declares a service code of exactly 100 characters: 49 × `a`, one `-`, 50 × `b`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Start the service (one start-up load run). 2. Read the load report with API-REG-003 GET /api/v1/load-results. 3. Read the service with API-REG-002 GET /api/v1/services/{that code}.
Expected     : 1 SERVICE_PACKAGE row with outcome REGISTERED and no reason; API-REG-002 returns 200 with that 100-character serviceCode and available = true.
Test data    : service code of 100 characters (`a`×49 + `-` + `b`×50)
<!-- TC:TC-REG-099:END -->

<!-- SUB:RULE-SCENARIOS:END -->
