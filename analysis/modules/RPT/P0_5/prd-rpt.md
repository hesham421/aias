# PRD — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module          : RPT     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : 12   Policies covered : 23/23   Deferred : 0
Status          : APPROVED — prd-approval 2026-10-01 (Hesham Ezzat, owner — standing instruction "do all with recommended"; ADR-RPT-016)
══════════════════════════════════════════════════════════════════

## USER STORIES

US-RPT-001
  Title          : Every Check I start is on record at once
  Story          : As an employee, I need every Check I start from the host screen to be on record straight away — for the service and request I asked about, under my identity as the host knows me, on the service package version it runs on — so that I can follow it by its identifier and later see exactly what it was built on.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-001, POL-RPT-002
  Source         : [KB:raw-idea.md §5] "the host starts it and then polls"; §9 `CHECK_RUN`; §8 "passes the employee's identity, which is recorded with the check"; profile `conventions.identifiers`; ADR-RPT-001
  Status         : DRAFT

US-RPT-002
  Title          : The status I poll tells the truth
  Story          : As an employee, I need the status of a Check I am following to move only forward — waiting for my documents, running, then completed or failed once — so that a status I have seen as ended never changes under me.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-003
  Source         : [KB:raw-idea.md §5] "the host starts it and then polls for the result"; CON-CHK-001; ADR-RPT-002
  Status         : DRAFT

US-RPT-003
  Title          : The whole report, or none of it
  Story          : As an employee, I need a completed report to be kept complete — the Overall Status together with every finding, every document read, missing or unreadable, every service query that could not be read and the metadata — so that I never see a result without the findings that justify it.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-004, POL-RPT-007
  Source         : [KB:raw-idea.md §7] report model; §12 "Anything that could not be read appears in the report. It is never skipped silently"; domain-profile §5 G6; ADR-RPT-001
  Status         : DRAFT

US-RPT-004
  Title          : Results I can read the same way every time
  Story          : As an employee, I need every stored Check to speak the service's fixed vocabulary — a completed Check with exactly one Overall Status, a failed Check with its reason and no result, and every finding and document in their fixed outcomes — so that every report reads the same way whatever the service.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-005, POL-RPT-006
  Source         : [KB:raw-idea.md §7] "The report has a fixed structure for every service"; profile `conventions.lookups`; CON-CHK-001 … CON-CHK-003; CON-DOC-001, CON-DOC-002
  Status         : DRAFT

US-RPT-005
  Title          : Evidence kept, not the request's files
  Story          : As a service administrator, I need the Report Store to keep the evidence, notes and outcomes of a report but never the content of the request's documents or the data its queries returned, so that the service holds no more of a citizen's request than the report needs.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-008
  Source         : CON-CHK-008 "Carries no document content"; ADR-DOC-008; domain-profile §5 G9, G10
  Status         : DRAFT

US-RPT-006
  Title          : The report I decided on stays as it was
  Story          : As an employee, I need a report that has ended to stay exactly as it was when I read it, so that my decision always stands beside the report it was based on.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-009
  Source         : [KB:raw-idea.md §9] decision beside the result; §11 "records the report the approval was based on"; ADR-RPT-002
  Status         : DRAFT

US-RPT-007
  Title          : No Check left hanging
  Story          : As an employee, I need the Check Engine to be able to find every Check that is still waiting or running, so that a Check interrupted by a restart or abandoned before its uploads were confirmed is ended and shown as failed instead of running for ever.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-010
  Source         : CON-CHK-011; ADR-CHK-004; ADR-CHK-005
  Status         : DRAFT

US-RPT-008
  Title          : See a Check and its report
  Story          : As an employee, I need to see, in the frontend embedded in my host screen, the status of a Check and, once it has ended, its report with each finding beside its evidence, so that I can verify every finding before I decide.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-011
  Source         : [KB:raw-idea.md §7] "Every finding carries its evidence so the employee can verify it"; §8 `GET /checks/{id}`; §15 A1; domain-profile §5 G10
  Status         : DRAFT

US-RPT-009
  Title          : See all the Checks of a request
  Story          : As an employee, I need to see all the Checks of the request on my screen, newest first, each standing on its own, so that I know whether the request was checked before and what each Check found.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-012, POL-RPT-023
  Source         : [KB:raw-idea.md §15 A1] "the checks of a request"; §12 "No data is carried from one check to another"; ADR-CHK-007; ADR-RPT-005
  Status         : DRAFT

US-RPT-010
  Title          : My decision kept beside the result
  Story          : As an employee, I need my approve / reject decision on a completed Check to be kept beside its result — once, under my identity, with the time, and noting whether the service carried it out through the Approval API — while the service itself never decides or approves anything, so that the record shows what I decided, on which report, and that the decision was mine.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-013, POL-RPT-014, POL-RPT-015, POL-RPT-016, POL-RPT-018
  Source         : [KB:raw-idea.md §1] "The employee stays the decision maker"; §9; §11 approval options; §12 "Approval is executed only as a result of the employee's action"; domain-profile §5 G2; ADR-RPT-003
  Status         : DRAFT

US-RPT-011
  Title          : Know where the service and the employees disagree
  Story          : As a service administrator, I need to see, for a service and each of its package versions, how the decided Checks of each Overall Status were approved or rejected, so that I know where the service knowledge or a check needs attention.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-017
  Source         : [KB:raw-idea.md §9] "gives a direct measure of accuracy: where the two disagree, the service knowledge or a check needs attention"; domain-profile §5 G11; ADR-RPT-005
  Status         : DRAFT

US-RPT-012
  Title          : Reports kept as long as the request, then removed
  Story          : As a service administrator, I need each report kept for as long as the host keeps the request it verified — a period I configure — and then removed completely, never while its Check is still unfinished and never when no period is set, so that reports are neither lost early nor kept for ever.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-019, POL-RPT-020, POL-RPT-021, POL-RPT-022
  Source         : domain-profile §8 D4; profile `delete_semantics: hard`; ADR-RPT-004
  Status         : DRAFT

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-RPT-001 | POL-RPT-001, POL-RPT-002 | [KB:raw-idea.md §5, §8, §9]; profile conventions.identifiers |
| US-RPT-002 | POL-RPT-003 | [KB:raw-idea.md §5]; CON-CHK-001; ADR-RPT-002 |
| US-RPT-003 | POL-RPT-004, POL-RPT-007 | [KB:raw-idea.md §7, §12]; G6 |
| US-RPT-004 | POL-RPT-005, POL-RPT-006 | [KB:raw-idea.md §7]; profile conventions.lookups |
| US-RPT-005 | POL-RPT-008 | CON-CHK-008; G9, G10 |
| US-RPT-006 | POL-RPT-009 | [KB:raw-idea.md §9, §11]; ADR-RPT-002 |
| US-RPT-007 | POL-RPT-010 | CON-CHK-011; ADR-CHK-005 |
| US-RPT-008 | POL-RPT-011 | [KB:raw-idea.md §7, §8, §15 A1] |
| US-RPT-009 | POL-RPT-012, POL-RPT-023 | [KB:raw-idea.md §12, §15 A1]; ADR-CHK-007 |
| US-RPT-010 | POL-RPT-013, POL-RPT-014, POL-RPT-015, POL-RPT-016, POL-RPT-018 | [KB:raw-idea.md §1, §9, §11, §12]; G2 |
| US-RPT-011 | POL-RPT-017 | [KB:raw-idea.md §9] |
| US-RPT-012 | POL-RPT-019, POL-RPT-020, POL-RPT-021, POL-RPT-022 | domain-profile D4 |

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Which roles the RPT stories speak for | The employee (follows Checks, reads reports, takes the decision) and the service administrator (retention, accuracy measure, data held); host systems, CHK and INT are callers of RPT, not story roles — the same choice as the CHK and DOC PRDs | yes — owner standing instruction "do all with recommended" (prd-approval) | domain-profile §7.1 (Employee, Service Administrator); CHK PRD decision 1 |
| 2 | Which stories carry a priority | HIGH for the stories of report integrity and visibility the employee relies on (US-RPT-001, US-RPT-002, US-RPT-003, US-RPT-006, US-RPT-008) and for the decision record (US-RPT-010, G2); every other story "—" | yes — owner standing instruction (prd-approval) | [KB:raw-idea.md §1, §7, §12] |
| 3 | Whether the accuracy measure is a story of v1 or left to ad-hoc queries | A story of v1 (US-RPT-011): the raw idea names it as the reason the decision is stored beside the result (ADR-RPT-005) | yes — owner standing instruction (prd-approval) | [KB:raw-idea.md §9] |
| 4 | Whether "who may view stored reports" becomes a story | No — deferred with caller authentication (D4, A2); kept as a scope exception of the business policies, not a DEFERRED story, as the owner already deferred it | yes — owner D4, A2 | domain-profile §8 D4, D7 |

## DEFERRED
| US | Reason | Activation trigger |
|---|---|---|
| None | No RPT story is deferred. Out-of-scope items (viewer restriction, changing a decision, per-service retention, archiving) stay in the RPT SCOPE EXCEPTIONS, not as stories | — |

## APPROVAL
Approved by : Hesham Ezzat (owner — standing instruction "do all with recommended")   Date : 2026-10-01   Record : _state/approvals/prd-approval.json; ADR-RPT-016
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════
