# BACKEND EXECUTION PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Dialect : oracle19c   Framework : spring-boot-4-java-21 (+ Spring AI 2.0, unused by RPT — REQ-RPT-049)
Inputs : srs-rpt.md · db-script-rpt.md · registry-srs-rpt.md · registry-db-rpt.md · contract-rpt.md · contract-chk.md (the Check result port RPT implements and the closed lists it stores) · contract-doc.md (the closed lists it stores by value)
Governance : FULL (db-script present)   Open ADRs : 0 BLOCKED — decisions applied: ADR-RPT-001 … ADR-RPT-013 (analysis/decisions/RPT/)
══════════════════════════════════════════════════════════════════

## EXECUTION PLAN INDEX — RPT v1

### Entity registry
| ENT | Name | Table | Business code | Operations |
|---|---|---|---|---|
| ENT-RPT-001 | Check Run | RPT_CHECK_RUN | — | in-process create (result port createCheckRun); in-process update (markRunning, completeCheck, failCheck; recordDecision); read (API-RPT-001, API-RPT-002, API-RPT-003; result port getCheck, listUnfinishedChecks); delete (scheduled purge) |
| ENT-RPT-002 | Finding | RPT_FINDING | — | in-process create (completeCheck); read (API-RPT-001); delete (purge, cascade) |
| ENT-RPT-003 | Check Document | RPT_CHECK_DOCUMENT | — | in-process create (completeCheck); read (API-RPT-001); delete (purge, cascade) |
| ENT-RPT-004 | Unread Query | RPT_UNREAD_QUERY | — | in-process create (completeCheck); read (API-RPT-001); delete (purge, cascade) |

### API registry
| API | Operation | Verb | Path | Traces |
|---|---|---|---|---|
| API-RPT-001 | Read a Check and its report | GET | /api/v1/checks/{checkId} | REQ-RPT-023, REQ-RPT-024, REQ-RPT-025, REQ-RPT-026, REQ-RPT-027 |
| API-RPT-002 | List the Checks of a request | GET | /api/v1/checks | REQ-RPT-028, REQ-RPT-029, REQ-RPT-031, REQ-RPT-050 |
| API-RPT-003 | Read the decision agreement of a service | GET | /api/v1/decision-agreement | REQ-RPT-040, REQ-RPT-041 |

### Rule registry
| RULE | Name | Scope | Enforced where | Message en / ar |
|---|---|---|---|---|
| RULE-RPT-001 | A Check run is complete | ENT-RPT-001 | result port createCheckRun | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-002 | Initial status agrees with the fetch mode | ENT-RPT-001 | createCheckRun + CHK_RPT_CHECK_RUN_AWAITING | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-003 | Status moves forward only | ENT-RPT-001 | markRunning, completeCheck, failCheck (conditional UPDATE) | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-004 | Report metadata agrees with its Check run | ENT-RPT-001 | completeCheck | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-005 | COMPLIANT only when fully verified | ENT-RPT-001 | completeCheck | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-006 | Closed codes only | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003 | Java enums at the port + CHECK constraints | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-007 | A finding is complete | ENT-RPT-002 | completeCheck | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-008 | Reason exactly on UNREADABLE | ENT-RPT-003 | completeCheck + CHK_RPT_CHECK_DOCUMENT_REASON | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-009 | A request's Checks need both keys | ENT-RPT-001 | API-RPT-002 (HTTP 400) | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-010 | A failure is complete | ENT-RPT-001 | failCheck | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-011 | One decision per Check | ENT-RPT-001 | recordDecision (conditional UPDATE) | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-012 | Decision only on a completed Check | ENT-RPT-001 | recordDecision + CHK_RPT_CHECK_RUN_DECISION | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-013 | A decision is complete | ENT-RPT-001 | recordDecision | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-014 | Approval API execution only with APPROVED | ENT-RPT-001 | recordDecision + CHK_RPT_CHECK_RUN_DECISION | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-015 | Decision agreement needs a service code | ENT-RPT-001 | API-RPT-003 (HTTP 400) | ✓ / PENDING ADR-RPT-013 |

### Screen registry
None — RPT has no screen (SRS PART B not applicable); the embedded frontend reads API-RPT-001 and API-RPT-002.

### QRC summary
| QR | Operation | Phase | ENT |
|---|---|---|---|
| QR-RPT-001 | Check Run by identifier | SVC-API | ENT-RPT-001 |
| QR-RPT-002 | Findings of a Check Run | SVC-API | ENT-RPT-002 |
| QR-RPT-003 | Check Documents of a Check Run | SVC-API | ENT-RPT-003 |
| QR-RPT-004 | Unread Queries of a Check Run | SVC-API | ENT-RPT-004 |
| QR-RPT-005 | Newest 100 Checks of a request | SVC-API | ENT-RPT-001 |
| QR-RPT-006 | Number of Checks of a request | SVC-API | ENT-RPT-001 |
| QR-RPT-007 | Decision agreement of a service | SVC-API | ENT-RPT-001 |

DB ALIGNMENT: see manifest — ALIGNED ✓ / issues: 0 · INTEGRATION: 0 edges (RPT consumes no entity; it implements the Check Engine's result port — see CROSS-MOD; ADR-RPT-001) · SECURITY: 0 screens × 0 roles (no permission model, caller authentication deferred — raw-idea A2)

```yaml name=totals
DBF: 46
XM: 0
API: 3
QR: 7
```

## DB ALIGNMENT MANIFEST — RPT v1

Columns, types and SRS references are read from db-script-rpt.md (dbf-matrix) by DBF id. Writer: the reason a required column is written by no HTTP endpoint (every RPT write is in-process — ADR-RPT-006).

| DBF | ENT | Plan property | Plan type | XM | Status | Writer |
|---|---|---|---|---|---|---|
| DBF-RPT-001 | ENT-RPT-001 | checkRunId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-002 | ENT-RPT-001 | serviceCode | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-003 | ENT-RPT-001 | versionNumber | Integer | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-004 | ENT-RPT-001 | fetchMode | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-005 | ENT-RPT-001 | requestNumber | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-006 | ENT-RPT-001 | employeeId | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-007 | ENT-RPT-001 | checkStatus | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-008 | ENT-RPT-001 | startedAt | OffsetDateTime | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-009 | ENT-RPT-001 | runningSince | OffsetDateTime | — | ✓ | system-generated — written by the in-process result port `markRunning` |
| DBF-RPT-010 | ENT-RPT-001 | endedAt | OffsetDateTime | — | ✓ | system-generated — written by the in-process result port `completeCheck` (endedAt also by `failCheck`) |
| DBF-RPT-011 | ENT-RPT-001 | overallStatus | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` (endedAt also by `failCheck`) |
| DBF-RPT-012 | ENT-RPT-001 | comparisonModel | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` (endedAt also by `failCheck`) |
| DBF-RPT-013 | ENT-RPT-001 | failureReason | String | — | ✓ | system-generated — written by the in-process result port `failCheck` |
| DBF-RPT-014 | ENT-RPT-001 | failureDetail | String (lob) | — | ✓ | system-generated — written by the in-process result port `failCheck` |
| DBF-RPT-015 | ENT-RPT-001 | employeeDecision | String | — | ✓ | system-generated — written by the in-process `recordDecision` (INT) |
| DBF-RPT-016 | ENT-RPT-001 | decidedBy | String | — | ✓ | system-generated — written by the in-process `recordDecision` (INT) |
| DBF-RPT-017 | ENT-RPT-001 | decidedAt | OffsetDateTime | — | ✓ | system-generated — RPT clock in the in-process `recordDecision` (ADR-RPT-009) |
| DBF-RPT-018 | ENT-RPT-001 | approvalApiExecuted | Boolean | — | ✓ | system-generated — written by the in-process `recordDecision` (INT) |
| DBF-RPT-019 | ENT-RPT-001 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-020 | ENT-RPT-001 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-021 | ENT-RPT-002 | findingId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-022 | ENT-RPT-002 | position | Integer | — | ✓ | system-generated — index of the item in the list `completeCheck` receives, from 1 |
| DBF-RPT-023 | ENT-RPT-002 | conditionText | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-024 | ENT-RPT-002 | findingOutcome | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-025 | ENT-RPT-002 | evidence | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-026 | ENT-RPT-002 | note | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-027 | ENT-RPT-002 | checkRunId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-028 | ENT-RPT-002 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-029 | ENT-RPT-002 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-030 | ENT-RPT-003 | checkDocumentId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-031 | ENT-RPT-003 | position | Integer | — | ✓ | system-generated — index of the item in the list `completeCheck` receives, from 1 |
| DBF-RPT-032 | ENT-RPT-003 | documentType | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-033 | ENT-RPT-003 | sourceMode | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-034 | ENT-RPT-003 | readStatus | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-035 | ENT-RPT-003 | unreadableReason | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-036 | ENT-RPT-003 | detail | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-037 | ENT-RPT-003 | checkRunId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-038 | ENT-RPT-003 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-039 | ENT-RPT-003 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-040 | ENT-RPT-004 | unreadQueryId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-041 | ENT-RPT-004 | position | Integer | — | ✓ | system-generated — index of the item in the list `completeCheck` receives, from 1 |
| DBF-RPT-042 | ENT-RPT-004 | queryName | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-043 | ENT-RPT-004 | detail | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-044 | ENT-RPT-004 | checkRunId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-045 | ENT-RPT-004 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-046 | ENT-RPT-004 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |

Legend ✓ aligned. No derived property. No cross-module column: SERVICE_CODE, VERSION_NUMBER, DOCUMENT_TYPE and QUERY_NAME are values carried by the result port, never foreign keys (ADR-RPT-011).

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

<!-- PHASE:DATA-DOM:START traces=ENT-RPT-001,ENT-RPT-002,ENT-RPT-003,ENT-RPT-004,DBF-RPT-001,DBF-RPT-002,DBF-RPT-003,DBF-RPT-004,DBF-RPT-005,DBF-RPT-006,DBF-RPT-007,DBF-RPT-008,DBF-RPT-009,DBF-RPT-010,DBF-RPT-011,DBF-RPT-012,DBF-RPT-013,DBF-RPT-014,DBF-RPT-015,DBF-RPT-016,DBF-RPT-017,DBF-RPT-018,DBF-RPT-019,DBF-RPT-020,DBF-RPT-021,DBF-RPT-022,DBF-RPT-023,DBF-RPT-024,DBF-RPT-025,DBF-RPT-026,DBF-RPT-027,DBF-RPT-028,DBF-RPT-029,DBF-RPT-030,DBF-RPT-031,DBF-RPT-032,DBF-RPT-033,DBF-RPT-034,DBF-RPT-035,DBF-RPT-036,DBF-RPT-037,DBF-RPT-038,DBF-RPT-039,DBF-RPT-040,DBF-RPT-041,DBF-RPT-042,DBF-RPT-043,DBF-RPT-044,DBF-RPT-045,DBF-RPT-046,REQ-RPT-003,REQ-RPT-006,REQ-RPT-020 -->
## PHASE DATA-DOM — DATA+DOM

### ENT-RPT-001 — Check Run      kind: transactional
BINDINGS   table RPT_CHECK_RUN · PK CHECK_RUN_ID (DBF-RPT-001) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · db-script-rpt.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-RPT-001 | checkRunId | CHECK_RUN_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | checkRunId / PENDING ADR-RPT-013 |
| DBF-RPT-002 | serviceCode | SERVICE_CODE | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | serviceCode / PENDING ADR-RPT-013 |
| DBF-RPT-003 | versionNumber | VERSION_NUMBER | NUMBER(10) | NOT NULL | yes (no HTTP write) | — | versionNumber / PENDING ADR-RPT-013 |
| DBF-RPT-004 | fetchMode | FETCH_MODE | VARCHAR2(10 CHAR) | NOT NULL | yes (no HTTP write) | — | fetchMode / PENDING ADR-RPT-013 |
| DBF-RPT-005 | requestNumber | REQUEST_NUMBER | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | requestNumber / PENDING ADR-RPT-013 |
| DBF-RPT-006 | employeeId | EMPLOYEE_ID | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | employeeId / PENDING ADR-RPT-013 |
| DBF-RPT-007 | checkStatus | CHECK_STATUS | VARCHAR2(30 CHAR) | NOT NULL | yes (no HTTP write) | — | checkStatus / PENDING ADR-RPT-013 |
| DBF-RPT-008 | startedAt | STARTED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | startedAt / PENDING ADR-RPT-013 |
| DBF-RPT-009 | runningSince | RUNNING_SINCE | TIMESTAMP WITH TIME ZONE | NULL | yes (no HTTP write) | — | runningSince / PENDING ADR-RPT-013 |
| DBF-RPT-010 | endedAt | ENDED_AT | TIMESTAMP WITH TIME ZONE | NULL | yes (no HTTP write) | — | endedAt / PENDING ADR-RPT-013 |
| DBF-RPT-011 | overallStatus | OVERALL_STATUS | VARCHAR2(30 CHAR) | NULL | yes (no HTTP write) | — | overallStatus / PENDING ADR-RPT-013 |
| DBF-RPT-012 | comparisonModel | COMPARISON_MODEL | VARCHAR2(200 CHAR) | NULL | yes (no HTTP write) | — | comparisonModel / PENDING ADR-RPT-013 |
| DBF-RPT-013 | failureReason | FAILURE_REASON | VARCHAR2(30 CHAR) | NULL | yes (no HTTP write) | — | failureReason / PENDING ADR-RPT-013 |
| DBF-RPT-014 | failureDetail | FAILURE_DETAIL | CLOB | NULL | yes (no HTTP write) | — | failureDetail / PENDING ADR-RPT-013 |
| DBF-RPT-015 | employeeDecision | EMPLOYEE_DECISION | VARCHAR2(30 CHAR) | NULL | yes (no HTTP write) | — | employeeDecision / PENDING ADR-RPT-013 |
| DBF-RPT-016 | decidedBy | DECIDED_BY | VARCHAR2(100 CHAR) | NULL | yes (no HTTP write) | — | decidedBy / PENDING ADR-RPT-013 |
| DBF-RPT-017 | decidedAt | DECIDED_AT | TIMESTAMP WITH TIME ZONE | NULL | yes (no HTTP write) | — | decidedAt / PENDING ADR-RPT-013 |
| DBF-RPT-018 | approvalApiExecuted | APPROVAL_API_EXECUTED | NUMBER(1) | NULL | yes (no HTTP write) | — | approvalApiExecuted / PENDING ADR-RPT-013 |
| DBF-RPT-019 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-RPT-013 |
| DBF-RPT-020 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-RPT-013 |

DTO MEMBERSHIP  create-request: none over HTTP (in-process `createCheckRun` takes serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt) · update-request: none over HTTP · response: CheckReport (API-RPT-001), CheckSummary (API-RPT-002), AgreementRow (API-RPT-003); checkRunId is exposed as `checkId`; createdAt / updatedAt are never exposed
LOOKUP FIELDS  fetchMode → FETCH_MODE · checkStatus → CHECK_STATUS · overallStatus → OVERALL_STATUS · failureReason → CHECK_FAILURE_REASON · employeeDecision → EMPLOYEE_DECISION — each stores the code; no lookup endpoint (closed enums, CHECK constraints — ADR-RPT-011)
DOMAIN RULES
- RULE-RPT-001 — A Check run is complete · trigger: on create Check run · scope: CREATE
  - statement: The system shall require a service code, a version number, a fetch mode, a request number, an employee identity, an initial status and a start time, each present and not blank, to create a Check run.
  - message (en): The Check run was not stored: {field} is missing. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: NOT NULL on DBF-RPT-002 … DBF-RPT-008 (+ app-level blank check) · owner layer: domain (`CheckRun.create`)
- RULE-RPT-002 — Initial status agrees with the fetch mode · trigger: on create Check run · scope: CREATE
  - statement: The system shall require the initial status AWAITING_DOCUMENTS when the fetch mode is `manual` and RUNNING when it is `path` or `blob`.
  - message (en): The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual. / The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_AWAITING (AWAITING_DOCUMENTS ⇒ manual) + app-level (manual ⇒ AWAITING_DOCUMENTS at creation) · owner layer: domain
- RULE-RPT-003 — Status moves forward only · trigger: on mark RUNNING, complete, fail · scope: UPDATE
  - statement: The system shall allow only the changes AWAITING_DOCUMENTS → RUNNING, RUNNING → RUNNING, RUNNING → COMPLETED, AWAITING_DOCUMENTS → FAILED and RUNNING → FAILED, and shall prevent every change from COMPLETED or FAILED.
  - message (en): Check {checkId} has already ended; its status cannot change. / Check {checkId} is not running; it cannot be completed. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_RESULT (shape per status) + conditional UPDATE `WHERE CHECK_STATUS IN (…)` (app-level) · owner layer: domain (`CheckRun.markRunning / complete / fail`) + repository guard
- RULE-RPT-004 — Report metadata agrees with its Check run · trigger: on complete · scope: UPDATE
  - statement: The system shall require the report metadata to carry a comparison model and an end time not earlier than the start time, and its service code, version number, fetch mode, employee identity and start time to equal those stored on the Check run.
  - message (en): The report of Check {checkId} was not stored: its metadata {field} {value} differs from the Check run ({stored}). / The report of Check {checkId} was not stored: {field} is missing. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_ENDED_AT, CHK_RPT_CHECK_RUN_RESULT (model present when COMPLETED) + app-level comparison · owner layer: domain
- RULE-RPT-005 — COMPLIANT only when fully verified · trigger: on complete · scope: UPDATE
  - statement: The system shall prevent storing the Overall Status COMPLIANT when any finding of the report is not SATISFIED or the report has any unread service query.
  - message (en): The report of Check {checkId} was not stored: COMPLIANT needs every finding SATISFIED and every service query read. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: app-level (spans three tables) · owner layer: domain (`CheckReport` value object)
- RULE-RPT-006 — Closed codes only · trigger: on create Check run, complete, fail · scope: CREATE / UPDATE
  - statement: The system shall require every Check status, Overall Status, failure reason, fetch mode, source mode, finding outcome, document read status and unreadable reason to be a code of its closed list in A6.
  - message (en): Not stored: `{value}` is not a code of {lookupKey}. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_CHECK_STATUS, CHK_RPT_CHECK_RUN_FETCH_MODE, CHK_RPT_CHECK_RUN_OVERALL_STATUS, CHK_RPT_CHECK_RUN_FAILURE_REASON, CHK_RPT_FINDING_FINDING_OUTCOME, CHK_RPT_CHECK_DOCUMENT_SOURCE_MODE, CHK_RPT_CHECK_DOCUMENT_READ_STATUS, CHK_RPT_CHECK_DOCUMENT_UNREADABLE_REASON (+ app-level: a null enum on a required field) · owner layer: domain
- RULE-RPT-010 — A failure is complete · trigger: on fail · scope: UPDATE
  - statement: The system shall require a failure reason, a detail not blank and an end time not earlier than the start time to store a failure.
  - message (en): The failure of Check {checkId} was not stored: {field} is missing. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_RESULT, CHK_RPT_CHECK_RUN_ENDED_AT + app-level blank check of FAILURE_DETAIL (CLOB) · owner layer: domain
- RULE-RPT-011 — One decision per Check · trigger: on record decision · scope: UPDATE
  - statement: The system shall prevent recording an Employee Decision on a Check run that already holds one.
  - message (en): Check {checkId} already has an Employee Decision. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: conditional UPDATE `WHERE EMPLOYEE_DECISION IS NULL` · owner layer: domain + repository guard
- RULE-RPT-012 — Decision only on a completed Check · trigger: on record decision · scope: UPDATE
  - statement: The system shall prevent recording an Employee Decision on a Check run whose status is not COMPLETED.
  - message (en): Check {checkId} is not completed; a decision can only be recorded on a completed Check. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_DECISION + conditional UPDATE `AND CHECK_STATUS = 'COMPLETED'` · owner layer: domain
- RULE-RPT-013 — A decision is complete · trigger: on record decision · scope: UPDATE
  - statement: The system shall require a decision code of EMPLOYEE_DECISION, a deciding employee's identity not blank and a yes / no value for execution through the Approval API to record an Employee Decision.
  - message (en): The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. / The decision was not recorded: say whether it was executed through the Approval API. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_EMPLOYEE_DECISION, CHK_RPT_CHECK_RUN_APPROVAL_API_EXECUTED, CHK_RPT_CHECK_RUN_DECISION (all-or-nothing) + app-level · owner layer: domain
- RULE-RPT-014 — Approval API execution only with APPROVED · trigger: on record decision · scope: UPDATE
  - statement: The system shall prevent recording a decision REJECTED as executed through the Approval API.
  - message (en): The decision was not recorded: only an APPROVED decision is executed through the Approval API. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_DECISION + app-level · owner layer: domain
- RULE-RPT-009, RULE-RPT-015 — request validation of API-RPT-002 and API-RPT-003, stated in full in those blocks.
STATE MACHINE  CHECK_STATUS (DBF-RPT-007) · values AWAITING_DOCUMENTS, RUNNING, COMPLETED, FAILED · initial AWAITING_DOCUMENTS (`manual`) or RUNNING (`path` / `blob`) · transitions: AWAITING_DOCUMENTS → RUNNING (markRunning), RUNNING → RUNNING (markRunning, keeps the first runningSince), RUNNING → COMPLETED (completeCheck), AWAITING_DOCUMENTS | RUNNING → FAILED (failCheck) — actor: the Check Engine through the result port only · terminal COMPLETED, FAILED · invalid transition → RULE-RPT-003 · the Employee Decision is not a status (RULE-RPT-011 … RULE-RPT-014)
CROSS-MODULE   none — 0 XM
REPOSITORY OPS QR-RPT-001, QR-RPT-005, QR-RPT-006, QR-RPT-007 (+ the in-process writes of SVC-API)

### ENT-RPT-002 — Finding      kind: transactional
BINDINGS   table RPT_FINDING · PK FINDING_ID (DBF-RPT-021) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · FK FK_RPT_FINDING_RPT_CHECK_RUN (ON DELETE CASCADE) · db-script-rpt.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-RPT-021 | findingId | FINDING_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | findingId / PENDING ADR-RPT-013 |
| DBF-RPT-022 | position | POSITION | NUMBER(10) | NOT NULL | yes (no HTTP write) | — | position / PENDING ADR-RPT-013 |
| DBF-RPT-023 | conditionText | CONDITION_TEXT | CLOB | NOT NULL | yes (no HTTP write) | — | conditionText / PENDING ADR-RPT-013 |
| DBF-RPT-024 | findingOutcome | FINDING_OUTCOME | VARCHAR2(30 CHAR) | NOT NULL | yes (no HTTP write) | — | findingOutcome / PENDING ADR-RPT-013 |
| DBF-RPT-025 | evidence | EVIDENCE | CLOB | NOT NULL | yes (no HTTP write) | — | evidence / PENDING ADR-RPT-013 |
| DBF-RPT-026 | note | NOTE | CLOB | NOT NULL | yes (no HTTP write) | — | note / PENDING ADR-RPT-013 |
| DBF-RPT-027 | checkRunId | CHECK_RUN_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | FK → RPT_CHECK_RUN (ON DELETE CASCADE) | checkRunId / PENDING ADR-RPT-013 |
| DBF-RPT-028 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-RPT-013 |
| DBF-RPT-029 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-RPT-013 |

DTO MEMBERSHIP  create-request: none over HTTP (in-process `completeCheck` findings [condition, outcome, evidence, note]) · update-request: none — never updated (REQ-RPT-020) · response: FindingView {position, condition, outcome, evidence, note} inside CheckReport
LOOKUP FIELDS  findingOutcome → FINDING_OUTCOME — stores the code
DOMAIN RULES
- RULE-RPT-007 — A finding is complete · trigger: on complete · scope: CREATE
  - statement: The system shall require every finding to carry a condition, an outcome, an evidence and a note, each present and not blank.
  - message (en): The report of Check {checkId} was not stored: finding {position} has no {field}. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: NOT NULL on DBF-RPT-023 … DBF-RPT-026 + app-level blank check (CLOB) · owner layer: domain
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-RPT-002 (+ insert in completeCheck)

### ENT-RPT-003 — Check Document      kind: transactional
BINDINGS   table RPT_CHECK_DOCUMENT · PK CHECK_DOCUMENT_ID (DBF-RPT-030) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · FK FK_RPT_CHECK_DOCUMENT_RPT_CHECK_RUN (ON DELETE CASCADE) · db-script-rpt.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-RPT-030 | checkDocumentId | CHECK_DOCUMENT_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | checkDocumentId / PENDING ADR-RPT-013 |
| DBF-RPT-031 | position | POSITION | NUMBER(10) | NOT NULL | yes (no HTTP write) | — | position / PENDING ADR-RPT-013 |
| DBF-RPT-032 | documentType | DOCUMENT_TYPE | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | documentType / PENDING ADR-RPT-013 |
| DBF-RPT-033 | sourceMode | SOURCE_MODE | VARCHAR2(10 CHAR) | NOT NULL | yes (no HTTP write) | — | sourceMode / PENDING ADR-RPT-013 |
| DBF-RPT-034 | readStatus | READ_STATUS | VARCHAR2(30 CHAR) | NOT NULL | yes (no HTTP write) | — | readStatus / PENDING ADR-RPT-013 |
| DBF-RPT-035 | unreadableReason | UNREADABLE_REASON | VARCHAR2(30 CHAR) | NULL | yes (no HTTP write) | — | unreadableReason / PENDING ADR-RPT-013 |
| DBF-RPT-036 | detail | DETAIL | CLOB | NULL | yes (no HTTP write) | — | detail / PENDING ADR-RPT-013 |
| DBF-RPT-037 | checkRunId | CHECK_RUN_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | FK → RPT_CHECK_RUN (ON DELETE CASCADE) | checkRunId / PENDING ADR-RPT-013 |
| DBF-RPT-038 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-RPT-013 |
| DBF-RPT-039 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-RPT-013 |

DTO MEMBERSHIP  create-request: none over HTTP (in-process `completeCheck` documentOutcomes [documentType, sourceMode, readStatus, reason, detail] — no content field exists, REQ-RPT-019) · update-request: none — never updated · response: DocumentView {position, documentType, sourceMode, readStatus, unreadableReason, detail} inside CheckReport
LOOKUP FIELDS  sourceMode → FETCH_MODE · readStatus → DOCUMENT_READ_STATUS · unreadableReason → UNREADABLE_REASON — each stores the code
DOMAIN RULES
- RULE-RPT-008 — Reason exactly on UNREADABLE · trigger: on complete · scope: CREATE
  - statement: The system shall require an unreadable reason on every UNREADABLE document outcome and prevent one on a READ or MISSING outcome, and shall require a document type and source mode on every outcome.
  - message (en): The report of Check {checkId} was not stored: document {position} is UNREADABLE without a reason. / The report of Check {checkId} was not stored: document {position} is {readStatus} and cannot carry a reason. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_DOCUMENT_REASON (+ app-level) · owner layer: domain
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-RPT-003 (+ insert in completeCheck)

### ENT-RPT-004 — Unread Query      kind: transactional
BINDINGS   table RPT_UNREAD_QUERY · PK UNREAD_QUERY_ID (DBF-RPT-040) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · FK FK_RPT_UNREAD_QUERY_RPT_CHECK_RUN (ON DELETE CASCADE) · db-script-rpt.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-RPT-040 | unreadQueryId | UNREAD_QUERY_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | unreadQueryId / PENDING ADR-RPT-013 |
| DBF-RPT-041 | position | POSITION | NUMBER(10) | NOT NULL | yes (no HTTP write) | — | position / PENDING ADR-RPT-013 |
| DBF-RPT-042 | queryName | QUERY_NAME | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | queryName / PENDING ADR-RPT-013 |
| DBF-RPT-043 | detail | DETAIL | CLOB | NOT NULL | yes (no HTTP write) | — | detail / PENDING ADR-RPT-013 |
| DBF-RPT-044 | checkRunId | CHECK_RUN_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | FK → RPT_CHECK_RUN (ON DELETE CASCADE) | checkRunId / PENDING ADR-RPT-013 |
| DBF-RPT-045 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-RPT-013 |
| DBF-RPT-046 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-RPT-013 |

DTO MEMBERSHIP  create-request: none over HTTP (in-process `completeCheck` unreadQueries [queryName, detail]) · update-request: none — never updated · response: UnreadQueryView {position, queryName, detail} inside CheckReport
LOOKUP FIELDS  none
DOMAIN RULES
- none specific to this entity (presence of queryName and detail is checked with RULE-RPT-007's not-blank guard; the COMPLIANT guard RULE-RPT-005 reads this list)
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-RPT-004 (+ insert in completeCheck)
<!-- PHASE:DATA-DOM:END -->

<!-- PHASE:PORTS:START traces=REQ-RPT-001,REQ-RPT-005,REQ-RPT-008,REQ-RPT-018,REQ-RPT-021,REQ-RPT-022,REQ-RPT-047,REQ-RPT-048,REQ-RPT-049,REQ-RPT-053 -->
## PHASE PORTS — PORTS+ADAPTERS

RPT runs no host query, fetches no document and calls no model: the QUERY, DOCUMENT and MODEL ports of the profile belong to the modules that run Checks and RPT has none of them (REQ-RPT-047, REQ-RPT-048, REQ-RPT-049). RPT has one inbound adapter:
- `CheckResultStore` — the RPT bean that **implements the Check result port interface `CheckResultPort` declared by the Check Engine** (ADR-RPT-001). It is the only class of RPT that imports the port and the carried enums; each method delegates to `CheckRunCommandService` / `CheckRunQueryService` (SVC-API). Implements, one method each, exactly as the Check Engine's published contract signs them: `createCheckRun`, `markRunning`, `completeCheck`, `failCheck`, `getCheck`, `listUnfinishedChecks` (the contract items are listed in CROSS-MOD). The Check Engine never reads RPT tables and RPT never calls the Check Engine.
- Closed value types (REQ-RPT-053, ADR-RPT-018): `completeCheck` and `failCheck` accept only Java records whose components are exactly the fields the Check Engine's published contract declares for them (items listed in CROSS-MOD) — finding {condition, outcome, evidence, note}; document outcome {documentType, sourceMode, readStatus, reason, detail}; unread query {queryName, detail}; metadata {serviceCode, versionNumber, fetchMode, comparisonModel, employeeId, startedAt, endedAt}; failCheck (checkId, failureReason, detail, endedAt). No component is a map, a byte array or an untyped object and no "extra attributes" holder exists, so a hand-over carrying an undeclared field cannot be constructed and nothing of it is written; an undeclared value in a declared code field is refused by RULE-RPT-006 (RPT-422-UNKNOWN-CODE).
- Exceptions and transactions at the port (ADR-RPT-012): every method joins the caller's transaction (`Propagation.REQUIRED`). Every RULE is checked on the values received **before the first write**; a refusal throws an `RptRefusalException` subclass (a `RuntimeException`, code and message from the SVC-API table) declared `noRollbackFor`, so the caller's transaction stays usable and the Check Engine can still fail the Check in the same transaction. A database failure during the writes propagates and rolls the caller's transaction back whole — nothing of the report is left (REQ-RPT-009).
<!-- PHASE:PORTS:END -->

<!-- PHASE:SVC-API:START traces=DBF-RPT-001,DBF-RPT-002,DBF-RPT-003,DBF-RPT-004,DBF-RPT-005,DBF-RPT-006,DBF-RPT-007,DBF-RPT-008,DBF-RPT-009,DBF-RPT-010,DBF-RPT-011,DBF-RPT-012,DBF-RPT-013,DBF-RPT-014,DBF-RPT-015,DBF-RPT-016,DBF-RPT-017,DBF-RPT-018,DBF-RPT-022,DBF-RPT-023,DBF-RPT-024,DBF-RPT-025,DBF-RPT-026,DBF-RPT-031,DBF-RPT-032,DBF-RPT-033,DBF-RPT-034,DBF-RPT-035,DBF-RPT-036,DBF-RPT-041,DBF-RPT-042,DBF-RPT-043,REQ-RPT-001,REQ-RPT-005,REQ-RPT-008,REQ-RPT-018,REQ-RPT-021,REQ-RPT-022,REQ-RPT-023,REQ-RPT-028,REQ-RPT-032,REQ-RPT-040,REQ-RPT-043 -->
## PHASE SVC-API — SVC+API

### Service layer — the Check result port implementation (`CheckRunCommandService`, `CheckRunQueryService`)
All writes are READ_WRITE in the caller's transaction (ADR-RPT-012); timestamps are those the Check Engine hands over, except createdAt / updatedAt (DEFAULT SYSTIMESTAMP; updatedAt set to SYSTIMESTAMP on every UPDATE).

**`createCheckRun(serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt)` → checkId** (port operation; REQ-RPT-001 … REQ-RPT-004, REQ-RPT-015, REQ-RPT-030)
  1. RULE-RPT-001 — any value absent or blank → `CheckRunIncompleteException` (RPT-400-CHECK-RUN-INCOMPLETE) "The Check run was not stored: {field} is missing." (REQ-RPT-003).
  2. RULE-RPT-006 — a code outside its enum (status not RUNNING / AWAITING_DOCUMENTS at creation counts as outside) → `UnknownCodeException` (RPT-422-UNKNOWN-CODE) "Not stored: `{value}` is not a code of {lookupKey}." (REQ-RPT-015).
  3. RULE-RPT-002 — status vs fetch mode → `InitialStatusMismatchException` (RPT-422-INITIAL-STATUS-MISMATCH) with the RULE message (REQ-RPT-004).
  4. Persist: INSERT INTO RPT_CHECK_RUN (SERVICE_CODE DBF-RPT-002, VERSION_NUMBER DBF-RPT-003, FETCH_MODE DBF-RPT-004, REQUEST_NUMBER DBF-RPT-005, EMPLOYEE_ID DBF-RPT-006 — both exactly as received, REQ-RPT-002; CHECK_STATUS DBF-RPT-007, STARTED_AT DBF-RPT-008); CHECK_RUN_ID DBF-RPT-001 by the identity clause; every result and decision column NULL; return CHECK_RUN_ID as checkId. A second Check of the same request is simply another row — nothing is looked up or copied (REQ-RPT-030).
  - Concurrency: NONE — the identifier is allocated by the identity clause; no read precedes the insert.

**`markRunning(checkId, runningSince)`** (port operation; REQ-RPT-005 … REQ-RPT-007)
  1. UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'RUNNING' (DBF-RPT-007), RUNNING_SINCE = COALESCE(RUNNING_SINCE, :runningSince) (DBF-RPT-009), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING').
  2. 0 rows → read QR-RPT-001: no row → `CheckNotFoundException` (RPT-404-CHECK-NOT-FOUND) "Check {checkId} was not found." (REQ-RPT-007); row ended → `CheckEndedException` (RPT-409-CHECK-ENDED) "Check {checkId} has already ended; its status cannot change." (RULE-RPT-003, REQ-RPT-006).
  - Concurrency: the conditional UPDATE is the guard — a concurrent end makes it update 0 rows.

**`completeCheck(checkId, overallStatus, findings, documentOutcomes, unreadQueries, metadata)`** (port operation; REQ-RPT-008 … REQ-RPT-017, REQ-RPT-019, REQ-RPT-020, REQ-RPT-053)
  1. Load the Check Run (QR-RPT-001) with a locking read `FOR UPDATE`; none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-007); COMPLETED / FAILED → RPT-409-CHECK-ENDED (REQ-RPT-020); AWAITING_DOCUMENTS → `CheckNotRunningException` (RPT-409-CHECK-NOT-RUNNING) "Check {checkId} is not running; it cannot be completed." (RULE-RPT-003, REQ-RPT-006).
  2. Validate the whole report in memory, in this order, before any write (ADR-RPT-012): RULE-RPT-006 codes (REQ-RPT-015) → RULE-RPT-004 metadata vs the loaded row (`MetadataMismatchException`, RPT-422-METADATA-MISMATCH; REQ-RPT-013) → RULE-RPT-007 every finding complete (`FindingIncompleteException`, RPT-422-FINDING-INCOMPLETE; REQ-RPT-016) and every unread query with name and detail → RULE-RPT-008 reasons (`DocumentReasonMismatchException`, RPT-422-DOCUMENT-REASON-MISMATCH; REQ-RPT-017) → RULE-RPT-005 COMPLIANT guard (`CompliantNotVerifiedException`, RPT-422-COMPLIANT-NOT-VERIFIED; REQ-RPT-014). Messages exactly as the DATA-DOM rules state them.
  3. Persist in this order: UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'COMPLETED' (DBF-RPT-007), OVERALL_STATUS (DBF-RPT-011), COMPARISON_MODEL (DBF-RPT-012), ENDED_AT (DBF-RPT-010), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS = 'RUNNING'; INSERT one RPT_FINDING per finding (POSITION DBF-RPT-022 = index from 1, CONDITION_TEXT DBF-RPT-023, FINDING_OUTCOME DBF-RPT-024, EVIDENCE DBF-RPT-025, NOTE DBF-RPT-026, CHECK_RUN_ID DBF-RPT-027); one RPT_CHECK_DOCUMENT per document outcome (POSITION DBF-RPT-031, DOCUMENT_TYPE DBF-RPT-032, SOURCE_MODE DBF-RPT-033, READ_STATUS DBF-RPT-034, UNREADABLE_REASON DBF-RPT-035, DETAIL DBF-RPT-036, CHECK_RUN_ID DBF-RPT-037); one RPT_UNREAD_QUERY per unread query (POSITION DBF-RPT-041, QUERY_NAME DBF-RPT-042, DETAIL DBF-RPT-043, CHECK_RUN_ID DBF-RPT-044) — order kept as received (REQ-RPT-010, REQ-RPT-011, REQ-RPT-012). No document content and no query rows exist in the carried values or in any column (REQ-RPT-019).
  4. A database failure during step 3 → the exception propagates (`ReportNotStoredException`, RPT-500-REPORT-NOT-STORED) and the caller's transaction rolls back whole: no part of the report remains and the Check stays RUNNING (REQ-RPT-009).
  - Concurrency: the `FOR UPDATE` read and the conditional UPDATE serialise a completion against a concurrent fail — the second finds the row ended and is refused with RPT-409-CHECK-ENDED.

**`failCheck(checkId, failureReason, detail, endedAt)`** (port operation; REQ-RPT-018, REQ-RPT-051, REQ-RPT-053)
  1. RULE-RPT-010 — reason, detail (not blank) and endedAt present, endedAt ≥ STARTED_AT → else `FailureIncompleteException` (RPT-400-FAILURE-INCOMPLETE) "The failure of Check {checkId} was not stored: {field} is missing." (REQ-RPT-051); RULE-RPT-006 on the reason (REQ-RPT-015).
  2. UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'FAILED' (DBF-RPT-007), FAILURE_REASON (DBF-RPT-013), FAILURE_DETAIL (DBF-RPT-014), ENDED_AT (DBF-RPT-010), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING'); OVERALL_STATUS and COMPARISON_MODEL stay NULL and no Finding is written (REQ-RPT-018).
  3. 0 rows → as markRunning step 2 (RPT-404-CHECK-NOT-FOUND / RPT-409-CHECK-ENDED).
  - Concurrency: the conditional UPDATE is the guard.

**`getCheck(checkId)` → {checkId, status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt}** (port operation; REQ-RPT-021) — READ_ONLY; QR-RPT-001 projected; none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-007).

**`listUnfinishedChecks()` → list of {checkId, status, startedAt}** (port operation; REQ-RPT-022) — READ_ONLY; SELECT CHECK_RUN_ID, CHECK_STATUS, STARTED_AT FROM RPT_CHECK_RUN WHERE CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING') ORDER BY STARTED_AT (IDX_RPT_CHECK_RUN_CHECK_STATUS); none → empty list.

### Service layer — the in-process interface `ReportStore` offered to Host Integration (contract-rpt.md)
Injected into INT (profile `module_interface: in_process`). Value objects are Java records with unmodifiable lists. RPT never calls an Approval API and never sets a decision on its own (REQ-RPT-037; AIAS-4) — there is no outbound HTTP client in RPT.

**`readCheck(checkId)` → CheckReport** — the same service method that serves API-RPT-001 (`CheckReportQueryService.read`).
  - Honours: CON-RPT-003

**`listChecksOfRequest(serviceCode, requestNumber)` → ChecksOfRequest** — the same service method that serves API-RPT-002.
  - Honours: CON-RPT-004

**`readDecisionAgreement(serviceCode)` → list of AgreementRow** — the same service method that serves API-RPT-003.
  - Honours: CON-RPT-005

**`recordDecision(checkId, employeeDecision, decidedBy, approvalApiExecuted)` → RecordedDecision {checkId, employeeDecision, decidedBy, decidedAt, approvalApiExecuted}** — READ_WRITE, its own transaction (REQUIRED from INT). (REQ-RPT-032 … REQ-RPT-039)
  - Honours: CON-RPT-006
  1. RULE-RPT-013 — decision not APPROVED / REJECTED, decidedBy blank, or approvalApiExecuted absent → `DecisionIncompleteException` (RPT-400-DECISION-INCOMPLETE) with the RULE message (REQ-RPT-035).
  2. RULE-RPT-014 — REJECTED with approvalApiExecuted = true → `ApprovalFlagOnRejectionException` (RPT-422-APPROVAL-FLAG-ON-REJECTION) "The decision was not recorded: only an APPROVED decision is executed through the Approval API." (REQ-RPT-039).
  3. Persist: UPDATE RPT_CHECK_RUN SET EMPLOYEE_DECISION (DBF-RPT-015), DECIDED_BY (DBF-RPT-016 — exactly as received, REQ-RPT-002), DECIDED_AT = SYSTIMESTAMP (DBF-RPT-017, ADR-RPT-009), APPROVAL_API_EXECUTED (DBF-RPT-018, REQ-RPT-036), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS = 'COMPLETED' AND EMPLOYEE_DECISION IS NULL. Nothing else of the row or of its report changes (REQ-RPT-020).
  4. 0 rows → read QR-RPT-001: none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-038); a decision present → `DecisionAlreadyRecordedException` (RPT-409-DECISION-ALREADY-RECORDED) "Check {checkId} already has an Employee Decision." (RULE-RPT-011, REQ-RPT-033); status not COMPLETED → `CheckNotCompletedException` (RPT-409-CHECK-NOT-COMPLETED) "Check {checkId} is not completed; a decision can only be recorded on a completed Check." (RULE-RPT-012, REQ-RPT-034).
  - Concurrency: the conditional UPDATE is the guard — two simultaneous decisions both pass validation, the database lets exactly one update the row; the other updates 0 rows and is refused with RPT-409-DECISION-ALREADY-RECORDED.

### Service layer — the scheduled purge (`ReportPurgeService`, ADR-RPT-004, ADR-RPT-010)
`@Scheduled(cron = aias.reports.purge-schedule)`; no HTTP API, no caller (REQ-RPT-042 … REQ-RPT-046, REQ-RPT-052).
  1. retention-days absent or < 1 → log "Report purge skipped: no valid report retention period is configured." and return; nothing deleted (REQ-RPT-044).
  2. cutOff = now − retention-days; SELECT CHECK_RUN_ID FROM RPT_CHECK_RUN WHERE CHECK_STATUS IN ('COMPLETED', 'FAILED') AND ENDED_AT < :cutOff (IDX_RPT_CHECK_RUN_ENDED_AT) — AWAITING_DOCUMENTS and RUNNING rows are never selected, whatever their age (REQ-RPT-045); rows ended within the period are never selected (REQ-RPT-042).
  3. For each id, in its own transaction (`REQUIRES_NEW`): DELETE FROM RPT_CHECK_RUN WHERE CHECK_RUN_ID = :id AND CHECK_STATUS IN ('COMPLETED', 'FAILED') AND ENDED_AT < :cutOff — FK_RPT_FINDING_RPT_CHECK_RUN, FK_RPT_CHECK_DOCUMENT_RPT_CHECK_RUN and FK_RPT_UNREAD_QUERY_RPT_CHECK_RUN cascade the Findings, Check Documents and Unread Queries; the Employee Decision is a column of the row (REQ-RPT-043). A failure rolls back that Check Run only, which stays whole, is logged, and the purge continues (REQ-RPT-052).
  4. Log "Report purge deleted {n} Check runs ended before {cutOff}." (REQ-RPT-046).
  - Concurrency: two instances purging at once delete the same row at most once; the second DELETE finds 0 rows and counts nothing.

### In-process rejection codes (typed exceptions of the result port and of `ReportStore` — ADR-RPT-012, ADR-RPT-013)
| Code | Rule / REQ | Exception | Message en | Message ar |
|---|---|---|---|---|
| RPT-400-CHECK-RUN-INCOMPLETE | RULE-RPT-001 | CheckRunIncompleteException | The Check run was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-422-INITIAL-STATUS-MISMATCH | RULE-RPT-002 | InitialStatusMismatchException | The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual. / The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-ENDED | RULE-RPT-003 | CheckEndedException | Check {checkId} has already ended; its status cannot change. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-NOT-RUNNING | RULE-RPT-003 | CheckNotRunningException | Check {checkId} is not running; it cannot be completed. | PENDING ADR-RPT-013 |
| RPT-404-CHECK-NOT-FOUND | REQ-RPT-007, REQ-RPT-038 | CheckNotFoundException | Check {checkId} was not found. | PENDING ADR-RPT-013 |
| RPT-422-METADATA-MISMATCH | RULE-RPT-004 | MetadataMismatchException | The report of Check {checkId} was not stored: its metadata {field} {value} differs from the Check run ({stored}). / The report of Check {checkId} was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-422-COMPLIANT-NOT-VERIFIED | RULE-RPT-005 | CompliantNotVerifiedException | The report of Check {checkId} was not stored: COMPLIANT needs every finding SATISFIED and every service query read. | PENDING ADR-RPT-013 |
| RPT-422-UNKNOWN-CODE | RULE-RPT-006 | UnknownCodeException | Not stored: `{value}` is not a code of {lookupKey}. | PENDING ADR-RPT-013 |
| RPT-422-FINDING-INCOMPLETE | RULE-RPT-007 | FindingIncompleteException | The report of Check {checkId} was not stored: finding {position} has no {field}. | PENDING ADR-RPT-013 |
| RPT-422-DOCUMENT-REASON-MISMATCH | RULE-RPT-008 | DocumentReasonMismatchException | The report of Check {checkId} was not stored: document {position} is UNREADABLE without a reason. / The report of Check {checkId} was not stored: document {position} is {readStatus} and cannot carry a reason. | PENDING ADR-RPT-013 |
| RPT-400-FAILURE-INCOMPLETE | RULE-RPT-010 | FailureIncompleteException | The failure of Check {checkId} was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-409-DECISION-ALREADY-RECORDED | RULE-RPT-011 | DecisionAlreadyRecordedException | Check {checkId} already has an Employee Decision. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-NOT-COMPLETED | RULE-RPT-012 | CheckNotCompletedException | Check {checkId} is not completed; a decision can only be recorded on a completed Check. | PENDING ADR-RPT-013 |
| RPT-400-DECISION-INCOMPLETE | RULE-RPT-013 | DecisionIncompleteException | The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. / The decision was not recorded: say whether it was executed through the Approval API. | PENDING ADR-RPT-013 |
| RPT-422-APPROVAL-FLAG-ON-REJECTION | RULE-RPT-014 | ApprovalFlagOnRejectionException | The decision was not recorded: only an APPROVED decision is executed through the Approval API. | PENDING ADR-RPT-013 |
| RPT-500-REPORT-NOT-STORED | REQ-RPT-009 | ReportNotStoredException | The report of Check {checkId} was not stored. | PENDING ADR-RPT-013 |

### HTTP endpoints
Controller `CheckReportController` → services `CheckReportQueryService`, `DecisionAgreementQueryService`. RPT exposes no POST, PUT or DELETE (ADR-RPT-006); every value is returned exactly as stored, as JSON string data — no HTML, no template rendering (REQ-RPT-027).

<!-- API:API-RPT-001:START traces=REQ-RPT-023,REQ-RPT-024,REQ-RPT-025,REQ-RPT-026,REQ-RPT-027,DBF-RPT-001,DBF-RPT-002,DBF-RPT-003,DBF-RPT-004,DBF-RPT-005,DBF-RPT-006,DBF-RPT-007,DBF-RPT-008,DBF-RPT-009,DBF-RPT-010,DBF-RPT-011,DBF-RPT-012,DBF-RPT-013,DBF-RPT-014,DBF-RPT-015,DBF-RPT-016,DBF-RPT-017,DBF-RPT-018,DBF-RPT-022,DBF-RPT-023,DBF-RPT-024,DBF-RPT-025,DBF-RPT-026,DBF-RPT-031,DBF-RPT-032,DBF-RPT-033,DBF-RPT-034,DBF-RPT-035,DBF-RPT-036,DBF-RPT-041,DBF-RPT-042,DBF-RPT-043 -->
### API-RPT-001 — Read a Check and its report
Entity       : ENT-RPT-001
Endpoint     : /api/v1/checks/{checkId}   verb: GET
Layers       : controller CheckReportController → service CheckReportQueryService
Request      : path checkId (DBF-RPT-001, integer int64, required); no query parameter; no body
Response     : 200 · CheckReport {checkId (DBF-RPT-001), status (DBF-RPT-007), serviceCode (DBF-RPT-002), versionNumber (DBF-RPT-003), fetchMode (DBF-RPT-004), requestNumber (DBF-RPT-005), employeeId (DBF-RPT-006), startedAt (DBF-RPT-008), runningSince (DBF-RPT-009), endedAt (DBF-RPT-010), overallStatus (DBF-RPT-011, COMPLETED only), comparisonModel (DBF-RPT-012, COMPLETED only), failureReason (DBF-RPT-013, FAILED only), failureDetail (DBF-RPT-014, FAILED only), findings [FindingView {position (DBF-RPT-022), condition (DBF-RPT-023), outcome (DBF-RPT-024), evidence (DBF-RPT-025), note (DBF-RPT-026)}], documents [DocumentView {position (DBF-RPT-031), documentType (DBF-RPT-032), sourceMode (DBF-RPT-033), readStatus (DBF-RPT-034), unreadableReason (DBF-RPT-035), detail (DBF-RPT-036)}], unreadQueries [UnreadQueryView {position (DBF-RPT-041), queryName (DBF-RPT-042), detail (DBF-RPT-043)}], decision {employeeDecision (DBF-RPT-015), decidedBy (DBF-RPT-016), decidedAt (DBF-RPT-017), approvalApiExecuted (DBF-RPT-018)} or null} · lists empty until COMPLETED · not paginated · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-RPT-013)
Errors       : RPT-400-CHECK-ID-INVALID (400, PLATFORM-STD) · RPT-404-CHECK-NOT-FOUND (404, PLATFORM-STD — REQ-RPT-025; also a purged Check) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate checkId → load the Check Run (QR-RPT-001) → none → RPT-404-CHECK-NOT-FOUND → COMPLETED: load Findings (QR-RPT-002), Check Documents (QR-RPT-003), Unread Queries (QR-RPT-004), each ordered by POSITION → map to CheckReport, each finding one entry with its evidence (REQ-RPT-024); FAILED: reason and detail, no Overall Status (REQ-RPT-026); writes nothing
Repository   : QR-RPT-001, QR-RPT-002, QR-RPT-003, QR-RPT-004 · FIND_ONE / FIND_BY_CRITERIA · join NONE (four single-table reads) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes; the four reads run in one READ_ONLY transaction so a report is read as one consistent state
Security     : none — no permission model, endpoints are open per the SRS (caller authentication and report-viewing rights deferred, raw-idea A2, domain-profile D4; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-003, CON-RPT-001
Covers       : the data this endpoint returns is written only by the in-process result port and decision procedures of this phase, which implement and are listed here for the coverage check: REQ-RPT-001, REQ-RPT-002, REQ-RPT-003, REQ-RPT-004, REQ-RPT-005, REQ-RPT-006, REQ-RPT-007, REQ-RPT-008, REQ-RPT-009, REQ-RPT-010, REQ-RPT-011, REQ-RPT-012, REQ-RPT-013, REQ-RPT-014, REQ-RPT-015, REQ-RPT-016, REQ-RPT-017, REQ-RPT-018, REQ-RPT-019, REQ-RPT-020, REQ-RPT-021, REQ-RPT-022, REQ-RPT-032, REQ-RPT-033, REQ-RPT-034, REQ-RPT-035, REQ-RPT-036, REQ-RPT-037, REQ-RPT-038, REQ-RPT-039, REQ-RPT-051, REQ-RPT-053; the purge (REQ-RPT-042, REQ-RPT-043, REQ-RPT-044, REQ-RPT-045, REQ-RPT-046, REQ-RPT-052) is why a purged Check answers RPT-404-CHECK-NOT-FOUND; CORE carries REQ-RPT-047, REQ-RPT-048, REQ-RPT-049
<!-- API:API-RPT-001:END -->

<!-- API:API-RPT-002:START traces=REQ-RPT-028,REQ-RPT-029,REQ-RPT-030,REQ-RPT-031,REQ-RPT-050,DBF-RPT-001,DBF-RPT-002,DBF-RPT-005,DBF-RPT-007,DBF-RPT-008,DBF-RPT-010,DBF-RPT-011,DBF-RPT-015 -->
### API-RPT-002 — List the Checks of a request
Entity       : ENT-RPT-001
Endpoint     : /api/v1/checks   verb: GET
Layers       : controller CheckReportController → service CheckReportQueryService
Request      : query serviceCode (DBF-RPT-002, string ≤ 100, required) · query requestNumber (DBF-RPT-005, string ≤ 100, required) — both matched exactly as sent; no body
Response     : 200 · ChecksOfRequest {total (count of every Check of the pair), checks [CheckSummary {checkId (DBF-RPT-001), status (DBF-RPT-007), overallStatus (DBF-RPT-011), startedAt (DBF-RPT-008), endedAt (DBF-RPT-010), employeeDecision (DBF-RPT-015)}] — at most 100, newest first (STARTED_AT DESC, CHECK_RUN_ID DESC)} · not paginated (fixed cap, REQ-RPT-031) · no envelope
Validations  : RULE-RPT-009 — A request's Checks need both keys · trigger: on list Checks of a request · statement: The system shall require a service code and a request number, both present and not blank, to list the Checks of a request. · message (en): Both a service code and a request number are needed to list Checks. · message (ar): PENDING ADR-RPT-013
Errors       : RPT-400-REQUEST-KEYS-MISSING (400, RULE-RPT-009) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate RULE-RPT-009 → newest 100 (QR-RPT-005) and total (QR-RPT-006), both with serviceCode and requestNumber as bound parameters (REQ-RPT-050) → ChecksOfRequest; each Check is its own row, nothing merged across Checks (REQ-RPT-030); writes nothing
Repository   : QR-RPT-005, QR-RPT-006 · FIND_BY_CRITERIA / COUNT · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-004
<!-- API:API-RPT-002:END -->

<!-- API:API-RPT-003:START traces=REQ-RPT-040,REQ-RPT-041,DBF-RPT-002,DBF-RPT-003,DBF-RPT-011,DBF-RPT-015 -->
### API-RPT-003 — Read the decision agreement of a service
Entity       : ENT-RPT-001
Endpoint     : /api/v1/decision-agreement   verb: GET
Layers       : controller CheckReportController → service DecisionAgreementQueryService
Request      : query serviceCode (DBF-RPT-002, string ≤ 100, required); no body
Response     : 200 · array of AgreementRow {versionNumber (DBF-RPT-003), overallStatus (DBF-RPT-011), employeeDecision (DBF-RPT-015), count} ordered by versionNumber DESC, overallStatus, employeeDecision · only Checks with a recorded decision · empty array when none · not paginated · no envelope
Validations  : RULE-RPT-015 — Decision agreement needs a service code · trigger: on read decision agreement · statement: The system shall require a service code, present and not blank, to read the decision agreement. · message (en): A service code is needed to read the decision agreement. · message (ar): PENDING ADR-RPT-013
Errors       : RPT-400-SERVICE-CODE-MISSING (400, RULE-RPT-015) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate RULE-RPT-015 → aggregate (QR-RPT-007, serviceCode bound) → AgreementRow list; writes nothing
Repository   : QR-RPT-007 · AGGREGATE · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-005, CON-RPT-002
<!-- API:API-RPT-003:END -->

<!-- PHASE:SVC-API:END -->

<!-- PHASE:ALIGN-BE:START traces=REQ-RPT-023,ENT-RPT-001 -->
## PHASE ALIGN-BE — ALIGN-BE

```
ALIGN — RPT v1
row               backing check        assertion
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF
COVERED           orphans              every REQ is covered by ≥1 API or DBF
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares for it
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not
QRC (§5)          orphans              every catalogued query is reached by ≥1 API
API (R3)          code-format          every catalog code is an instance of the declared format and carries a status the platform can emit
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-rpt.yaml and every operation one block, agreeing on method and path
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation of api-spec-rpt.yaml with its status and code
DOCUMENT VALID    api-spec-valid       api-spec-rpt.yaml validates against OPENAPI 3.1.0 and reaches every required item
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from, or is deferred
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered — examined nothing (0 XM)
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase, and nothing else names its target — examined nothing (0 XM)
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's own registry
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why it is not — examined nothing (0 SCR-REQ)
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/RPT/
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves to something that exists
COVERAGE          (the report)         as stamped by the orchestrator from the analyze report
```
```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C7.19
- C7.20
- C7.23
- C7.24
- C7.26
- C7.27
- C7.28
- C7.5
- C7.5b
```
<!-- PHASE:ALIGN-BE:END -->

<!-- PHASE:CROSS-MOD:START traces=REQ-RPT-001 -->
## PHASE CROSS-MOD — CROSS-MODULE

No edge: RPT consumes no entity of another module (SRS A8 `consumes: []`, db-script `records: []`). The Check result port RPT implements is the Check Engine's declared port (ADR-RPT-001) — an inbound call, not a dependency on Check Engine data. RPT's `CheckResultStore` implements its six operations as contract-chk.md promises them: `createCheckRun` (CON-CHK-006), `markRunning` (CON-CHK-007), `completeCheck` (CON-CHK-008), `failCheck` (CON-CHK-009), `getCheck` (CON-CHK-010), `listUnfinishedChecks` (CON-CHK-011); the codes it stores are those of CON-CHK-001 … CON-CHK-003 and of Document Access CON-DOC-001, CON-DOC-002, enforced by the CHECK constraints of db-script-rpt.md; inbound edges from Host Integration reach RPT through contract-rpt.md.

Result-port operations implemented by `CheckResultStore` (PORTS) and served by `CheckRunCommandService` / `CheckRunQueryService` (SVC-API), each signed exactly as contract-chk.md declares it:

**`createCheckRun(serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt)` → checkId** — SVC-API `createCheckRun`.
  - Honours: CON-CHK-006

**`markRunning(checkId, runningSince)`** — SVC-API `markRunning`.
  - Honours: CON-CHK-007

**`completeCheck(checkId, overallStatus, findings, documentOutcomes, unreadQueries, metadata)`** — SVC-API `completeCheck`; closed value types per PORTS (REQ-RPT-053, ADR-RPT-018).
  - Honours: CON-CHK-008

**`failCheck(checkId, failureReason, detail, endedAt)`** — SVC-API `failCheck`; closed value types per PORTS (REQ-RPT-053, ADR-RPT-018).
  - Honours: CON-CHK-009

**`getCheck(checkId)` → {checkId, status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt}** — SVC-API `getCheck`.
  - Honours: CON-CHK-010

**`listUnfinishedChecks()` → list of {checkId, status, startedAt}** — SVC-API `listUnfinishedChecks`.
  - Honours: CON-CHK-011
<!-- PHASE:CROSS-MOD:END -->

## QUERY REFERENCE CATALOG — RPT v1

### QR-RPT-001 — Check Run by identifier
Phase        : SVC-API
API          : API-RPT-001
Entity       : ENT-RPT-001
Operation    : FIND_ONE
Intent       : one Check with its status, metadata, result or failure and decision
Logical spec : SELECT CHECK_RUN_ID, SERVICE_CODE, VERSION_NUMBER, FETCH_MODE, REQUEST_NUMBER, EMPLOYEE_ID, CHECK_STATUS, STARTED_AT, RUNNING_SINCE, ENDED_AT, OVERALL_STATUS, COMPARISON_MODEL, FAILURE_REASON, FAILURE_DETAIL, EMPLOYEE_DECISION, DECIDED_BY, DECIDED_AT, APPROVAL_API_EXECUTED FROM RPT_CHECK_RUN WHERE CHECK_RUN_ID = :checkId
Join         : NONE
Transaction  : READ_ONLY (also used, with FOR UPDATE, inside completeCheck — READ_WRITE)
Locking      : NONE for the HTTP read; `FOR UPDATE` when completeCheck decides on the row it then writes
Pagination   : NO
Filters      : CHECK_RUN_ID: EXACT (PK_RPT_CHECK_RUN)
Result shape : full entity minus audit columns
Null handling: RUNNING_SINCE, ENDED_AT, OVERALL_STATUS, COMPARISON_MODEL, FAILURE_REASON, FAILURE_DETAIL and the four decision columns are null when not applicable; no row → RPT-404-CHECK-NOT-FOUND

### QR-RPT-002 — Findings of a Check Run
Phase        : SVC-API
API          : API-RPT-001
Entity       : ENT-RPT-002
Operation    : FIND_BY_CRITERIA
Intent       : the findings of one completed report in report order
Logical spec : SELECT POSITION, CONDITION_TEXT, FINDING_OUTCOME, EVIDENCE, NOTE FROM RPT_FINDING WHERE CHECK_RUN_ID = :checkId ORDER BY POSITION
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : CHECK_RUN_ID: EXACT (UQ_RPT_FINDING_RUN_POS)
Result shape : projection POSITION, CONDITION_TEXT, FINDING_OUTCOME, EVIDENCE, NOTE
Null handling: none optional; empty list for a Check not COMPLETED

### QR-RPT-003 — Check Documents of a Check Run
Phase        : SVC-API
API          : API-RPT-001
Entity       : ENT-RPT-003
Operation    : FIND_BY_CRITERIA
Intent       : the document outcomes of one completed report in report order
Logical spec : SELECT POSITION, DOCUMENT_TYPE, SOURCE_MODE, READ_STATUS, UNREADABLE_REASON, DETAIL FROM RPT_CHECK_DOCUMENT WHERE CHECK_RUN_ID = :checkId ORDER BY POSITION
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : CHECK_RUN_ID: EXACT (UQ_RPT_CHECK_DOCUMENT_RUN_POS)
Result shape : projection POSITION, DOCUMENT_TYPE, SOURCE_MODE, READ_STATUS, UNREADABLE_REASON, DETAIL
Null handling: UNREADABLE_REASON null unless UNREADABLE; DETAIL nullable; empty list for a Check not COMPLETED

### QR-RPT-004 — Unread Queries of a Check Run
Phase        : SVC-API
API          : API-RPT-001
Entity       : ENT-RPT-004
Operation    : FIND_BY_CRITERIA
Intent       : the service queries of one completed report whose data could not be read
Logical spec : SELECT POSITION, QUERY_NAME, DETAIL FROM RPT_UNREAD_QUERY WHERE CHECK_RUN_ID = :checkId ORDER BY POSITION
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : CHECK_RUN_ID: EXACT (UQ_RPT_UNREAD_QUERY_RUN_POS)
Result shape : projection POSITION, QUERY_NAME, DETAIL
Null handling: none optional; empty list when every query was read

### QR-RPT-005 — Newest 100 Checks of a request
Phase        : SVC-API
API          : API-RPT-002
Entity       : ENT-RPT-001
Operation    : FIND_BY_CRITERIA
Intent       : the Checks of one service code and request number, newest first, capped at 100 (REQ-RPT-028, REQ-RPT-031)
Logical spec : SELECT CHECK_RUN_ID, CHECK_STATUS, OVERALL_STATUS, STARTED_AT, ENDED_AT, EMPLOYEE_DECISION FROM RPT_CHECK_RUN WHERE SERVICE_CODE = :serviceCode AND REQUEST_NUMBER = :requestNumber ORDER BY STARTED_AT DESC, CHECK_RUN_ID DESC FETCH FIRST 100 ROWS ONLY
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO (fixed cap of 100)
Filters      : SERVICE_CODE: EXACT · REQUEST_NUMBER: EXACT — bound parameters (IDX_RPT_CHECK_RUN_SVC_REQ)
Result shape : projection CHECK_RUN_ID, CHECK_STATUS, OVERALL_STATUS, STARTED_AT, ENDED_AT, EMPLOYEE_DECISION
Null handling: OVERALL_STATUS, ENDED_AT, EMPLOYEE_DECISION null when not applicable

### QR-RPT-006 — Number of Checks of a request
Phase        : SVC-API
API          : API-RPT-002
Entity       : ENT-RPT-001
Operation    : COUNT
Intent       : the total that keeps the 100-row cap visible (REQ-RPT-031)
Logical spec : SELECT COUNT(*) FROM RPT_CHECK_RUN WHERE SERVICE_CODE = :serviceCode AND REQUEST_NUMBER = :requestNumber
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : SERVICE_CODE: EXACT · REQUEST_NUMBER: EXACT — bound parameters
Result shape : count
Null handling: 0 when no Check exists

### QR-RPT-007 — Decision agreement of a service
Phase        : SVC-API
API          : API-RPT-003
Entity       : ENT-RPT-001
Operation    : AGGREGATE
Intent       : per service package version, how many decided Checks of each Overall Status were approved or rejected (REQ-RPT-040)
Logical spec : SELECT VERSION_NUMBER, OVERALL_STATUS, EMPLOYEE_DECISION, COUNT(*) FROM RPT_CHECK_RUN WHERE SERVICE_CODE = :serviceCode AND EMPLOYEE_DECISION IS NOT NULL GROUP BY VERSION_NUMBER, OVERALL_STATUS, EMPLOYEE_DECISION ORDER BY VERSION_NUMBER DESC, OVERALL_STATUS, EMPLOYEE_DECISION
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : SERVICE_CODE: EXACT (bound) · EMPLOYEE_DECISION: NOT NULL
Result shape : projection VERSION_NUMBER, OVERALL_STATUS, EMPLOYEE_DECISION, count
Null handling: OVERALL_STATUS is never null on a decided Check (CHK_RPT_CHECK_RUN_DECISION ⇒ COMPLETED); empty list when nothing is decided

## ERROR CATALOG — RPT v1

```yaml name=error-catalog
rows:
  - {code: "RPT-400-CHECK-ID-INVALID", rule: PLATFORM-STD, api: [API-RPT-001], http: 400, trigger: "the checkId path parameter is not a number", messages: {en: "A numeric Check identifier is required.", ar: "PENDING ADR-RPT-013"}, adr: "ADR-RPT-013"}
  - {code: "RPT-404-CHECK-NOT-FOUND", rule: PLATFORM-STD, api: [API-RPT-001], http: 404, trigger: "no Check Run exists for the checkId — never created or purged (REQ-RPT-025)", messages: {en: "Check {checkId} was not found.", ar: "PENDING ADR-RPT-013"}, adr: "ADR-RPT-013"}
  - {code: "RPT-400-REQUEST-KEYS-MISSING", rule: RULE-RPT-009, api: [API-RPT-002], http: 400, trigger: "serviceCode or requestNumber absent or blank", messages: {en: "Both a service code and a request number are needed to list Checks.", ar: "PENDING ADR-RPT-013"}}
  - {code: "RPT-400-SERVICE-CODE-MISSING", rule: RULE-RPT-015, api: [API-RPT-003], http: 400, trigger: "serviceCode absent or blank", messages: {en: "A service code is needed to read the decision agreement.", ar: "PENDING ADR-RPT-013"}}
  - {code: "RPT-500", rule: PLATFORM-STD, api: [API-RPT-001, API-RPT-002, API-RPT-003], http: 500, trigger: "an unexpected server failure", messages: {en: "The Report Store could not complete the request.", ar: "PENDING ADR-RPT-013"}, adr: "ADR-RPT-013"}
```

## Coverage
| RULE | Where enforced | Catalog / in-process code |
|---|---|---|
| RULE-RPT-001 | createCheckRun step 1 | RPT-400-CHECK-RUN-INCOMPLETE (in-process) |
| RULE-RPT-002 | createCheckRun step 3 + CHK_RPT_CHECK_RUN_AWAITING | RPT-422-INITIAL-STATUS-MISMATCH (in-process) |
| RULE-RPT-003 | markRunning / completeCheck / failCheck conditional UPDATE | RPT-409-CHECK-ENDED, RPT-409-CHECK-NOT-RUNNING (in-process) |
| RULE-RPT-004 | completeCheck step 2 | RPT-422-METADATA-MISMATCH (in-process) |
| RULE-RPT-005 | completeCheck step 2 | RPT-422-COMPLIANT-NOT-VERIFIED (in-process) |
| RULE-RPT-006 | port enums + BLOCK 5c CHECK constraints | RPT-422-UNKNOWN-CODE (in-process) |
| RULE-RPT-007 | completeCheck step 2 | RPT-422-FINDING-INCOMPLETE (in-process) |
| RULE-RPT-008 | completeCheck step 2 + CHK_RPT_CHECK_DOCUMENT_REASON | RPT-422-DOCUMENT-REASON-MISMATCH (in-process) |
| RULE-RPT-009 | API-RPT-002 | RPT-400-REQUEST-KEYS-MISSING |
| RULE-RPT-010 | failCheck step 1 | RPT-400-FAILURE-INCOMPLETE (in-process) |
| RULE-RPT-011 | recordDecision conditional UPDATE | RPT-409-DECISION-ALREADY-RECORDED (in-process) |
| RULE-RPT-012 | recordDecision conditional UPDATE + CHK_RPT_CHECK_RUN_DECISION | RPT-409-CHECK-NOT-COMPLETED (in-process) |
| RULE-RPT-013 | recordDecision step 1 | RPT-400-DECISION-INCOMPLETE (in-process) |
| RULE-RPT-014 | recordDecision step 2 + CHK_RPT_CHECK_RUN_DECISION | RPT-422-APPROVAL-FLAG-ON-REJECTION (in-process) |
| RULE-RPT-015 | API-RPT-003 | RPT-400-SERVICE-CODE-MISSING |

| ENT / DBF | Phases | QR | XM |
|---|---|---|---|
| ENT-RPT-001 / DBF-RPT-001 … DBF-RPT-020 | DATA-DOM, PORTS, SVC-API | QR-RPT-001, QR-RPT-005, QR-RPT-006, QR-RPT-007 | — |
| ENT-RPT-002 / DBF-RPT-021 … DBF-RPT-029 | DATA-DOM, SVC-API | QR-RPT-002 | — |
| ENT-RPT-003 / DBF-RPT-030 … DBF-RPT-039 | DATA-DOM, SVC-API | QR-RPT-003 | — |
| ENT-RPT-004 / DBF-RPT-040 … DBF-RPT-046 | DATA-DOM, SVC-API | QR-RPT-004 | — |

Integration: none (0 XM). ADRs cited: ADR-RPT-001, ADR-RPT-002, ADR-RPT-003, ADR-RPT-004, ADR-RPT-006, ADR-RPT-008, ADR-RPT-009, ADR-RPT-010, ADR-RPT-011, ADR-RPT-012, ADR-RPT-013.
