# SRS — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias
Inputs : prd, domain-profile, project-registry (PRD approved 2026-10-01)
Counts : REQ 52 · AC 60 · ENT 4 · RULE 15 · SCR-REQ 0 · ADR 5 (new: ADR-RPT-006 … ADR-RPT-010; applied: ADR-RPT-001 … ADR-RPT-010, ADR-REG-001, ADR-REG-002, ADR-REG-006, ADR-CHK-001, ADR-CHK-002, ADR-CHK-005, ADR-CHK-007, ADR-CHK-011, ADR-CHK-014, ADR-CHK-015, ADR-CHK-017, ADR-DOC-002, ADR-DOC-007, ADR-DOC-008, ADR-DOC-011)
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information
| Item | Value |
|---|---|
| Module | RPT — Report Store |
| Feature code | RPT |
| Version | v1 |
| Date | 2026-10-01 |
| Status | DRAFT — P1 output, PRD approved 2026-10-01 (gate prd-approval) |
| Prepared by | P1 SRS engine (operator run, lane analysis) |
| Decisions applied | 10 RPT ADRs (ADR-RPT-001 … ADR-RPT-010, of which 5 new), 3 REG ADRs, 8 CHK ADRs, 4 DOC ADRs and 4 DEFAULTs — see Decisions applied |

## A2 — Functional context

### In scope
- Implementing all six operations of the Check result port CHK declares (CON-CHK-006 … CON-CHK-011): create a Check run and return its identifier, mark it RUNNING, store a completed report whole, store a failure with its reason, read one Check, list the unfinished Checks (POL-RPT-001, POL-RPT-004, POL-RPT-006, POL-RPT-010; ADR-RPT-001).
- Keeping host identifiers exactly as sent and only the codes of the closed lists of CHK and DOC (POL-RPT-002, POL-RPT-005).
- Moving a Check's status only forward and never changing an ended report (POL-RPT-003, POL-RPT-009; ADR-RPT-002).
- Keeping every unread document and unread service query in the report, and no document content or query results (POL-RPT-007, POL-RPT-008).
- Serving, read-only, a Check's status and report, the Checks of a request and the decision agreement of a service (POL-RPT-011, POL-RPT-012, POL-RPT-017; ADR-RPT-005, ADR-RPT-006).
- Recording the Employee Decision handed over by INT beside the result (POL-RPT-013 … POL-RPT-016, POL-RPT-018; ADR-RPT-003, ADR-RPT-009).
- Retention and the hard-delete purge (POL-RPT-019 … POL-RPT-022; ADR-RPT-004, ADR-RPT-010).
- The raw-idea §12 guardrails at RPT's surface (ADR-RPT-008).

### Out of scope
- Running a Check, deriving the Overall Status, the Check timeout and upload window — CHK (ADR-CHK-001, ADR-CHK-002, ADR-CHK-005).
- Fetching and reading documents, the storage root, the maximum file size, uploaded files — DOC (ADR-DOC-001, ADR-DOC-008).
- Starting a Check, uploading documents, confirming uploads, the decision endpoint and the call to the Approval API — INT (G2; ADR-RPT-003, ADR-RPT-006).
- Who may view stored reports and caller authentication — deferred (domain-profile D4, D7; raw-idea A2); no role check is specified.
- Changing or withdrawing a decision, per-service retention, archiving, cross-service dashboards (scope exceptions of the business policies).
- Multi-tenancy, conversation memory, RAG, multi-agent orchestration, an administration UI.

### Module function
The Report Store keeps the record of every Check: what it was asked to verify, how far it got, what it found and what the employee decided. It is written by the Check Engine through the Check result port and by Host Integration for the Employee Decision; it serves the host, the employee frontend and the service administrator read-only views of that record; and it removes each record, whole, once the configured retention period has passed since the Check ended.

### Detailed description
When the Check Engine starts a Check, it asks the Report Store to create the Check run — service code, service package version, fetch mode, request number, employee identity, initial status and start time — and receives the Check's identifier. As the pipeline advances, the Check Engine marks the Check RUNNING and finally either completes it — handing over the Overall Status, the findings with condition, outcome, evidence and note, the document outcomes with type, source mode, read status and reason, the service queries that could not be read and the metadata — or fails it with one failure reason and a detail text. The Report Store stores a completed report in one piece or not at all, refuses a code outside its closed list and refuses any change to an ended Check. At start-up and on its schedule the Check Engine asks for the unfinished Checks, and when the employee confirms the uploads of a `manual` Check it reads that Check back. Host systems and the embedded employee frontend read a Check's status while it runs and its whole report once it has ended, and list the Checks of a request. When the employee decides, Host Integration — after calling the Approval API where the service enables it — hands the decision to the Report Store, which records it once, beside the result of a COMPLETED Check. The service administrator reads, per service package version, how the decided Checks of each Overall Status were approved or rejected. A purge, on the platform configuration's schedule, deletes every Check run that ended longer ago than the report retention period, with all its records. Roles: the Employee (follows Checks, reads reports, takes the decision) and the Service Administrator (retention, accuracy measure).

### Current situation
| Step | Party | Notes |
|---|---|---|
| Employee checks the request by hand and approves or rejects it in the host system | Employee | No record of what was checked, on which evidence, or whether a check would have agreed with the decision [KB:raw-idea.md §1, §9] |

### Current difficulties
Nothing records which conditions were verified, on what evidence, or how often the employee's decision differs from what the evidence shows, so neither the employees' work nor a future automated check can be measured [KB:raw-idea.md §1, §9].

### Proposed system and benefits
Every Check leaves a complete, unchangeable report (US-RPT-003, US-RPT-006) that the employee reads with each finding beside its evidence (US-RPT-008); the decision is kept beside the result (US-RPT-010), so the service administrator can see where the service and the employees disagree (US-RPT-011); reports leave only by a deliberate, configured purge (US-RPT-012).

### General notes
- Logical types only; physical types and tables belong to P2.
- The Check identifier carried by the Check result port (`checkId`) is the identifier of the Check Run (ENT-RPT-001.checkRunId); CHK, DOC and INT hold it as a value only (CON-CHK-006).
- The report retention period, in whole days, and the purge schedule are platform configuration (ADR-RPT-004, ADR-RPT-010) — not entities.
- Every write comes from in-process callers: CHK through the result port, INT for the decision; RPT's HTTP surface is read-only (ADR-RPT-006).
- No role check is specified in this version (raw-idea A2).

## A3 — Entities and fields

Standard fields — per profile: kind `transactional` carries `createdAt, updatedAt` (system-filled, never accepted from a client). Every identifier field of the service's own schema is a number key generated by identity (profile `conventions.identifiers`); host identifiers (request number, employee identity) are kept as text exactly as the host sent them and are never foreign keys.

### ENT-RPT-001 — Check Run
Kind reason: transactional — one row per Check, created when the Check starts, ended once, removed only by the purge.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | SHARED (owner) — the identifier travels by value to CHK, DOC and INT; no other module stores its rows | no | create (REQ-RPT-001), read (REQ-RPT-021, REQ-RPT-023, REQ-RPT-028, REQ-RPT-040), update (REQ-RPT-005, REQ-RPT-008, REQ-RPT-018, REQ-RPT-032), delete (purge — REQ-RPT-043) | CHK writes it through the result port; INT records the decision; DOC and INT hold `checkId` by value | POL-RPT-001; ADR-REG-001; ADR-RPT-001, ADR-RPT-003 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| checkRunId | number (identifier) | yes | identity | the Check identifier (`checkId`) of the result port | Check |
| serviceCode | text | yes | SERVICE_CODE (REG, by value) | as carried by CHK; never a foreign key | Service |
| versionNumber | number | yes | service package version (REG, by value) | with serviceCode, the version the report was built on (G11) | Service version |
| fetchMode | lookup | yes | FETCH_MODE | | Document source mode |
| requestNumber | text | yes | host | exactly as the host sent it (REQ-RPT-002) | Request number |
| employeeId | text | yes | host | the employee who started the Check, exactly as sent | Employee |
| checkStatus | lookup | yes | CHECK_STATUS | forward only (RULE-RPT-003) | Status |
| startedAt | date-time | yes | CHK | | Started at |
| runningSince | date-time | no | CHK | set by the first mark RUNNING | Running since |
| endedAt | date-time | no | CHK | set when COMPLETED or FAILED | Ended at |
| overallStatus | lookup | no | OVERALL_STATUS | present exactly when COMPLETED (REQ-RPT-008, REQ-RPT-018) | Overall Status |
| comparisonModel | text | no | CHK metadata | present exactly when COMPLETED | Model used |
| failureReason | lookup | no | CHECK_FAILURE_REASON | present exactly when FAILED | Failure reason |
| failureDetail | text | no | CHK | present exactly when FAILED | Failure detail |
| employeeDecision | lookup | no | EMPLOYEE_DECISION | at most once, only on a COMPLETED Check (RULE-RPT-011, RULE-RPT-012) | Employee Decision |
| decidedBy | text | no | host, through INT | exactly as sent; present exactly when employeeDecision is | Decided by |
| decidedAt | date-time | no | system | time the decision was recorded (ADR-RPT-009) | Decided at |
| approvalApiExecuted | flag | no | INT | true when the decision was carried out through the Approval API; present exactly when employeeDecision is | Executed through Approval API |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

### ENT-RPT-002 — Finding
Kind reason: transactional — one row per condition of a completed report, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-004; [KB:raw-idea.md §9 CHECK_FINDING]; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| findingId | number (identifier) | yes | identity | | Finding |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order of the finding in the report as handed over, from 1 | Position |
| conditionText | text | yes | CHK | the condition the finding is about | Condition |
| findingOutcome | lookup | yes | FINDING_OUTCOME | | Outcome |
| evidence | text | yes | CHK | the actual value found (G10) | Evidence |
| note | text | yes | CHK | the note for the employee | Note |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

### ENT-RPT-003 — Check Document
Kind reason: transactional — one row per document outcome of a completed report, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-007; [KB:raw-idea.md §9 CHECK_DOCUMENT]; ADR-REG-001; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| checkDocumentId | number (identifier) | yes | identity | | Check Document |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order of the outcome as handed over, from 1 | Position |
| documentType | text | yes | DOCUMENT_TYPE (REG, by value) | | Document type |
| sourceMode | lookup | yes | FETCH_MODE | | Source mode |
| readStatus | lookup | yes | DOCUMENT_READ_STATUS | | Read status |
| unreadableReason | lookup | no | UNREADABLE_REASON | present exactly when readStatus is UNREADABLE (RULE-RPT-008) | Reason |
| detail | text | no | CHK / DOC | | Detail |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

No document content field exists (REQ-RPT-019).

### ENT-RPT-004 — Unread Query
Kind reason: transactional — one row per service query whose data could not be read, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-007; CON-CHK-008; ADR-CHK-014; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| unreadQueryId | number (identifier) | yes | identity | | Unread query |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order as handed over, from 1 | Position |
| queryName | text | yes | CHK | the service query's name in the service definition | Query |
| detail | text | yes | CHK | why its data was not read | Detail |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

Consumed: none. RPT consumes no entity of another module: the service code, version number, document type and every closed code arrive by value through the Check result port (CON-CHK-006, CON-CHK-008) and are stored as values; RPT never reads REG, DOC or CHK at run time (ADR-RPT-001).

## A4 — Functional requirements (EARS) and acceptance criteria

### REQ-RPT-001 — Check run created, identifier returned
  Pattern    : event
  Statement  : When the Check Engine asks to create a Check run with a service code, a version number, a fetch mode, a request number, an employee identity, an initial status and a start time, the system shall store the Check run and return its identifier.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : One row per check is where the report and the decision are kept.
  Source     : POL-RPT-001; CON-CHK-006; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-001 — [REQ-RPT-001]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run for `scholarship-request` version 3, fetch mode `path`, request number `1001`, employee `E-2041`, status RUNNING, start time 2026-10-01T09:00:00Z
  Then   : 1 Check run is stored with exactly those values and its new identifier is returned

### REQ-RPT-002 — Host identifiers kept exactly as sent
  Pattern    : ubiquitous
  Statement  : The system shall keep the request number and the employee identity of a Check run as text exactly as received, without trimming, case change or reformatting.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : The host data lives outside the service's schema; the report must show the identifiers the host knows.
  Source     : POL-RPT-002; profile `conventions.identifiers`
  Priority   : HIGH

#### AC-RPT-002 — [REQ-RPT-002]
  Given  : the Check Engine creates a Check run with request number `00-1001/A` and employee identity ` e.2041 `
  When   : the Check run is read back
  Then   : the request number is "00-1001/A" and the employee identity is " e.2041 ", unchanged

### REQ-RPT-003 — Incomplete Check run refused
  Pattern    : unwanted
  Statement  : If a Check run to be created lacks its service code, version number, fetch mode, request number, employee identity, initial status or start time, then the system shall refuse to store it.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : A Check run without these values cannot be traced to what it verified.
  Source     : POL-RPT-001; RULE-RPT-001; CON-CHK-006 "errors: not stored"
  Priority   : HIGH

#### AC-RPT-003 — [REQ-RPT-003]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run whose employee identity is blank
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: employeeId is missing."

### REQ-RPT-004 — Initial status agrees with the fetch mode
  Pattern    : unwanted
  Statement  : If a Check run to be created has the initial status AWAITING_DOCUMENTS with a fetch mode other than `manual`, or the initial status RUNNING with the fetch mode `manual`, then the system shall refuse to store it.
  Traces     : US-RPT-001, US-RPT-002
  Entities   : ENT-RPT-001
  Rationale  : Only a `manual` Check waits for documents; a contradictory record would mislead the host polling it.
  Source     : RULE-RPT-002; ADR-CHK-004; ADR-RPT-007
  Priority   : —

#### AC-RPT-004 — [REQ-RPT-004]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run with fetch mode `path` and initial status AWAITING_DOCUMENTS
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual."

#### AC-RPT-005 — [REQ-RPT-004]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run with fetch mode `manual` and initial status RUNNING
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS."

### REQ-RPT-005 — Check marked RUNNING
  Pattern    : event
  Statement  : When the Check Engine marks a Check that is AWAITING_DOCUMENTS or RUNNING as running, the system shall set its status to RUNNING and, if no running time is stored yet, store the running time received.
  Traces     : US-RPT-002
  Entities   : ENT-RPT-001
  Rationale  : The host sees that the Check's pipeline is under way.
  Source     : POL-RPT-003; CON-CHK-007; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-006 — [REQ-RPT-005]
  Given  : Check 501 is AWAITING_DOCUMENTS with no running time
  When   : the Check Engine marks Check 501 running with running time 2026-10-01T09:05:00Z
  Then   : Check 501 has status RUNNING and running time 2026-10-01T09:05:00Z

#### AC-RPT-007 — [REQ-RPT-005]
  Given  : Check 502 is RUNNING with running time 2026-10-01T09:01:00Z
  When   : the Check Engine marks Check 502 running with running time 2026-10-01T09:02:00Z
  Then   : Check 502 stays RUNNING and its running time stays 2026-10-01T09:01:00Z

### REQ-RPT-006 — Status never moves backwards
  Pattern    : unwanted
  Statement  : If a status change would move a Check out of COMPLETED or FAILED, or complete a Check that is not RUNNING, then the system shall refuse the change and keep the stored status.
  Traces     : US-RPT-002, US-RPT-006
  Entities   : ENT-RPT-001
  Rationale  : COMPLETED and FAILED are final; a Check ends exactly once.
  Source     : POL-RPT-003; RULE-RPT-003; CON-CHK-001; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-008 — [REQ-RPT-006]
  Given  : Check 503 is COMPLETED
  When   : the Check Engine marks Check 503 running
  Then   : Check 503 stays COMPLETED and the call is refused with "Check 503 has already ended; its status cannot change."

#### AC-RPT-009 — [REQ-RPT-006]
  Given  : Check 504 is AWAITING_DOCUMENTS
  When   : the Check Engine completes Check 504
  Then   : nothing is stored for Check 504, it stays AWAITING_DOCUMENTS and the call is refused with "Check 504 is not running; it cannot be completed."

### REQ-RPT-007 — Unknown Check on the result port
  Pattern    : unwanted
  Statement  : If the Check Engine marks running, completes, fails or reads a Check that has no stored Check run, then the system shall refuse the call as not found.
  Traces     : US-RPT-002, US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : A result for a Check that was never created has nowhere to go.
  Source     : CON-CHK-007, CON-CHK-009, CON-CHK-010 "errors: not found"
  Priority   : —

#### AC-RPT-010 — [REQ-RPT-007]
  Given  : no Check run 999 exists
  When   : the Check Engine fails Check 999
  Then   : nothing is stored and the call is refused with "Check 999 was not found."

### REQ-RPT-008 — Completed report stored
  Pattern    : event
  Statement  : When the Check Engine completes a RUNNING Check, the system shall store its status COMPLETED, its Overall Status, its comparison model, its end time, every finding, every document outcome and every unread service query.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The report has a fixed structure for every service and is stored as data.
  Source     : POL-RPT-004; CON-CHK-008; [KB:raw-idea.md §7]; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-011 — [REQ-RPT-008]
  Given  : Check 505 is RUNNING
  When   : the Check Engine completes it with Overall Status NOT_COMPLIANT, comparison model `gemini-flash-lite`, end time 2026-10-01T09:10:00Z, 3 findings, 2 document outcomes and 1 unread query
  Then   : Check 505 is COMPLETED with Overall Status NOT_COMPLIANT, model `gemini-flash-lite`, end time 2026-10-01T09:10:00Z, and 3 Findings, 2 Check Documents and 1 Unread Query are stored for it

### REQ-RPT-009 — A report is stored whole or not at all
  Pattern    : unwanted
  Statement  : If any part of a completed report cannot be stored, then the system shall store no part of it, keep the Check RUNNING and refuse the call as not stored.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A half-stored report would show a result without the findings that justify it; the Check Engine then fails the Check INTERNAL_ERROR.
  Source     : POL-RPT-004; CON-CHK-008 "errors: not stored (CHK then fails the Check with INTERNAL_ERROR)"; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-012 — [REQ-RPT-009]
  Given  : Check 506 is RUNNING
  When   : the Check Engine completes it with 3 findings of which the third has outcome `PASSED`
  Then   : Check 506 stays RUNNING with no Overall Status, 0 Findings, 0 Check Documents and 0 Unread Queries are stored for it, and the call is refused as not stored

### REQ-RPT-010 — Report order kept
  Pattern    : ubiquitous
  Statement  : The system shall keep the findings, the document outcomes and the unread service queries of a report in the order in which the Check Engine handed them over.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The employee reads the report in the order the conditions were assessed.
  Source     : POL-RPT-004; [KB:raw-idea.md §7]
  Priority   : —

#### AC-RPT-013 — [REQ-RPT-010]
  Given  : Check 507 is completed with findings on conditions "GPA at least 3.0", "TRANSCRIPT present", "ID_CARD present" in that order
  When   : the report of Check 507 is read
  Then   : the findings are returned at positions 1, 2, 3 in that same order

### REQ-RPT-011 — Missing and unreadable documents kept with their reason
  Pattern    : ubiquitous
  Statement  : The system shall keep every document outcome of a completed report, including each MISSING document and each UNREADABLE document with its reason and detail.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-003
  Rationale  : Anything that could not be read appears in the report; it is never skipped silently.
  Source     : POL-RPT-007; [KB:raw-idea.md §7, §12]; domain-profile §5 G6; ADR-DOC-002, ADR-DOC-007
  Priority   : HIGH

#### AC-RPT-014 — [REQ-RPT-011]
  Given  : Check 508 is RUNNING
  When   : the Check Engine completes it with document outcomes TRANSCRIPT READ, ID_CARD MISSING and a second TRANSCRIPT UNREADABLE with reason TOO_LARGE and detail "file of 31 MB exceeds 20 MB"
  Then   : 3 Check Documents are stored for Check 508, the third with reason TOO_LARGE and detail "file of 31 MB exceeds 20 MB"

### REQ-RPT-012 — Unread service queries kept
  Pattern    : ubiquitous
  Statement  : The system shall keep every unread service query of a completed report with its query name and detail.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-004
  Rationale  : Data that could not be read must stay visible beside the result it weakened.
  Source     : POL-RPT-007; CON-CHK-008; ADR-CHK-014; domain-profile §5 G6
  Priority   : HIGH

#### AC-RPT-015 — [REQ-RPT-012]
  Given  : Check 509 is RUNNING
  When   : the Check Engine completes it with Overall Status NEEDS_MANUAL_REVIEW and unread query `request_details` with detail "more than 500 rows"
  Then   : 1 Unread Query `request_details` with detail "more than 500 rows" is stored for Check 509

### REQ-RPT-013 — Metadata agrees with the Check run
  Pattern    : unwanted
  Statement  : If the metadata of a completed report lacks the comparison model or the end time, names a service code, version number, fetch mode, employee identity or start time different from the stored Check run, or ends before the Check started, then the system shall refuse to store the report.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001
  Rationale  : A report whose metadata contradicts its Check run cannot be traced to what produced it.
  Source     : RULE-RPT-004; [KB:raw-idea.md §7] metadata; domain-profile §5 G11; ADR-RPT-007
  Priority   : —

#### AC-RPT-016 — [REQ-RPT-013]
  Given  : Check 510 is RUNNING for `scholarship-request` version 3
  When   : the Check Engine completes it with metadata version number 4
  Then   : nothing is stored for Check 510 and the call is refused with "The report of Check 510 was not stored: its metadata versionNumber 4 differs from the Check run (3)."

### REQ-RPT-014 — COMPLIANT only with every finding satisfied
  Pattern    : unwanted
  Statement  : If a completed report has the Overall Status COMPLIANT while any of its findings is not SATISFIED or any service query was not read, then the system shall refuse to store the report.
  Traces     : US-RPT-003, US-RPT-004
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-004
  Rationale  : A stored report must never claim more than was verified.
  Source     : RULE-RPT-005; CON-CHK-001 derivation; domain-profile §5 G6; ADR-RPT-007
  Priority   : HIGH

#### AC-RPT-017 — [REQ-RPT-014]
  Given  : Check 511 is RUNNING
  When   : the Check Engine completes it with Overall Status COMPLIANT and a finding "ID_CARD present" with outcome NOT_SATISFIED
  Then   : nothing is stored for Check 511 and the call is refused with "The report of Check 511 was not stored: COMPLIANT needs every finding SATISFIED and every service query read."

### REQ-RPT-015 — Only the codes of the closed lists
  Pattern    : unwanted
  Statement  : If a Check status, Overall Status, finding outcome, failure reason, document read status, unreadable reason or fetch mode received is not a code of its closed list, then the system shall refuse to store it.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003
  Rationale  : The closed lists are owned by the service; a code outside them is meaningless to every reader.
  Source     : POL-RPT-005; RULE-RPT-006; CON-CHK-001 … CON-CHK-003, CON-DOC-001, CON-DOC-002
  Priority   : —

#### AC-RPT-018 — [REQ-RPT-015]
  Given  : Check 512 is RUNNING
  When   : the Check Engine fails it with failure reason `CRASHED`
  Then   : Check 512 stays RUNNING and the call is refused with "Not stored: `CRASHED` is not a code of CHECK_FAILURE_REASON."

### REQ-RPT-016 — A finding is complete
  Pattern    : unwanted
  Statement  : If a finding of a completed report lacks its condition, outcome, evidence or note, then the system shall refuse to store the report.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-002
  Rationale  : Every finding carries its evidence so the employee can verify it.
  Source     : POL-RPT-004; RULE-RPT-007; CON-CHK-002; domain-profile §5 G10
  Priority   : HIGH

#### AC-RPT-019 — [REQ-RPT-016]
  Given  : Check 513 is RUNNING
  When   : the Check Engine completes it with a finding "GPA at least 3.0" whose evidence is blank
  Then   : nothing is stored for Check 513 and the call is refused with "The report of Check 513 was not stored: finding 1 has no evidence."

### REQ-RPT-017 — Reason exactly on UNREADABLE documents
  Pattern    : unwanted
  Statement  : If a document outcome is UNREADABLE without a reason, or READ or MISSING with a reason, then the system shall refuse to store the report.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-003
  Rationale  : The reason tells the employee why a document could not be read; it means nothing on a document that was read or absent.
  Source     : RULE-RPT-008; CON-DOC-001
  Priority   : —

#### AC-RPT-020 — [REQ-RPT-017]
  Given  : Check 514 is RUNNING
  When   : the Check Engine completes it with a document outcome ID_CARD UNREADABLE with no reason
  Then   : nothing is stored for Check 514 and the call is refused with "The report of Check 514 was not stored: document 1 is UNREADABLE without a reason."

### REQ-RPT-018 — Failed Check stored with its reason
  Pattern    : event
  Statement  : When the Check Engine fails a Check that is AWAITING_DOCUMENTS or RUNNING, the system shall store its status FAILED, its failure reason, its detail and its end time, with no Overall Status and no findings.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001
  Rationale  : A failed Check must be visible as failed, never with a result the pipeline did not reach.
  Source     : POL-RPT-006; CON-CHK-009; CON-CHK-003; ADR-CHK-005
  Priority   : HIGH

#### AC-RPT-021 — [REQ-RPT-018]
  Given  : Check 515 is RUNNING
  When   : the Check Engine fails it with reason TIMED_OUT, detail "Check exceeded 300 s", end time 2026-10-01T09:06:00Z
  Then   : Check 515 is FAILED with reason TIMED_OUT, detail "Check exceeded 300 s", end time 2026-10-01T09:06:00Z, no Overall Status and 0 Findings

### REQ-RPT-019 — No document content or query results kept
  Pattern    : ubiquitous
  Statement  : The system shall keep no document content and no query result rows in a stored report beyond the condition, evidence, note, outcome and detail texts the Check Engine hands over.
  Traces     : US-RPT-005
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The report needs the evidence the employee verifies, not a copy of the request's files and data.
  Source     : POL-RPT-008; CON-CHK-008 "Carries no document content"; ADR-DOC-008
  Priority   : —

#### AC-RPT-022 — [REQ-RPT-019]
  Given  : Check 516 is completed with a document outcome TRANSCRIPT READ
  When   : the Check Document of TRANSCRIPT is read
  Then   : it holds document type, source mode, read status and detail only — no field holds the transcript's text or file

### REQ-RPT-020 — An ended report never changes
  Pattern    : state
  Statement  : While a Check is COMPLETED or FAILED, the system shall refuse every change to its status, result, failure, findings, Check Documents, unread queries and metadata.
  Traces     : US-RPT-006
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The decision is measured against the report the employee saw.
  Source     : POL-RPT-009; RULE-RPT-003; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-023 — [REQ-RPT-020]
  Given  : Check 517 is COMPLETED with Overall Status NOT_COMPLIANT and 3 findings
  When   : the Check Engine completes Check 517 again with Overall Status COMPLIANT
  Then   : Check 517 keeps Overall Status NOT_COMPLIANT and its 3 findings, and the call is refused with "Check 517 has already ended; its status cannot change."

### REQ-RPT-021 — One Check read for the Check Engine
  Pattern    : event
  Statement  : When the Check Engine reads one Check, the system shall return its identifier, status, service code, version number, fetch mode, request number, employee identity and start time.
  Traces     : US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : The Check Engine resumes a `manual` Check on the version it recorded.
  Source     : CON-CHK-010; POL-RPT-010
  Priority   : —

#### AC-RPT-024 — [REQ-RPT-021]
  Given  : Check 518 is AWAITING_DOCUMENTS for `scholarship-request` version 3, fetch mode `manual`, request `1001`, employee `E-2041`
  When   : the Check Engine reads Check 518
  Then   : it receives 518, AWAITING_DOCUMENTS, `scholarship-request`, 3, `manual`, `1001`, `E-2041` and the start time

### REQ-RPT-022 — Unfinished Checks listed
  Pattern    : event
  Statement  : When the Check Engine asks for the unfinished Checks, the system shall return the identifier, status and start time of every Check that is AWAITING_DOCUMENTS or RUNNING, oldest first.
  Traces     : US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : Checks interrupted by a restart or whose upload window elapsed must be ended.
  Source     : POL-RPT-010; CON-CHK-011; ADR-CHK-005
  Priority   : —

#### AC-RPT-025 — [REQ-RPT-022]
  Given  : Checks 519 (RUNNING, started 09:00), 520 (AWAITING_DOCUMENTS, started 08:30) and 521 (COMPLETED) exist
  When   : the Check Engine asks for the unfinished Checks
  Then   : it receives 520 then 519, and not 521

#### AC-RPT-026 — [REQ-RPT-022]
  Given  : every stored Check is COMPLETED or FAILED
  When   : the Check Engine asks for the unfinished Checks
  Then   : it receives an empty list

### REQ-RPT-023 — A Check's status and report read
  Pattern    : event
  Statement  : When a host system or the employee frontend reads a Check, the system shall return its identifier, status, service code, version number, fetch mode, request number, employee identity and times and, once it is COMPLETED, its Overall Status, model used, findings, Check Documents, unread queries and Employee Decision.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The host polls the status; the employee reads the whole report.
  Source     : POL-RPT-011; [KB:raw-idea.md §8] `GET /checks/{id}`; §15 A1; ADR-RPT-005, ADR-RPT-006
  Priority   : HIGH

#### AC-RPT-027 — [REQ-RPT-023]
  Given  : Check 522 is RUNNING
  When   : the employee frontend reads Check 522
  Then   : it receives status RUNNING with the Check's service, version, request and times, and no Overall Status, findings or documents

#### AC-RPT-028 — [REQ-RPT-023]
  Given  : Check 523 is COMPLETED with Overall Status NOT_COMPLIANT, 3 findings, 2 Check Documents and 0 unread queries, with no decision
  When   : the employee frontend reads Check 523
  Then   : it receives status COMPLETED, Overall Status NOT_COMPLIANT, the model used, 3 findings, 2 documents, 0 unread queries and no Employee Decision

### REQ-RPT-024 — Each finding returned with its evidence
  Pattern    : ubiquitous
  Statement  : The system shall return every finding of a report as one entry holding its condition, outcome, evidence and note together.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-002
  Rationale  : Every finding carries its evidence so the employee can verify it.
  Source     : POL-RPT-011; [KB:raw-idea.md §7]; domain-profile §5 G10
  Priority   : HIGH

#### AC-RPT-029 — [REQ-RPT-024]
  Given  : Check 524 is completed with finding "GPA at least 3.0", outcome NOT_SATISFIED, evidence "GPA = 2.7", note "Below the 3.0 minimum"
  When   : the report of Check 524 is read
  Then   : one finding entry holds "GPA at least 3.0", NOT_SATISFIED, "GPA = 2.7" and "Below the 3.0 minimum"

### REQ-RPT-025 — Unknown Check read
  Pattern    : unwanted
  Statement  : If a host system or the employee frontend reads a Check that has no stored Check run, then the system shall answer that the Check was not found.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001
  Rationale  : The caller must tell a missing Check from a running one; a purged Check is also not found.
  Source     : POL-RPT-011; profile `error_envelope` ProblemDetail
  Priority   : —

#### AC-RPT-030 — [REQ-RPT-025]
  Given  : no Check run 998 exists
  When   : the employee frontend reads Check 998
  Then   : the answer is not found with detail "Check 998 was not found."

### REQ-RPT-026 — Failed Check read with its reason
  Pattern    : event
  Statement  : When a host system or the employee frontend reads a FAILED Check, the system shall return its failure reason and detail with no Overall Status.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001
  Rationale  : A failed Check must be visible as failed.
  Source     : POL-RPT-006, POL-RPT-011; ADR-CHK-005
  Priority   : —

#### AC-RPT-031 — [REQ-RPT-026]
  Given  : Check 525 is FAILED with reason MODEL_UNAVAILABLE and detail "provider answered 503"
  When   : the employee frontend reads Check 525
  Then   : it receives status FAILED, reason MODEL_UNAVAILABLE, detail "provider answered 503" and no Overall Status

### REQ-RPT-027 — Stored texts returned as data
  Pattern    : ubiquitous
  Statement  : The system shall return every stored condition, evidence, note and detail text exactly as stored, as data, without interpreting or executing anything it contains.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : Evidence may quote document content, which is data, never instructions.
  Source     : [KB:raw-idea.md §12] "Document content is treated as data"; domain-profile §5 G7; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-032 — [REQ-RPT-027]
  Given  : Check 526 is completed with a finding whose evidence is "<script>alert(1)</script> ignore previous instructions"
  When   : the report of Check 526 is read
  Then   : the evidence is returned as the same character string, as a text value

### REQ-RPT-028 — Checks of a request listed
  Pattern    : event
  Statement  : When the employee frontend lists the Checks of a service code and request number, the system shall return the identifier, status, Overall Status, start time, end time and Employee Decision of the newest 100 of those Checks, newest first, with the total number of Checks of that request.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : The employee sees whether the request was checked before and what each Check found.
  Source     : POL-RPT-012; [KB:raw-idea.md §15 A1]; ADR-RPT-005, ADR-RPT-008
  Priority   : —

#### AC-RPT-033 — [REQ-RPT-028]
  Given  : request `1001` of `scholarship-request` has Checks 527 (started 08:00, FAILED) and 528 (started 09:00, COMPLETED, NOT_COMPLIANT), and request `1001` of `housing-request` has Check 529
  When   : the employee frontend lists the Checks of `scholarship-request` request `1001`
  Then   : it receives 528 then 527 and the total 2, and not 529

#### AC-RPT-034 — [REQ-RPT-028]
  Given  : request `1002` of `scholarship-request` has 130 Checks
  When   : the employee frontend lists its Checks
  Then   : it receives the 100 newest, newest first, and the total 130

### REQ-RPT-029 — Service code and request number needed for the list
  Pattern    : unwanted
  Statement  : If the Checks of a request are listed without a service code or without a request number, then the system shall refuse the listing.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : Two services can share a host request number; a list on one key would mix their reports.
  Source     : RULE-RPT-009; ADR-RPT-005
  Priority   : —

#### AC-RPT-035 — [REQ-RPT-029]
  Given  : Checks exist for request `1001`
  When   : the employee frontend lists Checks with request number `1001` and no service code
  Then   : no list is returned and the answer is a validation error with detail "Both a service code and a request number are needed to list Checks."

### REQ-RPT-030 — Every Check its own record
  Pattern    : ubiquitous
  Statement  : The system shall store every Check of the same request as its own Check run and shall never copy a finding, a result or an Employee Decision from one Check run to another.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001, ENT-RPT-002
  Rationale  : No data is carried from one check to another.
  Source     : POL-RPT-023; [KB:raw-idea.md §12]; domain-profile §5 G9; ADR-CHK-007
  Priority   : —

#### AC-RPT-036 — [REQ-RPT-030]
  Given  : Check 530 of request `1001` is COMPLETED with 3 findings and decision APPROVED
  When   : the Check Engine creates a new Check run for request `1001` of the same service
  Then   : a new Check run with a new identifier is stored with no findings and no Employee Decision, and Check 530 is unchanged

### REQ-RPT-031 — Bounded list of a request's Checks
  Pattern    : ubiquitous
  Statement  : The system shall return at most 100 Checks in one listing of the Checks of a request.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : Every read has a limit; the total number keeps the cut visible, never silent.
  Source     : [KB:raw-idea.md §12] "Each check has limits"; domain-profile §5 G8; ADR-RPT-008
  Priority   : —

#### AC-RPT-037 — [REQ-RPT-031]
  Given  : request `1003` of `scholarship-request` has 101 Checks
  When   : the employee frontend lists its Checks
  Then   : exactly 100 entries are returned with the total 101

### REQ-RPT-032 — Employee Decision recorded
  Pattern    : event
  Statement  : When Host Integration hands over an Employee Decision on a COMPLETED Check with no decision, the system shall record the decision, the deciding employee's identity exactly as sent, whether it was executed through the Approval API and the recording time.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : Keeping the decision beside the result shows where the two disagree.
  Source     : POL-RPT-013; [KB:raw-idea.md §9, §11]; ADR-RPT-003, ADR-RPT-009
  Priority   : HIGH

#### AC-RPT-038 — [REQ-RPT-032]
  Given  : Check 531 is COMPLETED with Overall Status COMPLIANT and no decision
  When   : Host Integration hands over decision APPROVED by employee `E-3307`, not executed through the Approval API
  Then   : Check 531 holds decision APPROVED, decided by "E-3307", executed through Approval API false, and a recording time; its Overall Status and findings are unchanged

### REQ-RPT-033 — One decision per Check
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that already has one, then the system shall refuse it and keep the decision already recorded.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The decision is a fact about one report; a later change belongs to the host system's own record.
  Source     : POL-RPT-014; RULE-RPT-011; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-039 — [REQ-RPT-033]
  Given  : Check 532 is COMPLETED with decision REJECTED by `E-3307`
  When   : Host Integration hands over decision APPROVED by `E-4410`
  Then   : Check 532 keeps decision REJECTED by `E-3307` and the call is refused with "Check 532 already has an Employee Decision."

### REQ-RPT-034 — Decisions only on a completed Check
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that is not COMPLETED, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : A running or failed Check has no result to stand beside.
  Source     : POL-RPT-015; RULE-RPT-012; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-040 — [REQ-RPT-034]
  Given  : Check 533 is FAILED
  When   : Host Integration hands over decision APPROVED by `E-3307`
  Then   : no decision is recorded and the call is refused with "Check 533 is not completed; a decision can only be recorded on a completed Check."

### REQ-RPT-035 — A decision is complete
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over without a decision code of EMPLOYEE_DECISION, without the deciding employee's identity or without saying whether it was executed through the Approval API, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The record must show what was decided, by whom and how.
  Source     : POL-RPT-013, POL-RPT-016; RULE-RPT-013; ADR-RPT-009
  Priority   : —

#### AC-RPT-041 — [REQ-RPT-035]
  Given  : Check 534 is COMPLETED with no decision
  When   : Host Integration hands over decision `MAYBE` by `E-3307`
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: `MAYBE` is not APPROVED or REJECTED."

#### AC-RPT-042 — [REQ-RPT-035]
  Given  : Check 535 is COMPLETED with no decision
  When   : Host Integration hands over decision APPROVED with a blank employee identity
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: the deciding employee is missing."

### REQ-RPT-036 — Execution through the Approval API recorded
  Pattern    : optional
  Statement  : Where an Employee Decision was executed through the service's Approval API, the system shall record it as executed through the Approval API.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The record shows which approvals the service carried out and on which report.
  Source     : POL-RPT-016; [KB:raw-idea.md §11]; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-043 — [REQ-RPT-036]
  Given  : Check 536 is COMPLETED with no decision
  When   : Host Integration hands over decision APPROVED by `E-3307`, executed through the Approval API
  Then   : Check 536 holds decision APPROVED with executed through Approval API true

### REQ-RPT-037 — The Report Store never approves
  Pattern    : ubiquitous
  Statement  : The system shall never call an Approval API and never set an Employee Decision other than one handed over by Host Integration.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The employee stays the decision maker; approval happens only as a result of the employee's action.
  Source     : POL-RPT-018; [KB:raw-idea.md §12]; domain-profile §5 G2; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-044 — [REQ-RPT-037]
  Given  : Check 537 is completed with Overall Status COMPLIANT for a service whose Approval API is enabled
  When   : 24 hours pass with no decision handed over
  Then   : Check 537 has no Employee Decision and the Report Store has sent 0 calls to any Approval API

### REQ-RPT-038 — Unknown Check for a decision
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that has no stored Check run, then the system shall refuse it as not found.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : A decision must stand beside an existing report.
  Source     : POL-RPT-013; ADR-RPT-003
  Priority   : —

#### AC-RPT-045 — [REQ-RPT-038]
  Given  : no Check run 997 exists
  When   : Host Integration hands over decision APPROVED by `E-3307` for Check 997
  Then   : nothing is recorded and the call is refused with "Check 997 was not found."

### REQ-RPT-039 — Approval API execution only with an approval
  Pattern    : unwanted
  Statement  : If an Employee Decision REJECTED is handed over as executed through the Approval API, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The Approval API carries out approvals; a rejection is never executed through it.
  Source     : RULE-RPT-014; [KB:raw-idea.md §11]; ADR-RPT-009
  Priority   : —

#### AC-RPT-046 — [REQ-RPT-039]
  Given  : Check 538 is COMPLETED with no decision
  When   : Host Integration hands over decision REJECTED by `E-3307`, executed through the Approval API
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: only an APPROVED decision is executed through the Approval API."

### REQ-RPT-040 — Decision agreement of a service
  Pattern    : event
  Statement  : When the decision agreement of a service code is read, the system shall return, for each service package version with a decided Check, the number of decided Checks for each pair of Overall Status and Employee Decision.
  Traces     : US-RPT-011
  Entities   : ENT-RPT-001
  Rationale  : Where the result and the decision disagree, the service knowledge or a check needs attention.
  Source     : POL-RPT-017; [KB:raw-idea.md §9]; ADR-RPT-005
  Priority   : —

#### AC-RPT-047 — [REQ-RPT-040]
  Given  : `scholarship-request` version 3 has decided Checks: 4 COMPLIANT + APPROVED, 1 COMPLIANT + REJECTED, 2 NOT_COMPLIANT + REJECTED; version 2 has 1 NEEDS_MANUAL_REVIEW + APPROVED; and 3 undecided Checks exist
  When   : the decision agreement of `scholarship-request` is read
  Then   : it returns version 3: (COMPLIANT, APPROVED) 4, (COMPLIANT, REJECTED) 1, (NOT_COMPLIANT, REJECTED) 2; version 2: (NEEDS_MANUAL_REVIEW, APPROVED) 1; the undecided Checks are not counted

#### AC-RPT-048 — [REQ-RPT-040]
  Given  : no decided Check exists for `housing-request`
  When   : the decision agreement of `housing-request` is read
  Then   : it returns an empty list

### REQ-RPT-041 — Service code needed for the decision agreement
  Pattern    : unwanted
  Statement  : If the decision agreement is read without a service code, then the system shall refuse the read.
  Traces     : US-RPT-011
  Entities   : ENT-RPT-001
  Rationale  : The measure is per service; mixing services hides where attention is needed.
  Source     : RULE-RPT-015; ADR-RPT-005
  Priority   : —

#### AC-RPT-049 — [REQ-RPT-041]
  Given  : decided Checks exist
  When   : the decision agreement is read with no service code
  Then   : nothing is returned and the answer is a validation error with detail "A service code is needed to read the decision agreement."

### REQ-RPT-042 — Reports kept for the retention period
  Pattern    : ubiquitous
  Statement  : The system shall keep every Check run, with its findings, Check Documents, unread queries and Employee Decision, until it has been ended for longer than the report retention period of the platform configuration.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A report is kept as long as the host keeps the request it verified.
  Source     : POL-RPT-019; domain-profile §8 D4; ADR-RPT-004
  Priority   : —

#### AC-RPT-050 — [REQ-RPT-042]
  Given  : the report retention period is 365 days and Check 539 ended 364 days ago
  When   : the purge runs
  Then   : Check 539 and all its records are still stored

### REQ-RPT-043 — Purge of expired Check runs
  Pattern    : event
  Statement  : When the purge runs on the schedule of the platform configuration, the system shall permanently delete every Check run that has been ended for longer than the report retention period, together with its findings, Check Documents and unread queries.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A purge removes older runs with their findings and documents by hard delete.
  Source     : POL-RPT-020; domain-profile §8 D4; profile `delete_semantics: hard`; ADR-RPT-004, ADR-RPT-010
  Priority   : —

#### AC-RPT-051 — [REQ-RPT-043]
  Given  : the report retention period is 365 days and Check 540 ended 366 days ago with 3 findings, 2 Check Documents, 1 unread query and decision APPROVED
  When   : the purge runs
  Then   : Check 540, its 3 Findings, 2 Check Documents and 1 Unread Query no longer exist, and reading Check 540 answers not found

### REQ-RPT-044 — No retention period, no purge
  Pattern    : unwanted
  Statement  : If the report retention period is not configured or is not a whole number of days greater than zero, then the system shall delete no Check run when the purge runs and shall log that the purge was skipped.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : A missing or wrong setting must never destroy records.
  Source     : POL-RPT-021; ADR-RPT-004, ADR-RPT-010
  Priority   : —

#### AC-RPT-052 — [REQ-RPT-044]
  Given  : no report retention period is configured and Check 541 ended 1000 days ago
  When   : the purge runs
  Then   : Check 541 is still stored and the log holds "Report purge skipped: no valid report retention period is configured."

### REQ-RPT-045 — Unfinished Checks never purged
  Pattern    : unwanted
  Statement  : If a Check is AWAITING_DOCUMENTS or RUNNING, then the system shall not delete it when the purge runs, whatever its start time.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : The Check Engine still writes to an unfinished Check and ends it.
  Source     : POL-RPT-022; CON-CHK-011; ADR-RPT-004
  Priority   : —

#### AC-RPT-053 — [REQ-RPT-045]
  Given  : the report retention period is 30 days and Check 542 is RUNNING, started 40 days ago
  When   : the purge runs
  Then   : Check 542 is still stored

### REQ-RPT-046 — Purge outcome logged
  Pattern    : event
  Statement  : When a purge run ends, the system shall log the number of Check runs it deleted and the cut-off time it applied.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : A deletion of records must leave a trace of how much was removed and on what basis.
  Source     : POL-RPT-020; ADR-RPT-010
  Priority   : —

#### AC-RPT-054 — [REQ-RPT-046]
  Given  : the report retention period is 365 days and 7 Check runs ended more than 365 days ago
  When   : the purge runs at 2026-10-02T02:00:00Z
  Then   : the log holds "Report purge deleted 7 Check runs ended before 2025-10-02T02:00:00Z."

### REQ-RPT-047 — No access to host data
  Pattern    : ubiquitous
  Statement  : The system shall run no query on host data and hold no connection to a host database.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : All access to host data is read-only and belongs to the Check Engine and Document Access; the Report Store has no need of it.
  Source     : [KB:raw-idea.md §12] read-only host access; domain-profile §5 G3; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-055 — [REQ-RPT-047]
  Given  : the service runs with a Report Store and an activated `main-db` connection
  When   : the Report Store's operations are exercised end to end
  Then   : 0 queries are sent through any host connection by the Report Store

### REQ-RPT-048 — No file opened
  Pattern    : ubiquitous
  Statement  : The system shall open no file of host storage and no uploaded file.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : File paths are validated by Document Access; the Report Store keeps no document and has no path to open.
  Source     : [KB:raw-idea.md §12] storage root; domain-profile §5 G5; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-056 — [REQ-RPT-048]
  Given  : a completed report holds a Check Document whose detail names the path "/data/att/1001/t.pdf"
  When   : the report is read
  Then   : the path is returned as text and no file is opened

### REQ-RPT-049 — No model call
  Pattern    : ubiquitous
  Statement  : The system shall call no model and give no model a tool or any access to stored reports.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : The LLM analyses and summarises inside the Check Engine only; it never reaches the record.
  Source     : [KB:raw-idea.md §12] "The LLM analyses and summarises"; domain-profile §5 G1; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-057 — [REQ-RPT-049]
  Given  : a comparison model is configured for the service
  When   : a Check run is created, completed, read, decided and purged
  Then   : the Report Store sends 0 requests to any model

### REQ-RPT-050 — Read filters bound as parameters
  Pattern    : ubiquitous
  Statement  : The system shall pass the service code, the request number and the Check identifier of every read to its own store as bound parameters, never as part of the query text.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : SQL is never built from free text, including the service's own store.
  Source     : [KB:raw-idea.md §12] "Query parameters are bound"; domain-profile §5 G4; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-058 — [REQ-RPT-050]
  Given  : a Check of request `1001` exists
  When   : the employee frontend lists the Checks of `scholarship-request` and request number `1001' OR '1'='1`
  Then   : 0 Checks and the total 0 are returned, and no other request's Check is returned

### REQ-RPT-051 — Incomplete failure refused
  Pattern    : unwanted
  Statement  : If a failure handed over by the Check Engine lacks its failure reason, its detail or its end time, then the system shall refuse to store it.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001
  Rationale  : Every FAILED Check carries exactly one reason and a detail text.
  Source     : POL-RPT-006; RULE-RPT-010; CON-CHK-003
  Priority   : —

#### AC-RPT-059 — [REQ-RPT-051]
  Given  : Check 543 is RUNNING
  When   : the Check Engine fails it with reason INTERNAL_ERROR and a blank detail
  Then   : Check 543 stays RUNNING and the call is refused with "The failure of Check 543 was not stored: detail is missing."

### REQ-RPT-052 — Purge deletes each Check run whole
  Pattern    : unwanted
  Statement  : If the deletion of any record of an expired Check run fails during the purge, then the system shall keep that Check run with all its records and continue with the next expired Check run.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A report is removed completely or not at all; one failure must not stop the purge.
  Source     : POL-RPT-020; ADR-RPT-010
  Priority   : —

#### AC-RPT-060 — [REQ-RPT-052]
  Given  : Checks 544 and 545 have expired and the deletion of a Finding of Check 544 fails
  When   : the purge runs
  Then   : Check 544 is still stored with all its records, Check 545 no longer exists, and the log counts 1 deleted Check run

## A5 — Business rules

### RULE-RPT-001 — A Check run is complete
  Scope      : ENT-RPT-001
  Trigger    : on create Check run
  Statement  : The system shall require a service code, a version number, a fetch mode, a request number, an employee identity, an initial status and a start time, each present and not blank, to create a Check run.
  Message    : The Check run was not stored: {field} is missing.
  Traces     : REQ-RPT-003
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.versionNumber, ENT-RPT-001.fetchMode, ENT-RPT-001.requestNumber, ENT-RPT-001.employeeId, ENT-RPT-001.checkStatus, ENT-RPT-001.startedAt
  Source     : POL-RPT-001; CON-CHK-006

### RULE-RPT-002 — Initial status agrees with the fetch mode
  Scope      : ENT-RPT-001
  Trigger    : on create Check run
  Statement  : The system shall require the initial status AWAITING_DOCUMENTS when the fetch mode is `manual` and RUNNING when it is `path` or `blob`.
  Message    : The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual. / The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS.
  Traces     : REQ-RPT-004
  Data source: ENT-RPT-001.fetchMode, ENT-RPT-001.checkStatus
  Source     : ADR-CHK-004; ADR-RPT-007

### RULE-RPT-003 — Status moves forward only
  Scope      : ENT-RPT-001
  Trigger    : on mark RUNNING, complete, fail
  Statement  : The system shall allow only the changes AWAITING_DOCUMENTS → RUNNING, RUNNING → RUNNING, RUNNING → COMPLETED, AWAITING_DOCUMENTS → FAILED and RUNNING → FAILED, and shall prevent every change from COMPLETED or FAILED.
  Message    : Check {checkId} has already ended; its status cannot change. / Check {checkId} is not running; it cannot be completed.
  Traces     : REQ-RPT-005, REQ-RPT-006, REQ-RPT-020
  Data source: ENT-RPT-001.checkStatus
  Source     : POL-RPT-003, POL-RPT-009; CON-CHK-001; ADR-RPT-002
  Test-Hint  : walk every pair of the four statuses

### RULE-RPT-004 — Report metadata agrees with its Check run
  Scope      : ENT-RPT-001
  Trigger    : on complete
  Statement  : The system shall require the report metadata to carry a comparison model and an end time not earlier than the start time, and its service code, version number, fetch mode, employee identity and start time to equal those stored on the Check run.
  Message    : The report of Check {checkId} was not stored: its metadata {field} {value} differs from the Check run ({stored}). / The report of Check {checkId} was not stored: {field} is missing.
  Traces     : REQ-RPT-013
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.versionNumber, ENT-RPT-001.fetchMode, ENT-RPT-001.employeeId, ENT-RPT-001.startedAt, ENT-RPT-001.comparisonModel, ENT-RPT-001.endedAt
  Source     : POL-RPT-004; domain-profile §5 G11; ADR-RPT-007

### RULE-RPT-005 — COMPLIANT only when fully verified
  Scope      : ENT-RPT-001
  Trigger    : on complete
  Statement  : The system shall prevent storing the Overall Status COMPLIANT when any finding of the report is not SATISFIED or the report has any unread service query.
  Message    : The report of Check {checkId} was not stored: COMPLIANT needs every finding SATISFIED and every service query read.
  Traces     : REQ-RPT-014
  Data source: ENT-RPT-001.overallStatus, ENT-RPT-002.findingOutcome, ENT-RPT-004.queryName
  Source     : CON-CHK-001; domain-profile §5 G6; ADR-RPT-007

### RULE-RPT-006 — Closed codes only
  Scope      : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003
  Trigger    : on create Check run, complete, fail
  Statement  : The system shall require every Check status, Overall Status, failure reason, fetch mode, source mode, finding outcome, document read status and unreadable reason to be a code of its closed list in A6.
  Message    : Not stored: `{value}` is not a code of {lookupKey}.
  Traces     : REQ-RPT-015
  Data source: ENT-RPT-001.checkStatus, ENT-RPT-001.overallStatus, ENT-RPT-001.failureReason, ENT-RPT-001.fetchMode, ENT-RPT-002.findingOutcome, ENT-RPT-003.sourceMode, ENT-RPT-003.readStatus, ENT-RPT-003.unreadableReason
  Source     : POL-RPT-005; CON-CHK-001 … CON-CHK-003; CON-DOC-001, CON-DOC-002

### RULE-RPT-007 — A finding is complete
  Scope      : ENT-RPT-002
  Trigger    : on complete
  Statement  : The system shall require every finding to carry a condition, an outcome, an evidence and a note, each present and not blank.
  Message    : The report of Check {checkId} was not stored: finding {position} has no {field}.
  Traces     : REQ-RPT-016
  Data source: ENT-RPT-002.conditionText, ENT-RPT-002.findingOutcome, ENT-RPT-002.evidence, ENT-RPT-002.note
  Source     : POL-RPT-004; CON-CHK-002; domain-profile §5 G10

### RULE-RPT-008 — Reason exactly on UNREADABLE
  Scope      : ENT-RPT-003
  Trigger    : on complete
  Statement  : The system shall require an unreadable reason on every UNREADABLE document outcome and prevent one on a READ or MISSING outcome, and shall require a document type and source mode on every outcome.
  Message    : The report of Check {checkId} was not stored: document {position} is UNREADABLE without a reason. / The report of Check {checkId} was not stored: document {position} is {readStatus} and cannot carry a reason.
  Traces     : REQ-RPT-017
  Data source: ENT-RPT-003.readStatus, ENT-RPT-003.unreadableReason, ENT-RPT-003.documentType, ENT-RPT-003.sourceMode
  Source     : CON-DOC-001

### RULE-RPT-009 — A request's Checks need both keys
  Scope      : ENT-RPT-001
  Trigger    : on list Checks of a request
  Statement  : The system shall require a service code and a request number, both present and not blank, to list the Checks of a request.
  Message    : Both a service code and a request number are needed to list Checks.
  Traces     : REQ-RPT-029
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.requestNumber
  Source     : ADR-RPT-005

### RULE-RPT-010 — A failure is complete
  Scope      : ENT-RPT-001
  Trigger    : on fail
  Statement  : The system shall require a failure reason, a detail not blank and an end time not earlier than the start time to store a failure.
  Message    : The failure of Check {checkId} was not stored: {field} is missing.
  Traces     : REQ-RPT-051
  Data source: ENT-RPT-001.failureReason, ENT-RPT-001.failureDetail, ENT-RPT-001.endedAt, ENT-RPT-001.startedAt
  Source     : POL-RPT-006; CON-CHK-003, CON-CHK-009

### RULE-RPT-011 — One decision per Check
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording an Employee Decision on a Check run that already holds one.
  Message    : Check {checkId} already has an Employee Decision.
  Traces     : REQ-RPT-033
  Data source: ENT-RPT-001.employeeDecision
  Source     : POL-RPT-014; ADR-RPT-003

### RULE-RPT-012 — Decision only on a completed Check
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording an Employee Decision on a Check run whose status is not COMPLETED.
  Message    : Check {checkId} is not completed; a decision can only be recorded on a completed Check.
  Traces     : REQ-RPT-034
  Data source: ENT-RPT-001.checkStatus
  Source     : POL-RPT-015; ADR-RPT-003

### RULE-RPT-013 — A decision is complete
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall require a decision code of EMPLOYEE_DECISION, a deciding employee's identity not blank and a yes / no value for execution through the Approval API to record an Employee Decision.
  Message    : The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. / The decision was not recorded: say whether it was executed through the Approval API.
  Traces     : REQ-RPT-035
  Data source: ENT-RPT-001.employeeDecision, ENT-RPT-001.decidedBy, ENT-RPT-001.approvalApiExecuted
  Source     : POL-RPT-013, POL-RPT-016; ADR-RPT-009

### RULE-RPT-014 — Approval API execution only with APPROVED
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording a decision REJECTED as executed through the Approval API.
  Message    : The decision was not recorded: only an APPROVED decision is executed through the Approval API.
  Traces     : REQ-RPT-039
  Data source: ENT-RPT-001.employeeDecision, ENT-RPT-001.approvalApiExecuted
  Source     : [KB:raw-idea.md §11]; ADR-RPT-009

### RULE-RPT-015 — Decision agreement needs a service code
  Scope      : ENT-RPT-001
  Trigger    : on read decision agreement
  Statement  : The system shall require a service code, present and not blank, to read the decision agreement.
  Message    : A service code is needed to read the decision agreement.
  Traces     : REQ-RPT-041
  Data source: ENT-RPT-001.serviceCode
  Source     : ADR-RPT-005

## A6 — Lookups

```yaml name=lookups
lookups:
  - {key: EMPLOYEE_DECISION, seeded: [APPROVED, REJECTED], open: false, values: [APPROVED, REJECTED], fields: [employeeDecision], entity: ENT-RPT-001, control: lookup, source: "domain-profile §7.1 Employee Decision 'approve / reject'; ADR-RPT-003"}
  - {key: CHECK_STATUS, seeded: [], open: false, values: [AWAITING_DOCUMENTS, RUNNING, COMPLETED, FAILED], fields: [checkStatus], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-001; by value through the Check result port"}
  - {key: OVERALL_STATUS, seeded: [], open: false, values: [COMPLIANT, NOT_COMPLIANT, NEEDS_MANUAL_REVIEW], fields: [overallStatus], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-001; profile closed enum"}
  - {key: CHECK_FAILURE_REASON, seeded: [], open: false, values: [TIMED_OUT, MODEL_UNAVAILABLE, MODEL_OUTPUT_INVALID, MODEL_NOT_PERMITTED, UPLOAD_WINDOW_EXPIRED, INTERRUPTED, INTERNAL_ERROR], fields: [failureReason], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-003"}
  - {key: FINDING_OUTCOME, seeded: [], open: false, values: [SATISFIED, NOT_SATISFIED, UNDETERMINED], fields: [findingOutcome], owner: CHK, entity: ENT-RPT-002, control: lookup, source: "consumed — CHK CON-CHK-002"}
  - {key: FETCH_MODE, seeded: [], open: false, values: [path, blob, manual], fields: [fetchMode, sourceMode], owner: DOC, entity: ENT-RPT-001, control: lookup, source: "consumed — DOC CON-DOC-002; profile closed enum; also ENT-RPT-003.sourceMode"}
  - {key: DOCUMENT_READ_STATUS, seeded: [], open: false, values: [READ, MISSING, UNREADABLE], fields: [readStatus], owner: DOC, entity: ENT-RPT-003, control: lookup, source: "consumed — DOC CON-DOC-001; by value through the Check result port"}
  - {key: UNREADABLE_REASON, seeded: [], open: false, values: [OUTSIDE_STORAGE_ROOT, NOT_FOUND, TOO_LARGE, UNSUPPORTED_FORMAT, READING_FAILED, OUT_OF_TIME, SOURCE_QUERY_FAILED, MODEL_NOT_PERMITTED], fields: [unreadableReason], owner: DOC, entity: ENT-RPT-003, control: lookup, source: "consumed — DOC CON-DOC-001"}
  - {key: SERVICE_CODE, seeded: [], open: true, values: [scholarship-request], fields: [serviceCode], owner: REG, entity: ENT-RPT-001, control: reference, source: "consumed — REG by value through CHK; never hardcoded, never a foreign key"}
  - {key: DOCUMENT_TYPE, seeded: [], open: true, values: [TRANSCRIPT, ID_CARD], fields: [documentType], owner: REG, entity: ENT-RPT-003, control: lookup, source: "consumed — REG by value through CHK"}
```

| Key | Labels (en) | Rationale |
|---|---|---|
| EMPLOYEE_DECISION | Approved · Rejected | Closed: the approve / reject decision of the glossary (ADR-RPT-003) |
| CHECK_STATUS, OVERALL_STATUS, CHECK_FAILURE_REASON, FINDING_OUTCOME | as CHK labels them | Consumed from CHK by value; never redefined |
| FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON | as DOC labels them | Consumed from DOC by value through CHK; never redefined |
| SERVICE_CODE, DOCUMENT_TYPE | as REG labels them | Open lists of the service registry; stored as text values |

RPT owns one lookup, EMPLOYEE_DECISION. Every consumed closed list backs an RPT field and is enforced by RULE-RPT-006 (P2 states the codes as CHECK constraints — ADR-CHK-016 hands them to RPT).

## A7 — Status lifecycle

Check status (CHECK_STATUS, CHK's list) as stored on ENT-RPT-001.checkStatus:

```
   create (fetch mode manual)    ──► AWAITING_DOCUMENTS ──mark RUNNING──► RUNNING
   create (fetch mode path|blob) ──────────────────────────────────────► RUNNING ──mark RUNNING (no change)──► RUNNING
                                                                          RUNNING ──complete──► COMPLETED  (final)
   AWAITING_DOCUMENTS ──fail──► FAILED  (final)
   RUNNING            ──fail──► FAILED  (final)
```

Constrained transitions: create → RULE-RPT-001, RULE-RPT-002; every change → RULE-RPT-003; complete → RULE-RPT-004 … RULE-RPT-008; fail → RULE-RPT-010. On COMPLETED the Employee Decision may be recorded once (RULE-RPT-011 … RULE-RPT-014); it is not a status. No approval flow: the Report Store never approves (REQ-RPT-037).

## A8 — Module dependencies
```yaml name=module-dependencies
consumes: []
```
RPT consumes no entity of another module. CHK promises no entity (its Active Check is PRIVATE — CON-CHK contract); RPT implements CHK's Check result port (CON-CHK-006 … CON-CHK-011), so the RPT → CHK edge is the platform edge (ADR-REG-002). The service code, version number, document type and DOC's codes are stored as values with no runtime read of REG or DOC (CON-DOC-001, CON-DOC-002; ADR-RPT-001).

| External service | Purpose | Integration kind |
|---|---|---|
| Check Engine (CHK, in-process) | creates, advances, completes and fails Check runs; reads one Check; lists unfinished Checks | calls RPT's implementation of the Check result port (ADR-REG-002, ADR-CHK-011) |
| Host Integration (INT, in-process) | hands over the Employee Decision | RPT's in-process decision operation (ADR-RPT-003, ADR-RPT-006) |
| Platform configuration | report retention period, purge schedule | read at start-up (ADR-RPT-010) |

# PART B — SCREEN REQUIREMENTS

Not applicable: RPT has no screen of its own (module-registry AUTO-DECISION; [KB:raw-idea.md §15 A1]). The employee reads reports and records decisions in the embedded frontend, which uses RPT's read operations and INT's decision operation.

## API expectations (module level — no screen carries them)
Base path : /api/v1/{resource}
Verbs     : GET only — RPT's HTTP surface is read-only (ADR-RPT-006); every write is in-process
Errors    : ProblemDetail (RFC 9457) → {type, title, status, detail, code}; in-process rejections are typed with the RULE messages above, which INT maps to ProblemDetail

| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read a Check and its report | GET | /api/v1/checks/{checkId} | checkId | Check with status and, once ended, report or failure | — | REQ-RPT-023 … REQ-RPT-027 |
| list the Checks of a request | GET | /api/v1/checks | serviceCode, requestNumber | up to 100 Checks newest first + total | RULE-RPT-009 | REQ-RPT-028 … REQ-RPT-031, REQ-RPT-050 |
| read the decision agreement of a service | GET | /api/v1/decision-agreement | serviceCode | rows (versionNumber, overallStatus, employeeDecision, count) | RULE-RPT-015 | REQ-RPT-040, REQ-RPT-041 |
| record an Employee Decision (in-process, called by INT) | — | — | checkId, employeeDecision, decidedBy, approvalApiExecuted | Check with the recorded decision or rejection | RULE-RPT-011 … RULE-RPT-014 | REQ-RPT-032 … REQ-RPT-039 |
| Check result port — create a Check run (implements CON-CHK-006) | — | — | serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt | checkId | RULE-RPT-001, RULE-RPT-002, RULE-RPT-006 | REQ-RPT-001 … REQ-RPT-004 |
| Check result port — mark RUNNING (implements CON-CHK-007) | — | — | checkId, runningSince | — | RULE-RPT-003 | REQ-RPT-005, REQ-RPT-006, REQ-RPT-007 |
| Check result port — complete a Check (implements CON-CHK-008) | — | — | checkId, overallStatus, findings, documentOutcomes, unreadQueries, metadata | — | RULE-RPT-003 … RULE-RPT-008 | REQ-RPT-008 … REQ-RPT-017, REQ-RPT-020 |
| Check result port — fail a Check (implements CON-CHK-009) | — | — | checkId, failureReason, detail, endedAt | — | RULE-RPT-003, RULE-RPT-006, RULE-RPT-010 | REQ-RPT-018, REQ-RPT-051 |
| Check result port — read one Check (implements CON-CHK-010) | — | — | checkId | checkId, status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt | — | REQ-RPT-021, REQ-RPT-007 |
| Check result port — list unfinished Checks (implements CON-CHK-011) | — | — | — | list of checkId, status, startedAt | — | REQ-RPT-022 |
| purge (scheduled, no caller) | — | — | platform configuration | count deleted (log) | — | REQ-RPT-042 … REQ-RPT-046, REQ-RPT-052 |

# STANDALONE

## Traceability matrix
| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-RPT-001 | REQ-RPT-001, REQ-RPT-002, REQ-RPT-003, REQ-RPT-004 | AC-RPT-001, AC-RPT-002, AC-RPT-003, AC-RPT-004, AC-RPT-005 | RULE-RPT-001, RULE-RPT-002 | ENT-RPT-001 | — |
| US-RPT-002 | REQ-RPT-004, REQ-RPT-005, REQ-RPT-006, REQ-RPT-007 | AC-RPT-006, AC-RPT-007, AC-RPT-008, AC-RPT-009, AC-RPT-010 | RULE-RPT-003 | ENT-RPT-001 | — |
| US-RPT-003 | REQ-RPT-008, REQ-RPT-009, REQ-RPT-010, REQ-RPT-011, REQ-RPT-012, REQ-RPT-013, REQ-RPT-014 | AC-RPT-011, AC-RPT-012, AC-RPT-013, AC-RPT-014, AC-RPT-015, AC-RPT-016, AC-RPT-017 | RULE-RPT-004, RULE-RPT-005 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-004 | REQ-RPT-014, REQ-RPT-015, REQ-RPT-016, REQ-RPT-017, REQ-RPT-018, REQ-RPT-051 | AC-RPT-017, AC-RPT-018, AC-RPT-019, AC-RPT-020, AC-RPT-021, AC-RPT-059 | RULE-RPT-005, RULE-RPT-006, RULE-RPT-007, RULE-RPT-008, RULE-RPT-010 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003 | — |
| US-RPT-005 | REQ-RPT-019, REQ-RPT-047, REQ-RPT-048, REQ-RPT-049 | AC-RPT-022, AC-RPT-055, AC-RPT-056, AC-RPT-057 | — | ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-006 | REQ-RPT-006, REQ-RPT-020 | AC-RPT-008, AC-RPT-009, AC-RPT-023 | RULE-RPT-003 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-007 | REQ-RPT-007, REQ-RPT-021, REQ-RPT-022 | AC-RPT-010, AC-RPT-024, AC-RPT-025, AC-RPT-026 | — | ENT-RPT-001 | — |
| US-RPT-008 | REQ-RPT-023, REQ-RPT-024, REQ-RPT-025, REQ-RPT-026, REQ-RPT-027 | AC-RPT-027, AC-RPT-028, AC-RPT-029, AC-RPT-030, AC-RPT-031, AC-RPT-032 | — | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-009 | REQ-RPT-028, REQ-RPT-029, REQ-RPT-030, REQ-RPT-031, REQ-RPT-050 | AC-RPT-033, AC-RPT-034, AC-RPT-035, AC-RPT-036, AC-RPT-037, AC-RPT-058 | RULE-RPT-009 | ENT-RPT-001, ENT-RPT-002 | — |
| US-RPT-010 | REQ-RPT-032, REQ-RPT-033, REQ-RPT-034, REQ-RPT-035, REQ-RPT-036, REQ-RPT-037, REQ-RPT-038, REQ-RPT-039 | AC-RPT-038, AC-RPT-039, AC-RPT-040, AC-RPT-041, AC-RPT-042, AC-RPT-043, AC-RPT-044, AC-RPT-045, AC-RPT-046 | RULE-RPT-011, RULE-RPT-012, RULE-RPT-013, RULE-RPT-014 | ENT-RPT-001 | — |
| US-RPT-011 | REQ-RPT-040, REQ-RPT-041 | AC-RPT-047, AC-RPT-048, AC-RPT-049 | RULE-RPT-015 | ENT-RPT-001 | — |
| US-RPT-012 | REQ-RPT-042, REQ-RPT-043, REQ-RPT-044, REQ-RPT-045, REQ-RPT-046, REQ-RPT-052 | AC-RPT-050, AC-RPT-051, AC-RPT-052, AC-RPT-053, AC-RPT-054, AC-RPT-060 | — | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |

Raw-idea §12 guardrails at RPT's surface (AIAS-1, ADR-RPT-008): (1) LLM analyses only → REQ-RPT-049 · (2) approval only on the employee's action → REQ-RPT-037, REQ-RPT-039 · (3) read-only host access → REQ-RPT-047 · (4) bound parameters → REQ-RPT-050 · (5) storage root → REQ-RPT-048 · (6) nothing skipped silently → REQ-RPT-011, REQ-RPT-012, REQ-RPT-014 · (7) content is data → REQ-RPT-019, REQ-RPT-027 · (8) limits → REQ-RPT-031 · (9) nothing carried between Checks → REQ-RPT-030.

## Decisions applied
| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| ADR-REG-001 | RPT owns the Check run, findings and Check Document rows | P0 (REG) | ACCEPTED by owner |
| ADR-REG-002 | CHK declares the result port; RPT implements it | P0 (REG) | ACCEPTED by owner |
| ADR-REG-006 | Limits are platform configuration (pattern for the retention period) | P0 (REG) | ACCEPTED by owner |
| ADR-CHK-001 | Closed lists carried by value; RPT stores the codes | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-002 | Overall Status derivation (basis of RULE-RPT-005) | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-005 | FAILED with a reason and no Overall Status; start-up closes unfinished Checks | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-007 | Several independent Checks per request | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-011 | The six result port operations | P1 (CHK) | ACCEPTED |
| ADR-CHK-014 | Unread queries as query name + detail | P1 (CHK) | ACCEPTED |
| ADR-CHK-015 | Every Check ends exactly once on CHK's side | P1 (CHK) | ACCEPTED |
| ADR-CHK-017 | Pattern: a module's own HTTP surface is read-only | P3.1 (CHK) | ACCEPTED |
| ADR-DOC-002, ADR-DOC-007 | Read status and unreadable reason lists | DOC | ACCEPTED by owner |
| ADR-DOC-008 | Fetched content lives only for the fetching call | DOC | ACCEPTED by owner |
| ADR-DOC-011 | Pattern: read-only HTTP surface, writes in-process | DOC | ACCEPTED |
| ADR-RPT-001 | Result port implemented; four records; stored whole; codes by value | P0 | Confirmed at prd-approval |
| ADR-RPT-002 | Forward-only status; ended report final | P0 | Confirmed at prd-approval |
| ADR-RPT-003 | Employee Decision on the Check Run, once, COMPLETED only, Approval API flag | P0 | Confirmed at prd-approval |
| ADR-RPT-004 | Retention period and hard-delete purge | P0 | Confirmed at prd-approval |
| ADR-RPT-005 | Three reads; no viewer restriction | P0 | Confirmed at prd-approval |
| ADR-RPT-006 | RPT serves its three reads over HTTP itself; all writes in-process | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-007 | Store-side shape guards: initial status vs fetch mode, metadata agreement, COMPLIANT consistency | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-008 | Every §12 guardrail stated at RPT's surface; listing capped at 100 with a total | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-009 | Decision inputs; recording time is RPT's clock; Approval API flag only with APPROVED | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-010 | Retention and purge configuration: whole days, schedule default daily 02:00, one transaction per Check run, logged | P1 (this stage) | ACCEPTED — non-breaking |
| DEFAULT — purge schedule daily at 02:00 server time | The purge runs once a day at 02:00 | ADR-RPT-010; domain best practice | Override: set the purge schedule in the platform configuration |
| DEFAULT — age measured from the end time | A Check run's age for the purge counts from endedAt | ADR-RPT-004 | Override: count from startedAt |
| DEFAULT — listing cap 100 | The Checks of a request are returned 100 at most, newest first, with the total | ADR-RPT-008 | Override: add paging in a later version |
| DEFAULT — decidedAt is the recording time | The decision time is RPT's clock when INT hands the decision over | ADR-RPT-009 | Override: INT passes the employee's confirmation time |

## Access summary
| Role | Screens | Operations |
|---|---|---|
| Employee | none in RPT (embedded frontend) | reads a Check and its report, lists the Checks of a request (RPT HTTP); records the decision (INT → RPT in-process) |
| Service Administrator | none | reads the decision agreement (RPT HTTP); sets the report retention period and purge schedule (platform configuration) |
| CHK, INT (in-process) | — | CHK: the six result port operations · INT: record an Employee Decision |
Caller authentication and who may view stored reports are deferred (raw-idea A2, domain-profile D4); no role check is specified in this version.
══════════════════════════════════════════════════════════════════
