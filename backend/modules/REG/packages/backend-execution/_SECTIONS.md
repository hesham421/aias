<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# BACKEND EXECUTION PLAN — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Dialect : oracle19c   Framework : spring-boot-4-java-21 (+ Spring AI 2.0, unused by REG)
Inputs : srs-reg.md · db-script-reg.md · registry-srs-reg.md · registry-db-reg.md · contract-reg.md (no other module's contract is read — REG consumes nothing)
Governance : FULL (db-script present)   Open ADRs : 0 BLOCKED — decisions applied: ADR-REG-001 … ADR-REG-011, ADR-REG-013 … ADR-REG-022 (analysis/decisions/REG/; gate-analysis REVISE 2026-10-01, round 2: ADR-REG-020 … ADR-REG-022)
══════════════════════════════════════════════════════════════════

## EXECUTION PLAN INDEX — REG v1

### Entity registry
| ENT | Name | Table | Business code | Operations |
|---|---|---|---|---|
| ENT-REG-001 | Service Package | REG_SVC_PKG | serviceCode | load-run create / withdraw / make available; read (API-REG-001, API-REG-002) |
| ENT-REG-002 | Service Package Version | REG_SVC_PKG_VER | serviceCode + versionNumber | load-run create; read (API-REG-001, API-REG-002); in-process supply |
| ENT-REG-003 | Service Query | REG_SVC_QUERY | — | load-run create; in-process supply |
| ENT-REG-004 | Required Document | REG_REQ_DOC | — | load-run create; read (API-REG-001, API-REG-002) |
| ENT-REG-005 | Connection | REG_CONNECTION | connectionName | activation create / update / remove; in-process supply |
| ENT-REG-006 | Load Result | REG_LOAD_RESULT | — | load-run create / delete; read (API-REG-003) |

### API registry
| API | Operation | Verb | Path | Traces |
|---|---|---|---|---|
| API-REG-001 | List services | GET | /api/v1/services | REQ-REG-013 |
| API-REG-002 | Read one service | GET | /api/v1/services/{serviceCode} | REQ-REG-014, REQ-REG-015 |
| API-REG-003 | Read the load report | GET | /api/v1/load-results | REQ-REG-008 |

### Rule registry
| RULE | Name | Scope | Enforced where | Message en / ar |
|---|---|---|---|---|
| RULE-REG-001 | Complete package | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-002 | One folder per service code | ENT-REG-001 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-003 | Versions are never edited in place | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-004 | Versions only move forward | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-005 | Query connection registered | ENT-REG-003 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-006 | Bound parameters only | ENT-REG-003 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-007 | Single SELECT statement | ENT-REG-003 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-008 | Fetch mode closed | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-009 | Document source complete | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-010 | Blob over jdbc only | ENT-REG-002, ENT-REG-003 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-011 | Approval definition required | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-012 | Closed service definition structure | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-013 | Unique connection name | ENT-REG-005 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-014 | Connection type closed | ENT-REG-005 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-015 | Read-only declaration required | ENT-REG-005 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-016 | Withdrawn or unknown service not supplied | ENT-REG-001 | API-REG-002 (HTTP 404) + in-process | ✓ / PENDING ADR-REG-011 |
| RULE-REG-017 | Current version needs activated connections | ENT-REG-003 | in-process interface | ✓ / PENDING ADR-REG-011 |
| RULE-REG-018 | Service knowledge not empty | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-019 | Unique query names | ENT-REG-003 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-020 | Package folder content closed | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-021 | Unique required document types | ENT-REG-004 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-022 | Valid service code | ENT-REG-001 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-023 | Package directory reachable | ENT-REG-006 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-024 | Stable package read | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-025 | Blob connection stays jdbc | ENT-REG-005, ENT-REG-002, ENT-REG-003 | load run (activation) | ✓ / PENDING ADR-REG-011 |
| RULE-REG-026 | Package files readable | ENT-REG-002 | load run | ✓ / PENDING ADR-REG-011 |
| RULE-REG-027 | Value fits its stored field | ENT-REG-002, ENT-REG-003, ENT-REG-004, ENT-REG-005 | load run (packages and activation) | ✓ / PENDING ADR-REG-011 |
| RULE-REG-028 | Load Result text shortened | ENT-REG-006 | load run (Load Result writer) | — (no message) |

### Screen registry
None — REG has no screen (SRS PART B not applicable; administration UI out of scope).

### QRC summary
| QR | Operation | Phase | ENT |
|---|---|---|---|
| QR-REG-001 | List available service packages | SVC-API | ENT-REG-001 |
| QR-REG-002 | Current version of a service package | SVC-API | ENT-REG-002 |
| QR-REG-003 | Required document types of a version | SVC-API | ENT-REG-004 |
| QR-REG-004 | Service package by service code | SVC-API | ENT-REG-001 |
| QR-REG-005 | Load results of the latest load run | SVC-API | ENT-REG-006 |

DB ALIGNMENT: see manifest — ALIGNED ✓ / issues: 0 · INTEGRATION: 0 edges (REG consumes nothing) · SECURITY: 0 screens × 0 roles (no permission model, caller authentication deferred — raw-idea A2)

```yaml name=totals
DBF: 49
XM: 0
API: 3
QR: 5
```

## DB ALIGNMENT MANIFEST — REG v1

Columns, types and SRS references are read from db-script-reg.md (dbf-matrix) by DBF id. Writer: the reason a required column is written by no endpoint.

| DBF | ENT | Plan property | Plan type | XM | Status | Writer |
|---|---|---|---|---|---|---|
| DBF-REG-001 | ENT-REG-001 | servicePackageId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-REG-002 | ENT-REG-001 | serviceCode | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-003 | ENT-REG-001 | available | Boolean | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-004 | ENT-REG-001 | registeredAt | OffsetDateTime | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-005 | ENT-REG-001 | withdrawnAt | OffsetDateTime | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-006 | ENT-REG-002 | servicePackageVersionId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-REG-007 | ENT-REG-002 | versionNumber | Integer | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-008 | ENT-REG-002 | serviceKnowledge | String (lob) | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-009 | ENT-REG-002 | serviceDefinition | String (lob) | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-010 | ENT-REG-002 | inputName | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-011 | ENT-REG-002 | fetchMode | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-012 | ENT-REG-002 | documentSourceQueryName | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-013 | ENT-REG-002 | documentTypeColumn | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-014 | ENT-REG-002 | documentPathColumn | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-015 | ENT-REG-002 | documentContentColumn | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-016 | ENT-REG-002 | approvalEnabled | Boolean | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-017 | ENT-REG-002 | approvalApi | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-018 | ENT-REG-002 | contentHash | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-019 | ENT-REG-002 | loadedAt | OffsetDateTime | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-020 | ENT-REG-002 | servicePackageId | Long | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-021 | ENT-REG-003 | serviceQueryId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-REG-022 | ENT-REG-003 | queryName | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-023 | ENT-REG-003 | connectionName | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-024 | ENT-REG-003 | sqlText | String (lob) | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-025 | ENT-REG-003 | servicePackageVersionId | Long | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-026 | ENT-REG-004 | requiredDocumentId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-REG-027 | ENT-REG-004 | documentType | String | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-028 | ENT-REG-004 | servicePackageVersionId | Long | — | ✓ | system-generated — written by the start-up load run from the package folder (ADR-REG-007) |
| DBF-REG-029 | ENT-REG-005 | connectionId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-REG-030 | ENT-REG-005 | connectionName | String | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-031 | ENT-REG-005 | connectionType | String | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-032 | ENT-REG-005 | endpoint | String | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-033 | ENT-REG-005 | queryTool | String | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-034 | ENT-REG-005 | dialect | String | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-035 | ENT-REG-005 | credentialReference | String | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-036 | ENT-REG-005 | readOnly | Boolean | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-037 | ENT-REG-005 | limitedToViews | Boolean | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-038 | ENT-REG-005 | environmentName | String | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-039 | ENT-REG-005 | activatedAt | OffsetDateTime | — | ✓ | system-generated — written by the activation step of the load run (ADR-REG-009) |
| DBF-REG-040 | ENT-REG-006 | loadResultId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-REG-041 | ENT-REG-006 | loadRunAt | OffsetDateTime | — | ✓ | system-generated — written by the load run (ADR-REG-007) |
| DBF-REG-042 | ENT-REG-006 | subjectKind | String | — | ✓ | system-generated — written by the load run (ADR-REG-007) |
| DBF-REG-043 | ENT-REG-006 | subjectName | String | — | ✓ | system-generated — written by the load run (ADR-REG-007) |
| DBF-REG-044 | ENT-REG-006 | serviceCode | String | — | ✓ | system-generated — written by the load run (ADR-REG-007) |
| DBF-REG-045 | ENT-REG-006 | versionNumber | Integer | — | ✓ | system-generated — written by the load run (ADR-REG-007) |
| DBF-REG-046 | ENT-REG-006 | outcome | String | — | ✓ | system-generated — written by the load run (ADR-REG-007) |
| DBF-REG-047 | ENT-REG-006 | reason | String | — | ✓ | system-generated — written by the load run (ADR-REG-007) |
| DBF-REG-048 | ENT-REG-006 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-REG-049 | ENT-REG-006 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |

Legend ✓ aligned. No derived property. No cross-module column.













## QUERY REFERENCE CATALOG — REG v1

### QR-REG-001 — List available service packages
Phase        : SVC-API
API          : API-REG-001
Entity       : ENT-REG-001
Operation    : FIND_BY_CRITERIA
Intent       : the service codes new Checks can use
Logical spec : SELECT SERVICE_PACKAGE_ID, SERVICE_CODE FROM REG_SVC_PKG WHERE AVAILABLE = 1 ORDER BY SERVICE_CODE
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : AVAILABLE: EXACT (= 1)
Result shape : projection SERVICE_PACKAGE_ID, SERVICE_CODE
Null handling: none optional

### QR-REG-002 — Current version of a service package
Phase        : SVC-API
API          : API-REG-001, API-REG-002
Entity       : ENT-REG-002
Operation    : FIND_ONE
Intent       : the highest stored version of one package (the current version, ADR-REG-003)
Logical spec : SELECT SERVICE_PACKAGE_VERSION_ID, VERSION_NUMBER, FETCH_MODE, APPROVAL_ENABLED FROM REG_SVC_PKG_VER WHERE SERVICE_PACKAGE_ID = :servicePackageId ORDER BY VERSION_NUMBER DESC FETCH FIRST 1 ROW ONLY
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : SERVICE_PACKAGE_ID: EXACT
Result shape : projection VERSION_NUMBER, FETCH_MODE, APPROVAL_ENABLED (never SERVICE_KNOWLEDGE, SERVICE_DEFINITION)
Null handling: no row → package has no stored version (cannot occur for a registered code; treated as REG-500)

### QR-REG-003 — Required document types of a version
Phase        : SVC-API
API          : API-REG-001, API-REG-002
Entity       : ENT-REG-004
Operation    : FIND_ALL
Intent       : the documentType values of one version
Logical spec : SELECT DOCUMENT_TYPE FROM REG_REQ_DOC WHERE SERVICE_PACKAGE_VERSION_ID = :servicePackageVersionId ORDER BY DOCUMENT_TYPE
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : SERVICE_PACKAGE_VERSION_ID: EXACT
Result shape : projection DOCUMENT_TYPE
Null handling: empty list allowed (manual services without required documents)

### QR-REG-004 — Service package by service code
Phase        : SVC-API
API          : API-REG-002
Entity       : ENT-REG-001
Operation    : FIND_ONE
Intent       : the package of one service code, available or withdrawn
Logical spec : SELECT SERVICE_PACKAGE_ID, SERVICE_CODE, AVAILABLE FROM REG_SVC_PKG WHERE SERVICE_CODE = :serviceCode
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : SERVICE_CODE: EXACT on the canonical (trimmed, lower-case) code (REQ-REG-064)
Result shape : projection SERVICE_PACKAGE_ID, SERVICE_CODE, AVAILABLE (AVAILABLE → ServiceSummary.available)
Null handling: no row → REG-404-SERVICE-NOT-FOUND (RULE-REG-016)

### QR-REG-005 — Load results of the latest load run
Phase        : SVC-API
API          : API-REG-003
Entity       : ENT-REG-006
Operation    : FIND_BY_CRITERIA
Intent       : every outcome of the most recent load run
Logical spec : SELECT LOAD_RESULT_ID, LOAD_RUN_AT, SUBJECT_KIND, SUBJECT_NAME, SERVICE_CODE, VERSION_NUMBER, OUTCOME, REASON FROM REG_LOAD_RESULT WHERE LOAD_RUN_AT = (SELECT MAX(LOAD_RUN_AT) FROM REG_LOAD_RESULT) ORDER BY SUBJECT_KIND, SUBJECT_NAME
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : LOAD_RUN_AT: EXACT (latest)
Result shape : full entity minus audit columns
Null handling: SERVICE_CODE, VERSION_NUMBER, REASON nullable → omitted as null

## ERROR CATALOG — REG v1

```yaml name=error-catalog
rows:
  - {code: "REG-404-SERVICE-NOT-FOUND", rule: RULE-REG-016, api: [API-REG-002], http: 404, trigger: "the service code is not held by the registry", messages: {en: "The service \"{serviceCode}\" is not available.", ar: "PENDING ADR-REG-011"}}
  - {code: "REG-500", rule: PLATFORM-STD, api: [API-REG-001, API-REG-002, API-REG-003], http: 500, trigger: "an unexpected server failure", messages: {en: "The service registry could not complete the request.", ar: "PENDING ADR-REG-011"}, adr: "ADR-REG-011"}
```

## Coverage
| RULE | API | Catalog / load reason code |
|---|---|---|
| RULE-REG-001 | load run (outcome via API-REG-003) | REG-LOAD-INCOMPLETE-PACKAGE |
| RULE-REG-002 | load run (outcome via API-REG-003) | REG-LOAD-DUPLICATE-SERVICE-CODE |
| RULE-REG-003 | load run (outcome via API-REG-003) | REG-LOAD-VERSION-EDITED-IN-PLACE |
| RULE-REG-004 | load run (outcome via API-REG-003) | REG-LOAD-VERSION-OLDER-THAN-CURRENT |
| RULE-REG-005 | load run (outcome via API-REG-003) | REG-LOAD-CONNECTION-NOT-ACTIVATED |
| RULE-REG-006 | load run (outcome via API-REG-003) | REG-LOAD-UNBOUND-PARAMETER |
| RULE-REG-007 | load run (outcome via API-REG-003) | REG-LOAD-NOT-SINGLE-SELECT |
| RULE-REG-008 | load run (outcome via API-REG-003) | REG-LOAD-UNKNOWN-FETCH-MODE |
| RULE-REG-009 | load run (outcome via API-REG-003) | REG-LOAD-DOCUMENT-SOURCE-INCOMPLETE |
| RULE-REG-010 | load run (outcome via API-REG-003) | REG-LOAD-BLOB-NOT-JDBC |
| RULE-REG-011 | load run (outcome via API-REG-003) | REG-LOAD-APPROVAL-API-UNDEFINED |
| RULE-REG-012 | load run (outcome via API-REG-003) | REG-LOAD-ELEMENT-NOT-ALLOWED |
| RULE-REG-013 | load run (outcome via API-REG-003) | REG-LOAD-DUPLICATE-CONNECTION-NAME |
| RULE-REG-014 | load run (outcome via API-REG-003) | REG-LOAD-UNKNOWN-CONNECTION-TYPE |
| RULE-REG-015 | load run (outcome via API-REG-003) | REG-LOAD-CONNECTION-NOT-READ-ONLY |
| RULE-REG-016 | API-REG-002 | REG-404-SERVICE-NOT-FOUND |
| RULE-REG-017 | in-process | ServiceConnectionNotActivatedException (REG-SERVE-CONNECTION-NOT-ACTIVATED) |
| RULE-REG-018 | load run (outcome via API-REG-003) | REG-LOAD-EMPTY-SERVICE-KNOWLEDGE |
| RULE-REG-019 | load run (outcome via API-REG-003) | REG-LOAD-DUPLICATE-QUERY-NAME |
| RULE-REG-020 | load run (outcome via API-REG-003) | REG-LOAD-FOREIGN-FILE-IN-PACKAGE |
| RULE-REG-021 | load run (outcome via API-REG-003) | REG-LOAD-DUPLICATE-DOCUMENT-TYPE |
| RULE-REG-022 | load run (outcome via API-REG-003) | REG-LOAD-INVALID-SERVICE-CODE |
| RULE-REG-023 | load run (outcome via API-REG-003) | REG-LOAD-PACKAGE-DIRECTORY-UNAVAILABLE |
| RULE-REG-024 | load run (outcome via API-REG-003) | REG-LOAD-PACKAGE-CHANGED-DURING-READ |
| RULE-REG-025 | load run (outcome via API-REG-003) | REG-LOAD-CONNECTION-TYPE-BREAKS-BLOB |
| RULE-REG-026 | load run (outcome via API-REG-003) | REG-LOAD-PACKAGE-FILE-UNREADABLE |
| RULE-REG-027 | load run (outcome via API-REG-003) | REG-LOAD-VALUE-TOO-LONG |
| RULE-REG-028 | load run (Load Result writer) | — (shortens text, no code) |

Integration: none (0 XM). ADRs cited: ADR-REG-003, ADR-REG-006, ADR-REG-007, ADR-REG-009, ADR-REG-010, ADR-REG-011, ADR-REG-015, ADR-REG-016, ADR-REG-017, ADR-REG-018, ADR-REG-019, ADR-REG-020, ADR-REG-021, ADR-REG-022.
