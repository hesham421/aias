## BUSINESS POLICIES — Host Integration (INT)
══════════════════════════════════════════════════════════════════
Module   : INT     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

Status legend: CONFIRMED = the owner's text or an owner-confirmed decision states the need; RECOMMENDED = the dialogue's recommended answer, confirmed by the owner at prd-approval (ADR-INT-001 … ADR-INT-009).

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-INT-001 — A Check started at the host's request, answered at once
  Statement : When a host system or the employee frontend asks to start a Check for a service code, a request number and an employee identity, the system shall start the Check and answer at once with its identifier and status, without waiting for the report.
  Pattern   : event
  Trigger   : Start Check
  Rationale : A Check takes time; the host starts it and then polls for the result.
  Source    : [KB:raw-idea.md §5] "the host starts it and then polls for the result"; §8 `POST /checks`; ADR-INT-002
  Status    : CONFIRMED

POL-INT-002 — The employee identity handed on as sent
  Statement : The system shall hand on the employee identity the host system sends exactly as sent and shall not check it against any user directory.
  Pattern   : ubiquitous
  Trigger   : Start Check / Record decision
  Rationale : The host identifies the employee; caller authentication is deferred to the security version.
  Source    : [KB:raw-idea.md §8] "passes the employee's identity, which is recorded with the check"; §15 A2; profile `conventions.identifiers`; ADR-INT-002
  Status    : CONFIRMED

POL-INT-003 — Refusals explained in the owner's words
  Statement : If the module that owns a request's subject refuses it, then the system shall answer with that module's refusal code and explanation in the platform's standard error form.
  Pattern   : unwanted
  Trigger   : Start Check / Upload / Confirm uploads / Record decision
  Rationale : The employee and the host must learn why nothing happened, in the same words wherever the refusal arose.
  Source    : profile `stack.backend.api.error_envelope` "ProblemDetail (RFC 9457)"; CON-CHK-004, CON-CHK-005, CON-DOC-003, CON-RPT-006 "for INT to show as a ProblemDetail"; ADR-INT-003
  Status    : RECOMMENDED

POL-INT-004 — Manual uploads handed to Document Access
  Statement : When the employee uploads a document for a Check, the system shall hand it to Document Access together with the Check's service code and service package version.
  Pattern   : event
  Trigger   : Upload
  Rationale : In `manual` mode the employee provides the files the service may not fetch itself.
  Source    : [KB:raw-idea.md §6] "`manual` — The employee uploads the files"; §8 `POST /checks/{id}/documents`; CON-DOC-003; ADR-INT-005
  Status    : CONFIRMED

POL-INT-005 — Uploads only while the Check waits for documents
  Statement : If a document is uploaded for a Check that is not waiting for documents, then the system shall refuse the upload.
  Pattern   : unwanted
  Trigger   : Upload
  Rationale : A file uploaded to a running or ended Check would never be read; it must not disappear silently.
  Source    : [KB:raw-idea.md §12] "never skipped silently"; CON-CHK-001; CON-DOC-005; ADR-INT-005
  Status    : RECOMMENDED

POL-INT-006 — Confirmed uploads continue the Check
  Statement : When the employee confirms that the uploads of a Check are complete, the system shall have the Check Engine continue the Check.
  Pattern   : event
  Trigger   : Confirm uploads
  Rationale : The pipeline of a `manual` Check runs only on the documents the employee says are complete.
  Source    : [KB:raw-idea.md §6]; CON-CHK-005; ADR-INT-005
  Status    : CONFIRMED

POL-INT-007 — The Employee Decision handed to the Report Store
  Statement : When the employee records a decision on a Check, the system shall hand the decision and the deciding employee's identity to the Report Store to be recorded beside the result.
  Pattern   : event
  Trigger   : Record decision
  Rationale : The decision beside the report result is the direct measure of the service's accuracy.
  Source    : [KB:raw-idea.md §8] `POST /checks/{id}/decision`; §9; §11 "The host notifies the service of the decision for the record"; CON-RPT-006; ADR-INT-004
  Status    : CONFIRMED

POL-INT-008 — Approval only from the employee's decision
  Statement : The system shall call a host Approval API only as the result of an employee recording an approve decision, and never from any other action.
  Pattern   : ubiquitous
  Trigger   : Record decision
  Rationale : The employee stays the decision maker; the LLM and the pipeline never trigger approval.
  Source    : [KB:raw-idea.md §12] "Approval is executed only as a result of the employee's action"; domain-profile §5 G1, G2; CON-REG-012; ADR-INT-004
  Status    : CONFIRMED

POL-INT-009 — The Approval API only where the service enables it
  Statement : Where the service package version of a Check enables the Approval API, the system shall call it after the employee confirms an approve decision and before the decision is recorded.
  Pattern   : optional
  Trigger   : Record decision
  Rationale : Some hosts expose an approval endpoint; for them the service executes the approval the employee confirmed and records the report it was based on.
  Source    : [KB:raw-idea.md §11] "Optional: where the host exposes an approval API, the service calls it after the employee confirms"; §4 `approval: enabled`; domain-profile §5 G2; ADR-INT-004, ADR-INT-008, ADR-INT-009
  Status    : CONFIRMED

POL-INT-010 — A failed approval records nothing
  Statement : If the Approval API call fails or does not answer in time, then the system shall record no decision and tell the employee that the approval was not executed.
  Pattern   : unwanted
  Trigger   : Record decision
  Rationale : A decision recorded as executed when the host never approved would mislead every later reader; the employee can retry.
  Source    : ADR-RPT-003 "a failed approval call records nothing so the employee can retry"; profile `http_statuses` 502 / 504 "the host approval API failed / timed out at decision time"; ADR-INT-004
  Status    : RECOMMENDED

POL-INT-011 — A rejection never calls the Approval API
  Statement : If the employee's decision is a rejection, then the system shall record it without calling any Approval API.
  Pattern   : unwanted
  Trigger   : Record decision
  Rationale : The Approval API executes approvals; a rejection stays the host's own action.
  Source    : [KB:raw-idea.md §4] `api: POST /requests/{requestId}/approve`; CON-RPT-006 (RULE-RPT-014 refuses an executed rejection); ADR-INT-004
  Status    : RECOMMENDED

POL-INT-012 — The Checks of a request on the host screen
  Statement : When the employee opens the frontend from the host screen for a request, the system shall show the Checks of that request.
  Pattern   : event
  Trigger   : Open frontend
  Rationale : The employee works on one request at a time, inside the host screen.
  Source    : [KB:raw-idea.md §15 A1] "gives the employee: the checks of a request"; ADR-INT-006
  Status    : CONFIRMED

POL-INT-013 — Every finding shown beside its evidence
  Statement : The system shall show every finding of a report with its evidence beside it, together with the documents that were read, missing or unreadable.
  Pattern   : ubiquitous
  Trigger   : View report
  Rationale : Every finding carries its evidence so the employee can verify it.
  Source    : [KB:raw-idea.md §7] "Every finding carries its evidence so the employee can verify it"; §15 A1 "the report (overall status, findings with evidence, documents read / missing / unreadable)"; domain-profile §5 G10; review AIAS-11
  Status    : CONFIRMED

POL-INT-014 — Never shown COMPLIANT while a required document is missing or unreadable
  Statement : If a required document of a Check is missing or unreadable, then the system shall never present the Check's report as COMPLIANT.
  Pattern   : unwanted
  Trigger   : View report
  Rationale : A missing or unreadable required document prevents a COMPLIANT status.
  Source    : [KB:raw-idea.md §7] "A required document that is missing or unreadable prevents a `COMPLIANT` status"; domain-profile §5 G6; review AIAS-11; ADR-INT-006
  Status    : CONFIRMED

POL-INT-015 — A running Check followed without reloading
  Statement : While a Check the employee is viewing is waiting for documents or running, the system shall keep its status current without the employee reloading the screen.
  Pattern   : state
  Trigger   : View report
  Rationale : The Check runs asynchronously and is polled for its result.
  Source    : [KB:raw-idea.md §5] "the host starts it and then polls for the result"; profile `stack.frontend.libraries.server-state` "also drives polling of a running check"; ADR-INT-006
  Status    : CONFIRMED

POL-INT-016 — One REST API for hosts and the frontend
  Statement : The system shall give the employee frontend no operation that the REST API does not offer to every host system.
  Pattern   : ubiquitous
  Trigger   : Any
  Rationale : The frontend consumes the same REST API as any host, so a host can build its own display without changing the service.
  Source    : [KB:raw-idea.md §15 A1] "It consumes the same REST API as any host"; §11 "The same report is available as JSON"; ADR-INT-001, ADR-INT-006
  Status    : CONFIRMED

POL-INT-017 — Host Integration keeps nothing of its own
  Statement : The system shall keep no request data, document, report or decision in Host Integration beyond the handling of one request.
  Pattern   : ubiquitous
  Trigger   : Any
  Rationale : Each fact is kept once, by the module that owns it; no data is carried from one Check to another.
  Source    : [KB:raw-idea.md §12] "No data is carried from one check to another"; domain-profile §5 G9; ADR-INT-007
  Status    : RECOMMENDED

POL-INT-018 — Upload, confirmation and decision are separate actions
  Statement : The system shall offer the manual upload, the confirmation of the uploads and the Employee Decision as separate employee actions, each submitted on its own.
  Pattern   : ubiquitous
  Trigger   : Upload / Confirm uploads / Record decision
  Rationale : One screen, one job, one submit — an upload never records a decision by accident.
  Source    : profile `conventions.screen_composition` "upload and decision never share a save"; ADR-INT-005, ADR-INT-006
  Status    : RECOMMENDED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
None — standard values apply.

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Server-rendered report page | `GET /checks/{id}/view` is not planned; the employee frontend is the display path | — (superseded) | [KB:raw-idea.md §15 A1]; domain-profile D6; ADR-INT-001 |
| Caller authentication (API key or mTLS) | Every caller reaching the REST API is served; the employee identity is recorded as sent | Security version (A2) | [KB:raw-idea.md §15 A2]; domain-profile D7 |
| Automatic retry of the Approval API | A failed call is retried only by the employee deciding again | A later version with an idempotent host API | ADR-INT-009 |
| Changing or withdrawing a recorded decision | Not offered; the Report Store keeps the first decision | A later version with a stated need | ADR-RPT-003 |
| A full administration UI | The frontend gives the employee the four jobs only | Out of scope | [KB:raw-idea.md §2, §15 A1] |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Which operations INT serves and how a start is answered | Four writes; start answered at once (POL-INT-001, POL-INT-016; ADR-INT-001, ADR-INT-002) | recommended — confirmed at prd-approval | [KB:raw-idea.md §5, §8, §15 A1] |
| 2 | How refusals are answered | Owner's code and explanation in ProblemDetail (POL-INT-003; ADR-INT-003) | recommended — confirmed at prd-approval | profile error envelope |
| 3 | Uploads and their confirmation | One file per upload while AWAITING_DOCUMENTS; confirmation separate (POL-INT-004 … POL-INT-006, POL-INT-018; ADR-INT-005) | recommended — confirmed at prd-approval | [KB:raw-idea.md §6]; CON-DOC-003, CON-CHK-005 |
| 4 | The decision and the Approval API | Approve only, where enabled, call first, failure records nothing, rejection never calls (POL-INT-007 … POL-INT-011; ADR-INT-004, ADR-INT-009) | recommended — confirmed at prd-approval | [KB:raw-idea.md §11, §12] |
| 5 | What the frontend shows | Checks of a request, report with evidence beside each finding, documents read / missing / unreadable, never COMPLIANT beside a missing or unreadable required document, status followed while running (POL-INT-012 … POL-INT-015; ADR-INT-006) | yes — owner A1; details recommended — confirmed at prd-approval | [KB:raw-idea.md §7, §15 A1] |
| 6 | Does INT keep records | No (POL-INT-017; ADR-INT-007) | recommended — confirmed at prd-approval | domain-profile G9 |
══════════════════════════════════════════════════════════════════
