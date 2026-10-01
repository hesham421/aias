<!-- source: PHASE:CORE -->
<!-- traces: REQ-REG-016, REQ-REG-034, REQ-REG-059, REQ-REG-064, REQ-REG-068 -->
<!-- PHASE:CORE:START traces=REQ-REG-034,REQ-REG-059,REQ-REG-016,REQ-REG-064,REQ-REG-068 -->
## PHASE CORE — CORE

### R1 — Core configuration
- Type mapping (oracle19c → Java), column types only:

| oracle19c | Java |
|---|---|
| NUMBER(19) | Long |
| VARCHAR2(n CHAR) | String |
| NUMBER(1) | Boolean (0/1) |
| TIMESTAMP WITH TIME ZONE | OffsetDateTime |
| NUMBER(10,0) | Integer |
| CLOB | String (@Lob) |

- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` — every error-catalog row below is an instance of it, e.g. `REG-404-SERVICE-NOT-FOUND`.
- Error envelope: ProblemDetail (RFC 9457) → {type, title, status, detail, code}.
- Lookup values: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded. REG stores codes (FETCH_MODE, CONNECTION_TYPE, LOAD_OUTCOME, LOAD_SUBJECT as Java enums matching the CHECK constraints of db-script-reg.md; SERVICE_CODE and DOCUMENT_TYPE as strings from package data — ADR-REG-010).
- Workflow engine: **forbidden**.
- Search contract: no SRS screen, so no filter list; API-REG-001 and API-REG-003 return full lists ordered by service code / subject; empty result = 200 with an empty array.
- Languages: messages en (SRS); ar PENDING ADR-REG-011.
- Configuration properties (environment settings, never in the deployable): `aias.registry.package-directory` (ADR-REG-007), `aias.registry.environment-name` and `aias.registry.connections[]` with `name, type, endpoint, query-tool, dialect, credential-reference, read-only, limited-to-views` (ADR-REG-009), `aias.registry.load-lock-timeout` (Duration, default PT30S — how long a starting instance waits for the load lock, REQ-REG-068, ADR-REG-015). The check limits are platform configuration and are never read from a service definition (ADR-REG-006, REQ-REG-034): `aias.check.timeout` (Duration, default PT2M), `aias.check.max-rows` (int, default 100), `aias.check.max-file-size` (DataSize, default 10MB) — declared and applied by the CORE phase of CHK and DOC; REG reads none of them and rejects `timeout`, `max_rows`, `max_file_size` in a service definition (RULE-REG-012, ADR-REG-019).
- Service codes (ADR-REG-017): one `ServiceCodes.canonical(code)` = trim + lower case (Locale.ROOT) is applied to every declared code and to every code a read or in-process call receives, before any comparison or query; only canonical codes are stored (REQ-REG-064).
- No request data is held by REG (REQ-REG-059): no REG table, DTO or cache carries a request number, employee identity, query result or document content.
- Packages are read only from `aias.registry.package-directory` (REQ-REG-016); a folder path is resolved and normalised and must lie inside that directory.
<!-- PHASE:CORE:END -->
