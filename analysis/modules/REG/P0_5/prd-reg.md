# PRD — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module          : REG     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : 13   Policies covered : 15/15   Deferred : 0
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

## USER STORIES

US-REG-001
  Title          : One service package per service
  Story          : As a service administrator, I need to describe each service as one service package made of its service knowledge and its service definition, kept as separate parts, so that the conditions are interpreted by the LLM while queries and document locations are executed exactly as I wrote them.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-001, POL-REG-002
  Source         : [KB:raw-idea.md §4] "one package per service … The two files are separate on purpose"; business-policies-reg POL-REG-001, POL-REG-002
  Status         : DRAFT

US-REG-002
  Title          : Add a service without code
  Story          : As a service administrator, I need to add a new service by adding its service package, with its service code coming from the service registry alone, so that a new service that uses existing check types goes live without a code change or a release.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-003, POL-REG-013
  Source         : [KB:raw-idea.md §1, §4] "Adding a service needs no code as long as it uses existing check types"; profile `conventions.lookups`
  Status         : DRAFT

US-REG-003
  Title          : Service knowledge comes only from the service administrator
  Story          : As an employee, I need the conditions a Check applies to come only from the service package the service administrator maintains, so that I never have to supply or vouch for service knowledge myself.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-004
  Source         : [KB:raw-idea.md §2] "Employees never supply knowledge files"; business-policies-reg POL-REG-004
  Status         : DRAFT

US-REG-004
  Title          : Every service package change is a new version
  Story          : As a service administrator, I need every service package to carry a version that each Check receives, so that every report can be traced to the exact service knowledge and service definition it was built on.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-005
  Source         : [KB:raw-idea.md §4] "Each package carries a version, and every report records the version it was built on"; domain-profile G11; ADR-REG-003
  Status         : DRAFT

US-REG-005
  Title          : Service knowledge read whole
  Story          : As a service administrator, I need each Check to receive the whole service knowledge of its service, so that no condition is missed because only part of the knowledge was retrieved.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-006
  Source         : [KB:raw-idea.md §2] "each service's knowledge fits whole in the prompt, which is more reliable for compliance than retrieving fragments"
  Status         : DRAFT

US-REG-006
  Title          : Queries exactly as written in the service definition
  Story          : As a service administrator, I need a Check to use only the queries I wrote in the service definition, each against its named connection and with its parameters supplied safely rather than pasted into the SQL, so that no query is ever invented, altered or built from free text.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-REG-007
  Source         : [KB:raw-idea.md §4, §6, §12]; [KB:raw-idea.md §0] "Section 12 Guardrails are non-negotiable"; domain-profile G1, G4
  Status         : DRAFT

US-REG-007
  Title          : Fetch mode and required documents per service
  Story          : As a service administrator, I need to state for each service how its documents are obtained — `path`, `blob` or `manual` — and which documents it requires, so that every Check fetches the right documents and knows which ones must be present.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-008
  Source         : [KB:raw-idea.md §4, §6] "All three modes are available, chosen per service"; §13 Decided "Documents"
  Status         : DRAFT

US-REG-008
  Title          : Approval API enabled per service
  Story          : As a service administrator, I need to state for each service whether the host's approval API may be called after the employee confirms a decision, so that services without that API keep approval in the host system as today.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-009
  Source         : [KB:raw-idea.md §4 `approval.enabled`, §11] "Two options, chosen per service"; domain-profile G2
  Status         : DRAFT

US-REG-009
  Title          : Connections defined once and named
  Story          : As a service administrator, I need to define each connection once, outside the service packages, and refer to it by name from any service definition, so that a data source is configured in one place and shared by every service that uses it.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-010
  Source         : [KB:raw-idea.md §4] "Connections are defined once, outside the service packages"
  Status         : DRAFT

US-REG-010
  Title          : Connections set per environment at activation
  Story          : As a service administrator, I need to set the connections of each environment when the service is activated there, so that the same service packages run unchanged in every environment.
  Priority       : —
  Success metric : —
  Traces         : POL-REG-011
  Source         : [KB:raw-idea.md §4] "set at activation time for each environment"; §13 Decided "Database access … specified at activation"
  Status         : DRAFT

US-REG-011
  Title          : Host data reached read-only
  Story          : As a service administrator, I need every connection to host data — through MCP or, for `blob` documents, through JDBC — to use a read-only database user, preferably limited to specific views, so that the service can never change a host system's data.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-REG-012
  Source         : [KB:raw-idea.md §6, §12]; [KB:raw-idea.md §0] guardrails non-negotiable; domain-profile G3, G14; ADR-REG-004
  Status         : DRAFT

US-REG-012
  Title          : Scholarship request as the pilot service
  Story          : As an employee who approves scholarship requests, I need the scholarship request — with its explicit numeric conditions, its required TRANSCRIPT and ID_CARD documents and the `path` fetch mode — to be the first service package available, so that the first verified requests are the ones I handle.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-REG-014
  Source         : domain-profile §8 D5 "Scholarship request — the raw idea's own example" (owner-confirmed 2026-10-01); [KB:raw-idea.md §4]
  Status         : DRAFT

US-REG-013
  Title          : No request data kept in the service registry
  Story          : As an employee, I need the service registry to hold only service configuration and no request data, so that nothing from one request can reach the Check of another.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-REG-015
  Source         : [KB:raw-idea.md §2, §12] "No data is carried from one check to another"; domain-profile G9
  Status         : DRAFT

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-REG-001 | POL-REG-001, POL-REG-002 | [KB:raw-idea.md §4] |
| US-REG-002 | POL-REG-003, POL-REG-013 | [KB:raw-idea.md §1, §4]; profile `conventions.lookups` |
| US-REG-003 | POL-REG-004 | [KB:raw-idea.md §2] |
| US-REG-004 | POL-REG-005 | [KB:raw-idea.md §4]; G11 |
| US-REG-005 | POL-REG-006 | [KB:raw-idea.md §2] |
| US-REG-006 | POL-REG-007 | [KB:raw-idea.md §4, §6, §12]; G1, G4 |
| US-REG-007 | POL-REG-008 | [KB:raw-idea.md §4, §6] |
| US-REG-008 | POL-REG-009 | [KB:raw-idea.md §4, §11]; G2 |
| US-REG-009 | POL-REG-010 | [KB:raw-idea.md §4] |
| US-REG-010 | POL-REG-011 | [KB:raw-idea.md §4, §13] |
| US-REG-011 | POL-REG-012 | [KB:raw-idea.md §6, §12]; G3, G14 |
| US-REG-012 | POL-REG-014 | domain-profile D5 |
| US-REG-013 | POL-REG-015 | [KB:raw-idea.md §2, §12]; G9 |

Every policy of the module (POL-REG-001 … POL-REG-015) appears in at least one row.

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Which roles the REG stories speak for | The service administrator (maintains service packages and connections) and the employee (relies on them); host systems and the Check Engine are consumers of REG, not story roles | recommended — pending owner confirmation at prd-approval | domain-profile §7.1 (Service Administrator, Employee) |
| 2 | Which stories carry a priority | HIGH only where the source makes it clear: the §12 guardrail stories (US-REG-006, US-REG-011, US-REG-013 — raw idea §0 "non-negotiable") and the pilot (US-REG-012, D5); every other story "—" | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §0, §12]; D5 |
| 3 | How the service administrator maintains service packages without an administration UI | Stories state the need only; no screen is implied — the full administration UI is out of scope for this version | yes — owner scope statement | [KB:raw-idea.md §2]; profile review AIAS-2 |
| 4 | Whether a story covers rejecting an incomplete or malformed service package | Not written as a story: no REG policy states it; P1 derives the acceptance of a service package from POL-REG-001, POL-REG-007 and POL-REG-008 | recommended — pending owner confirmation at prd-approval | business-policies-reg; engine §3 "DO NOT EXTRACT" |
| 5 | Version handling behind US-REG-004 | Versions never changed in place; new Checks use the current version; every loaded version stays resolvable | recommended — pending owner confirmation at prd-approval (ADR-REG-003) | [KB:raw-idea.md §4]; G11 |

## DEFERRED
| US | Reason | Activation trigger |
|---|---|---|
| None | No REG story is deferred. Out-of-scope items (administration UI, permission system, caller authentication) stay in the platform summary DEFERRED table and the REG SCOPE EXCEPTIONS, not as stories | — |

## APPROVAL
Approved by : —   Date : —
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════
