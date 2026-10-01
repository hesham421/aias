<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# BACKEND EXECUTION PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Dialect : oracle19c   Framework : spring-boot-4-java-21 (+ Spring AI 2.0, unused by RPT — REQ-RPT-049)
Inputs : srs-rpt.md · db-script-rpt.md · registry-srs-rpt.md · registry-db-rpt.md · contract-rpt.md · contract-chk.md (the Check result port RPT implements and the closed lists it stores) · contract-doc.md (the closed lists it stores by value)
Governance : FULL (db-script present)   Open ADRs : 0 BLOCKED — decisions applied: ADR-RPT-001 … ADR-RPT-013, ADR-RPT-018, ADR-RPT-020 (analysis/decisions/RPT/)
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
