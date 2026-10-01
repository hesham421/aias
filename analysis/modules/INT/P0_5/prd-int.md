# PRD — Host Integration (INT)
══════════════════════════════════════════════════════════════════
Module          : INT     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : 12   Policies covered : 18/18   Deferred : 0
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

## USER STORIES

US-INT-001
  Title          : Start a Check from the host screen and keep working
  Story          : As an employee, I need to start a Check for the request on my host screen — naming the service, the request and myself as the host knows me — and get its identifier back straight away, so that I can carry on while the Check runs and come back to its result.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INT-001, POL-INT-002
  Source         : [KB:raw-idea.md §5] "The host system sends the service code, the request number and the employee's identity … the host starts it and then polls for the result"; §8 `POST /checks`; ADR-INT-002
  Status         : DRAFT

US-INT-002
  Title          : Know why nothing happened
  Story          : As a host system, I need every refused request to come back in one standard error form carrying the refusing module's own code and explanation, so that the employee is told in the same words why the Check, upload or decision did not go through.
  Priority       : —
  Success metric : —
  Traces         : POL-INT-003
  Source         : profile `stack.backend.api.error_envelope`; CON-CHK-004, CON-CHK-005, CON-DOC-003, CON-RPT-006; ADR-INT-003
  Status         : DRAFT

US-INT-003
  Title          : Upload the documents of a manual Check
  Story          : As an employee, I need to upload the documents of a Check whose service obtains them from me, one document at a time and only while the Check is waiting for them, so that the Check reads exactly the files I provided and no file I upload is lost unnoticed.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INT-004, POL-INT-005
  Source         : [KB:raw-idea.md §6] "`manual` — The employee uploads the files; the report is marked accordingly"; §8 `POST /checks/{id}/documents`; §12 "never skipped silently"; ADR-INT-005
  Status         : DRAFT

US-INT-004
  Title          : Say my uploads are complete
  Story          : As an employee, I need to tell the service, as a separate action, that I have uploaded every document of a manual Check, so that the Check continues only when I am ready and an upload never starts it by accident.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INT-006, POL-INT-018
  Source         : [KB:raw-idea.md §6]; CON-CHK-005; profile `conventions.screen_composition`; ADR-INT-005
  Status         : DRAFT

US-INT-005
  Title          : Record my decision beside the report
  Story          : As an employee, I need to record my approve or reject decision on a completed Check, under my identity as the host knows me and as an action of its own, so that my decision stands beside the report it was based on.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INT-007, POL-INT-002, POL-INT-018
  Source         : [KB:raw-idea.md §8] `POST /checks/{id}/decision`; §9 "Storing the employee's decision beside the report result gives a direct measure of accuracy"; §11 option 1; CON-RPT-006; ADR-INT-004
  Status         : DRAFT

US-INT-006
  Title          : Approval executed for me where the host allows it
  Story          : As an employee, I need my confirmed approval to be executed through the host's Approval API when the service enables it — and never otherwise, never for a rejection and never without my action — so that I do not approve twice while the decision always stays mine.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INT-008, POL-INT-009, POL-INT-011
  Source         : [KB:raw-idea.md §11] "Optional: where the host exposes an approval API, the service calls it after the employee confirms"; §12 "Approval is executed only as a result of the employee's action"; domain-profile §5 G2; ADR-INT-004, ADR-INT-009
  Status         : DRAFT

US-INT-007
  Title          : A failed approval leaves nothing half-done
  Story          : As an employee, I need to be told when the host's Approval API failed or did not answer, with nothing recorded, so that I can try again knowing the request was not approved.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INT-010
  Source         : ADR-RPT-003 "a failed approval call records nothing so the employee can retry"; profile `http_statuses` 502 / 504; ADR-INT-004
  Status         : DRAFT

US-INT-008
  Title          : The Checks of the request I am working on
  Story          : As an employee, I need the frontend opened from my host screen to show the Checks of the request I am working on, newest first, so that I can open the latest report or an earlier one.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INT-012
  Source         : [KB:raw-idea.md §15 A1] "gives the employee: the checks of a request"; ADR-INT-006; CON-RPT-004
  Status         : DRAFT

US-INT-009
  Title          : Verify each finding against its evidence
  Story          : As an employee, I need to see a report's Overall Status, every finding with its evidence beside it and every document that was read, missing or unreadable — and never see a report presented as compliant when a required document is missing or unreadable — so that I decide on verified facts.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-INT-013, POL-INT-014
  Source         : [KB:raw-idea.md §7] "Every finding carries its evidence so the employee can verify it. A required document that is missing or unreadable prevents a `COMPLIANT` status"; §15 A1; domain-profile §5 G6, G10; review AIAS-11
  Status         : DRAFT

US-INT-010
  Title          : Follow a running Check without reloading
  Story          : As an employee, I need the status of a Check that is waiting for documents or running to stay current on my screen until it ends, so that I see the report as soon as it is ready without reloading.
  Priority       : MEDIUM
  Success metric : —
  Traces         : POL-INT-015
  Source         : [KB:raw-idea.md §5] "the host starts it and then polls for the result"; profile `stack.frontend.libraries.server-state`; ADR-INT-006
  Status         : DRAFT

US-INT-011
  Title          : The same API for my own display
  Story          : As a host system, I need the employee frontend to use only the REST API offered to me, so that I can later show the same Checks and reports with my own components without changing the service.
  Priority       : —
  Success metric : —
  Traces         : POL-INT-016
  Source         : [KB:raw-idea.md §11] "The same report is available as JSON, so it can later be rendered with ADF components … without changing the service"; §15 A1 "It consumes the same REST API as any host"; ADR-INT-001
  Status         : DRAFT

US-INT-012
  Title          : No second copy of a request
  Story          : As a service administrator, I need Host Integration to keep no request data, documents, reports or decisions of its own, so that each fact lives in one place and nothing passes from one Check to another.
  Priority       : —
  Success metric : —
  Traces         : POL-INT-017
  Source         : [KB:raw-idea.md §12] "No data is carried from one check to another"; domain-profile §5 G9; ADR-INT-007
  Status         : DRAFT

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-INT-001 | POL-INT-001, POL-INT-002 | [KB:raw-idea.md §5, §8] |
| US-INT-002 | POL-INT-003 | profile error envelope; ADR-INT-003 |
| US-INT-003 | POL-INT-004, POL-INT-005 | [KB:raw-idea.md §6, §8, §12] |
| US-INT-004 | POL-INT-006, POL-INT-018 | [KB:raw-idea.md §6]; CON-CHK-005 |
| US-INT-005 | POL-INT-007, POL-INT-002, POL-INT-018 | [KB:raw-idea.md §8, §9, §11] |
| US-INT-006 | POL-INT-008, POL-INT-009, POL-INT-011 | [KB:raw-idea.md §11, §12] |
| US-INT-007 | POL-INT-010 | ADR-RPT-003; profile 502 / 504 |
| US-INT-008 | POL-INT-012 | [KB:raw-idea.md §15 A1] |
| US-INT-009 | POL-INT-013, POL-INT-014 | [KB:raw-idea.md §7, §15 A1] |
| US-INT-010 | POL-INT-015 | [KB:raw-idea.md §5] |
| US-INT-011 | POL-INT-016 | [KB:raw-idea.md §11, §15 A1] |
| US-INT-012 | POL-INT-017 | [KB:raw-idea.md §12] |

Every policy POL-INT-001 … POL-INT-018 appears in at least one row.

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Who the stories speak for | The employee (all frontend and decision needs), the host system (the API and its errors) and the service administrator (no second copy of data); no story for a caller check (A2) | recommended — confirmed at prd-approval | domain-profile §7.1; [KB:raw-idea.md §15 A2] |
| 2 | Is the server-rendered report page a story | No — superseded by the employee frontend (A1); not planned (ADR-INT-001) | yes — owner A1 | [KB:raw-idea.md §15 A1]; domain-profile D6 |
| 3 | Are the reads the frontend needs INT stories | No — the reads are RPT's, REG's and DOC's and already served; INT's stories cover what the employee does and sees through them (ADR-INT-001, ADR-INT-006) | recommended — confirmed at prd-approval | ADR-RPT-005 |
| 4 | Priorities | HIGH for starting, uploading, confirming, deciding, approving, reading the report and the Checks of a request — the A1 jobs and the §11/§12 approval path; MEDIUM for following a running Check; the rest unstated | recommended — confirmed at prd-approval | [KB:raw-idea.md §5, §11, §12, §15 A1] |

## DEFERRED
| US | Reason | Activation trigger |
|---|---|---|
| — | No story is deferred. Caller authentication is scope, not a story, and is deferred by the owner (A2). | Security version |

## APPROVAL
Approved by : —   Date : —
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════
