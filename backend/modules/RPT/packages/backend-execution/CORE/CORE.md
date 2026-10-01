<!-- source: PHASE:CORE -->
<!-- traces: REQ-RPT-002, REQ-RPT-015, REQ-RPT-042, REQ-RPT-044, REQ-RPT-047, REQ-RPT-048, REQ-RPT-049 -->
<!-- PHASE:CORE:START traces=REQ-RPT-002,REQ-RPT-015,REQ-RPT-042,REQ-RPT-044,REQ-RPT-047,REQ-RPT-048,REQ-RPT-049 -->
## PHASE CORE — CORE

### R1 — Core configuration
- Type mapping (oracle19c → Java), column types only:

| oracle19c | Java |
|---|---|
| NUMBER(19) | Long |
| NUMBER(10) | Integer |
| VARCHAR2(n CHAR) | String |
| NUMBER(1) | Boolean (0/1) |
| TIMESTAMP WITH TIME ZONE | OffsetDateTime |
| CLOB | String (@Lob) |

- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` — every error-catalog row below is an instance of it, e.g. `RPT-404-CHECK-NOT-FOUND`; the in-process rejection codes of the SVC-API phase follow the same format (ADR-RPT-013).
- Error envelope: ProblemDetail (RFC 9457) → {type, title, status, detail, code}.
- Lookup values: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded. RPT stores codes only. It defines one Java enum of its own, `EmployeeDecision {APPROVED, REJECTED}` (EMPLOYEE_DECISION, CON-RPT-002), and uses by value the enums the Check result port carries: `CheckStatus`, `OverallStatus`, `FindingOutcome`, `CheckFailureReason` (Check Engine) and `FetchMode`, `DocumentReadStatus`, `UnreadableReason` (Document Access) — the published lists are named in CROSS-MOD. Each enum matches the CHECK constraint of the column that stores it (db-script-rpt.md BLOCK 5c); JPA maps them `EnumType.STRING`. SERVICE_CODE and DOCUMENT_TYPE are plain strings, never hardcoded (RULE-RPT-006).
- Workflow engine: **forbidden** — the status lifecycle is guarded by conditional updates in the domain (RULE-RPT-003).
- Search contract: no SRS screen, so no filter list beyond the SRS operations: API-RPT-002 filters on serviceCode + requestNumber (both EXACT, both required) and sorts STARTED_AT DESC only; API-RPT-003 filters on serviceCode. No client-chosen sort, no paging parameters (the profile declares none); an empty result is 200 with an empty list.
- Languages: messages en (SRS); ar PENDING ADR-RPT-013.
- Configuration properties (environment settings, never in the deployable; bound with `@ConfigurationProperties` and validated at start-up):
  - `aias.reports.retention-days` (Integer, optional) — the report retention period in whole days; absent, zero or negative → the purge deletes nothing and logs "Report purge skipped: no valid report retention period is configured." (REQ-RPT-042, REQ-RPT-044; ADR-RPT-004, ADR-RPT-010).
  - `aias.reports.purge-schedule` (cron, default `0 0 2 * * *`) — when the purge runs (ADR-RPT-010).
  - `aias.reports.list-limit` is NOT a property: the listing cap is the constant 100 of REQ-RPT-031 (ADR-RPT-008).
- No host access, no file, no model (REQ-RPT-047, REQ-RPT-048, REQ-RPT-049; ADR-RPT-008): the RPT package declares no dependency on the query channel, on any `DataSource` other than the service's own schema, on file-system APIs or on Spring AI; an architecture test (ArchUnit) fails the build if an RPT class imports `org.springframework.ai`, `java.nio.file` or the query port types.
- Identifiers kept exactly as received (REQ-RPT-002): no trimming, case folding or normalisation of requestNumber, employeeId or decidedBy anywhere in the RPT code path; a blank value is refused, never repaired.
<!-- PHASE:CORE:END -->
