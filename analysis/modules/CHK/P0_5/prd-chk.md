# PRD — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module          : CHK     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : 18   Policies covered : 29/29   Deferred : 0
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

## USER STORIES

US-CHK-001
  Title          : Start a Check without waiting
  Story          : As an employee, I need a Check of the request I am about to approve to start from my host screen and give me a Check I can follow at once, while the verification runs in the background, so that my screen is never blocked while the service gathers and compares the request's data and documents.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-001
  Source         : [KB:raw-idea.md §5] "A check takes time, so it runs asynchronously: the host starts it and then polls for the result"; §8 `POST /checks`; ADR-CHK-008
  Status         : DRAFT

US-CHK-002
  Title          : Checks only against registered services, on a recorded version
  Story          : As a service administrator, I need every Check to run only for a service that is available in the service registry, on the service package version that was current when it started, and to record that version, so that each report can be traced back to exactly the service knowledge and service definition it was built on.
  Priority       : —
  Success metric : —
  Traces         : POL-CHK-002, POL-CHK-003
  Source         : [KB:raw-idea.md §4] "every report records the version it was built on"; profile `conventions.lookups`; domain-profile G11; ADR-REG-003; ADR-CHK-007
  Status         : DRAFT

US-CHK-003
  Title          : One fixed pipeline for every service
  Story          : As a service administrator, I need every Check, whatever its service, to go through the same fixed steps — package, queries, documents, deterministic checks, LLM comparison, report — so that adding a service is a matter of configuration and every report is produced the same way.
  Priority       : —
  Success metric : —
  Traces         : POL-CHK-004
  Source         : [KB:raw-idea.md §3, §5] "fixed pipeline (the only fixed logic)"; profile `workflow_engine: forbidden`
  Status         : DRAFT

US-CHK-004
  Title          : Host data read exactly as configured, and only read
  Story          : As a service administrator, I need a Check to read host data only through the queries I wrote in the service definition, with the request number bound into them, over read-only connections, and to leave the documents' own query to Document Access, so that a Check can never change host data or run anything I did not write.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-005, POL-CHK-006
  Source         : [KB:raw-idea.md §6, §12] "Query parameters are bound or strictly type-validated; SQL is never built from free text"; "read-only database user"; domain-profile G3, G4, G14; ADR-DOC-001
  Status         : DRAFT

US-CHK-005
  Title          : Required documents taken into account
  Story          : As an employee, I need a Check to use the documents and read status that Document Access provides and never to call a request compliant while a required document is missing or could not be read, so that I never approve on the belief that a required document was verified when it was not.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-007, POL-CHK-008
  Source         : [KB:raw-idea.md §7] "A required document that is missing or unreadable prevents a `COMPLIANT` status"; domain-profile G6; ADR-DOC-001, ADR-DOC-002
  Status         : DRAFT

US-CHK-006
  Title          : Numbers and dates checked exactly
  Story          : As an employee, I need conditions on explicit values and dates — such as a minimum grade or a deadline — to be decided by exact computation from the value actually found in the request, and any finding whose evidence is not really in the request to be flagged for my review, so that I can trust the findings on clear-cut conditions without re-checking them by hand.
  Priority       : —
  Success metric : —
  Traces         : POL-CHK-009, POL-CHK-014
  Source         : [KB:raw-idea.md §5 step 5] "It runs the deterministic checks in code: required documents present, explicit values and dates"; [KB:raw-idea.md §7] "the evidence (actual value found)"; domain-profile §8 D5 (pilot with explicit numeric conditions); ADR-CHK-003
  Status         : DRAFT

US-CHK-007
  Title          : The model analyses and never acts
  Story          : As a service administrator, I need the LLM to be used only to compare a request with its service knowledge — with no tool, no query of its own and no way to approve — and the Check Engine itself never to approve or record a decision, so that the employee stays the only decision maker and host data is touched only by the queries I configured.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-010, POL-CHK-029
  Source         : [KB:raw-idea.md §1, §6, §12] "The LLM analyses and summarises. It does not write SQL and does not trigger approval"; "Approval is executed only as a result of the employee's action"; domain-profile G1, G2
  Status         : DRAFT

US-CHK-008
  Title          : Request content never steers the assessment
  Story          : As an employee, I need anything written in a request's data or documents to be treated only as content to verify against the service knowledge, never as an instruction, so that an applicant cannot change how their own request is assessed.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-011
  Source         : [KB:raw-idea.md §12] "Document content is treated as data, never as instructions to the model"; domain-profile G7
  Status         : DRAFT

US-CHK-009
  Title          : A finding for every condition, with its evidence
  Story          : As an employee, I need the report to give one finding for each condition of the service — whether it is met, the actual value found and a note for me — in the same structure for every service, so that I see exactly where the problems are and can verify each one.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-012, POL-CHK-013
  Source         : [KB:raw-idea.md §7] "One per condition: satisfied or not, the evidence (actual value found), and a note for the employee"; "fixed structure for every service, produced as structured output"; domain-profile G10
  Status         : DRAFT

US-CHK-010
  Title          : An overall status that never claims more than was verified
  Story          : As an employee, I need the report's overall status to follow plainly from its findings — not compliant when a condition fails, needing my review when something could not be decided or read, compliant only when every condition is met — so that the overall status at the top of the report matches what the findings below it show.
  Priority       : —
  Success metric : —
  Traces         : POL-CHK-015
  Source         : [KB:raw-idea.md §7] overall status `COMPLIANT`, `NOT_COMPLIANT`, `NEEDS_MANUAL_REVIEW`; domain-profile G6; ADR-CHK-002
  Status         : DRAFT

US-CHK-011
  Title          : Every report traceable and kept in the Report Store
  Story          : As a service administrator, I need every Check's result to be handed to the Report Store with the service version, fetch mode, model, times and employee it belongs to, so that reports can be traced, shown to the employee and compared with the employee's decision to measure accuracy.
  Priority       : —
  Success metric : —
  Traces         : POL-CHK-016, POL-CHK-017
  Source         : [KB:raw-idea.md §7] "Metadata: Service version, document source mode, model used, time, employee"; [KB:raw-idea.md §9] "Storing the employee's decision beside the report result gives a direct measure of accuracy"; ADR-REG-001, ADR-REG-002
  Status         : DRAFT

US-CHK-012
  Title          : Unread host data never passes silently
  Story          : As an employee, I need to be told in the report when part of the request's data could not be read or was too large to read in full, and never to see such a request called compliant, so that I do not approve on incomplete data.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-018
  Source         : [KB:raw-idea.md §12] "Anything that could not be read appears in the report. It is never skipped silently"; domain-profile G6, G8; ADR-CHK-005
  Status         : DRAFT

US-CHK-013
  Title          : Every Check comes to a clear end
  Story          : As an employee, I need a Check that runs too long, cannot reach the model, gets an unusable answer or is interrupted by a restart to end visibly as failed with the reason, rather than hang or show an overall status it never reached, so that I know to start a new Check or verify the request myself.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-019, POL-CHK-020, POL-CHK-022
  Source         : [KB:raw-idea.md §12] "Each check has limits: timeout, maximum rows, maximum file size"; "never skipped silently"; domain-profile G6, G8; ADR-REG-006; ADR-CHK-005
  Status         : DRAFT

US-CHK-014
  Title          : Upload before a manual Check runs
  Story          : As an employee, I need a Check of a service that has no direct access to its documents to wait until I confirm I have uploaded them, and to end if I leave it unconfirmed too long, so that the Check verifies the files I provided and does not stay open for ever.
  Priority       : —
  Success metric : —
  Traces         : POL-CHK-023, POL-CHK-024
  Source         : [KB:raw-idea.md §6] "`manual` — The employee uploads the files"; [KB:raw-idea.md §15 A1] "manual document upload"; ADR-DOC-003; ADR-CHK-004
  Status         : DRAFT

US-CHK-015
  Title          : Uploads cleared when their Check ends
  Story          : As an employee, I need the documents I uploaded for a Check to be cleared as soon as that Check ends, however it ends, so that no applicant's files linger in the service after their verification.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-021
  Source         : ADR-DOC-008 "The CHK analysis must send the end-of-Check notice on every ending path"; [KB:raw-idea.md §12] "No data is carried from one check to another"; domain-profile G9
  Status         : DRAFT

US-CHK-016
  Title          : Checks that never share anything
  Story          : As an employee, I need each Check — even a second Check of the same request — to start from nothing and keep nothing for later, so that no applicant's data or earlier result influences another assessment.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-CHK-025
  Source         : [KB:raw-idea.md §2, §12] "No data is carried from one check to another"; [KB:raw-idea.md §15 A1] "the checks of a request"; domain-profile G9; ADR-CHK-007
  Status         : DRAFT

US-CHK-017
  Title          : Replaceable comparison model, proven on known requests
  Story          : As a service administrator, I need to change the comparison model's provider by configuration alone and to have the fixed set of test requests with known expected results run on every model change, so that a new model is used for real requests only after it reaches the known overall statuses.
  Priority       : —
  Success metric : the fixed known-result request set reaches its expected overall statuses on the new model
  Traces         : POL-CHK-026, POL-CHK-028
  Source         : [KB:raw-idea.md §10] "The engine depends on Spring AI's `ChatModel` only"; "A fixed set of test requests with known expected results is run on every model change"; domain-profile G12, D3
  Status         : DRAFT

US-CHK-018
  Title          : Only test data on a free-tier comparison model
  Story          : As a service administrator, I need the comparison model to receive only synthetic or anonymised requests and documents while a free-tier provider is configured, so that no real applicant's data can end up in a provider's training data.
  Priority       : —
  Success metric : —
  Traces         : POL-CHK-027
  Source         : [KB:raw-idea.md §10] "Only synthetic or anonymised requests and documents are sent while a free provider is in use"; domain-profile G13, D3; ADR-CHK-006
  Status         : DRAFT

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-CHK-001 | POL-CHK-001 | [KB:raw-idea.md §5, §8] |
| US-CHK-002 | POL-CHK-002, POL-CHK-003 | [KB:raw-idea.md §4]; G11; ADR-REG-003 |
| US-CHK-003 | POL-CHK-004 | [KB:raw-idea.md §3, §5] |
| US-CHK-004 | POL-CHK-005, POL-CHK-006 | [KB:raw-idea.md §6, §12]; G3, G4, G14 |
| US-CHK-005 | POL-CHK-007, POL-CHK-008 | [KB:raw-idea.md §7]; G6 |
| US-CHK-006 | POL-CHK-009, POL-CHK-014 | [KB:raw-idea.md §5, §7]; ADR-CHK-003 |
| US-CHK-007 | POL-CHK-010, POL-CHK-029 | [KB:raw-idea.md §1, §6, §12]; G1, G2 |
| US-CHK-008 | POL-CHK-011 | [KB:raw-idea.md §12]; G7 |
| US-CHK-009 | POL-CHK-012, POL-CHK-013 | [KB:raw-idea.md §7]; G10 |
| US-CHK-010 | POL-CHK-015 | [KB:raw-idea.md §7]; ADR-CHK-002 |
| US-CHK-011 | POL-CHK-016, POL-CHK-017 | [KB:raw-idea.md §7, §9]; ADR-REG-001 |
| US-CHK-012 | POL-CHK-018 | [KB:raw-idea.md §12]; G6, G8 |
| US-CHK-013 | POL-CHK-019, POL-CHK-020, POL-CHK-022 | [KB:raw-idea.md §12]; G8; ADR-CHK-005 |
| US-CHK-014 | POL-CHK-023, POL-CHK-024 | [KB:raw-idea.md §6, §15 A1]; ADR-CHK-004 |
| US-CHK-015 | POL-CHK-021 | ADR-DOC-008; G9 |
| US-CHK-016 | POL-CHK-025 | [KB:raw-idea.md §2, §12, §15 A1]; G9 |
| US-CHK-017 | POL-CHK-026, POL-CHK-028 | [KB:raw-idea.md §10]; G12 |
| US-CHK-018 | POL-CHK-027 | [KB:raw-idea.md §10]; G13 |

Every policy of the module (POL-CHK-001 … POL-CHK-029) appears in at least one row.

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Which roles the CHK stories speak for | The employee (starts and relies on Checks, uploads in `manual` mode) and the service administrator (configures services, connections and models); host systems, RPT, DOC and INT are consumers or providers of CHK, not story roles — the same choice as the DOC PRD | recommended — pending owner confirmation at prd-approval | domain-profile §7.1 (Employee, Service Administrator); DOC PRD decision 1 |
| 2 | Which stories carry a priority | HIGH for the §12 guardrail stories (US-CHK-004, US-CHK-005, US-CHK-007, US-CHK-008, US-CHK-012, US-CHK-013, US-CHK-015, US-CHK-016 — raw idea §0 "non-negotiable"), the report-content story (US-CHK-009, G10) and the asynchronous start (US-CHK-001, raw idea §5); every other story "—" | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §0, §5, §7, §12] |
| 3 | Whether polling a Check's status and reading its report are CHK stories | No — CHK writes the status and report through its result port; the host and the frontend read them from RPT through INT (ADR-REG-001, ADR-REG-002); US-CHK-001 covers the employee's need that a Check starts and can be followed | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §8]; domain-profile §6 |
| 4 | Whether the known-result request set on model change is a CHK story although DOC treated it as a test-stage practice | Yes — CHK owns the comparison model whose change triggers it (POL-CHK-028, US-CHK-017); its test cases belong to the MODEL-EVAL test phase (profile AIAS-10) | yes — owner input [KB:raw-idea.md §10] | [KB:raw-idea.md §10]; G12 |
| 5 | Whether the upload story belongs to CHK although DOC has one | Yes, at a different need — DOC's US-DOC-005 is about the Check using exactly the uploaded files; US-CHK-014 is about the Check waiting for them and ending when abandoned (ADR-CHK-004) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §6, §15 A1]; ADR-DOC-003 |

## DEFERRED
| US | Reason | Activation trigger |
|---|---|---|
| None | No CHK story is deferred. Out-of-scope items (caller authentication on starting a Check, declared check types, per-service limits, resuming interrupted Checks) stay in the CHK SCOPE EXCEPTIONS, not as stories | — |

## APPROVAL
Approved by : —   Date : —
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════
