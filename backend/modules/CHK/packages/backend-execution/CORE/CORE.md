<!-- source: PHASE:CORE -->
<!-- traces: REQ-CHK-016, REQ-CHK-051, REQ-CHK-060, REQ-CHK-063, REQ-CHK-067, REQ-CHK-068, REQ-CHK-072, REQ-CHK-074, REQ-CHK-075 -->
<!-- PHASE:CORE:START traces=REQ-CHK-016,REQ-CHK-051,REQ-CHK-060,REQ-CHK-067,REQ-CHK-068,REQ-CHK-072,REQ-CHK-074,REQ-CHK-075,REQ-CHK-063 -->
## PHASE CORE — CORE

### R1 — Core configuration
- Type mapping (oracle19c → Java), column types only:

| oracle19c | Java |
|---|---|
| NUMBER(19) | Long |
| VARCHAR2(n CHAR) | String / enum |
| TIMESTAMP WITH TIME ZONE | OffsetDateTime |

- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` — every error-catalog row below is an instance of it, e.g. `CHK-404-ACTIVE-CHECK-NOT-FOUND`; the in-process rejection codes of the SVC-API phase follow the same format (ADR-CHK-018).
- Error envelope: ProblemDetail (RFC 9457) → {type, title, status, detail, code}.
- Lookup values: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded. CHK defines the Java enums `OverallStatus {COMPLIANT, NOT_COMPLIANT, NEEDS_MANUAL_REVIEW}`, `CheckStatus {AWAITING_DOCUMENTS, RUNNING, COMPLETED, FAILED}`, `ActiveCheckStatus {AWAITING_DOCUMENTS, RUNNING}` (the two unfinished values — RULE-CHK-009), `FindingOutcome {SATISFIED, NOT_SATISFIED, UNDETERMINED}` and `CheckFailureReason {TIMED_OUT, MODEL_UNAVAILABLE, MODEL_OUTPUT_INVALID, MODEL_NOT_PERMITTED, UPLOAD_WINDOW_EXPIRED, INTERRUPTED, INTERNAL_ERROR}` (CON-CHK-001 … CON-CHK-003; ADR-CHK-001, ADR-CHK-016). Fetch mode, document read status and unreadable reason are the enums of the Document Access interface, used by value. Service codes and document types are strings read through XM-CHK-001 and XM-CHK-004, never hardcoded.
- Workflow engine: **forbidden** — the pipeline is plain sequential service code (REQ-CHK-009).
- Search contract: no SRS screen, so no filter list; API-CHK-001 reads one row by checkId.
- Languages: messages en (SRS); ar PENDING ADR-CHK-018.
- Configuration properties (environment settings, never in the deployable; bound with `@ConfigurationProperties` and validated at start-up — AIAS-7: every Check declares its timeout, maximum rows and maximum file size):
  - Platform per-Check limits, one group for every module: `aias.check.timeout` (Duration — Check timeout, counted from RUNNING, REQ-CHK-051, REQ-CHK-052), `aias.check.max-rows` (int — REQ-CHK-016, REQ-CHK-050), `aias.check.max-file-size` (DataSize — applied by Document Access on every fetch; CHK never opens a document, REQ-CHK-018), `aias.check.upload-window` (Duration — REQ-CHK-060, ADR-CHK-004). None is ever read from a service definition.
  - Environment data class: CHK binds read-only the **single** platform data-class property `aias.documents.data-class` (`SYNTHETIC` | `REAL`, default `REAL`) that Document Access binds; no second property is defined (REQ-CHK-074; ADR-CHK-010).
  - Comparison model: `aias.check.comparison-model.provider`, `aias.check.comparison-model.model` (the identifier recorded in the report metadata, REQ-CHK-045, REQ-CHK-068), `aias.check.comparison-model.tier` (`FREE` | `APPROVED`, default `FREE` — REQ-CHK-075). Changing these properties and restarting switches the model with no code change (REQ-CHK-067). The document-reading model is a different configuration and a different bean, never injected into CHK.
  - `aias.check.deadline-check-interval` (Duration, default 30 s — DEFAULT) — the period of the deadline check (REQ-CHK-080).
  - `aias.check.pipeline-threads` (int, default 4 — DEFAULT) — the bounded executor that runs pipelines in the background (REQ-CHK-001).
- No state between Checks (REQ-CHK-063 … REQ-CHK-065): no cache, static field, conversation memory or session holds a query result, document content or model output; a pipeline's working data is a local `CheckContext` object dropped when the pipeline returns; the only stored data is CHK_ACTIVE_CHECK, which holds no request data (REQ-CHK-082).
<!-- PHASE:CORE:END -->
