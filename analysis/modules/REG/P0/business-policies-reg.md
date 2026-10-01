## BUSINESS POLICIES — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module   : REG     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-REG-001 — One service package per service
  Statement : The system shall hold exactly one service package per service, made of the service knowledge and the service definition.
  Pattern   : ubiquitous
  Trigger   : Register service
  Rationale : Everything that differs between services lives in one package per service.
  Source    : [KB:raw-idea.md §4] "Everything that differs between services lives in one package per service"; §13 Decided "One package per service"
  Status    : CONFIRMED

POL-REG-002 — Service knowledge and service definition kept apart
  Statement : The system shall keep the service knowledge and the service definition as separate parts of a service package, so that queries and document locations are executed literally and only the conditions are interpreted by the LLM.
  Pattern   : ubiquitous
  Trigger   : Register service
  Rationale : SQL and file locations must never pass through the LLM's interpretation.
  Source    : [KB:raw-idea.md §4] "The two files are separate on purpose"
  Status    : CONFIRMED

POL-REG-003 — A new service needs no code
  Statement : Where a new service uses only existing check types, the system shall accept it as a new service package without any code change.
  Pattern   : optional
  Trigger   : Register service
  Rationale : The service is generic; every service it verifies is configuration, not code.
  Source    : [KB:raw-idea.md §1, §4] "Adding a service needs no code as long as it uses existing check types"
  Status    : CONFIRMED

POL-REG-004 — Only the service administrator supplies service packages
  Statement : The system shall take service knowledge and service definitions only from the service packages the service administrator maintains, never from an employee or a host system.
  Pattern   : ubiquitous
  Trigger   : Register service / Update service
  Rationale : Employees never supply service knowledge; configuration is the administrator's.
  Source    : [KB:raw-idea.md §2] "maintained by the service administrator. Employees never supply knowledge files"; §13 Decided
  Status    : CONFIRMED

POL-REG-005 — Every service package carries a version
  Statement : The system shall carry a version on every service package and give every Check the version of the package it uses, so that every report records the version it was built on.
  Pattern   : ubiquitous
  Trigger   : Update service / Start check
  Rationale : Traceability of every report to the configuration it was built on.
  Source    : [KB:raw-idea.md §4] "Each package carries a version, and every report records the version it was built on"; domain-profile §5 G11
  Status    : CONFIRMED

POL-REG-006 — Service knowledge is read whole
  Statement : The system shall give each Check the whole service knowledge of its service, with no retrieval of fragments.
  Pattern   : ubiquitous
  Trigger   : Start check
  Rationale : Whole knowledge in the prompt is more reliable for compliance than retrieved fragments.
  Source    : [KB:raw-idea.md §2] "each service's knowledge fits whole in the prompt"; domain-profile §7.1 Service Knowledge
  Status    : CONFIRMED

POL-REG-007 — Queries are exactly those in the service definition
  Statement : The system shall supply to a Check only the queries written in its service definition, each naming its connection, with parameters that are bound or strictly type-validated and never built from free text.
  Pattern   : ubiquitous
  Trigger   : Register service / Start check
  Rationale : Injection prevention and literal execution of the service definition.
  Source    : [KB:raw-idea.md §4, §6, §12]; domain-profile §5 G1, G4
  Status    : CONFIRMED

POL-REG-008 — Fetch mode and required documents per service
  Statement : The system shall record in each service definition one fetch mode — `path`, `blob` or `manual` — and the documents the service requires.
  Pattern   : ubiquitous
  Trigger   : Register service
  Rationale : All three fetch modes are available and chosen per service; the required documents drive the deterministic check.
  Source    : [KB:raw-idea.md §4, §6] "All three modes are available, chosen per service"; §13 Decided "Documents"
  Status    : CONFIRMED

POL-REG-009 — The approval API is optional per service
  Statement : Where a service definition enables the approval API, the system shall make that API's definition available to the Employee Decision path, and the employee otherwise approves in the host system.
  Pattern   : optional
  Trigger   : Register service / Record decision
  Rationale : The employee is the decision maker; the approval API is an optional second path.
  Source    : [KB:raw-idea.md §4 `approval.enabled`, §11, §13 Decided "Decision maker"]; domain-profile §5 G2
  Status    : CONFIRMED

POL-REG-010 — Connections defined once, outside the service packages
  Statement : The system shall define each connection once, outside the service packages, and let service definitions refer to it by name.
  Pattern   : ubiquitous
  Trigger   : Activate
  Rationale : A data source is configured in one place and reused by every service that needs it.
  Source    : [KB:raw-idea.md §4] "Connections are defined once, outside the service packages"
  Status    : CONFIRMED

POL-REG-011 — Connections set per environment at activation
  Statement : When the service is activated in an environment, the system shall take the connections of that environment from the activation configuration and leave the service packages unchanged.
  Pattern   : event
  Trigger   : Activate
  Rationale : The same service package runs in every environment; only the connections differ.
  Source    : [KB:raw-idea.md §4] "set at activation time for each environment"; §13 Decided "Database access … specified at activation"
  Status    : CONFIRMED

POL-REG-012 — Host data only through read-only connections
  Statement : The system shall reach host data only through connections that use a read-only database user, preferably limited to specific views.
  Pattern   : ubiquitous
  Trigger   : Activate / Start check
  Rationale : The service needs no write access to any host system.
  Source    : [KB:raw-idea.md §6, §12]; domain-profile §5 G3, G14; D2
  Status    : CONFIRMED

POL-REG-013 — Service codes come only from the service registry
  Statement : The system shall treat the service registry as the only source of service codes, with no service code hardcoded.
  Pattern   : ubiquitous
  Trigger   : Register service / Start check
  Rationale : Adding or retiring a service must stay a configuration act.
  Source    : profile `conventions.lookups` "service codes come only from the service registry and are never hardcoded"
  Status    : CONFIRMED

POL-REG-014 — Scholarship request is the pilot service package
  Statement : The system shall include the scholarship request as the pilot service package, with its explicit numeric conditions, the required documents TRANSCRIPT and ID_CARD, and the `path` fetch mode.
  Pattern   : ubiquitous
  Trigger   : Register service
  Rationale : The first real service proves the generic model on clear numeric conditions.
  Source    : domain-profile §8 D5 (owner-confirmed 2026-10-01); [KB:raw-idea.md §4]
  Status    : CONFIRMED

POL-REG-015 — The service registry holds no request data
  Statement : The system shall hold no request data in the service registry, so that nothing is carried from one Check to another.
  Pattern   : ubiquitous
  Trigger   : Start check
  Rationale : Carrying state between requests risks leaking one request's data into another.
  Source    : [KB:raw-idea.md §2, §12] "No data is carried from one check to another"; domain-profile §5 G9, §7.2 configuration context
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
| Lookup key | Added values | Source |
|---|---|---|
| Service code | `scholarship-request` | [KB:raw-idea.md §4]; D5 |
| Required document type (pilot service) | `TRANSCRIPT`, `ID_CARD` | [KB:raw-idea.md §4]; D5 |
| Connection type | `mcp` | [KB:raw-idea.md §4] `type: mcp` |
| Connection type | `jdbc` (read-only `blob` channel) | [KB:raw-idea.md §6] "Read the column directly over JDBC with a read-only user"; ADR-REG-004 |

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Administration UI | No screen for maintaining service packages or connections in this version; the service administrator maintains them directly | A later version that adds a full administration UI | [KB:raw-idea.md §2]; profile review AIAS-2 |
| Internal permission system | No internal roles or rights over service packages in this version | Security version (A2) | [KB:raw-idea.md §2, §15 A2]; D7 |
| Retrieval over service knowledge (RAG, vector store) | Service knowledge is read whole | One service's knowledge grows to hundreds of pages | [KB:raw-idea.md §2] |
| Multi-tenancy | One registry for one organisation (single-tenancy) | — (not planned) | [KB:raw-idea.md §2, §13] |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Is the pilot service a REG policy or only a test fixture | A client-specific policy (POL-REG-014): the owner chose it as the first service package to implement | yes — D5, 2026-10-01 | domain-profile §8 D5 |
| 2 | Does the read-only rule bind the `blob` channel as well as MCP | Yes — POL-REG-012 covers every connection to host data, including the read-only JDBC connection for `blob` (ADR-REG-004) | recommended — confirmed by owner at prd-approval 2026-10-01 | [KB:raw-idea.md §6, §12]; G3, G14 |
| 3 | Do the check limits (timeout, maximum rows, maximum file size) belong to the service definition | No — they are platform configuration applied to every Check (profile backend phase CORE "configuration, limits"); no REG policy is written for them (ADR-REG-006); names and defaults `aias.check.timeout` PT2M, `aias.check.max-rows` 100, `aias.check.max-file-size` 10MB (ADR-REG-019) | recommended — confirmed by owner at prd-approval 2026-10-01 | [KB:raw-idea.md §12]; G8; profile tracks.backend CORE |
══════════════════════════════════════════════════════════════════
