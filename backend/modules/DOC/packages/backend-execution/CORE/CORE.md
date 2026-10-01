<!-- source: PHASE:CORE -->
<!-- traces: REQ-DOC-010, REQ-DOC-030, REQ-DOC-031, REQ-DOC-039, REQ-DOC-040, REQ-DOC-042, REQ-DOC-058, REQ-DOC-063 -->
<!-- PHASE:CORE:START traces=REQ-DOC-010,REQ-DOC-030,REQ-DOC-031,REQ-DOC-039,REQ-DOC-040,REQ-DOC-042,REQ-DOC-058,REQ-DOC-063 -->
## PHASE CORE — CORE

### R1 — Core configuration
- Type mapping (oracle19c → Java), column types only:

| oracle19c | Java |
|---|---|
| NUMBER(19) | Long |
| VARCHAR2(n CHAR) | String |
| NUMBER(10,0) | Integer |
| NUMBER(19,0) | Long |
| NUMBER(1) | Boolean (0/1) |
| TIMESTAMP WITH TIME ZONE | OffsetDateTime |
| BLOB | byte[] (@Lob, read and written as a stream) |

- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` — every error-catalog row below is an instance of it, e.g. `DOC-400-CHECK-ID-REQUIRED`; the in-process rejection codes of the SVC-API phase follow the same format (ADR-DOC-012).
- Error envelope: ProblemDetail (RFC 9457) → {type, title, status, detail, code}.
- Lookup values: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded. DOC defines the Java enums `FetchMode {path, blob, manual}`, `DocumentReadStatus {READ, MISSING, UNREADABLE}` and `UnreadableReason {OUTSIDE_STORAGE_ROOT, NOT_FOUND, TOO_LARGE, UNSUPPORTED_FORMAT, READING_FAILED, OUT_OF_TIME, SOURCE_QUERY_FAILED, MODEL_NOT_PERMITTED}` (CON-DOC-001, CON-DOC-002; ADR-DOC-010). Service codes and document types are strings read through XM-DOC-001 and XM-DOC-003, never hardcoded.
- Workflow engine: **forbidden**.
- Search contract: no SRS screen, so no filter list; API-DOC-001 filters by checkId only and returns the full list ordered by upload time; empty result = 200 with an empty array.
- Languages: messages en (SRS); ar PENDING ADR-DOC-012.
- Configuration properties (environment settings, never in the deployable; bound with `@ConfigurationProperties` and validated at start-up):
  - `aias.documents.storage-root` — the allowed storage root of the environment (REQ-DOC-010; read only from here, never from a service package, a request or a document). Absent → every `path` document is UNREADABLE / OUTSIDE_STORAGE_ROOT (REQ-DOC-011); the service still starts.
  - `aias.documents.data-class` — `SYNTHETIC` | `REAL`, default `REAL` (ADR-DOC-009).
  - `aias.documents.reading-model.*` — the document-reading model's own configuration, separate from the comparison model's: `provider`, `model`, `tier` (`FREE` | `APPROVED`, default `FREE`), `instruction` (the fixed reading instruction, REQ-DOC-046). Changing these properties and restarting switches the model with no code change (REQ-DOC-030, REQ-DOC-031). Absent `model` → scanned documents and images UNREADABLE / READING_FAILED (REQ-DOC-033).
  - Platform per-Check limits (this CORE phase owns them for every module — AIAS-7): `aias.check.timeout` (Duration), `aias.check.max-rows` (int), `aias.check.max-file-size` (DataSize), `aias.check.max-uploads` (int, default 20 — ADR-DOC-016). DOC applies the first three on every fetch (REQ-DOC-039, REQ-DOC-040, REQ-DOC-042) and the maximum uploads on every handover (REQ-DOC-063); none is ever read from a service definition.
- No state between Checks (REQ-DOC-055, REQ-DOC-057): no cache, static field or session holds a document, a query result or read content; the only stored data is DOC_UPLOADED_DOC, deleted per Check (ADR-DOC-008), and DOC_ENDED_CHECK, which holds only the identifiers of ended Checks (ADR-DOC-015).
<!-- PHASE:CORE:END -->
