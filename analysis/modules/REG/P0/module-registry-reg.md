## MODULE REGISTRY — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module Code    : REG   (profile.vocabulary.module_prefixes)
Bounded context: configuration
Layer / Type   : L1 / master data (configuration)     Execution tier : 0.1
Source         : NEW
Knowledge      : profiles/aias/knowledge/raw-idea.md §2, §4, §6, §12, §13; domain-profile §4 row 1, §5, §7, §8
Readiness      : READY
══════════════════════════════════════════════════════════════════

Scope: what a service IS — its service packages (service knowledge + service definition), their versions, and the connections set per environment at activation. Read by every Check, written only by the service administrator (domain-profile §7.2).

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity | Kind (config / transactional) | PRIVATE / SHARED | Source |
|---|---|---|---|
| Service Package (service knowledge + service definition, versioned) | config | SHARED | project-registry §4 CAND-REG-001, §5; [KB:raw-idea.md §4]; G11 |
| Connection | config | SHARED | project-registry §4 CAND-REG-002, §5; [KB:raw-idea.md §4, §6] |

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| Service code | The code that identifies a service and its service package; the only source of service codes | `scholarship-request` | profile `conventions.lookups`; [KB:raw-idea.md §4]; D5 |
| Connection type | How a connection reaches host data | `mcp`, `jdbc` | [KB:raw-idea.md §4 `type: mcp`, §6 JDBC for `blob`]; ADR-REG-004 |
Rule (profile): Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| Fetch mode (`path` / `blob` / `manual`) — a profile closed enum; the service definition carries the value; no runtime read, so no REG → DOC edge (ADR-REG-005) | DOC |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| None — REG is the root of the platform graph | — | — | platform-summary DEPENDENCIES |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| None | — | — |
ROOT: YES

CONSUMERS (context — the edges belong to the consuming modules)
| Module code | What it reads from REG | Source |
|---|---|---|
| CHK | service package (service knowledge, queries, required documents, version, approval flag), connections | domain-profile §6 |
| DOC | fetch mode and required documents of the service definition; the `blob` connection | owner dependency input; [KB:raw-idea.md §6] |
| RPT | the service package version recorded on each report (received through CHK) | G11 |
| INT | whether a service enables the approval API, and its definition | [KB:raw-idea.md §11]; G2 |

POLICIES (business-policies-reg.md)
| Policy | Short name |
|---|---|
| POL-REG-001 | One service package per service |
| POL-REG-002 | Service knowledge and service definition kept apart |
| POL-REG-003 | A new service needs no code |
| POL-REG-004 | Only the service administrator supplies service packages |
| POL-REG-005 | Every service package carries a version |
| POL-REG-006 | Service knowledge is read whole |
| POL-REG-007 | Queries are exactly those in the service definition |
| POL-REG-008 | Fetch mode and required documents per service |
| POL-REG-009 | The approval API is optional per service |
| POL-REG-010 | Connections defined once, outside the service packages |
| POL-REG-011 | Connections set per environment at activation |
| POL-REG-012 | Host data only through read-only connections |
| POL-REG-013 | Service codes come only from the service registry |
| POL-REG-014 | Scholarship request is the pilot service package |
| POL-REG-015 | The service registry holds no request data |

AUTO-DECISIONS
AUTO: Service Knowledge and Service Definition are parts of the Service Package entity, not separate entities  FROM: project-registry §4 notes; domain-profile §7.1  IF WRONG: P1 registers them as two entities owned by REG
AUTO: The version is carried by the Service Package; whether a version is an entity of its own is left to P1  FROM: [KB:raw-idea.md §4]; G11  IF WRONG: P1 registers a Service Package Version entity owned by REG
AUTO: Service Package and Connection are SHARED (read through the REG interface by CHK, DOC, RPT, INT)  FROM: project-registry §5; domain-profile §6  IF WRONG: mark PRIVATE and route all reads through CHK
AUTO: A loaded service package version is never changed in place; a change is a new version; a new Check uses the current version; every loaded version stays resolvable so a stored report's version can be traced to its content  FROM: [KB:raw-idea.md §4]; G11; ADR-REG-003  IF WRONG: allow in-place edits and record only the version number
AUTO: The read-only JDBC data source used by `blob` fetching is a Connection of type `jdbc` held in REG  FROM: [KB:raw-idea.md §6]; G14; ADR-REG-004  IF WRONG: DOC holds its own JDBC data source outside REG
AUTO: Fetch mode values are the profile's closed enum; REG accepts only `path`, `blob`, `manual` in a service definition without reading DOC  FROM: profile `conventions.lookups`; ADR-REG-005  IF WRONG: REG soft-reads the list from DOC (would break REG's tier 0)
AUTO: The allowed storage root (G5) is DOC's environment setting and the check limits (G8) are platform configuration (`aias.check.timeout` PT2M, `aias.check.max-rows` 100, `aias.check.max-file-size` 10MB — ADR-REG-019); neither is REG data  FROM: [KB:raw-idea.md §12]; profile tracks.backend CORE; ADR-REG-006; ADR-REG-019  IF WRONG: add them to the Connection or the service definition
AUTO: No administration UI; the service administrator maintains service packages and connections directly  FROM: [KB:raw-idea.md §2]; profile review AIAS-2  IF WRONG: a later version adds the administration UI

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Owner of the Check run record, findings and Check Document record (OQ-1, OQ-2) — none of them is REG's | RPT owns them; CHK writes through RPT's interface; DOC reads but does not own (ADR-REG-001) | yes — owner statement 2026-10-01 | [KB:raw-idea.md §14] |
| 2 | REG's tier and dependencies | Tier 0, no dependencies, root of the graph | yes — owner statement 2026-10-01 | owner platform-dependency input |
| 3 | Version immutability and the version a new Check uses | Versions are never changed in place; new Checks use the current version; every loaded version stays resolvable (ADR-REG-003) | recommended — confirmed by owner at prd-approval 2026-10-01 | [KB:raw-idea.md §4]; G11 |
| 4 | Where the `blob` JDBC data source lives | A REG Connection of type `jdbc` (ADR-REG-004) | recommended — confirmed by owner at prd-approval 2026-10-01 | [KB:raw-idea.md §6]; G14 |
| 5 | Fetch mode values without a REG → DOC edge | Profile closed enum used directly (ADR-REG-005) | recommended — confirmed by owner at prd-approval 2026-10-01 | profile `conventions.lookups` |
| 6 | Storage root and check limits | Not REG data — DOC environment setting and platform configuration (ADR-REG-006) | recommended — confirmed by owner at prd-approval 2026-10-01 | [KB:raw-idea.md §12]; G5, G8 |
══════════════════════════════════════════════════════════════════

✓ Service Registry — P0 complete
  Next : P0.5 reads platform-summary.md · module-registry-reg.md · business-policies-reg.md
  Precondition for P0.5: every module in `depends_on` has a published contract or a passed gate — REG has none
  Another module? 1.1 DOC (next in `gov.py plan-order`)
