<!-- source: PHASE:SVC-API -->
<!-- traces: DBF-REG-002, DBF-REG-003, DBF-REG-007, DBF-REG-011, DBF-REG-016, DBF-REG-027, DBF-REG-040, DBF-REG-041, DBF-REG-042, DBF-REG-043, DBF-REG-044, DBF-REG-045, DBF-REG-046, DBF-REG-047, REQ-REG-005, REQ-REG-007, REQ-REG-008, REQ-REG-013, REQ-REG-014, REQ-REG-015, REQ-REG-024, REQ-REG-030, REQ-REG-048, REQ-REG-063, REQ-REG-064, REQ-REG-065, REQ-REG-066, REQ-REG-067, REQ-REG-068, REQ-REG-069, REQ-REG-070, REQ-REG-071, REQ-REG-072, REQ-REG-073, REQ-REG-074 -->
<!-- PHASE:SVC-API:START traces=DBF-REG-002,DBF-REG-003,DBF-REG-007,DBF-REG-011,DBF-REG-016,DBF-REG-027,DBF-REG-040,DBF-REG-041,DBF-REG-042,DBF-REG-043,DBF-REG-044,DBF-REG-045,DBF-REG-046,DBF-REG-047,REQ-REG-005,REQ-REG-007,REQ-REG-008,REQ-REG-013,REQ-REG-014,REQ-REG-015,REQ-REG-024,REQ-REG-030,REQ-REG-048,REQ-REG-063,REQ-REG-064,REQ-REG-065,REQ-REG-066,REQ-REG-067,REQ-REG-068,REQ-REG-069,REQ-REG-070,REQ-REG-071,REQ-REG-072,REQ-REG-073,REQ-REG-074 -->
## PHASE SVC-API — SVC+API

### Service layer — the start-up load run (ADR-REG-007, ADR-REG-011)
`RegistryLoadRun` runs once per start, after the schema is ready and before the service accepts traffic, as ONE READ_WRITE transaction under one exclusive database lock (REQ-REG-067, ADR-REG-015); each package and each connection is processed behind its own savepoint, so a failing item rolls back to its savepoint only and never undoes another (REQ-REG-007). Steps, in order:
0. **Take the load lock** — the transaction's first statement is `LOCK TABLE REG_LOAD_RESULT IN EXCLUSIVE MODE WAIT {aias.registry.load-lock-timeout in seconds}`; the lock is held until the run commits (or rolls back) after step 6. If it is not granted in time (ORA-30006) the run ends without writing anything and the instance serves the registry as stored (REQ-REG-068). An instance granted the lock after another instance committed runs a full, idempotent load run (unchanged packages → UNCHANGED). Readers never wait: they see the last committed run only (QR-REG-005 always returns one complete run).
1. **Start the run** — loadRunAt = now; delete every REG_LOAD_RESULT row of earlier runs (REQ-REG-009).
2. **Activate connections** (REQ-REG-049 … REQ-REG-056, ADR-REG-009) — read `ActivationSource`; refuse a name listed twice (RULE-REG-013), a type outside mcp | jdbc (RULE-REG-014), an entry not declared read-only (RULE-REG-015), an entry with a text value longer than its column — name 100, endpoint 500, query tool 100, dialect 30, credential reference 200, or an environment name longer than 50, which refuses every entry (RULE-REG-027, REQ-REG-073, ADR-REG-022), and an entry whose type is not `jdbc` while any stored version with FETCH_MODE = 'blob' has its document source query on that connection name (RULE-REG-025, REQ-REG-072, ADR-REG-021 — an existing row keeps every previous setting, a removed name is not re-registered); insert new REG_CONNECTION rows (ACTIVATED), update rows whose settings changed (UPDATED, REQ-REG-050), delete rows no longer listed (REMOVED, REQ-REG-051); store credentialReference only (REQ-REG-054). Each outcome → one REG_LOAD_RESULT row (subjectKind CONNECTION).
3. **Load packages** (REQ-REG-005 … REQ-REG-007, REQ-REG-016) — guard first (REQ-REG-066, RULE-REG-023, ADR-REG-018): if `PackageSource` reports MISSING or UNREADABLE, or EMPTY while REG_SVC_PKG holds ≥ 1 row with AVAILABLE = 1, record one REG_LOAD_RESULT row (subjectKind PACKAGE_DIRECTORY, subjectName = the configured path, outcome REJECTED, reason REG-LOAD-PACKAGE-DIRECTORY-UNAVAILABLE) and skip steps 4 and 5 entirely — no version is registered and no service is withdrawn or restored; EMPTY with no available service is a normal empty load. Otherwise, for every folder: validate in this order RULE-REG-026 (every file readable — REQ-REG-071), RULE-REG-024 (stable read), RULE-REG-020 (only two files), RULE-REG-001 (both files), RULE-REG-018 (knowledge not empty), RULE-REG-012 (closed structure), RULE-REG-022 (valid service code, after canonicalisation), RULE-REG-027 (folder name ≤ 200 and every bounded definition value within its column — REQ-REG-073), RULE-REG-008, RULE-REG-019, RULE-REG-021 (unique required document types), RULE-REG-006, RULE-REG-007, RULE-REG-005, RULE-REG-009, RULE-REG-010, RULE-REG-011; then RULE-REG-002 across folders on canonical codes (REQ-REG-004, REQ-REG-064) — counted over every folder whose service definition declared a readable service code, whether or not that folder failed a per-folder rule: a valid folder whose twin failed for another reason is still rejected under RULE-REG-002, while the twin keeps its own first reason (one Load Result row per folder). A failing folder → REJECTED with the rule's load reason code and message (REQ-REG-007, REQ-REG-061); processing continues with the next folder.
   Every Load Result row of steps 2–5 is written by `LoadResultRecorder`, which shortens subjectName / serviceCode / reason to 200 / 100 / 1000 characters with a trailing "…" (RULE-REG-028, REQ-REG-074); the PACKAGE_DIRECTORY row's subjectName is the configured path shortened the same way.
4. **Register versions** (REQ-REG-006, REQ-REG-019 … REQ-REG-023, ADR-REG-003) — contentHash = SHA-256 of knowledge + definition; unknown service code → insert REG_SVC_PKG (available = 1, registeredAt = now) and version (REGISTERED); declared version > current → insert version (REGISTERED, becomes current); equal number + equal hash → UNCHANGED; equal number + different hash → RULE-REG-003; lower and not stored → RULE-REG-004. A version insert writes REG_SVC_PKG_VER with its REG_SVC_QUERY and REG_REQ_DOC rows in one transaction; concurrent load runs are excluded by the load lock of step 0 (ADR-REG-015); the unique constraint UQ_REG_SVC_PKG_VER_PKG_VERSION stays as a backstop. No version, query or required document is ever updated or deleted (REQ-REG-026).
5. **Withdraw / restore** — a stored service code with no folder → available = 0, withdrawnAt = now, WITHDRAWN (REQ-REG-010); a withdrawn code with a valid folder → available = 1, withdrawnAt = null (REQ-REG-011). Stored versions stay (REQ-REG-026).
6. The pilot package `scholarship-request` is delivered as a folder of the package directory (fetch `path`, required TRANSCRIPT and ID_CARD, input `requestId`, numeric thresholds written as digits — REQ-REG-057, REQ-REG-058); it passes through steps 3–4 like any other package.

### Load reason codes (Load Result REASON — not HTTP errors, ADR-REG-011)
| Rule | Load reason code | Message en | Message ar |
|---|---|---|---|
| RULE-REG-001 | REG-LOAD-INCOMPLETE-PACKAGE | The service package "{folder}" is incomplete: it needs both its service knowledge and its service definition. | PENDING ADR-REG-011 |
| RULE-REG-002 | REG-LOAD-DUPLICATE-SERVICE-CODE | The service code "{serviceCode}" is declared by more than one package folder; keep one folder per service. | PENDING ADR-REG-011 |
| RULE-REG-003 | REG-LOAD-VERSION-EDITED-IN-PLACE | Version {versionNumber} of "{serviceCode}" already exists with different content; publish the change as a new version. | PENDING ADR-REG-011 |
| RULE-REG-004 | REG-LOAD-VERSION-OLDER-THAN-CURRENT | Version {versionNumber} of "{serviceCode}" is older than the current version {currentVersion}. | PENDING ADR-REG-011 |
| RULE-REG-005 | REG-LOAD-CONNECTION-NOT-ACTIVATED | The query "{queryName}" uses the connection "{connectionName}", which is not activated in this environment. | PENDING ADR-REG-011 |
| RULE-REG-006 | REG-LOAD-UNBOUND-PARAMETER | The query "{queryName}" uses "{marker}"; queries take only the bind parameter ":{inputName}". | PENDING ADR-REG-011 |
| RULE-REG-007 | REG-LOAD-NOT-SINGLE-SELECT | The query "{queryName}" must be one SELECT statement; it cannot change data or run several statements. | PENDING ADR-REG-011 |
| RULE-REG-008 | REG-LOAD-UNKNOWN-FETCH-MODE | The fetch mode "{fetchMode}" is not supported; use path, blob or manual. | PENDING ADR-REG-011 |
| RULE-REG-009 | REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE | The documents of "{serviceCode}" cannot be fetched: the document source is incomplete. | PENDING ADR-REG-011 |
| RULE-REG-010 | REG-LOAD-BLOB-NOT-JDBC | Documents stored in the database are read over a read-only jdbc connection; "{connectionName}" is not one. | PENDING ADR-REG-011 |
| RULE-REG-011 | REG-LOAD-APPROVAL-API-UNDEFINED | The approval API of "{serviceCode}" is enabled but not defined. | PENDING ADR-REG-011 |
| RULE-REG-012 | REG-LOAD-ELEMENT-NOT-ALLOWED | The service definition of "{serviceCode}" contains "{element}", which a service definition cannot set. | PENDING ADR-REG-011 |
| RULE-REG-013 | REG-LOAD-DUPLICATE-CONNECTION-NAME | The connection name "{connectionName}" is listed more than once. | PENDING ADR-REG-011 |
| RULE-REG-014 | REG-LOAD-UNKNOWN-CONNECTION-TYPE | The connection "{connectionName}" has the type "{connectionType}"; use mcp or jdbc. | PENDING ADR-REG-011 |
| RULE-REG-015 | REG-LOAD-CONNECTION-NOT-READ-ONLY | The connection "{connectionName}" must use a read-only database user. | PENDING ADR-REG-011 |
| RULE-REG-018 | REG-LOAD-EMPTY-SERVICE-KNOWLEDGE | The service knowledge of "{serviceCode}" is empty. | PENDING ADR-REG-011 |
| RULE-REG-019 | REG-LOAD-DUPLICATE-QUERY-NAME | The query name "{queryName}" is used twice in "{serviceCode}". | PENDING ADR-REG-011 |
| RULE-REG-020 | REG-LOAD-FOREIGN-FILE-IN-PACKAGE | The package folder "{folder}" holds "{file}"; a package folder holds only its service knowledge and its service definition. | PENDING ADR-REG-011 |
| RULE-REG-021 | REG-LOAD-DUPLICATE-DOCUMENT-TYPE | The document type "{documentType}" is required more than once in "{serviceCode}". | PENDING ADR-REG-011 |
| RULE-REG-022 | REG-LOAD-INVALID-SERVICE-CODE | The service code "{serviceCode}" is not valid; use lower-case letters, digits and single hyphens, at most 100 characters. | PENDING ADR-REG-011 |
| RULE-REG-023 | REG-LOAD-PACKAGE-DIRECTORY-UNAVAILABLE | The package directory "{directory}" is missing, unreadable or empty; no service was loaded or withdrawn. | PENDING ADR-REG-011 |
| RULE-REG-024 | REG-LOAD-PACKAGE-CHANGED-DURING-READ | The package folder "{folder}" changed while it was being read; publish it again and restart. | PENDING ADR-REG-011 |
| RULE-REG-025 | REG-LOAD-CONNECTION-TYPE-BREAKS-BLOB | The connection "{connectionName}" must stay of type jdbc: version {versionNumber} of "{serviceCode}" reads its documents through it. | PENDING ADR-REG-011 |
| RULE-REG-026 | REG-LOAD-PACKAGE-FILE-UNREADABLE | The file "{file}" of the package folder "{folder}" cannot be read; check its permissions and restart. | PENDING ADR-REG-011 |
| RULE-REG-027 | REG-LOAD-VALUE-TOO-LONG | The {field} of "{subject}" has {length} characters; at most {limit} are allowed. | PENDING ADR-REG-011 |

RULE-REG-028 has no reason code: it shortens Load Result text and refuses nothing. RULE-REG-017 is not a load reason: it is refused at serve time with the in-process code below and never written to REG_LOAD_RESULT.

### Service layer — the in-process interface `ServiceRegistry` (contract-reg.md)
Injected into the consumer modules (profile `module_interface: in_process`); every method canonicalises a received service code first (trim + lower case — REQ-REG-064) and is READ_ONLY and returns immutable value objects (Java records with unmodifiable lists — REQ-REG-060). The service knowledge is a separate field from the queries and document settings (REQ-REG-018, REQ-REG-030); no method returns request data (REQ-REG-059).
- `getCurrentServicePackage(serviceCode)` → ServicePackage {serviceCode, versionNumber, serviceKnowledge, inputName, queries[queryName, connectionName, sqlText], fetchMode, documentSource, requiredDocumentTypes}; refuses with `ServiceNotAvailableException` (RULE-REG-016) for an unknown or withdrawn code and `ServiceConnectionNotActivatedException` (RULE-REG-017, in-process code REG-SERVE-CONNECTION-NOT-ACTIVATED — the REG-SERVE-* namespace marks serve-time refusals that never appear in the load report) when a query names a connection absent from REG_CONNECTION (REQ-REG-012, REQ-REG-024, REQ-REG-027, REQ-REG-029, REQ-REG-053). Carries no approval API (REQ-REG-044). This is the resolution step of a new Check (CHK pins the answer's versionNumber) and a non-Check current read; it is never the source of a running Check's configuration (ADR-REG-020).
  - Honours: CON-REG-007
- `getServicePackageVersion(serviceCode, versionNumber)` → ServicePackageVersion {serviceCode, versionNumber, serviceKnowledge, serviceDefinition, inputName, queries[queryName, connectionName, sqlText], fetchMode, documentSource, requiredDocumentTypes, approvalEnabled} — the exact stored version whatever version is current and whether or not the service was withdrawn since; `VersionNotFoundException` otherwise (REQ-REG-025, REQ-REG-070). Every read inside a running Check, by any consumer, goes through this method with the Check's pinned version (ADR-REG-020; DOC v1 still reads CON-REG-007 at fetch time until DOC v2 — project-registry PF-7).
  - Honours: CON-REG-009
- `getConnection(connectionName)` → ConnectionSettings {connectionName, connectionType, endpoint, queryTool, dialect, credentialReference, limitedToViews}; `ConnectionNotFoundException` otherwise (REQ-REG-048).
  - Honours: CON-REG-011
- `getApprovalApi(serviceCode, versionNumber)` → {approvalEnabled, approvalApi when enabled} — exposed on a separate interface `ApprovalApiRegistry` that only the Employee Decision operation is given; no other bean, and never the LLM, receives it (REQ-REG-044, REQ-REG-045; AIAS-3, AIAS-4).
  - Honours: CON-REG-012
- `isServiceAvailable(serviceCode)` → boolean (REQ-REG-012).
  - Honours: CON-REG-013

### HTTP endpoints
Controller `ServiceRegistryController` → service `ServiceRegistryQueryService`. REG exposes no POST, PUT or DELETE (REQ-REG-017, ADR-REG-007).

<!-- API:API-REG-001:START traces=REQ-REG-013,REQ-REG-030,DBF-REG-002,DBF-REG-003,DBF-REG-007,DBF-REG-011,DBF-REG-016,DBF-REG-027 -->
### API-REG-001 — List services
Entity       : ENT-REG-001
Endpoint     : /api/v1/services   verb: GET
Layers       : controller ServiceRegistryController → service ServiceRegistryQueryService
Request      : no path or query parameter; no body
Response     : 200 · array of ServiceSummary {serviceCode (DBF-REG-002), available (DBF-REG-003 — always true here, the list holds available services only; ADR-REG-016), versionNumber (DBF-REG-007), fetchMode (DBF-REG-011), requiredDocumentTypes (DBF-REG-027), approvalEnabled (DBF-REG-016)} · not paginated · no envelope; never SQL text or connection settings (REQ-REG-030)
Validations  : none
Errors       : REG-500 (PLATFORM-STD)
Orchestration : load available packages (QR-REG-001, filter on DBF-REG-003) → per package its current version (QR-REG-002) and its document types (QR-REG-003) → map to ServiceSummary (available = true); writes nothing
Repository   : QR-REG-001, QR-REG-002, QR-REG-003 · FIND_BY_CRITERIA / FIND_ONE / FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; REQ-REG-013, REQ-REG-014, REQ-REG-008 name no role check)
Localization : messages en per SRS; ar PENDING ADR-REG-011
Honours      : CON-REG-008
Covers       : REQ-REG-017 (read-only surface), REQ-REG-059 (no request data in any response), REQ-REG-060 (responses are copies; nothing a caller sends changes the registry)
<!-- API:API-REG-001:END -->

<!-- API:API-REG-002:START traces=REQ-REG-014,REQ-REG-015,REQ-REG-064,DBF-REG-002,DBF-REG-003,DBF-REG-007,DBF-REG-011,DBF-REG-016,DBF-REG-027 -->
### API-REG-002 — Read one service
Entity       : ENT-REG-001
Endpoint     : /api/v1/services/{serviceCode}   verb: GET
Layers       : controller ServiceRegistryController → service ServiceRegistryQueryService
Request      : path serviceCode (DBF-REG-002, string ≤ 100, required; trimmed and lower-cased before lookup — REQ-REG-064, ADR-REG-017); no body
Response     : 200 · ServiceSummary {serviceCode (canonical), available (DBF-REG-003: false when withdrawn — ADR-REG-016), versionNumber, fetchMode, requiredDocumentTypes, approvalEnabled} of the current version, available or withdrawn · no envelope
Validations  : RULE-REG-016 — Withdrawn or unknown service not supplied · trigger: on package request and on service read · statement: The system shall refuse to supply or return a service when its service code is not held by the registry or its Service Package is withdrawn (for a read, only an unknown code is refused). · message (en): The service "{serviceCode}" is not available. · message (ar): PENDING ADR-REG-011
Errors       : REG-404-SERVICE-NOT-FOUND (404, RULE-REG-016) · REG-500 (PLATFORM-STD)
Orchestration : canonicalise the code → load package by code (QR-REG-004) → none → REG-404-SERVICE-NOT-FOUND → current version (QR-REG-002) → document types (QR-REG-003) → ServiceSummary; writes nothing
Repository   : QR-REG-004, QR-REG-002, QR-REG-003 · FIND_ONE / FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; REQ-REG-013, REQ-REG-014, REQ-REG-008 name no role check)
Localization : messages en per SRS; ar PENDING ADR-REG-011
Honours      : CON-REG-010
Covers       : REQ-REG-026 (every stored version stays; no delete operation exists), REQ-REG-057 (the pilot `scholarship-request` is readable here)
<!-- API:API-REG-002:END -->

<!-- API:API-REG-003:START traces=REQ-REG-008,REQ-REG-007,DBF-REG-040,DBF-REG-041,DBF-REG-042,DBF-REG-043,DBF-REG-044,DBF-REG-045,DBF-REG-046,DBF-REG-047 -->
### API-REG-003 — Read the load report
Entity       : ENT-REG-006
Endpoint     : /api/v1/load-results   verb: GET
Layers       : controller ServiceRegistryController → service ServiceRegistryQueryService
Request      : no path or query parameter; no body
Response     : 200 · array of LoadResult {loadResultId (DBF-REG-040), loadRunAt (DBF-REG-041), subjectKind (DBF-REG-042), subjectName (DBF-REG-043), serviceCode (DBF-REG-044), versionNumber (DBF-REG-045), outcome (DBF-REG-046), reason (DBF-REG-047)} of the latest run · not paginated · no envelope
Validations  : none
Errors       : REG-500 (PLATFORM-STD)
Orchestration : load the latest run's rows (QR-REG-005) → map to LoadResult; writes nothing
Repository   : QR-REG-005 · FIND_BY_CRITERIA · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; REQ-REG-013, REQ-REG-014, REQ-REG-008 name no role check)
Localization : messages en per SRS; ar PENDING ADR-REG-011
Covers       : the outcomes recorded by the load run for REQ-REG-071, REQ-REG-072, REQ-REG-073, REQ-REG-074, REQ-REG-003, REQ-REG-004, REQ-REG-005, REQ-REG-016, REQ-REG-051, REQ-REG-058, REQ-REG-061, REQ-REG-062, REQ-REG-063, REQ-REG-065, REQ-REG-066, REQ-REG-067, REQ-REG-069 (see the load run and the load reason codes above)
<!-- API:API-REG-003:END -->

<!-- PHASE:SVC-API:END -->
