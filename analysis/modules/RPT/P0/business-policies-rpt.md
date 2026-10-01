## BUSINESS POLICIES — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module   : RPT     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

Status legend: CONFIRMED = the owner's text or an owner-confirmed decision states the need; RECOMMENDED = the dialogue's recommended answer, confirmed by the owner at prd-approval (ADR-RPT-001 … ADR-RPT-005).

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-RPT-001 — Every started Check gets a stored Check run
  Statement : When the Check Engine starts a Check, the system shall store a Check run with its service code, service package version, fetch mode, request number, employee identity, status and start time, and return the Check's identifier.
  Pattern   : event
  Trigger   : Create Check run
  Rationale : One row per check is where the report and the decision are kept; the host and the frontend follow the Check by its identifier.
  Source    : [KB:raw-idea.md §9] "`CHECK_RUN` — One row per check: service, request number, employee, status, result, service version, model, timestamps"; ADR-REG-001; CON-CHK-006; ADR-RPT-001
  Status    : CONFIRMED

POL-RPT-002 — Host identifiers kept exactly as sent
  Statement : The system shall keep the request number and the employee identity exactly as the host system sent them and shall never link them to host data.
  Pattern   : ubiquitous
  Trigger   : Create Check run / Record decision
  Rationale : The host data lives outside the service's schema; the report must show the identifiers the host knows.
  Source    : profile `conventions.identifiers` "stored as strings exactly as the host sent them and are never foreign keys"; [KB:raw-idea.md §5 step 1, §8] "passes the employee's identity, which is recorded with the check"
  Status    : CONFIRMED

POL-RPT-003 — A Check's status only moves forward
  Statement : If a status change would move a Check backwards or out of an ended status, then the system shall refuse the change and keep the stored status.
  Pattern   : unwanted
  Trigger   : Mark running / Complete / Fail
  Rationale : The status a host polls must tell the truth about the Check's life; an ended Check cannot restart or end twice.
  Source    : [KB:raw-idea.md §5] "the host starts it and then polls"; CON-CHK-001 "COMPLETED and FAILED are final"; ADR-CHK-015; ADR-RPT-002
  Status    : RECOMMENDED

POL-RPT-004 — A completed report is stored whole or not at all
  Statement : When the Check Engine completes a Check, the system shall store its Overall Status, every finding, every document outcome, every unread service query and its metadata together, or store none of them.
  Pattern   : event
  Trigger   : Complete Check
  Rationale : A half-stored report would show the employee an Overall Status without the findings that justify it.
  Source    : [KB:raw-idea.md §7] "The report has a fixed structure for every service, produced as structured output and stored as data"; CON-CHK-008 "errors: not stored (CHK then fails the Check with INTERNAL_ERROR)"; ADR-RPT-001
  Status    : RECOMMENDED

POL-RPT-005 — Only the codes of the closed lists are stored
  Statement : If a Check status, Overall Status, finding outcome, failure reason, document read status, unreadable reason or fetch mode received is not a code of its closed list, then the system shall refuse to store it.
  Pattern   : unwanted
  Trigger   : Create Check run / Complete / Fail
  Rationale : The closed lists are owned by the service; a stored code outside them would be meaningless to every reader.
  Source    : profile `conventions.lookups` "closed enums owned by the service"; CON-CHK-001, CON-CHK-002, CON-CHK-003, CON-DOC-001, CON-DOC-002; ADR-RPT-001
  Status    : CONFIRMED

POL-RPT-006 — A completed Check has a result, a failed Check a reason
  Statement : The system shall store exactly one Overall Status for every completed Check, and exactly one failure reason with no Overall Status and no findings for every failed Check.
  Pattern   : ubiquitous
  Trigger   : Complete / Fail
  Rationale : A failed Check must be visible as failed; a result the pipeline did not reach would mislead the employee.
  Source    : CON-CHK-001 "a COMPLETED Check always carries exactly one Overall Status and a FAILED Check never carries one"; CON-CHK-003; CON-CHK-009; ADR-CHK-005
  Status    : CONFIRMED

POL-RPT-007 — Nothing unread is dropped from the report
  Statement : The system shall keep in the stored report every document that was missing or could not be read, with its reason, and every service query whose data could not be read.
  Pattern   : ubiquitous
  Trigger   : Complete Check
  Rationale : Anything that could not be read appears in the report; it is never skipped silently.
  Source    : [KB:raw-idea.md §7] "Documents: What was read, what is missing, what could not be read"; §12 "never skipped silently"; domain-profile §5 G6; ADR-CHK-014; ADR-RPT-001
  Status    : CONFIRMED

POL-RPT-008 — No document content or query results kept
  Statement : The system shall keep no document content and no query results in a stored report beyond the evidence, notes and outcomes the Check Engine reports.
  Pattern   : ubiquitous
  Trigger   : Complete Check
  Rationale : The report needs the evidence the employee verifies, not a copy of the request's files and data.
  Source    : CON-CHK-008 "Carries no document content"; ADR-DOC-008 (fetched content lives only for the fetching call); domain-profile §5 G9, G10
  Status    : CONFIRMED

POL-RPT-009 — An ended Check's report never changes
  Statement : While a Check is completed or failed, the system shall keep its stored report unchanged and accept only the recording of its Employee Decision.
  Pattern   : state
  Trigger   : Complete / Fail / Record decision
  Rationale : The decision is measured against the report the employee saw; a report that changes afterwards breaks that measure.
  Source    : [KB:raw-idea.md §9] "Storing the employee's decision beside the report result gives a direct measure of accuracy"; §11 "records the report the approval was based on"; ADR-RPT-002
  Status    : RECOMMENDED

POL-RPT-010 — Unfinished Checks answered to the Check Engine
  Statement : When the Check Engine asks for the unfinished Checks, the system shall return every stored Check whose status is `AWAITING_DOCUMENTS` or `RUNNING`.
  Pattern   : event
  Trigger   : List unfinished Checks
  Rationale : The Check Engine ends Checks a restart interrupted and Checks whose upload window elapsed, and only the Report Store knows them.
  Source    : CON-CHK-011; ADR-CHK-005; ADR-CHK-004
  Status    : CONFIRMED

POL-RPT-011 — A Check's status and report readable
  Statement : When a host system or the employee frontend asks for a Check through Host Integration, the system shall return its status and, once it has ended, its stored report with every finding beside its evidence.
  Pattern   : event
  Trigger   : Read Check
  Rationale : The host polls the status, and the employee verifies each finding against its evidence.
  Source    : [KB:raw-idea.md §5] "makes it available to the host system"; §8 "`GET /checks/{id}` Return the status and the report as JSON"; §15 A1; domain-profile §6 (RPT → INT); ADR-RPT-005
  Status    : CONFIRMED

POL-RPT-012 — The Checks of a request listed
  Statement : When the employee frontend asks for the Checks of a request through Host Integration, the system shall return every stored Check of that service code and request number, newest first.
  Pattern   : event
  Trigger   : List Checks of a request
  Rationale : The employee sees the checks of the request on the host screen, including earlier Checks of the same request.
  Source    : [KB:raw-idea.md §15 A1] "gives the employee: the checks of a request"; ADR-CHK-007; ADR-RPT-005
  Status    : CONFIRMED

POL-RPT-013 — Employee Decision recorded beside the result
  Statement : When Host Integration hands over the employee's decision on a Check, the system shall record the decision, the identity of the employee who took it and the time, beside the Check's result.
  Pattern   : event
  Trigger   : Record decision
  Rationale : The service informs the decision; keeping the decision beside the result shows where the two disagree.
  Source    : [KB:raw-idea.md §9] "employee decision" in `CHECK_RUN`; §8 `POST /checks/{id}/decision`; §11 "The host notifies the service of the decision for the record"; domain-profile §7.1; ADR-RPT-003
  Status    : CONFIRMED

POL-RPT-014 — One Employee Decision per Check
  Statement : If an Employee Decision is handed over for a Check that already has one, then the system shall refuse it and keep the decision already recorded.
  Pattern   : unwanted
  Trigger   : Record decision
  Rationale : The decision is a fact about one report; a later change belongs to the host system's own record.
  Source    : [KB:raw-idea.md §9]; ADR-RPT-003
  Status    : RECOMMENDED

POL-RPT-015 — Decisions only on a completed Check
  Statement : If an Employee Decision is handed over for a Check that is not completed, then the system shall refuse it.
  Pattern   : unwanted
  Trigger   : Record decision
  Rationale : The decision is recorded beside the report result; a running or failed Check has no result to measure it against.
  Source    : [KB:raw-idea.md §9] "beside the report result"; CON-CHK-001; ADR-RPT-003
  Status    : RECOMMENDED

POL-RPT-016 — Execution through the Approval API recorded
  Statement : Where the employee's decision was executed through the service's Approval API, the system shall record that with the decision.
  Pattern   : optional
  Trigger   : Record decision
  Rationale : The record must show which approvals the service carried out for the host and on which report they were based.
  Source    : [KB:raw-idea.md §11] "the service calls it after the employee confirms, and records the report the approval was based on"; domain-profile §5 G2; ADR-RPT-003
  Status    : CONFIRMED

POL-RPT-017 — Decision agreement per service package version
  Statement : When asked for the decision agreement of a service, the system shall return, for each service package version, how many decided Checks of each Overall Status were approved and how many were rejected.
  Pattern   : event
  Trigger   : Read decision agreement
  Rationale : Where the result and the decision disagree, the service knowledge or a check needs attention.
  Source    : [KB:raw-idea.md §9] "gives a direct measure of accuracy: where the two disagree, the service knowledge or a check needs attention"; domain-profile §5 G11; ADR-RPT-005
  Status    : RECOMMENDED

POL-RPT-018 — The Report Store never approves
  Statement : The system shall never call an Approval API and never take a decision on a request itself; it only records the Employee Decision it receives.
  Pattern   : ubiquitous
  Trigger   : Record decision
  Rationale : The employee stays the decision maker; approval happens only as a result of the employee's action.
  Source    : [KB:raw-idea.md §1, §12] "Approval is executed only as a result of the employee's action"; domain-profile §5 G2
  Status    : CONFIRMED

POL-RPT-019 — Reports kept for the retention period
  Statement : The system shall keep every stored Check run, with its report and Employee Decision, until it has been ended for longer than the report retention period of the platform configuration.
  Pattern   : ubiquitous
  Trigger   : Retention
  Rationale : A report is kept as long as the host keeps the request it verified; the period is configuration.
  Source    : domain-profile §8 D4 "A report is kept as long as the host keeps the request it verified. The period is configuration"; ADR-RPT-004
  Status    : CONFIRMED

POL-RPT-020 — Purge removes the whole Check run
  Statement : When a stored Check run has been ended for longer than the report retention period, the system shall permanently delete it together with its findings, Check Documents, unread queries and Employee Decision.
  Pattern   : event
  Trigger   : Purge
  Rationale : A purge removes older runs with their findings and documents; reports are records, never deactivated.
  Source    : domain-profile §8 D4 "a purge removes older runs with their findings and documents (hard delete)"; profile `delete_semantics: hard`; ADR-RPT-004
  Status    : CONFIRMED

POL-RPT-021 — No retention period, no purge
  Statement : If no report retention period is configured, then the system shall delete no stored Check run.
  Pattern   : unwanted
  Trigger   : Purge
  Rationale : A missing setting must never destroy records; keeping too long is recoverable, deleting is not.
  Source    : domain-profile §8 D4; ADR-RPT-004
  Status    : RECOMMENDED

POL-RPT-022 — Unfinished Checks never purged
  Statement : If a Check has not ended, then the system shall not purge it whatever its age.
  Pattern   : unwanted
  Trigger   : Purge
  Rationale : The Check Engine still writes to an unfinished Check and ends it at start-up; purging it would lose its outcome.
  Source    : CON-CHK-011; ADR-CHK-005; ADR-RPT-004
  Status    : RECOMMENDED

POL-RPT-023 — Every Check stored independently
  Statement : The system shall store every Check as its own record — including several Checks of the same request — and shall never copy a finding, a result or a decision from one Check to another.
  Pattern   : ubiquitous
  Trigger   : Create Check run / Record decision
  Rationale : No data is carried from one check to another; each report and decision stands for one Check only.
  Source    : [KB:raw-idea.md §2, §12] "No data is carried from one check to another"; domain-profile §5 G9; ADR-CHK-007
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
| Lookup key | Added values | Source |
|---|---|---|
| Employee Decision | `APPROVED`, `REJECTED` | domain-profile §7.1 "approve / reject decision"; recommended — ADR-RPT-003 |

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Who may view stored reports | Every caller reaching Host Integration reads any stored report and records decisions; no viewer restriction in this version | Security version (A2) | domain-profile §8 D4, D7; [KB:raw-idea.md §15 A2] |
| Changing or withdrawing an Employee Decision | A recorded decision is final in the Report Store; a later change is the host system's own record | A later version with a stated need | ADR-RPT-003 |
| Per-service retention periods | One report retention period applies to every service | A later version with a stated per-service need | domain-profile §8 D4; ADR-RPT-004 |
| Archiving before purge | A purged report is deleted, not archived | A later version with a stated need | domain-profile §8 D4 (hard delete) |
| Reports across services or dashboards | Only the decision agreement of one service by version is offered; a full administration UI stays out of scope | Out of scope [KB:raw-idea.md §2] | [KB:raw-idea.md §2]; ADR-RPT-005 |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | What RPT stores and how the closed codes of CHK and DOC are held | Check Run, Finding, Check Document and Unread Query, stored whole, codes by value (POL-RPT-001, POL-RPT-004 … POL-RPT-008; ADR-RPT-001) | recommended — confirmed at prd-approval | ADR-REG-001; CON-CHK-006 … CON-CHK-011 |
| 2 | Whether a stored report can change | Status only forward; an ended report is final (POL-RPT-003, POL-RPT-009; ADR-RPT-002) | recommended — confirmed at prd-approval | [KB:raw-idea.md §9, §11] |
| 3 | How the Employee Decision is recorded | Beside the result, `APPROVED` / `REJECTED`, once, only on a COMPLETED Check, with Approval API execution noted (POL-RPT-013 … POL-RPT-016; ADR-RPT-003) | recommended — confirmed at prd-approval | [KB:raw-idea.md §9, §11] |
| 4 | How retention works | Configured period; hard-delete purge of ended runs; no period → no purge (POL-RPT-019 … POL-RPT-022; ADR-RPT-004) | yes — owner D4; details recommended — confirmed at prd-approval | domain-profile D4 |
| 5 | Which reads are offered, and to whom | Report, Checks of a request, decision agreement — through INT; no viewer restriction (POL-RPT-011, POL-RPT-012, POL-RPT-017; ADR-RPT-005) | recommended — confirmed at prd-approval | [KB:raw-idea.md §8, §9, §15 A1, A2] |
══════════════════════════════════════════════════════════════════
