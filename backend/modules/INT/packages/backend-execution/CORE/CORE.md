<!-- source: PHASE:CORE -->
<!-- traces: REQ-INT-007, REQ-INT-008, REQ-INT-014, REQ-INT-037, REQ-INT-057, REQ-INT-059, REQ-INT-060 -->
<!-- PHASE:CORE:START traces=REQ-INT-007,REQ-INT-008,REQ-INT-014,REQ-INT-037,REQ-INT-057,REQ-INT-059,REQ-INT-060 -->
## PHASE CORE — CORE

### R1 — Core configuration
- Type mapping (oracle19c → Java), column types only (for the bound values INT carries):

| oracle19c | Java |
|---|---|
| NUMBER(19) | Long |
| NUMBER(10) | Integer |
| VARCHAR2(n CHAR) | String |
| NUMBER(1) | Boolean (0/1) |
| TIMESTAMP WITH TIME ZONE | OffsetDateTime |

- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` — every error-catalog row below is an instance of it; INT's own rows start with `INT-`, the pass-through rows keep their owner's prefix (ADR-INT-003).
- Error envelope: ProblemDetail (RFC 9457) → {type, title, status, detail, code}. One `@RestControllerAdvice` — `IntegrationProblemAdvice` — maps: INT's own exceptions to their catalog codes; every typed refusal arriving from the Check Engine, Document Access or the Report Store to a ProblemDetail carrying that refusal's own code, HTTP status (the `{http}` part of its code) and message, unchanged (REQ-INT-006); `MaxUploadSizeExceededException` → `INT-413-UPLOAD-TOO-LARGE`; an unreadable body, a missing multipart part or a non-numeric `checkId` → `INT-400-REQUEST-INVALID` (REQ-INT-007); any other exception → `INT-500`, logged with the request path and the Check identifier, the answer carrying no stack trace (REQ-INT-008).
- Lookup values: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded. INT uses the enums the owners publish (`CheckStatus` of the Check Engine, `EmployeeDecision` of the Report Store) and owns none (ADR-INT-013); service codes and document types pass through as strings.
- Workflow engine: **forbidden**.
- Search contract: INT has no search endpoint; its two lists (API-INT-006, API-INT-007) take exact keys, are bounded by their owners (at most 100 Checks; the uploaded documents of one Check) and are not paginated.
- Languages: messages en (SRS); ar PENDING ADR-INT-017.
- Configuration properties (environment settings, bound with `@ConfigurationProperties`, validated at start-up — ADR-INT-012):
  - `aias.integration.approval.timeout` — Duration, default `10s`; connect + read timeout of the host Approval API call (REQ-INT-037).
  - `aias.integration.approval.base-address` — URI of the host Approval API per environment; the version's definition supplies only the method and the path (ADR-INT-009). Absent → an approval-path decision answers `INT-502-APPROVAL-API-FAILED` with `{status}` = "no answer — no address is configured" and records nothing.
  - `aias.integration.upload.request-limit` — DataSize, default `50MB`, never below `aias.check.max-file-size` (start-up fails if lower); bound to `spring.servlet.multipart.max-request-size` and `max-file-size` (REQ-INT-014).
- No state between requests (REQ-INT-059): no table, no cache, no static or session field holds a request, a file, a report or a decision; an uploaded part lives only for its request (the container discards its temporary part when the request ends). The only in-memory structure is the per-Check approval lock of API-INT-004, released at the end of the request.
- No host database (REQ-INT-060): INT declares no `DataSource`, JDBC template or MCP client; its only outbound host connection is the Approval API adapter (PORTS).
- One REST API (REQ-INT-057, REQ-INT-058): the employee frontend calls only INT's operations (API-INT-001 … API-INT-008 — ADR-INT-020); the owners' own reads stay published for hosts; no server-rendered page or view controller exists (`/api/v1/checks/{checkId}/view` is not mapped → 404).
<!-- PHASE:CORE:END -->
