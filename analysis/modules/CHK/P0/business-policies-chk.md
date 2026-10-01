## BUSINESS POLICIES — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module   : CHK     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

Status legend: CONFIRMED = the owner's text or an owner-confirmed ADR states the need; RECOMMENDED = the dialogue's recommended answer, confirmed by the owner at prd-approval (ADR-CHK-001 … ADR-CHK-008).

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-CHK-001 — A Check starts at once and runs in the background
  Statement : When a host system starts a Check with a service code, a request number and the employee's identity, the system shall accept the Check, record the employee's identity with it and return the Check's identifier without waiting for the pipeline to finish.
  Pattern   : event
  Trigger   : Start Check
  Rationale : A check takes time; the host starts it and then polls for the result.
  Source    : [KB:raw-idea.md §5] step 1 and "A check takes time, so it runs asynchronously: the host starts it and then polls for the result"; §8 `POST /checks`; ADR-CHK-008
  Status    : CONFIRMED

POL-CHK-002 — Only available services are checked
  Statement : If the service code of a Check to be started is not an available service of the service registry, then the system shall refuse to start the Check.
  Pattern   : unwanted
  Trigger   : Start Check
  Rationale : Service codes come only from the service registry; a Check against an unknown or withdrawn service would produce a meaningless report.
  Source    : profile `conventions.lookups` "service codes come only from the service registry"; REG CON-REG-013; ADR-CHK-008
  Status    : CONFIRMED

POL-CHK-003 — One service package version per Check, recorded
  Statement : The system shall run each Check on the service package version that is current when the Check starts, use that version for the whole Check and record it with the Check.
  Pattern   : ubiquitous
  Trigger   : Start Check
  Rationale : Every report records the version it was built on, which is only traceable if one version serves the whole Check.
  Source    : [KB:raw-idea.md §4] "every report records the version it was built on"; domain-profile §5 G11; ADR-REG-003; ADR-CHK-007
  Status    : CONFIRMED

POL-CHK-004 — The same fixed pipeline for every Check
  Statement : The system shall run every Check through the same fixed pipeline — load the service package, run its queries, obtain the documents, run the deterministic checks, compare with the LLM, store the report — whatever the service.
  Pattern   : ubiquitous
  Trigger   : Run Check
  Rationale : The pipeline is the only fixed logic; everything that differs between services is configuration.
  Source    : [KB:raw-idea.md §3] "Check Engine fixed pipeline (the only fixed logic)", §5; profile `workflow_engine: forbidden`
  Status    : CONFIRMED

POL-CHK-005 — Queries run exactly as written, the request number bound
  Statement : The system shall run only the queries written in the Check's service definition, exactly as written, with the request number passed as a bound parameter and never built into the query text.
  Pattern   : ubiquitous
  Trigger   : Run Check
  Rationale : SQL is executed literally by the engine and never built from free text.
  Source    : [KB:raw-idea.md §4, §6, §12] "Query parameters are bound or strictly type-validated; SQL is never built from free text"; domain-profile §5 G4; D2
  Status    : CONFIRMED

POL-CHK-006 — Host data read-only, document source query left to Document Access
  Statement : The system shall reach host data only through the read-only connections of the service registry, by the platform query channel, and shall leave the service definition's document source query to Document Access.
  Pattern   : ubiquitous
  Trigger   : Run Check
  Rationale : The service needs no write access to host systems, and BLOB content must never travel through MCP.
  Source    : [KB:raw-idea.md §6, §12] "All access to host data uses a read-only database user"; domain-profile §5 G3, G14; ADR-DOC-001 (owner-confirmed)
  Status    : CONFIRMED

POL-CHK-007 — Documents only through Document Access
  Statement : The system shall obtain the documents of a Check, their read status and their content only from Document Access and shall never open host files, host BLOB columns or uploaded files itself.
  Pattern   : ubiquitous
  Trigger   : Run Check
  Rationale : Document Access is where the storage-root, file-size and read-only guardrails are applied; a second way in would bypass them.
  Source    : [KB:raw-idea.md §5 step 3–4, §6]; domain-profile §6 (DOC → CHK); ADR-DOC-001; DOC CON-DOC-004
  Status    : CONFIRMED

POL-CHK-008 — Missing or unreadable required document prevents COMPLIANT
  Statement : If a required document of the Check's service is missing or unreadable, then the system shall not give the Check the overall status `COMPLIANT`.
  Pattern   : unwanted
  Trigger   : Decide overall status
  Rationale : A requirement that was not seen cannot be declared met.
  Source    : [KB:raw-idea.md §7] "A required document that is missing or unreadable prevents a `COMPLIANT` status"; domain-profile §5 G6; ADR-DOC-002, ADR-DOC-007
  Status    : CONFIRMED

POL-CHK-009 — Explicit values and dates decided in code
  Statement : The system shall decide every condition on an explicit value or date in code, from the value found in the Check's data or documents and the limit stated in the service knowledge, and never by the model's judgement alone.
  Pattern   : ubiquitous
  Trigger   : Run deterministic checks
  Rationale : Numbers and dates have one right answer; the engine computes it so the outcome on them does not depend on the model.
  Source    : [KB:raw-idea.md §5 step 5] "It runs the deterministic checks in code: required documents present, explicit values and dates"; ADR-CHK-003
  Status    : CONFIRMED

POL-CHK-010 — The model compares only and has no tools
  Statement : The system shall use the model only to compare the Check's data and document content with the service knowledge, shall give it no tool, and shall never let it write a query or trigger an approval.
  Pattern   : ubiquitous
  Trigger   : Compare with LLM
  Rationale : The LLM analyses and summarises; SQL and approval stay under engine and employee control.
  Source    : [KB:raw-idea.md §6, §12] "The LLM analyses and summarises. It does not write SQL and does not trigger approval"; "The LLM is never given a tool that runs SQL"; domain-profile §5 G1
  Status    : CONFIRMED

POL-CHK-011 — Service knowledge is the only instruction
  Statement : The system shall give the model the service knowledge as its only service instructions and the query results and document content only as delimited data, never acting on an instruction found in them.
  Pattern   : ubiquitous
  Trigger   : Compare with LLM
  Rationale : A request's data and documents are evidence to be checked, not commands that could change how the request is assessed.
  Source    : [KB:raw-idea.md §12] "Document content is treated as data, never as instructions to the model"; domain-profile §5 G7; REG REQ-REG-018
  Status    : CONFIRMED

POL-CHK-012 — Structured output in the fixed report structure
  Statement : The system shall obtain the model's comparison as structured output in the report structure that is fixed for every service.
  Pattern   : ubiquitous
  Trigger   : Compare with LLM
  Rationale : The report is stored as data and shown the same way for every service.
  Source    : [KB:raw-idea.md §7] "The report has a fixed structure for every service, produced as structured output and stored as data"
  Status    : CONFIRMED

POL-CHK-013 — One finding per condition, with evidence and a note
  Statement : The system shall give the report one finding for each condition of the service — including each required document — stating whether it is satisfied, the evidence found and a note for the employee.
  Pattern   : ubiquitous
  Trigger   : Build report
  Rationale : The employee must see where the problems are and verify each finding.
  Source    : [KB:raw-idea.md §7] "One per condition: satisfied or not, the evidence (actual value found), and a note for the employee"; domain-profile §5 G10
  Status    : CONFIRMED

POL-CHK-014 — Evidence must be found in the Check's own data
  Statement : If the evidence of a finding cannot be found in the Check's query results or document content, then the system shall mark that condition as undetermined for manual review instead of satisfied or not satisfied.
  Pattern   : unwanted
  Trigger   : Build report
  Rationale : Evidence is the actual value found; a value the model did not take from the request cannot support a finding.
  Source    : [KB:raw-idea.md §7] "the evidence (actual value found)"; domain-profile §5 G10; ADR-CHK-002, ADR-CHK-003
  Status    : RECOMMENDED

POL-CHK-015 — Overall status derived from the findings
  Statement : The system shall give the Check the overall status `NOT_COMPLIANT` when any condition is not satisfied, otherwise `NEEDS_MANUAL_REVIEW` when any condition is undetermined or anything required could not be read, and `COMPLIANT` only when every condition is satisfied.
  Pattern   : ubiquitous
  Trigger   : Decide overall status
  Rationale : The overall status must follow from the findings the employee sees, and never claim more than was verified.
  Source    : [KB:raw-idea.md §7] overall status values; domain-profile §5 G6; ADR-CHK-002
  Status    : RECOMMENDED

POL-CHK-016 — Report metadata
  Statement : The system shall record with every report the service package version, the document fetch mode, the comparison model used, the start and end time of the Check and the employee's identity.
  Pattern   : ubiquitous
  Trigger   : Build report
  Rationale : The report must be traceable to what produced it and who asked for it.
  Source    : [KB:raw-idea.md §7] "Metadata: Service version, document source mode, model used, time, employee"; §6 "the report is marked accordingly"; domain-profile §5 G11
  Status    : CONFIRMED

POL-CHK-017 — Results stored only through the Report Store
  Statement : The system shall hand every Check's status, report and failure reason to the Report Store and shall keep no report of its own.
  Pattern   : ubiquitous
  Trigger   : Store report
  Rationale : The Report Store owns runs, findings and documents; the host and the frontend read them there.
  Source    : [KB:raw-idea.md §5 step 7, §9, §14]; ADR-REG-001, ADR-REG-002 (owner-confirmed)
  Status    : CONFIRMED

POL-CHK-018 — Unread query data reported, never skipped
  Statement : If a service query fails or returns more rows than the maximum rows of the platform configuration, then the system shall record in the report that its data could not be read and shall not give the Check the overall status `COMPLIANT`.
  Pattern   : unwanted
  Trigger   : Run Check
  Rationale : Anything that could not be read appears in the report; truncated data must never pass as complete.
  Source    : [KB:raw-idea.md §12] "Anything that could not be read appears in the report. It is never skipped silently"; "maximum rows"; domain-profile §5 G6, G8; ADR-CHK-005
  Status    : RECOMMENDED

POL-CHK-019 — Check timeout
  Statement : If a Check runs longer than the Check timeout of the platform configuration, then the system shall stop it and end it as failed with the reason.
  Pattern   : unwanted
  Trigger   : Run Check
  Rationale : Each check has limits; a stuck Check must not run or hold resources forever.
  Source    : [KB:raw-idea.md §12] "Each check has limits: timeout, maximum rows, maximum file size"; domain-profile §5 G8; ADR-REG-006; ADR-CHK-005
  Status    : RECOMMENDED

POL-CHK-020 — A Check that cannot finish fails with a reason, never a guessed status
  Statement : If a Check cannot complete its pipeline because the model is unavailable or not permitted, the model's output does not fit the fixed report structure, or the Check is interrupted, then the system shall end the Check as failed with the reason and give it no overall status.
  Pattern   : unwanted
  Trigger   : Run Check
  Rationale : A failed Check must be visible as failed; an overall status the pipeline did not reach would mislead the employee.
  Source    : [KB:raw-idea.md §1, §12] "never skipped silently"; domain-profile §5 G6; ADR-CHK-005
  Status    : RECOMMENDED

POL-CHK-021 — Document Access told of every Check's end
  Statement : When a Check ends, whatever its outcome, the system shall notify Document Access that the Check has ended.
  Pattern   : event
  Trigger   : Check ends
  Rationale : Document Access discards the files uploaded for a Check when the Check ends, and only the Check Engine knows when that is.
  Source    : ADR-DOC-008 (owner-confirmed) "The CHK analysis must send the end-of-Check notice on every ending path"; domain-profile §5 G9; DOC CON-DOC-005
  Status    : CONFIRMED

POL-CHK-022 — Unfinished Checks closed at start-up
  Statement : When the service starts, the system shall end as failed every Check that an earlier run of the service left unfinished.
  Pattern   : event
  Trigger   : Service start
  Rationale : A Check interrupted by a restart would otherwise stay running for ever, and its uploads would never be discarded.
  Source    : [KB:raw-idea.md §5] asynchronous run; domain-profile §5 G6, G9; ADR-DOC-008; ADR-CHK-005
  Status    : RECOMMENDED

POL-CHK-023 — A `manual` Check waits for the employee's uploads
  Statement : While a Check whose fetch mode is `manual` awaits the employee's uploads, the system shall not run its pipeline until the employee confirms that the uploads are complete.
  Pattern   : state
  Trigger   : Upload document / Confirm uploads
  Rationale : In manual mode the documents exist only once the employee has uploaded them for that Check.
  Source    : [KB:raw-idea.md §6] "`manual` — The employee uploads the files"; §8 `POST /checks/{id}/documents`; ADR-DOC-003; ADR-CHK-004
  Status    : RECOMMENDED

POL-CHK-024 — Upload window
  Statement : If the employee does not confirm the uploads of a `manual` Check within the upload window of the platform configuration, then the system shall end the Check as failed with the reason.
  Pattern   : unwanted
  Trigger   : Upload window elapses
  Rationale : An abandoned Check must end so that its uploads are discarded and it does not wait for ever.
  Source    : domain-profile §5 G8, G9; ADR-DOC-008; ADR-CHK-004
  Status    : RECOMMENDED

POL-CHK-025 — Every Check independent
  Statement : The system shall run every Check independently — including several Checks of the same request — and shall carry no data, model conversation or result of one Check into another.
  Pattern   : ubiquitous
  Trigger   : Run Check
  Rationale : Carrying state between requests risks leaking one request's data into another.
  Source    : [KB:raw-idea.md §2, §12] "No data is carried from one check to another"; §15 A1 "the checks of a request"; domain-profile §5 G9; ADR-CHK-007
  Status    : CONFIRMED

POL-CHK-026 — Comparison model through `ChatModel` only
  Statement : The system shall reach the comparison model only through Spring AI `ChatModel`, with no provider-specific feature, so that the provider is replaceable by configuration alone.
  Pattern   : ubiquitous
  Trigger   : Compare with LLM / Change model
  Rationale : The provider for real requests is decided before go-live and must be swappable without touching the engine.
  Source    : [KB:raw-idea.md §10] "The engine depends on Spring AI's `ChatModel` only, with no provider-specific features"; domain-profile §5 G12, §8 D3
  Status    : CONFIRMED

POL-CHK-027 — Free-tier comparison model gets only synthetic or anonymised data
  Statement : While a free-tier provider is configured as the comparison model, the system shall send it only synthetic or anonymised requests and documents.
  Pattern   : state
  Trigger   : Compare with LLM / Change model
  Rationale : Free tiers may use submitted data for model training; real requests wait for the go-live provider decision.
  Source    : [KB:raw-idea.md §10] "Only synthetic or anonymised requests and documents are sent while a free provider is in use"; domain-profile §5 G13, §8 D3; ADR-CHK-006
  Status    : CONFIRMED

POL-CHK-028 — Known-result request set on every model change
  Statement : When the configured comparison model changes, the system shall be run on the fixed set of test requests with known expected results before it verifies real requests with the new model.
  Pattern   : event
  Trigger   : Change model
  Rationale : A new model must be shown to reach the known overall statuses before employees rely on it.
  Source    : [KB:raw-idea.md §10] "A fixed set of test requests with known expected results is run on every model change"; domain-profile §5 G12
  Status    : CONFIRMED

POL-CHK-029 — The Check Engine never approves
  Statement : The system shall never call a host approval API or record an Employee Decision as part of a Check; the report only informs the employee.
  Pattern   : ubiquitous
  Trigger   : Run Check
  Rationale : The employee stays the decision maker; approval happens only as a result of the employee's action.
  Source    : [KB:raw-idea.md §1, §11, §12] "Approval is executed only as a result of the employee's action"; domain-profile §5 G2; REG CON-REG-012 (approval API supplied to the Employee Decision path only)
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
| Lookup key | Added values | Source |
|---|---|---|
| Overall status | `COMPLIANT`, `NOT_COMPLIANT`, `NEEDS_MANUAL_REVIEW` | [KB:raw-idea.md §7]; profile `conventions.lookups` |
| Check status | `AWAITING_DOCUMENTS`, `RUNNING`, `COMPLETED`, `FAILED` | recommended — ADR-CHK-001, ADR-CHK-004 |
| Finding outcome | `SATISFIED`, `NOT_SATISFIED`, `UNDETERMINED` | recommended — ADR-CHK-002 |
| Check failure reason | `TIMED_OUT`, `MODEL_UNAVAILABLE`, `MODEL_OUTPUT_INVALID`, `MODEL_NOT_PERMITTED`, `UPLOAD_WINDOW_EXPIRED`, `INTERRUPTED`, `INTERNAL_ERROR` | recommended — ADR-CHK-005, ADR-CHK-006 |

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Per-service limits | The timeout, maximum rows and upload window are not set per service; one platform configuration applies to every Check | A later version with a stated per-service need | ADR-REG-006; REG RULE-REG-012 |
| Check types declared in the service definition | v1 decides explicit values and dates in code from what the model states and code verifies (ADR-CHK-003); the service definition declares no check rules (CON-REG-007) | A REG version that adds declared check types | [KB:raw-idea.md §4] "existing check types"; CON-REG-007; ADR-CHK-003 |
| Caller authentication on starting a Check | Checks are started through INT without caller authentication in this version; the employee's identity is recorded as the host sent it | Security version (A2) | [KB:raw-idea.md §15 A2]; D7 |
| Conversation memory and retrieval (RAG) | Each Check gives the model the whole service knowledge and nothing from other Checks | Same trigger as the platform RAG deferral | [KB:raw-idea.md §2] |
| Resuming an interrupted Check | An interrupted Check ends FAILED; the employee starts a new one | A later version with a stated need | ADR-CHK-005 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Where the run results go and who runs the document source query | Through CHK's result port to RPT (POL-CHK-017); the document source query is DOC's (POL-CHK-006); end-of-Check notice on every ending path (POL-CHK-021) | yes — owner confirmed ADR-REG-001, ADR-REG-002, ADR-DOC-001, ADR-DOC-008 | ADRs cited |
| 2 | Which closed lists CHK masters | Overall status, Check status, finding outcome, failure reason (ADR-CHK-001) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §7, §8, §9] |
| 3 | How the overall status is derived, and what an undecidable condition is | Three finding outcomes; NOT_COMPLIANT > NEEDS_MANUAL_REVIEW > COMPLIANT (POL-CHK-014, POL-CHK-015; ADR-CHK-002) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §7]; G6, G10 |
| 4 | How explicit values and dates are decided in code without declared check rules | The model states value, comparison and limit; code verifies and recomputes (POL-CHK-009; ADR-CHK-003) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §5 step 5]; CON-REG-007 |
| 5 | How a `manual` Check gets its documents | Waits for the employee's confirmation within an upload window (POL-CHK-023, POL-CHK-024; ADR-CHK-004) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §6, §8] |
| 6 | Which failures still give a report and which end the Check | Unread query data → report, no COMPLIANT (POL-CHK-018); timeout, model or structure failures, interruption → FAILED with reason (POL-CHK-019, POL-CHK-020, POL-CHK-022; ADR-CHK-005) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §12]; G6, G8 |
| 7 | How the free-tier rule is made testable for the comparison model | Model tier and environment data class, as DOC (ADR-CHK-006) | recommended — pending owner confirmation at prd-approval | ADR-DOC-009; G13 |
| 8 | Several Checks for the same request | Allowed and independent (POL-CHK-025; ADR-CHK-007) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §15 A1] |
══════════════════════════════════════════════════════════════════
