# BACKEND EXECUTION PLAN — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Dialect : oracle19c   Framework : spring-boot-4-java-21 (+ Spring AI 2.0, unused by REG)
Inputs : srs-reg.md · db-script-reg.md · registry-srs-reg.md · registry-db-reg.md · contract-reg.md (no other module's contract is read — REG consumes nothing)
Governance : FULL (db-script present)   Open ADRs : 0 BLOCKED — decisions applied: ADR-REG-001 … ADR-REG-011 (analysis/decisions/REG/)
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

<!-- PHASE:CORE:START traces=REQ-REG-034,REQ-REG-059,REQ-REG-016 -->
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
- Configuration properties (environment settings, never in the deployable): `aias.registry.package-directory` (ADR-REG-007), `aias.registry.environment-name` and `aias.registry.connections[]` with `name, type, endpoint, query-tool, dialect, credential-reference, read-only, limited-to-views` (ADR-REG-009). The check limits (timeout, maximum rows, maximum file size) are platform configuration of this phase and are never read from a service definition (ADR-REG-006, REQ-REG-034).
- No request data is held by REG (REQ-REG-059): no REG table, DTO or cache carries a request number, employee identity, query result or document content.
- Packages are read only from `aias.registry.package-directory` (REQ-REG-016); a folder path is resolved and normalised and must lie inside that directory.
<!-- PHASE:CORE:END -->

<!-- PHASE:DATA-DOM:START traces=ENT-REG-001,ENT-REG-002,ENT-REG-003,ENT-REG-004,ENT-REG-005,ENT-REG-006,DBF-REG-001,DBF-REG-002,DBF-REG-003,DBF-REG-004,DBF-REG-005,DBF-REG-006,DBF-REG-007,DBF-REG-008,DBF-REG-009,DBF-REG-010,DBF-REG-011,DBF-REG-012,DBF-REG-013,DBF-REG-014,DBF-REG-015,DBF-REG-016,DBF-REG-017,DBF-REG-018,DBF-REG-019,DBF-REG-020,DBF-REG-021,DBF-REG-022,DBF-REG-023,DBF-REG-024,DBF-REG-025,DBF-REG-026,DBF-REG-027,DBF-REG-028,DBF-REG-029,DBF-REG-030,DBF-REG-031,DBF-REG-032,DBF-REG-033,DBF-REG-034,DBF-REG-035,DBF-REG-036,DBF-REG-037,DBF-REG-038,DBF-REG-039,DBF-REG-040,DBF-REG-041,DBF-REG-042,DBF-REG-043,DBF-REG-044,DBF-REG-045,DBF-REG-046,DBF-REG-047,DBF-REG-048,DBF-REG-049 -->
## PHASE DATA-DOM — DATA+DOM

<!-- SUB:DATA-DOM-CONFIG:START traces=ENT-REG-001,ENT-REG-002,ENT-REG-003,ENT-REG-004,ENT-REG-005 -->
### SUB DATA-DOM-CONFIG
### ENT-REG-001 — Service Package      kind: config
BINDINGS   table REG_SVC_PKG · PK SERVICE_PACKAGE_ID (DBF-REG-001) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · db-script-reg.md v1
DEFAULT FIELDS per kind: config → none

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-REG-001 | servicePackageId | SERVICE_PACKAGE_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | servicePackageId / PENDING ADR-REG-011 |
| DBF-REG-002 | serviceCode | SERVICE_CODE | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | serviceCode / PENDING ADR-REG-011 |
| DBF-REG-003 | available | AVAILABLE | NUMBER(1) | NOT NULL | yes (no HTTP write) | — | available / PENDING ADR-REG-011 |
| DBF-REG-004 | registeredAt | REGISTERED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | registeredAt / PENDING ADR-REG-011 |
| DBF-REG-005 | withdrawnAt | WITHDRAWN_AT | TIMESTAMP WITH TIME ZONE | None | yes (no HTTP write) | — | withdrawnAt / PENDING ADR-REG-011 |

DTO MEMBERSHIP  create-request: none (no HTTP create — ADR-REG-007) · update-request: none · response: see API blocks (PK never exposed except loadResultId; serviceCode always present)
LOOKUP FIELDS  serviceCode → SERVICE_CODE (open, data) — stores the code; no lookup endpoint (values are CHECK constraints or package data, ADR-REG-010)
DOMAIN RULES
- RULE-REG-002 — One folder per service code · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject every package that declares a service code when two or more folders declare that service code.
  - message (en): The service code "{serviceCode}" is declared by more than one package folder; keep one folder per service. · message (ar): PENDING ADR-REG-011
  - DB enforcement: UQ_REG_SVC_PKG_SERVICE_CODE (+ app-level, load run) · owner layer: service (load run / registry service)
- RULE-REG-016 — Withdrawn or unknown service not supplied · trigger: on package request and on service read · scope: ALL (read)
  - statement: The system shall refuse to supply or return a service when its service code is not held by the registry or its Service Package is withdrawn (for a read, only an unknown code is refused).
  - message (en): The service "{serviceCode}" is not available. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
STATE MACHINE  AVAILABLE flag (DBF-REG-003): 1 ⇄ 0 — load run only (REQ-REG-010, REQ-REG-011); ≤ 2 states
CROSS-MODULE   none
REPOSITORY OPS QR-REG-001, QR-REG-004

### ENT-REG-002 — Service Package Version      kind: config
BINDINGS   table REG_SVC_PKG_VER · PK SERVICE_PACKAGE_VERSION_ID (DBF-REG-006) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · db-script-reg.md v1
DEFAULT FIELDS per kind: config → none

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-REG-006 | servicePackageVersionId | SERVICE_PACKAGE_VERSION_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | servicePackageVersionId / PENDING ADR-REG-011 |
| DBF-REG-007 | versionNumber | VERSION_NUMBER | NUMBER(10,0) | NOT NULL | yes (no HTTP write) | — | versionNumber / PENDING ADR-REG-011 |
| DBF-REG-008 | serviceKnowledge | SERVICE_KNOWLEDGE | CLOB | NOT NULL | yes (no HTTP write) | — | serviceKnowledge / PENDING ADR-REG-011 |
| DBF-REG-009 | serviceDefinition | SERVICE_DEFINITION | CLOB | NOT NULL | yes (no HTTP write) | — | serviceDefinition / PENDING ADR-REG-011 |
| DBF-REG-010 | inputName | INPUT_NAME | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | inputName / PENDING ADR-REG-011 |
| DBF-REG-011 | fetchMode | FETCH_MODE | VARCHAR2(10 CHAR) | NOT NULL | yes (no HTTP write) | — | fetchMode / PENDING ADR-REG-011 |
| DBF-REG-012 | documentSourceQueryName | DOCUMENT_SOURCE_QUERY_NAME | VARCHAR2(100 CHAR) | None | yes (no HTTP write) | — | documentSourceQueryName / PENDING ADR-REG-011 |
| DBF-REG-013 | documentTypeColumn | DOCUMENT_TYPE_COLUMN | VARCHAR2(128 CHAR) | None | yes (no HTTP write) | — | documentTypeColumn / PENDING ADR-REG-011 |
| DBF-REG-014 | documentPathColumn | DOCUMENT_PATH_COLUMN | VARCHAR2(128 CHAR) | None | yes (no HTTP write) | — | documentPathColumn / PENDING ADR-REG-011 |
| DBF-REG-015 | documentContentColumn | DOCUMENT_CONTENT_COLUMN | VARCHAR2(128 CHAR) | None | yes (no HTTP write) | — | documentContentColumn / PENDING ADR-REG-011 |
| DBF-REG-016 | approvalEnabled | APPROVAL_ENABLED | NUMBER(1) | NOT NULL | yes (no HTTP write) | — | approvalEnabled / PENDING ADR-REG-011 |
| DBF-REG-017 | approvalApi | APPROVAL_API | VARCHAR2(500 CHAR) | None | yes (no HTTP write) | — | approvalApi / PENDING ADR-REG-011 |
| DBF-REG-018 | contentHash | CONTENT_HASH | VARCHAR2(64 CHAR) | NOT NULL | yes (no HTTP write) | — | contentHash / PENDING ADR-REG-011 |
| DBF-REG-019 | loadedAt | LOADED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | loadedAt / PENDING ADR-REG-011 |
| DBF-REG-020 | servicePackageId | SERVICE_PACKAGE_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | — | servicePackageId / PENDING ADR-REG-011 |

DTO MEMBERSHIP  create-request: none (no HTTP create — ADR-REG-007) · update-request: none · response: see API blocks (PK never exposed except loadResultId; serviceCode always present)
LOOKUP FIELDS  fetchMode → FETCH_MODE (closed: path, blob, manual) — stores the code; no lookup endpoint (values are CHECK constraints or package data, ADR-REG-010)
DOMAIN RULES
- RULE-REG-001 — Complete package · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its folder lacks the service knowledge file or the service definition file.
  - message (en): The service package "{folder}" is incomplete: it needs both its service knowledge and its service definition. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-003 — Versions are never edited in place · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its version number is already stored for its service and its content hash differs from the stored one.
  - message (en): Version {versionNumber} of "{serviceCode}" already exists with different content; publish the change as a new version. · message (ar): PENDING ADR-REG-011
  - DB enforcement: UQ_REG_SVC_PKG_VER_PKG_VERSION (+ app-level content-hash comparison) · owner layer: service (load run / registry service)
- RULE-REG-004 — Versions only move forward · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its version number is lower than the current version and is not stored.
  - message (en): Version {versionNumber} of "{serviceCode}" is older than the current version {currentVersion}. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-008 — Fetch mode closed · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its fetch mode is not `path`, `blob` or `manual`.
  - message (en): The fetch mode "{fetchMode}" is not supported; use path, blob or manual. · message (ar): PENDING ADR-REG-011
  - DB enforcement: CHK_REG_SVC_PKG_VER_FETCH_MODE (+ app-level) · owner layer: service (load run / registry service)
- RULE-REG-009 — Document source complete · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its fetch mode is `path` or `blob` and the document source query, the document type column or the document location column (path column for `path`, content column for `blob`) is missing, or the document source query is not a query of the same service definition.
  - message (en): The documents of "{serviceCode}" cannot be fetched: the document source is incomplete. · message (ar): PENDING ADR-REG-011
  - DB enforcement: CHK_REG_SVC_PKG_VER_DOC_SOURCE, CHK_REG_SVC_PKG_VER_PATH_COLUMN, CHK_REG_SVC_PKG_VER_CONTENT_COLUMN (+ app-level query-name check) · owner layer: service (load run / registry service)
- RULE-REG-010 — Blob over jdbc only · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its fetch mode is `blob` and the connection of its document source query is not of type `jdbc`.
  - message (en): Documents stored in the database are read over a read-only jdbc connection; "{connectionName}" is not one. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-011 — Approval definition required · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when the approval API is enabled and no approval API definition is given.
  - message (en): The approval API of "{serviceCode}" is enabled but not defined. · message (ar): PENDING ADR-REG-011
  - DB enforcement: CHK_REG_SVC_PKG_VER_APPROVAL_API (+ app-level) · owner layer: service (load run / registry service)
- RULE-REG-012 — Closed service definition structure · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its service definition contains an element outside the service definition structure (service, version, input, queries, documents, approval), including any timeout, maximum row count, maximum file size or file location.
  - message (en): The service definition of "{serviceCode}" contains "{element}", which a service definition cannot set. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-018 — Service knowledge not empty · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its service knowledge text is empty.
  - message (en): The service knowledge of "{serviceCode}" is empty. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-020 — Package folder content closed · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when its folder holds any file besides the service knowledge file and the service definition file.
  - message (en): The package folder "{folder}" holds "{file}"; a package folder holds only its service knowledge and its service definition. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-REG-002

### ENT-REG-003 — Service Query      kind: config
BINDINGS   table REG_SVC_QUERY · PK SERVICE_QUERY_ID (DBF-REG-021) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · db-script-reg.md v1
DEFAULT FIELDS per kind: config → none

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-REG-021 | serviceQueryId | SERVICE_QUERY_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | serviceQueryId / PENDING ADR-REG-011 |
| DBF-REG-022 | queryName | QUERY_NAME | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | queryName / PENDING ADR-REG-011 |
| DBF-REG-023 | connectionName | CONNECTION_NAME | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | connectionName / PENDING ADR-REG-011 |
| DBF-REG-024 | sqlText | SQL_TEXT | CLOB | NOT NULL | yes (no HTTP write) | — | sqlText / PENDING ADR-REG-011 |
| DBF-REG-025 | servicePackageVersionId | SERVICE_PACKAGE_VERSION_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | — | servicePackageVersionId / PENDING ADR-REG-011 |

DTO MEMBERSHIP  create-request: none (no HTTP create — ADR-REG-007) · update-request: none · response: see API blocks (PK never exposed except loadResultId; serviceCode always present)
LOOKUP FIELDS  none — stores the code; no lookup endpoint (values are CHECK constraints or package data, ADR-REG-010)
DOMAIN RULES
- RULE-REG-005 — Query connection registered · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when a query names a connection that the environment does not register.
  - message (en): The query "{queryName}" uses the connection "{connectionName}", which is not activated in this environment. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-006 — Bound parameters only · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when a query uses a parameter other than the declared input or a substitution marker other than a named bind parameter.
  - message (en): The query "{queryName}" uses "{marker}"; queries take only the bind parameter ":{inputName}". · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-007 — Single SELECT statement · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when a query text holds anything other than one SELECT statement, with or without a leading WITH clause.
  - message (en): The query "{queryName}" must be one SELECT statement; it cannot change data or run several statements. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-017 — Current version needs activated connections · trigger: on package request · scope: ALL (read)
  - statement: The system shall refuse to supply the current version of a service when one of its queries names a connection the environment does not register.
  - message (en): The service "{serviceCode}" uses the connection "{connectionName}", which is not activated in this environment. · message (ar): PENDING ADR-REG-011
  - DB enforcement: app-level · owner layer: service (load run / registry service)
- RULE-REG-019 — Unique query names · trigger: on load · scope: CREATE (load run)
  - statement: The system shall reject a package when two queries of its service definition carry the same query name.
  - message (en): The query name "{queryName}" is used twice in "{serviceCode}". · message (ar): PENDING ADR-REG-011
  - DB enforcement: UQ_REG_SVC_QUERY_VER_NAME (+ app-level) · owner layer: service (load run / registry service)
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS none catalogued (load run / in-process — ADR-REG-011)

### ENT-REG-004 — Required Document      kind: config
BINDINGS   table REG_REQ_DOC · PK REQUIRED_DOCUMENT_ID (DBF-REG-026) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · db-script-reg.md v1
DEFAULT FIELDS per kind: config → none

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-REG-026 | requiredDocumentId | REQUIRED_DOCUMENT_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | requiredDocumentId / PENDING ADR-REG-011 |
| DBF-REG-027 | documentType | DOCUMENT_TYPE | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | documentType / PENDING ADR-REG-011 |
| DBF-REG-028 | servicePackageVersionId | SERVICE_PACKAGE_VERSION_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | — | servicePackageVersionId / PENDING ADR-REG-011 |

DTO MEMBERSHIP  create-request: none (no HTTP create — ADR-REG-007) · update-request: none · response: see API blocks (PK never exposed except loadResultId; serviceCode always present)
LOOKUP FIELDS  documentType → DOCUMENT_TYPE (open, data) — stores the code; no lookup endpoint (values are CHECK constraints or package data, ADR-REG-010)
DOMAIN RULES
- none specific to this entity
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-REG-003

### ENT-REG-005 — Connection      kind: config
BINDINGS   table REG_CONNECTION · PK CONNECTION_ID (DBF-REG-029) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · db-script-reg.md v1
DEFAULT FIELDS per kind: config → none

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-REG-029 | connectionId | CONNECTION_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | connectionId / PENDING ADR-REG-011 |
| DBF-REG-030 | connectionName | CONNECTION_NAME | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | connectionName / PENDING ADR-REG-011 |
| DBF-REG-031 | connectionType | CONNECTION_TYPE | VARCHAR2(10 CHAR) | NOT NULL | yes (no HTTP write) | — | connectionType / PENDING ADR-REG-011 |
| DBF-REG-032 | endpoint | ENDPOINT | VARCHAR2(500 CHAR) | NOT NULL | yes (no HTTP write) | — | endpoint / PENDING ADR-REG-011 |
| DBF-REG-033 | queryTool | QUERY_TOOL | VARCHAR2(100 CHAR) | None | yes (no HTTP write) | — | queryTool / PENDING ADR-REG-011 |
| DBF-REG-034 | dialect | DIALECT | VARCHAR2(30 CHAR) | NOT NULL | yes (no HTTP write) | — | dialect / PENDING ADR-REG-011 |
| DBF-REG-035 | credentialReference | CREDENTIAL_REFERENCE | VARCHAR2(200 CHAR) | NOT NULL | yes (no HTTP write) | — | credentialReference / PENDING ADR-REG-011 |
| DBF-REG-036 | readOnly | READ_ONLY | NUMBER(1) | NOT NULL | yes (no HTTP write) | — | readOnly / PENDING ADR-REG-011 |
| DBF-REG-037 | limitedToViews | LIMITED_TO_VIEWS | NUMBER(1) | NOT NULL | yes (no HTTP write) | — | limitedToViews / PENDING ADR-REG-011 |
| DBF-REG-038 | environmentName | ENVIRONMENT_NAME | VARCHAR2(50 CHAR) | NOT NULL | yes (no HTTP write) | — | environmentName / PENDING ADR-REG-011 |
| DBF-REG-039 | activatedAt | ACTIVATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | activatedAt / PENDING ADR-REG-011 |

DTO MEMBERSHIP  create-request: none (no HTTP create — ADR-REG-007) · update-request: none · response: see API blocks (PK never exposed except loadResultId; serviceCode always present)
LOOKUP FIELDS  connectionType → CONNECTION_TYPE (closed: mcp, jdbc) — stores the code; no lookup endpoint (values are CHECK constraints or package data, ADR-REG-010)
DOMAIN RULES
- RULE-REG-013 — Unique connection name · trigger: on activation · scope: CREATE (load run)
  - statement: The system shall refuse every connection of a name when the activation configuration lists that name more than once.
  - message (en): The connection name "{connectionName}" is listed more than once. · message (ar): PENDING ADR-REG-011
  - DB enforcement: UQ_REG_CONNECTION_CONNECTION_NAME (+ app-level) · owner layer: service (load run / registry service)
- RULE-REG-014 — Connection type closed · trigger: on activation · scope: CREATE (load run)
  - statement: The system shall refuse a connection when its type is neither `mcp` nor `jdbc`.
  - message (en): The connection "{connectionName}" has the type "{connectionType}"; use mcp or jdbc. · message (ar): PENDING ADR-REG-011
  - DB enforcement: CHK_REG_CONNECTION_CONNECTION_TYPE (+ app-level) · owner layer: service (load run / registry service)
- RULE-REG-015 — Read-only declaration required · trigger: on activation · scope: CREATE (load run)
  - statement: The system shall refuse a connection when it is not declared read-only.
  - message (en): The connection "{connectionName}" must use a read-only database user. · message (ar): PENDING ADR-REG-011
  - DB enforcement: CHK_REG_CONNECTION_READ_ONLY (+ app-level) · owner layer: service (load run / registry service)
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS none catalogued (load run / in-process — ADR-REG-011)

<!-- SUB:DATA-DOM-CONFIG:END -->
<!-- SUB:DATA-DOM-TRANSACTIONAL:START traces=ENT-REG-006 -->
### SUB DATA-DOM-TRANSACTIONAL
### ENT-REG-006 — Load Result      kind: transactional
BINDINGS   table REG_LOAD_RESULT · PK LOAD_RESULT_ID (DBF-REG-040) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · db-script-reg.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-REG-040 | loadResultId | LOAD_RESULT_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | loadResultId / PENDING ADR-REG-011 |
| DBF-REG-041 | loadRunAt | LOAD_RUN_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | loadRunAt / PENDING ADR-REG-011 |
| DBF-REG-042 | subjectKind | SUBJECT_KIND | VARCHAR2(20 CHAR) | NOT NULL | yes (no HTTP write) | — | subjectKind / PENDING ADR-REG-011 |
| DBF-REG-043 | subjectName | SUBJECT_NAME | VARCHAR2(200 CHAR) | NOT NULL | yes (no HTTP write) | — | subjectName / PENDING ADR-REG-011 |
| DBF-REG-044 | serviceCode | SERVICE_CODE | VARCHAR2(100 CHAR) | None | yes (no HTTP write) | — | serviceCode / PENDING ADR-REG-011 |
| DBF-REG-045 | versionNumber | VERSION_NUMBER | NUMBER(10,0) | None | yes (no HTTP write) | — | versionNumber / PENDING ADR-REG-011 |
| DBF-REG-046 | outcome | OUTCOME | VARCHAR2(20 CHAR) | NOT NULL | yes (no HTTP write) | — | outcome / PENDING ADR-REG-011 |
| DBF-REG-047 | reason | REASON | VARCHAR2(1000 CHAR) | None | yes (no HTTP write) | — | reason / PENDING ADR-REG-011 |
| DBF-REG-048 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-REG-011 |
| DBF-REG-049 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-REG-011 |

DTO MEMBERSHIP  create-request: none (no HTTP create — ADR-REG-007) · update-request: none · response: see API blocks (PK never exposed except loadResultId; serviceCode always present)
LOOKUP FIELDS  subjectKind → LOAD_SUBJECT; outcome → LOAD_OUTCOME (closed) — stores the code; no lookup endpoint (values are CHECK constraints or package data, ADR-REG-010)
DOMAIN RULES
- none specific to this entity
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-REG-005

<!-- SUB:DATA-DOM-TRANSACTIONAL:END -->
<!-- PHASE:DATA-DOM:END -->

<!-- PHASE:PORTS:START traces=REQ-REG-005,REQ-REG-049,REQ-REG-016 -->
## PHASE PORTS — PORTS+ADAPTERS

REG runs no query, fetches no document and calls no model: the QUERY, DOCUMENT and MODEL ports of the profile belong to the modules that run Checks. REG owns two inbound configuration ports, each behind an interface with a replaceable adapter (profile layers port / adapter):
- `PackageSource` (port) → `FileSystemPackageSource` (adapter): lists the folders of `aias.registry.package-directory`, returns per folder its name, the service knowledge file text, the service definition file text and the names of every other file in it (REQ-REG-005, REQ-REG-016, REQ-REG-062). Resolves each folder with `toRealPath()` and refuses one outside the directory.
- `ActivationSource` (port) → `PropertiesActivationSource` (adapter): returns the environment name and the connection entries of `aias.registry.connections[]` (REQ-REG-049, ADR-REG-009). Never resolves the credential: only its reference name is passed on.
- `ServiceDefinitionParser` (domain service, no I/O): parses the service definition YAML into the closed structure `service, version, input, queries, documents, approval`; any other element → RULE-REG-012.
<!-- PHASE:PORTS:END -->

<!-- PHASE:SVC-API:START traces=DBF-REG-002,DBF-REG-003,DBF-REG-007,DBF-REG-011,DBF-REG-016,DBF-REG-027,DBF-REG-040,DBF-REG-041,DBF-REG-042,DBF-REG-043,DBF-REG-044,DBF-REG-045,DBF-REG-046,DBF-REG-047,REQ-REG-005,REQ-REG-007,REQ-REG-008,REQ-REG-013,REQ-REG-014,REQ-REG-015,REQ-REG-024,REQ-REG-030,REQ-REG-048 -->
## PHASE SVC-API — SVC+API

### Service layer — the start-up load run (ADR-REG-007, ADR-REG-011)
`RegistryLoadRun` runs once per start, after the schema is ready and before the service accepts traffic, in one READ_WRITE transaction per item (a failing item never rolls back another). Steps, in order:
1. **Start the run** — loadRunAt = now; delete every REG_LOAD_RESULT row of earlier runs (REQ-REG-009).
2. **Activate connections** (REQ-REG-049 … REQ-REG-056, ADR-REG-009) — read `ActivationSource`; refuse a name listed twice (RULE-REG-013), a type outside mcp | jdbc (RULE-REG-014), an entry not declared read-only (RULE-REG-015); insert new REG_CONNECTION rows (ACTIVATED), update rows whose settings changed (UPDATED, REQ-REG-050), delete rows no longer listed (REMOVED, REQ-REG-051); store credentialReference only (REQ-REG-054). Each outcome → one REG_LOAD_RESULT row (subjectKind CONNECTION).
3. **Load packages** (REQ-REG-005 … REQ-REG-007, REQ-REG-016) — for every folder of `PackageSource`: validate in this order RULE-REG-020 (only two files), RULE-REG-001 (both files), RULE-REG-018 (knowledge not empty), RULE-REG-012 (closed structure), RULE-REG-008, RULE-REG-019, RULE-REG-006, RULE-REG-007, RULE-REG-005, RULE-REG-009, RULE-REG-010, RULE-REG-011; then RULE-REG-002 across folders (REQ-REG-004). A failing folder → REJECTED with the rule's load reason code and message (REQ-REG-007, REQ-REG-061); processing continues with the next folder.
4. **Register versions** (REQ-REG-006, REQ-REG-019 … REQ-REG-023, ADR-REG-003) — contentHash = SHA-256 of knowledge + definition; unknown service code → insert REG_SVC_PKG (available = 1, registeredAt = now) and version (REGISTERED); declared version > current → insert version (REGISTERED, becomes current); equal number + equal hash → UNCHANGED; equal number + different hash → RULE-REG-003; lower and not stored → RULE-REG-004. A version insert writes REG_SVC_PKG_VER with its REG_SVC_QUERY and REG_REQ_DOC rows in one transaction; the unique constraint UQ_REG_SVC_PKG_VER_PKG_VERSION is the concurrency guard (a second instance starting at the same time fails the insert and records UNCHANGED after re-reading). No version, query or required document is ever updated or deleted (REQ-REG-026).
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

### Service layer — the in-process interface `ServiceRegistry` (contract-reg.md)
Injected into the consumer modules (profile `module_interface: in_process`); every method is READ_ONLY and returns immutable value objects (Java records with unmodifiable lists — REQ-REG-060). The service knowledge is a separate field from the queries and document settings (REQ-REG-018, REQ-REG-030); no method returns request data (REQ-REG-059).
- `getCurrentServicePackage(serviceCode)` → ServicePackage {serviceCode, versionNumber, serviceKnowledge, inputName, queries[queryName, connectionName, sqlText], fetchMode, documentSource, requiredDocumentTypes}; refuses with `ServiceNotAvailableException` (RULE-REG-016) for an unknown or withdrawn code and `ServiceConnectionNotActivatedException` (RULE-REG-017) when a query names a connection absent from REG_CONNECTION (REQ-REG-012, REQ-REG-024, REQ-REG-027, REQ-REG-029, REQ-REG-053). Carries no approval API (REQ-REG-044).
  - Honours: CON-REG-007
- `getServicePackageVersion(serviceCode, versionNumber)` → full immutable version; `VersionNotFoundException` otherwise (REQ-REG-025).
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
Response     : 200 · array of ServiceSummary {serviceCode (DBF-REG-002), versionNumber (DBF-REG-007), fetchMode (DBF-REG-011), requiredDocumentTypes (DBF-REG-027), approvalEnabled (DBF-REG-016)} · not paginated · no envelope; never SQL text or connection settings (REQ-REG-030)
Validations  : none
Errors       : REG-500 (PLATFORM-STD)
Orchestration : load available packages (QR-REG-001, filter on DBF-REG-003) → per package its current version (QR-REG-002) and its document types (QR-REG-003) → map to ServiceSummary; writes nothing
Repository   : QR-REG-001, QR-REG-002, QR-REG-003 · FIND_BY_CRITERIA / FIND_ONE / FIND_ALL · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; REQ-REG-013, REQ-REG-014, REQ-REG-008 name no role check)
Localization : messages en per SRS; ar PENDING ADR-REG-011
Honours      : CON-REG-008
Covers       : REQ-REG-017 (read-only surface), REQ-REG-059 (no request data in any response), REQ-REG-060 (responses are copies; nothing a caller sends changes the registry)
<!-- API:API-REG-001:END -->

<!-- API:API-REG-002:START traces=REQ-REG-014,REQ-REG-015,DBF-REG-002,DBF-REG-003,DBF-REG-007,DBF-REG-011,DBF-REG-016,DBF-REG-027 -->
### API-REG-002 — Read one service
Entity       : ENT-REG-001
Endpoint     : /api/v1/services/{serviceCode}   verb: GET
Layers       : controller ServiceRegistryController → service ServiceRegistryQueryService
Request      : path serviceCode (DBF-REG-002, string ≤ 100, required); no body
Response     : 200 · ServiceSummary {serviceCode, versionNumber, fetchMode, requiredDocumentTypes, approvalEnabled} of the current version, available or withdrawn · no envelope
Validations  : RULE-REG-016 — Withdrawn or unknown service not supplied · trigger: on package request and on service read · statement: The system shall refuse to supply or return a service when its service code is not held by the registry or its Service Package is withdrawn (for a read, only an unknown code is refused). · message (en): The service "{serviceCode}" is not available. · message (ar): PENDING ADR-REG-011
Errors       : REG-404-SERVICE-NOT-FOUND (404, RULE-REG-016) · REG-500 (PLATFORM-STD)
Orchestration : load package by code (QR-REG-004) → none → REG-404-SERVICE-NOT-FOUND → current version (QR-REG-002) → document types (QR-REG-003) → ServiceSummary; writes nothing
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
Covers       : the outcomes recorded by the load run for REQ-REG-003, REQ-REG-004, REQ-REG-005, REQ-REG-016, REQ-REG-051, REQ-REG-058, REQ-REG-061, REQ-REG-062 (see the load run and the load reason codes above)
<!-- API:API-REG-003:END -->

<!-- PHASE:SVC-API:END -->

<!-- PHASE:ALIGN-BE:START traces=REQ-REG-001,ENT-REG-001 -->
## PHASE ALIGN-BE — ALIGN-BE

```
ALIGN — REG v1
row               backing check        assertion
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF
COVERED           orphans              every REQ is covered by ≥1 API or DBF
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares for it
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not
QRC (§5)          orphans              every catalogued query is reached by ≥1 API
API (R3)          code-format          every catalog code is an instance of the declared format and carries a status the platform can emit
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-reg.yaml and every operation one block, agreeing on method and path
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation of api-spec-reg.yaml with its status and code
DOCUMENT VALID    api-spec-valid       api-spec-reg.yaml validates against OPENAPI 3.1.0 and reaches every required item
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from, or is deferred
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered — examined nothing (0 XM)
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase, and nothing else names its target — examined nothing (0 XM)
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's own registry
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why it is not — examined nothing (0 SCR-REQ)
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/REG/
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

<!-- PHASE:CROSS-MOD:START traces=REQ-REG-001 -->
## PHASE CROSS-MOD — CROSS-MODULE

No edge: REG consumes no entity of another module (SRS A8 `consumes: []`, db-script `records: []`). Inbound edges are the consumers' and reach REG through contract-reg.md.
<!-- PHASE:CROSS-MOD:END -->

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
Filters      : SERVICE_CODE: EXACT
Result shape : projection SERVICE_PACKAGE_ID, SERVICE_CODE, AVAILABLE
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
| RULE-REG-017 | in-process | ServiceConnectionNotActivatedException (REG-LOAD-SERVICE-CONNECTION-NOT-ACTIVATED message) |
| RULE-REG-018 | load run (outcome via API-REG-003) | REG-LOAD-EMPTY-SERVICE-KNOWLEDGE |
| RULE-REG-019 | load run (outcome via API-REG-003) | REG-LOAD-DUPLICATE-QUERY-NAME |
| RULE-REG-020 | load run (outcome via API-REG-003) | REG-LOAD-FOREIGN-FILE-IN-PACKAGE |

Integration: none (0 XM). ADRs cited: ADR-REG-003, ADR-REG-006, ADR-REG-007, ADR-REG-009, ADR-REG-010, ADR-REG-011.
