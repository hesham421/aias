# SRS — Host Integration (INT)
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias
Inputs : prd, domain-profile, project-registry (PRD approved 2026-10-01)
Counts : REQ 60 · AC 66 · ENT 0 · RULE 4 · SCR-REQ 5 · ADR 4 (new: ADR-INT-010 … ADR-INT-013; applied: ADR-INT-001 … ADR-INT-013, ADR-REG-001, ADR-REG-006, ADR-REG-008, ADR-CHK-018, ADR-DOC-006, ADR-DOC-012, ADR-RPT-003, ADR-RPT-005, ADR-RPT-006, ADR-RPT-013)
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information
| Item | Value |
|---|---|
| Module | INT — Host Integration |
| Feature code | INT |
| Version | v1 |
| Date | 2026-10-01 |
| Status | DRAFT — P1 output, PRD approved 2026-10-01 (gate prd-approval) |
| Prepared by | P1 SRS engine (operator run, lane analysis) |
| Decisions applied | 13 INT ADRs (ADR-INT-001 … ADR-INT-013, of which 4 new), 3 REG, 1 CHK, 2 DOC and 4 RPT ADRs, and 4 DEFAULTs — see Decisions applied |

## A2 — Functional context

### In scope
- Starting a Check at a host system's or the employee frontend's request and answering at once (POL-INT-001, POL-INT-002; ADR-INT-002).
- Handing a manual upload to Document Access with the Check's service code and version, only while the Check waits for documents (POL-INT-004, POL-INT-005; ADR-INT-005).
- Confirming the uploads of a `manual` Check to the Check Engine (POL-INT-006).
- Recording the Employee Decision in the Report Store, calling the host Approval API first only for an APPROVED decision where the Check's version enables it (POL-INT-007 … POL-INT-011; ADR-INT-004, ADR-INT-009, ADR-INT-010).
- Answering every refusal in the standard error form with the refusing module's code (POL-INT-003; ADR-INT-003).
- The employee frontend embedded in the host screen: the Checks of a request, the report, the document upload, the upload confirmation and the decision (POL-INT-012 … POL-INT-016, POL-INT-018; ADR-INT-006, ADR-INT-011).
- The raw-idea §12 guardrails at INT's surface (see Traceability — guardrails).

### Out of scope
- Reading a Check, its report, the Checks of a request or the decision agreement — served by the Report Store (ADR-RPT-005, ADR-RPT-006; ADR-INT-001).
- The service reads (REG), the list of uploaded documents (DOC) and the active-Check read (CHK) — served by their owners.
- The server-rendered report page `GET /checks/{id}/view` — superseded by the employee frontend (A1; ADR-INT-001).
- Caller authentication (API key or mTLS) and who may view stored reports — deferred (raw-idea A2; domain-profile D4, D7); no role check is specified.
- Running a Check, fetching or reading documents, storing reports — CHK, DOC, RPT.
- Multi-tenancy, conversation memory, RAG, multi-agent orchestration, an administration UI.

### Module function
Host Integration is the door of the service: host systems and the employee frontend reach the service through it to start a Check, to hand over the documents of a `manual` Check and confirm them, and to record the employee's decision — with the host's Approval API called on the employee's behalf where the service enables it. It owns the employee frontend that shows the Checks of a request and their reports, and it keeps nothing of its own.

### Detailed description
The host screen opens the embedded frontend for one request, passing the service code, the request number and the employee identity. The frontend lists the Checks of that request (from the Report Store) and lets the employee start a new one; Host Integration has the Check Engine start it and answers at once with the Check's identifier and its first status — RUNNING, or AWAITING_DOCUMENTS for a `manual` service. The frontend follows a Check that has not ended by reading it every few seconds. For a `manual` Check the employee uploads each document — Host Integration reads the Check to learn its service code, version and status, and hands the file to Document Access — and then confirms, as a separate action, that the uploads are complete; Host Integration asks the Check Engine to continue. When the Check is COMPLETED the employee reads the report — the Overall Status, each finding beside its evidence, the documents read, missing or unreadable, the service queries that could not be read — and records a decision. For an APPROVED decision on a version that enables the Approval API, Host Integration checks that the decision is complete and the Check is COMPLETED and undecided, calls the host's Approval API once, and only after it succeeds hands the decision to the Report Store marked as executed; a failed or timed-out call records nothing and the employee can try again. A REJECTED decision, or any decision on a version without the Approval API, goes straight to the Report Store. Every refusal reaches the caller in the standard error form with the refusing module's code. Roles: the Employee (all screens) and the Host System (the REST API).

### Current situation
| Step | Party | Notes |
|---|---|---|
| Employee checks the request by hand and approves it in the host system | Employee | No service to start a check from the host screen, no report beside the request, no record of the decision against a report [KB:raw-idea.md §1, §11] |

### Current difficulties
The host systems have no way to ask for a verification and show its evidence inside their screens, and an approval taken in the host leaves no link to the facts it was based on [KB:raw-idea.md §1, §9, §11].

### Proposed system and benefits
A host starts a Check with one call and the employee follows it in the embedded frontend (US-INT-001, US-INT-008, US-INT-010), verifies each finding against its evidence (US-INT-009), and records the decision beside the report — executed through the host's Approval API where the host offers one (US-INT-005, US-INT-006). Hosts can build their own display on the same API (US-INT-011).

### General notes
- INT declares no entity (ADR-INT-007, ADR-INT-013); the fields it reads belong to the Report Store's Check Run (ENT-RPT-001) and the Service Registry's Service Package Version (ENT-REG-002), reached only through their contracts.
- The Approval API timeout, the upload request limit and the host Approval API base address are platform configuration (ADR-INT-012); the frontend's polling interval is frontend configuration (ADR-INT-011).
- Codes follow the profile format `{MOD}-{http}[-{SLUG}]`; refusals raised by CHK, DOC and RPT keep their owner's code (ADR-INT-003, ADR-INT-010).
- No role check is specified in this version (raw-idea A2).

## A3 — Entities and fields

Standard fields — per profile: kind `transactional` carries `createdAt, updatedAt`. Not applicable here: Host Integration declares no entity of its own (ADR-INT-007, ADR-INT-013). Host identifiers (request number, employee identity) are handed on as text exactly as the host sent them and are never foreign keys.

### Consumed fields (read through the owners' contracts — not redefined)
| Owner entity | Field | Read through | Used for |
|---|---|---|---|
| ENT-RPT-001 — Check Run (RPT) | checkRunId | the Check identifier `checkId` (CON-RPT-001) | addressing every write on a Check |
| ENT-RPT-001 | checkStatus | CON-RPT-003 | upload only while AWAITING_DOCUMENTS (RULE-INT-001); approval guard (RULE-INT-003); screen actions |
| ENT-RPT-001 | serviceCode, versionNumber | CON-RPT-003 | handed to Document Access with an upload (REQ-INT-010); approval API of the Check's version (REQ-INT-030) |
| ENT-RPT-001 | requestNumber | CON-RPT-003 | filled into the Approval API path (REQ-INT-031) |
| ENT-RPT-001 | employeeDecision, decidedBy | CON-RPT-003, CON-RPT-006 | approval guard (RULE-INT-003); the decision request (RULE-INT-002) |
| ENT-RPT-001 | serviceCode, requestNumber, employeeId | the launch context of the frontend; CON-RPT-004 | the Checks of a request (RULE-INT-004) |
| ENT-REG-002 — Service Package Version (REG) | approvalEnabled, approvalApi | CON-REG-012 | whether and where the Approval API is called (REQ-INT-025, REQ-INT-030) |

## A4 — Functional requirements (EARS) and acceptance criteria

### REQ-INT-001 — A Check started on request
  Pattern    : event
  Statement  : When a host system asks to start a Check with a service code, a request number and an employee identity, the system shall have the Check Engine start the Check and answer that it is accepted, with the Check's identifier and status.
  Traces     : US-INT-001
  Entities   : ENT-RPT-001
  Rationale  : The host starts a Check and follows it by its identifier.
  Source     : POL-INT-001; [KB:raw-idea.md §5, §8]; CON-CHK-004; ADR-INT-002
  Priority   : HIGH

#### AC-INT-001 — [REQ-INT-001]
  Given  : service `scholarship-request` is available with fetch mode `path`
  When   : a host asks to start a Check for `scholarship-request`, request `REQ-2026-0042`, employee `E-3307`
  Then   : the answer is accepted (HTTP 202) with a new Check identifier and status RUNNING

#### AC-INT-002 — [REQ-INT-001]
  Given  : service `manual-service` is available with fetch mode `manual`
  When   : a host asks to start a Check for `manual-service`, request `M-77`, employee `E-3307`
  Then   : the answer is accepted (HTTP 202) with a new Check identifier and status AWAITING_DOCUMENTS

### REQ-INT-002 — The start answered without waiting for the report
  Pattern    : ubiquitous
  Statement  : The system shall answer a Check start as soon as the Check Engine has created the Check, without waiting for its report.
  Traces     : US-INT-001
  Entities   : ENT-RPT-001
  Rationale  : A Check takes time; the host polls for the result.
  Source     : POL-INT-001; [KB:raw-idea.md §5]; ADR-INT-002
  Priority   : HIGH

#### AC-INT-003 — [REQ-INT-002]
  Given  : a `path` Check whose pipeline takes 40 seconds
  When   : a host starts it
  Then   : the answer arrives with status RUNNING before the Check's report exists, and a read of the Check right after shows no Overall Status

### REQ-INT-003 — Host identifiers handed on as sent
  Pattern    : ubiquitous
  Statement  : The system shall hand the request number and the employee identity of a Check start to the Check Engine exactly as the host sent them.
  Traces     : US-INT-001
  Entities   : ENT-RPT-001
  Rationale  : The report must show the identifiers the host knows.
  Source     : POL-INT-002; profile `conventions.identifiers`; CON-CHK-004
  Priority   : HIGH

#### AC-INT-004 — [REQ-INT-003]
  Given  : service `scholarship-request` is available
  When   : a host starts a Check for request `0042/B` and employee `e.ahmed@moe`
  Then   : the Check's read shows request number "0042/B" and employee "e.ahmed@moe", unchanged

### REQ-INT-004 — No directory check of the employee
  Pattern    : ubiquitous
  Statement  : The system shall accept the employee identity of a request without checking it against any user directory.
  Traces     : US-INT-001
  Entities   : ENT-RPT-001
  Rationale  : The host identifies the employee; caller authentication is deferred.
  Source     : POL-INT-002; [KB:raw-idea.md §15 A2]; ADR-INT-002
  Priority   : —

#### AC-INT-005 — [REQ-INT-004]
  Given  : employee identity `X-999` is known to no directory of the service
  When   : a host starts a Check for an available service with employee `X-999`
  Then   : the Check is accepted (HTTP 202) and its read shows employee "X-999"

### REQ-INT-005 — The accepted start points to the Check's read
  Pattern    : event
  Statement  : When a Check start is accepted, the system shall give the caller the address at which the Check is read.
  Traces     : US-INT-001
  Entities   : ENT-RPT-001
  Rationale  : The host polls the Check's read for its status and report.
  Source     : POL-INT-001; [KB:raw-idea.md §5, §8] `GET /checks/{id}`; ADR-INT-001
  Priority   : —

#### AC-INT-006 — [REQ-INT-005]
  Given  : a host starts a Check that receives identifier 611
  When   : the start is accepted
  Then   : the answer names `/api/v1/checks/611` as the address of the Check's read

### REQ-INT-006 — Refusals of the owning module passed through
  Pattern    : unwanted
  Statement  : If the Check Engine, Document Access or the Report Store refuses a request, then the system shall answer with that refusal's code, HTTP status and message unchanged in the standard error form.
  Traces     : US-INT-002
  Entities   : ENT-RPT-001
  Rationale  : The caller learns why nothing happened in the owner's words.
  Source     : POL-INT-003; CON-CHK-004, CON-CHK-005, CON-DOC-003, CON-RPT-006; ADR-INT-003, ADR-INT-010
  Priority   : —

#### AC-INT-007 — [REQ-INT-006]
  Given  : service `old-service` is withdrawn
  When   : a host starts a Check for `old-service`
  Then   : the answer is HTTP 422 with code `CHK-422-SERVICE-NOT-AVAILABLE` and detail "The service "old-service" is not available for Checks."; no Check is created

#### AC-INT-008 — [REQ-INT-006]
  Given  : Check 612 of `manual-service` is AWAITING_DOCUMENTS and `manual-service` requires TRANSCRIPT and ID_CARD
  When   : the employee uploads a file of type PASSPORT for Check 612
  Then   : the answer is HTTP 422 with code `DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE` and detail ""PASSPORT" is not a document type of the service "manual-service"; choose one of: TRANSCRIPT, ID_CARD."

#### AC-INT-009 — [REQ-INT-006]
  Given  : Check 613 is COMPLETED with decision REJECTED and its version does not enable the Approval API
  When   : the employee records decision APPROVED for Check 613
  Then   : the answer is HTTP 409 with code `RPT-409-DECISION-ALREADY-RECORDED` and detail "Check 613 already has an Employee Decision."

#### AC-INT-010 — [REQ-INT-006]
  Given  : Check 614 is RUNNING
  When   : the employee confirms the uploads of Check 614
  Then   : the answer is HTTP 409 with code `CHK-409-CHECK-NOT-AWAITING-DOCUMENTS` and detail "Check 614 is not waiting for documents; its status is RUNNING."

### REQ-INT-007 — An unreadable request refused
  Pattern    : unwanted
  Statement  : If a request's body or Check identifier cannot be read, then the system shall refuse it with code INT-400-REQUEST-INVALID and hand nothing to another module.
  Traces     : US-INT-002
  Entities   : —
  Rationale  : A malformed request must fail visibly before any module acts on it.
  Source     : POL-INT-003; profile error envelope; ADR-INT-003
  Priority   : —

#### AC-INT-011 — [REQ-INT-007]
  Given  : any state
  When   : a caller confirms the uploads of Check `abc`
  Then   : the answer is HTTP 400 with code `INT-400-REQUEST-INVALID` and detail "The request could not be read: checkId must be a number."; no module is called

### REQ-INT-008 — An unexpected failure answered without internals
  Pattern    : unwanted
  Statement  : If an unexpected failure occurs while a request is handled, then the system shall answer with code INT-500 in the standard error form without exposing internal details.
  Traces     : US-INT-002
  Entities   : —
  Rationale  : The caller needs a stable error; internals stay in the service log.
  Source     : POL-INT-003; profile error envelope; ADR-INT-003
  Priority   : —

#### AC-INT-012 — [REQ-INT-008]
  Given  : the Report Store is unreachable because of a database outage
  When   : the employee records a decision on Check 615
  Then   : the answer is HTTP 500 with code `INT-500` and detail "The request could not be completed because of an unexpected error."; the answer contains no stack trace

### REQ-INT-009 — An upload handed to Document Access
  Pattern    : event
  Statement  : When the employee uploads a file for a Check that is AWAITING_DOCUMENTS, the system shall hand the file, its file name and its document type to Document Access.
  Traces     : US-INT-003
  Entities   : ENT-RPT-001
  Rationale  : In `manual` mode the employee provides the documents.
  Source     : POL-INT-004; [KB:raw-idea.md §6, §8]; CON-DOC-003; ADR-INT-005
  Priority   : HIGH

#### AC-INT-013 — [REQ-INT-009]
  Given  : Check 616 of `manual-service` version 1 is AWAITING_DOCUMENTS
  When   : the employee uploads `transcript.pdf` (300 KB) as TRANSCRIPT for Check 616
  Then   : the answer is HTTP 201 with the uploaded document's identifier, type TRANSCRIPT, file name "transcript.pdf", file size 307200 and oversized false

### REQ-INT-010 — Service code and version taken from the Check
  Pattern    : ubiquitous
  Statement  : The system shall hand every upload to Document Access with the service code and service package version of the Check as the Report Store holds them.
  Traces     : US-INT-003
  Entities   : ENT-RPT-001
  Rationale  : Document Access checks an upload against the Check's own version; the uploader never names it.
  Source     : POL-INT-004; CON-DOC-003; ADR-DOC-006; ADR-INT-005
  Priority   : HIGH

#### AC-INT-014 — [REQ-INT-010]
  Given  : Check 617 runs `manual-service` version 1 and version 2 is now current
  When   : the employee uploads an ID_CARD for Check 617
  Then   : Document Access receives service `manual-service` and version 1 with the file

### REQ-INT-011 — Uploads only while the Check waits for documents
  Pattern    : unwanted
  Statement  : If a file is uploaded for a Check whose status is not AWAITING_DOCUMENTS, then the system shall refuse the upload and hand nothing to Document Access.
  Traces     : US-INT-003
  Entities   : ENT-RPT-001
  Rationale  : A file uploaded to a running or ended Check would never be read.
  Source     : POL-INT-005; RULE-INT-001; ADR-INT-005
  Priority   : HIGH

#### AC-INT-015 — [REQ-INT-011]
  Given  : Check 618 of `manual-service` is RUNNING
  When   : the employee uploads a TRANSCRIPT for Check 618
  Then   : the answer is HTTP 409 with code `INT-409-CHECK-NOT-AWAITING-DOCUMENTS` and detail "Documents can be uploaded only while Check 618 is waiting for documents; its status is RUNNING."; Document Access receives nothing

### REQ-INT-012 — An upload for an unknown Check refused
  Pattern    : unwanted
  Statement  : If a file is uploaded for a Check the Report Store does not hold, then the system shall refuse it with the Report Store's not-found refusal and hand nothing to Document Access.
  Traces     : US-INT-003
  Entities   : ENT-RPT-001
  Rationale  : An upload must belong to an existing Check.
  Source     : POL-INT-003, POL-INT-005; CON-RPT-003; ADR-INT-003
  Priority   : —

#### AC-INT-016 — [REQ-INT-012]
  Given  : no Check 99999 exists
  When   : the employee uploads a TRANSCRIPT for Check 99999
  Then   : the answer is HTTP 404 with code `RPT-404-CHECK-NOT-FOUND` and detail "Check 99999 was not found."

### REQ-INT-013 — The oversized-file notice passed on
  Pattern    : event
  Statement  : When Document Access accepts an uploaded file as oversized, the system shall answer the upload with Document Access's notice that the file will be reported unreadable.
  Traces     : US-INT-003
  Entities   : ENT-RPT-001
  Rationale  : Anything that cannot be read is said, never skipped silently.
  Source     : POL-INT-004; [KB:raw-idea.md §12]; CON-DOC-003 (RULE-DOC-005 notice)
  Priority   : —

#### AC-INT-017 — [REQ-INT-013]
  Given  : the maximum file size is 10 MB and Check 619 of `manual-service` is AWAITING_DOCUMENTS
  When   : the employee uploads a 12 MB ID_CARD for Check 619
  Then   : the answer is HTTP 201 with oversized true and Document Access's notice text

### REQ-INT-014 — The upload request limit
  Pattern    : unwanted
  Statement  : If an upload request is larger than the upload request limit of the platform configuration, then the system shall refuse it with code INT-413-UPLOAD-TOO-LARGE and hand nothing to Document Access.
  Traces     : US-INT-003
  Entities   : —
  Rationale  : Every request has a size limit; the limit is set at or above the maximum file size so oversized files still reach the report.
  Source     : POL-INT-004; [KB:raw-idea.md §12] "Each check has limits"; ADR-INT-012
  Priority   : —

#### AC-INT-018 — [REQ-INT-014]
  Given  : the upload request limit is 50 MB and Check 620 is AWAITING_DOCUMENTS
  When   : the employee uploads a 60 MB file for Check 620
  Then   : the answer is HTTP 413 with code `INT-413-UPLOAD-TOO-LARGE` and detail "The upload is larger than the 50 MB the service accepts in one request."; Document Access receives nothing

### REQ-INT-015 — An uploaded file passed as content only
  Pattern    : ubiquitous
  Statement  : The system shall pass an uploaded file to Document Access as content with its file name as text, and shall never open a file path named by a request.
  Traces     : US-INT-003
  Entities   : —
  Rationale  : File paths are opened only inside the storage root, by Document Access; Host Integration opens none.
  Source     : [KB:raw-idea.md §12] "File paths are validated to be inside the allowed storage root before opening"; domain-profile §5 G5; POL-INT-004
  Priority   : —

#### AC-INT-019 — [REQ-INT-015]
  Given  : Check 621 is AWAITING_DOCUMENTS
  When   : the employee uploads a file named `../../etc/passwd` as TRANSCRIPT
  Then   : Document Access receives the file's bytes with file name "../../etc/passwd" as text, and no file of the server's file system is opened by Host Integration

### REQ-INT-016 — Document type choices of an upload
  Pattern    : event
  Statement  : When the employee opens the document upload of a Check, the system shall offer the required document types of the Check's service as the only document type choices.
  Traces     : US-INT-003
  Entities   : ENT-RPT-001
  Rationale  : The employee uploads only documents the service asks for.
  Source     : POL-INT-004; CON-REG-010 (required document types); ADR-INT-011
  Priority   : —

#### AC-INT-020 — [REQ-INT-016]
  Given  : Check 622 runs `manual-service`, which requires TRANSCRIPT and ID_CARD
  When   : the employee opens the document upload of Check 622
  Then   : the document type choices are exactly TRANSCRIPT and ID_CARD

### REQ-INT-017 — Documents already uploaded listed
  Pattern    : event
  Statement  : When the employee opens the document upload of a Check, the system shall list the documents already uploaded for that Check with their document type, file name and size.
  Traces     : US-INT-003
  Entities   : ENT-RPT-001
  Rationale  : The employee sees what was handed over before uploading more or confirming.
  Source     : POL-INT-004; DOC uploaded-documents read; ADR-INT-011
  Priority   : —

#### AC-INT-021 — [REQ-INT-017]
  Given  : Check 623 has one uploaded TRANSCRIPT `t.pdf` of 300 KB
  When   : the employee opens the document upload of Check 623
  Then   : the list shows one entry: TRANSCRIPT, "t.pdf", 300 KB

### REQ-INT-018 — Confirmed uploads continue the Check
  Pattern    : event
  Statement  : When the employee confirms the uploads of a Check, the system shall ask the Check Engine to continue the Check and answer that it is accepted, with the Check's status.
  Traces     : US-INT-004
  Entities   : ENT-RPT-001
  Rationale  : A `manual` Check runs only on the documents the employee says are complete.
  Source     : POL-INT-006; CON-CHK-005; ADR-INT-005
  Priority   : HIGH

#### AC-INT-022 — [REQ-INT-018]
  Given  : Check 624 of `manual-service` is AWAITING_DOCUMENTS with a TRANSCRIPT and an ID_CARD uploaded
  When   : the employee confirms the uploads of Check 624
  Then   : the answer is accepted (HTTP 202) with Check 624 and status RUNNING

### REQ-INT-019 — An upload never continues the Check
  Pattern    : ubiquitous
  Statement  : The system shall continue a `manual` Check only on the employee's confirmation and never as part of an upload.
  Traces     : US-INT-004
  Entities   : ENT-RPT-001
  Rationale  : One action, one effect: an upload never starts the pipeline by accident.
  Source     : POL-INT-006, POL-INT-018; profile `conventions.screen_composition`; ADR-INT-005
  Priority   : HIGH

#### AC-INT-023 — [REQ-INT-019]
  Given  : Check 625 of `manual-service`, which requires TRANSCRIPT and ID_CARD, is AWAITING_DOCUMENTS
  When   : the employee uploads both documents and does not confirm
  Then   : a read of Check 625 still shows status AWAITING_DOCUMENTS

### REQ-INT-020 — The uploads shown before confirming
  Pattern    : state
  Statement  : While the employee is confirming the uploads of a Check, the system shall show the documents uploaded for the Check and the required document types that have no upload.
  Traces     : US-INT-004
  Entities   : ENT-RPT-001
  Rationale  : The employee confirms knowing which required documents will be reported missing.
  Source     : POL-INT-006; [KB:raw-idea.md §7] "what is missing"; ADR-INT-011
  Priority   : —

#### AC-INT-024 — [REQ-INT-020]
  Given  : Check 626 requires TRANSCRIPT and ID_CARD and only a TRANSCRIPT was uploaded
  When   : the employee opens the upload confirmation of Check 626
  Then   : the screen shows the TRANSCRIPT as uploaded and ID_CARD as having no upload, before the confirmation is submitted

### REQ-INT-021 — The decision handed to the Report Store
  Pattern    : event
  Statement  : When the employee records a decision on a Check whose version does not enable the Approval API, the system shall hand the decision and the deciding employee's identity, exactly as sent, to the Report Store as not executed through the Approval API.
  Traces     : US-INT-005
  Entities   : ENT-RPT-001
  Rationale  : The decision beside the result is the measure of the service's accuracy.
  Source     : POL-INT-007, POL-INT-002; [KB:raw-idea.md §9, §11 option 1]; CON-RPT-006
  Priority   : HIGH

#### AC-INT-025 — [REQ-INT-021]
  Given  : Check 627 is COMPLETED, NOT_COMPLIANT, undecided, and its version does not enable the Approval API
  When   : the employee records decision REJECTED by `E-3307` on Check 627
  Then   : the answer is HTTP 201 and Check 627 holds decision REJECTED, decided by "E-3307", executed through the Approval API false

### REQ-INT-022 — The recorded decision answered
  Pattern    : event
  Statement  : When the Report Store records a decision, the system shall answer with the recorded decision, the deciding employee, the recording time and whether it was executed through the Approval API.
  Traces     : US-INT-005
  Entities   : ENT-RPT-001
  Rationale  : The employee sees what was recorded.
  Source     : POL-INT-007; CON-RPT-006
  Priority   : HIGH

#### AC-INT-026 — [REQ-INT-022]
  Given  : Check 628 is COMPLETED and undecided, without the Approval API
  When   : the employee records decision APPROVED by `E-4410`
  Then   : the answer carries Check 628, decision APPROVED, decided by "E-4410", a recording time and executed through the Approval API false

### REQ-INT-023 — The deciding employee from the host
  Pattern    : event
  Statement  : When the employee records a decision in the frontend, the system shall send the employee identity the host passed when it opened the frontend as the deciding employee.
  Traces     : US-INT-005
  Entities   : ENT-RPT-001
  Rationale  : The host identifies the employee; the frontend never asks for it.
  Source     : POL-INT-002, POL-INT-007; ADR-INT-011
  Priority   : HIGH

#### AC-INT-027 — [REQ-INT-023]
  Given  : the host opened the frontend with employee `E-5120` and Check 629 is COMPLETED and undecided
  When   : the employee records decision APPROVED in the frontend
  Then   : the decision request carries deciding employee "E-5120"

### REQ-INT-024 — A decision only from its own request
  Pattern    : ubiquitous
  Statement  : The system shall record an Employee Decision only from a decision request and never from an upload or an upload confirmation.
  Traces     : US-INT-005
  Entities   : ENT-RPT-001
  Rationale  : One screen, one job, one submit.
  Source     : POL-INT-018; profile `conventions.screen_composition`; ADR-INT-005, ADR-INT-006
  Priority   : —

#### AC-INT-028 — [REQ-INT-024]
  Given  : Check 630 of `manual-service` is AWAITING_DOCUMENTS
  When   : the employee uploads a TRANSCRIPT and confirms the uploads
  Then   : Check 630 holds no Employee Decision

### REQ-INT-025 — The Approval API called where the version enables it
  Pattern    : state
  Statement  : While the service package version of a Check enables the Approval API, when the employee records an APPROVED decision on that Check, the system shall call the Approval API before handing the decision to the Report Store.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001, ENT-REG-002
  Rationale  : The service executes the approval the employee confirmed, then records the report it was based on.
  Source     : POL-INT-009; [KB:raw-idea.md §11 option 2]; CON-REG-012; ADR-INT-004
  Priority   : HIGH

#### AC-INT-029 — [REQ-INT-025]
  Given  : Check 631 of `approve-service` version 2 is COMPLETED, COMPLIANT, undecided, request `R-631`, and version 2 enables the Approval API `POST /requests/{requestId}/approve`
  When   : the employee records decision APPROVED by `E-3307`
  Then   : the host receives one `POST /requests/R-631/approve` before the Report Store receives the decision

### REQ-INT-026 — An executed approval recorded as executed
  Pattern    : event
  Statement  : When the Approval API call succeeds, the system shall hand the decision to the Report Store as executed through the Approval API.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001
  Rationale  : The record shows which approvals the service carried out for the host.
  Source     : POL-INT-009; CON-RPT-006; ADR-RPT-003; ADR-INT-004
  Priority   : HIGH

#### AC-INT-030 — [REQ-INT-026]
  Given  : AC-INT-029's call answers HTTP 200
  When   : the decision is handed to the Report Store
  Then   : the answer is HTTP 201 and Check 631 holds decision APPROVED, decided by "E-3307", executed through the Approval API true

### REQ-INT-027 — A rejection never calls the Approval API
  Pattern    : unwanted
  Statement  : If the employee's decision is REJECTED, then the system shall hand it to the Report Store without calling any Approval API.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001
  Rationale  : The Approval API executes approvals only.
  Source     : POL-INT-011; CON-RPT-006 (RULE-RPT-014); ADR-INT-004
  Priority   : HIGH

#### AC-INT-031 — [REQ-INT-027]
  Given  : Check 632 of `approve-service` version 2 (Approval API enabled) is COMPLETED and undecided
  When   : the employee records decision REJECTED
  Then   : the host receives no call and Check 632 holds decision REJECTED, executed through the Approval API false

### REQ-INT-028 — No call where the version does not enable it
  Pattern    : unwanted
  Statement  : If the service package version of a Check does not enable the Approval API, then the system shall hand an APPROVED decision to the Report Store without calling any Approval API.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001, ENT-REG-002
  Rationale  : By default the employee approves in the host system as today.
  Source     : POL-INT-009; [KB:raw-idea.md §11 option 1]; ADR-INT-004
  Priority   : HIGH

#### AC-INT-032 — [REQ-INT-028]
  Given  : Check 633 of `scholarship-request` version 3 (Approval API not enabled) is COMPLETED and undecided
  When   : the employee records decision APPROVED
  Then   : no Approval API is called and Check 633 holds decision APPROVED, executed through the Approval API false

### REQ-INT-029 — The Approval API called only from the decision
  Pattern    : ubiquitous
  Statement  : The system shall call an Approval API only from the handling of an employee's decision request, and from no other request, schedule or model output.
  Traces     : US-INT-006
  Entities   : ENT-REG-002
  Rationale  : The LLM never triggers approval; approval is executed only as a result of the employee's action.
  Source     : POL-INT-008; [KB:raw-idea.md §12]; domain-profile §5 G1, G2; CON-REG-012; review AIAS-4
  Priority   : HIGH

#### AC-INT-033 — [REQ-INT-029]
  Given  : `approve-service` version 2 enables the Approval API
  When   : a host starts a Check of `approve-service`, the Check runs to COMPLETED with Overall Status COMPLIANT, and no decision is recorded
  Then   : the host receives no Approval API call

### REQ-INT-030 — The approval definition of the Check's own version
  Pattern    : ubiquitous
  Statement  : The system shall take whether the Approval API is enabled, and its method and path, from the service package version the Check ran on.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001, ENT-REG-002
  Rationale  : The decision is executed under the configuration the report was built on.
  Source     : POL-INT-009; CON-REG-012; CON-REG-002; ADR-INT-008
  Priority   : HIGH

#### AC-INT-034 — [REQ-INT-030]
  Given  : Check 634 ran on `approve-service` version 2 (Approval API enabled); version 3, now current, does not enable it; Check 634 is COMPLETED and undecided
  When   : the employee records decision APPROVED
  Then   : the Approval API of version 2 is called

### REQ-INT-031 — The request number as one encoded value
  Pattern    : ubiquitous
  Statement  : The system shall place the Check's request number into the Approval API path as one URL-encoded value and shall never build the call from other free text.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001
  Rationale  : Parameters are bound or strictly typed; nothing is built from free text.
  Source     : [KB:raw-idea.md §12] "Query parameters are bound or strictly type-validated"; domain-profile §5 G4; ADR-INT-009
  Priority   : HIGH

#### AC-INT-035 — [REQ-INT-031]
  Given  : Check 635 of `approve-service` version 2 has request number `2026/77 A` and is COMPLETED and undecided
  When   : the employee records decision APPROVED
  Then   : the host receives `POST /requests/2026%2F77%20A/approve`

### REQ-INT-032 — The call carries the Check and the deciding employee
  Pattern    : event
  Statement  : When the system calls the Approval API, the system shall send the Check identifier and the deciding employee's identity with the call.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001
  Rationale  : The host can link the approval to the report it was based on.
  Source     : POL-INT-009; [KB:raw-idea.md §11] "records the report the approval was based on"; ADR-INT-009
  Priority   : —

#### AC-INT-036 — [REQ-INT-032]
  Given  : Check 636 of `approve-service` version 2 is COMPLETED and undecided
  When   : the employee `E-3307` records decision APPROVED
  Then   : the Approval API call carries Check identifier 636 and deciding employee "E-3307"

### REQ-INT-033 — One call, never retried on its own
  Pattern    : ubiquitous
  Statement  : The system shall call the Approval API at most once per decision request and shall never repeat the call on its own.
  Traces     : US-INT-006
  Entities   : —
  Rationale  : An approval may not be safe to repeat on the host; a retry is the employee's choice.
  Source     : POL-INT-008; ADR-INT-009
  Priority   : HIGH

#### AC-INT-037 — [REQ-INT-033]
  Given  : the Approval API of `approve-service` answers HTTP 503 and Check 637 is COMPLETED and undecided
  When   : the employee records decision APPROVED once
  Then   : the host receives exactly one call

### REQ-INT-034 — An incomplete decision refused before any call
  Pattern    : unwanted
  Statement  : If a decision request lacks a decision code of EMPLOYEE_DECISION or the deciding employee's identity, then the system shall refuse it without calling any Approval API.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001
  Rationale  : The Approval API is never called for a decision the Report Store would refuse.
  Source     : POL-INT-008; RULE-INT-002; ADR-INT-010
  Priority   : HIGH

#### AC-INT-038 — [REQ-INT-034]
  Given  : Check 638 of `approve-service` version 2 is COMPLETED and undecided
  When   : the employee records decision APPROVED with no deciding employee
  Then   : the answer is HTTP 400 with code `RPT-400-DECISION-INCOMPLETE` and detail "The decision was not recorded: the deciding employee is missing."; the host receives no call

### REQ-INT-035 — No approval call on a Check not completed or already decided
  Pattern    : unwanted
  Statement  : If an APPROVED decision would be executed through the Approval API for a Check that is not COMPLETED or already holds a decision, then the system shall refuse it without calling the Approval API.
  Traces     : US-INT-006
  Entities   : ENT-RPT-001
  Rationale  : An approval must stand beside one completed report and be recorded once.
  Source     : POL-INT-008, POL-INT-009; RULE-INT-003; ADR-INT-004, ADR-INT-010
  Priority   : HIGH

#### AC-INT-039 — [REQ-INT-035]
  Given  : Check 639 of `approve-service` version 2 is RUNNING
  When   : the employee records decision APPROVED
  Then   : the answer is HTTP 409 with code `RPT-409-CHECK-NOT-COMPLETED` and detail "Check 639 is not completed; a decision can only be recorded on a completed Check."; the host receives no call

#### AC-INT-040 — [REQ-INT-035]
  Given  : Check 640 of `approve-service` version 2 is COMPLETED with decision REJECTED
  When   : the employee records decision APPROVED
  Then   : the answer is HTTP 409 with code `RPT-409-DECISION-ALREADY-RECORDED` and detail "Check 640 already has an Employee Decision."; the host receives no call

### REQ-INT-036 — A failed approval records nothing
  Pattern    : unwanted
  Statement  : If the Approval API answers with a status outside 200–299 or cannot be reached, then the system shall record no decision and refuse with code INT-502-APPROVAL-API-FAILED.
  Traces     : US-INT-007
  Entities   : ENT-RPT-001
  Rationale  : A decision recorded as executed when the host never approved would mislead every reader.
  Source     : POL-INT-010; profile 502; ADR-INT-004
  Priority   : HIGH

#### AC-INT-041 — [REQ-INT-036]
  Given  : Check 641 of `approve-service` version 2 is COMPLETED and undecided and the Approval API answers HTTP 500
  When   : the employee records decision APPROVED
  Then   : the answer is HTTP 502 with code `INT-502-APPROVAL-API-FAILED` and detail "The approval was not executed: the host Approval API answered 500. Nothing was recorded; you can try again."; Check 641 holds no decision

### REQ-INT-037 — A timed-out approval records nothing
  Pattern    : unwanted
  Statement  : If the Approval API does not answer within the approval timeout of the platform configuration, then the system shall record no decision and refuse with code INT-504-APPROVAL-API-TIMED-OUT.
  Traces     : US-INT-007
  Entities   : ENT-RPT-001
  Rationale  : Every outbound call has a limit; the employee learns the approval was not confirmed.
  Source     : POL-INT-010; [KB:raw-idea.md §12] limits; profile 504; ADR-INT-012
  Priority   : HIGH

#### AC-INT-042 — [REQ-INT-037]
  Given  : the approval timeout is 10 seconds, Check 642 of `approve-service` version 2 is COMPLETED and undecided, and the Approval API does not answer
  When   : the employee records decision APPROVED
  Then   : after 10 seconds the answer is HTTP 504 with code `INT-504-APPROVAL-API-TIMED-OUT` and detail "The approval was not executed: the host Approval API did not answer within 10 seconds. Nothing was recorded; you can try again."; Check 642 holds no decision

### REQ-INT-038 — A retry is a new decision request
  Pattern    : event
  Statement  : When the employee records a decision again after a failed or timed-out Approval API call, the system shall handle it as a new decision request.
  Traces     : US-INT-007
  Entities   : ENT-RPT-001
  Rationale  : Nothing was recorded, so the employee can decide again.
  Source     : POL-INT-010; ADR-RPT-003; ADR-INT-004
  Priority   : HIGH

#### AC-INT-043 — [REQ-INT-038]
  Given  : AC-INT-041 happened and the Approval API now answers HTTP 200
  When   : the employee records decision APPROVED on Check 641 again
  Then   : the host receives one call and Check 641 holds decision APPROVED, executed through the Approval API true

### REQ-INT-039 — A refusal after an executed approval logged
  Pattern    : unwanted
  Statement  : If the Report Store refuses a decision after the Approval API call succeeded, then the system shall answer with the Report Store's refusal and log the executed approval with the Check identifier and request number.
  Traces     : US-INT-007
  Entities   : ENT-RPT-001
  Rationale  : A decision recorded by another request in between leaves an approval the operator must reconcile with the host.
  Source     : POL-INT-003, POL-INT-010; ADR-INT-004
  Priority   : —

#### AC-INT-044 — [REQ-INT-039]
  Given  : Check 643 of `approve-service` version 2, request `R-643`, is COMPLETED and undecided; while its Approval API call is answering HTTP 200, another request records decision REJECTED on Check 643
  When   : the employee's APPROVED decision is handed to the Report Store
  Then   : the answer is HTTP 409 with code `RPT-409-DECISION-ALREADY-RECORDED`, and the service log holds an entry naming Check 643, request "R-643" and the executed approval

### REQ-INT-040 — The Checks of the request the host opened
  Pattern    : event
  Statement  : When the host screen opens the employee frontend with a service code, a request number and an employee identity, the system shall show the Checks of that service code and request number, newest first.
  Traces     : US-INT-008
  Entities   : ENT-RPT-001
  Rationale  : The employee works on one request at a time, inside the host screen.
  Source     : POL-INT-012; [KB:raw-idea.md §15 A1]; CON-RPT-004; ADR-INT-006, ADR-INT-011
  Priority   : HIGH

#### AC-INT-045 — [REQ-INT-040]
  Given  : request `REQ-2026-0042` of `scholarship-request` has Checks 701 (started 09:00) and 702 (started 10:30)
  When   : the host opens the frontend with `scholarship-request`, `REQ-2026-0042` and employee `E-3307`
  Then   : the list shows Check 702 first and Check 701 second

### REQ-INT-041 — The frontend needs the request it is opened for
  Pattern    : unwanted
  Statement  : If the employee frontend is opened without a service code, a request number or an employee identity, then the system shall show no Check and tell the employee that the screen must be opened from the host system for one request.
  Traces     : US-INT-008
  Entities   : ENT-RPT-001
  Rationale  : Without its launch context the frontend cannot know which request, or which employee, it serves.
  Source     : POL-INT-012; RULE-INT-004; ADR-INT-011
  Priority   : HIGH

#### AC-INT-046 — [REQ-INT-041]
  Given  : the frontend is opened with service `scholarship-request` and employee `E-3307` but no request number
  When   : the screen loads
  Then   : no Check is listed and the screen shows "Open this screen from the host system for one request."

### REQ-INT-042 — What each Check of the request shows
  Pattern    : ubiquitous
  Statement  : The system shall show, for each Check of the request, its identifier, status, Overall Status, start time, end time and Employee Decision.
  Traces     : US-INT-008
  Entities   : ENT-RPT-001
  Rationale  : The employee picks the Check to open from its state and result.
  Source     : POL-INT-012; CON-RPT-004
  Priority   : —

#### AC-INT-047 — [REQ-INT-042]
  Given  : Check 703 of the opened request is COMPLETED, NOT_COMPLIANT, started 09:00, ended 09:01, decision REJECTED
  When   : the Checks of the request are shown
  Then   : the entry of Check 703 shows status COMPLETED, Overall Status NOT_COMPLIANT, start 09:00, end 09:01 and decision REJECTED

### REQ-INT-043 — The total when not every Check is listed
  Pattern    : unwanted
  Statement  : If a request has more Checks than the list shows, then the system shall show the total number of Checks of the request.
  Traces     : US-INT-008
  Entities   : ENT-RPT-001
  Rationale  : The Report Store lists 100 Checks at most; the employee must know when more exist.
  Source     : POL-INT-012; CON-RPT-004 (at most 100, with total)
  Priority   : —

#### AC-INT-048 — [REQ-INT-043]
  Given  : the opened request has 104 Checks
  When   : the Checks of the request are shown
  Then   : 100 Checks are listed and the screen states that the request has 104 Checks

### REQ-INT-044 — A Check started from the frontend
  Pattern    : event
  Statement  : When the employee starts a Check from the Checks of a request, the system shall start it for the service code and request number of the request under the employee identity the host passed.
  Traces     : US-INT-001, US-INT-008
  Entities   : ENT-RPT-001
  Rationale  : The employee starts a Check for the request on the host screen without typing its identifiers.
  Source     : POL-INT-001, POL-INT-002; ADR-INT-006, ADR-INT-011
  Priority   : HIGH

#### AC-INT-049 — [REQ-INT-044]
  Given  : the frontend was opened with `scholarship-request`, `REQ-2026-0042` and employee `E-3307`
  When   : the employee starts a Check
  Then   : a Check of `scholarship-request` for request "REQ-2026-0042" by employee "E-3307" is accepted and appears first in the list

### REQ-INT-045 — The report's header
  Pattern    : event
  Statement  : When the employee opens a Check, the system shall show its status, service code, service package version, fetch mode, request number, employee and start time, and once it has ended its end time.
  Traces     : US-INT-009
  Entities   : ENT-RPT-001
  Rationale  : The metadata tells the employee what the report was built on.
  Source     : POL-INT-013; [KB:raw-idea.md §7] metadata; domain-profile §5 G11; CON-RPT-003
  Priority   : HIGH

#### AC-INT-050 — [REQ-INT-045]
  Given  : Check 704 is COMPLETED on `scholarship-request` version 3, fetch mode `path`, request `REQ-2026-0042`, employee `E-3307`, model `gemini-flash-lite`
  When   : the employee opens Check 704
  Then   : the screen shows COMPLETED, `scholarship-request`, version 3, `path`, "REQ-2026-0042", "E-3307", its start and end times, its Overall Status and model "gemini-flash-lite"

### REQ-INT-046 — Every finding beside its evidence
  Pattern    : ubiquitous
  Statement  : The system shall show every finding of a report as one entry holding its condition, outcome, evidence and note side by side.
  Traces     : US-INT-009
  Entities   : ENT-RPT-001
  Rationale  : Every finding carries its evidence so the employee can verify it.
  Source     : POL-INT-013; [KB:raw-idea.md §7]; domain-profile §5 G10; review AIAS-11
  Priority   : HIGH

#### AC-INT-051 — [REQ-INT-046]
  Given  : Check 705 has a finding "GPA at least 3.0", NOT_SATISFIED, evidence "2.7", note "Below the minimum"
  When   : the employee opens Check 705
  Then   : one entry shows "GPA at least 3.0", NOT_SATISFIED, "2.7" and "Below the minimum" together

### REQ-INT-047 — Documents read, missing and unreadable
  Pattern    : ubiquitous
  Statement  : The system shall show every document outcome of a report with its document type, source mode and read status, and for an unreadable document its reason and detail.
  Traces     : US-INT-009
  Entities   : ENT-RPT-001
  Rationale  : The report says what was read, what is missing and what could not be read.
  Source     : POL-INT-013; [KB:raw-idea.md §7] "Documents: What was read, what is missing, what could not be read"; domain-profile §5 G6
  Priority   : HIGH

#### AC-INT-052 — [REQ-INT-047]
  Given  : Check 706 has TRANSCRIPT READ, ID_CARD UNREADABLE with reason TOO_LARGE and detail "12 MB exceeds 10 MB"
  When   : the employee opens Check 706
  Then   : the documents show TRANSCRIPT as read and ID_CARD as unreadable with reason TOO_LARGE and detail "12 MB exceeds 10 MB"

### REQ-INT-048 — Unread service queries shown
  Pattern    : ubiquitous
  Statement  : The system shall show every service query of a report whose data could not be read, with its detail.
  Traces     : US-INT-009
  Entities   : ENT-RPT-001
  Rationale  : Nothing that could not be read is skipped silently.
  Source     : POL-INT-013; [KB:raw-idea.md §12]; domain-profile §5 G6; CON-RPT-003 unreadQueries
  Priority   : —

#### AC-INT-053 — [REQ-INT-048]
  Given  : Check 707 has unread query `request_details` with detail "query timed out"
  When   : the employee opens Check 707
  Then   : the screen shows `request_details` as not read with "query timed out"

### REQ-INT-049 — Never presented as COMPLIANT with a missing document
  Pattern    : unwanted
  Statement  : If a report holds a MISSING document outcome, then the system shall not present its Overall Status as COMPLIANT.
  Traces     : US-INT-009
  Entities   : ENT-RPT-001
  Rationale  : A missing required document prevents COMPLIANT; the display never contradicts that.
  Source     : POL-INT-014; [KB:raw-idea.md §7]; domain-profile §5 G6; review AIAS-11; ADR-INT-011
  Priority   : HIGH

#### AC-INT-054 — [REQ-INT-049]
  Given  : Check 708 is COMPLETED with Overall Status NEEDS_MANUAL_REVIEW and ID_CARD MISSING
  When   : the employee opens Check 708
  Then   : the screen shows Overall Status NEEDS_MANUAL_REVIEW and ID_CARD as missing

#### AC-INT-055 — [REQ-INT-049]
  Given  : a report reaches the frontend with Overall Status COMPLIANT and a MISSING ID_CARD outcome
  When   : the employee opens it
  Then   : the screen shows "Not verified — a required document is missing" instead of COMPLIANT, with ID_CARD as missing

### REQ-INT-050 — A failed Check shows its reason
  Pattern    : event
  Statement  : When the employee opens a FAILED Check, the system shall show its failure reason and failure detail and no Overall Status.
  Traces     : US-INT-009
  Entities   : ENT-RPT-001
  Rationale  : A failed Check must be visible as failed, never as a result.
  Source     : POL-INT-013; CON-RPT-003; CON-CHK-003
  Priority   : —

#### AC-INT-056 — [REQ-INT-050]
  Given  : Check 709 is FAILED with reason TIMED_OUT and detail "The Check exceeded 120 seconds."
  When   : the employee opens Check 709
  Then   : the screen shows FAILED, TIMED_OUT and "The Check exceeded 120 seconds." and no Overall Status

### REQ-INT-051 — Report texts shown as plain text
  Pattern    : ubiquitous
  Statement  : The system shall show every condition, evidence, note, detail and file name text as plain text and shall never interpret it as markup or follow it as a link.
  Traces     : US-INT-009
  Entities   : ENT-RPT-001
  Rationale  : Document content is data, never instructions.
  Source     : [KB:raw-idea.md §12] "Document content is treated as data, never as instructions"; domain-profile §5 G7; ADR-INT-011
  Priority   : HIGH

#### AC-INT-057 — [REQ-INT-051]
  Given  : Check 710 has a finding whose evidence is `<script>alert(1)</script> <a href="x">here</a>`
  When   : the employee opens Check 710
  Then   : the evidence is shown literally as the characters `<script>alert(1)</script> <a href="x">here</a>`; no script runs and no link is shown

### REQ-INT-052 — A recorded decision shown
  Pattern    : event
  Statement  : When the opened Check holds an Employee Decision, the system shall show the decision, who took it, when, and whether it was executed through the Approval API.
  Traces     : US-INT-009
  Entities   : ENT-RPT-001
  Rationale  : The employee sees the decision beside the report it was based on.
  Source     : POL-INT-013, POL-INT-007; CON-RPT-003
  Priority   : —

#### AC-INT-058 — [REQ-INT-052]
  Given  : Check 711 holds decision APPROVED by `E-3307` at 11:05, executed through the Approval API true
  When   : the employee opens Check 711
  Then   : the screen shows APPROVED, "E-3307", 11:05 and "executed through the Approval API"

### REQ-INT-053 — Upload offered while documents are awaited
  Pattern    : state
  Statement  : While an opened Check is AWAITING_DOCUMENTS, the system shall offer the employee the document upload and the upload confirmation of that Check.
  Traces     : US-INT-003, US-INT-004
  Entities   : ENT-RPT-001
  Rationale  : The employee reaches the two manual-mode actions from the Check they belong to.
  Source     : POL-INT-004, POL-INT-006; ADR-INT-006
  Priority   : —

#### AC-INT-059 — [REQ-INT-053]
  Given  : Check 712 is AWAITING_DOCUMENTS
  When   : the employee opens Check 712
  Then   : the document upload and the upload confirmation are offered and the decision is not

### REQ-INT-054 — Decision offered on a completed, undecided Check
  Pattern    : state
  Statement  : While an opened Check is COMPLETED and holds no Employee Decision, the system shall offer the employee the decision on that Check.
  Traces     : US-INT-005
  Entities   : ENT-RPT-001
  Rationale  : A decision stands beside one completed report and is recorded once.
  Source     : POL-INT-007; CON-RPT-006; ADR-INT-006
  Priority   : —

#### AC-INT-060 — [REQ-INT-054]
  Given  : Check 713 is COMPLETED with no decision and Check 714 is COMPLETED with decision APPROVED
  When   : the employee opens each of them
  Then   : the decision is offered on Check 713 and not on Check 714

### REQ-INT-055 — A Check that has not ended kept current
  Pattern    : state
  Statement  : While an opened Check is AWAITING_DOCUMENTS or RUNNING, the system shall read it again at the polling interval of the frontend configuration.
  Traces     : US-INT-010
  Entities   : ENT-RPT-001
  Rationale  : The Check runs asynchronously and is polled for its result.
  Source     : POL-INT-015; [KB:raw-idea.md §5]; profile `stack.frontend.libraries.server-state`; ADR-INT-011
  Priority   : MEDIUM

#### AC-INT-061 — [REQ-INT-055]
  Given  : the polling interval is 5 seconds and Check 715 is RUNNING
  When   : the employee keeps Check 715 open for 20 seconds
  Then   : Check 715 is read 4 more times without the employee reloading

### REQ-INT-056 — The report shown when the Check ends
  Pattern    : event
  Statement  : When an opened Check becomes COMPLETED or FAILED, the system shall stop reading it again and show its report or its failure.
  Traces     : US-INT-010
  Entities   : ENT-RPT-001
  Rationale  : An ended Check never changes; further reads are useless.
  Source     : POL-INT-015; CON-CHK-001 "COMPLETED and FAILED are final"
  Priority   : MEDIUM

#### AC-INT-062 — [REQ-INT-056]
  Given  : Check 716 is RUNNING and open on the screen
  When   : a read shows Check 716 COMPLETED
  Then   : the report of Check 716 is shown and no further read of Check 716 is made while it stays open

### REQ-INT-057 — The frontend uses only the REST API
  Pattern    : ubiquitous
  Statement  : The system shall give the employee frontend only operations of the REST API that every host system may call.
  Traces     : US-INT-011
  Entities   : —
  Rationale  : A host can show the same Checks and reports with its own components without changing the service.
  Source     : POL-INT-016; [KB:raw-idea.md §11, §15 A1]; ADR-INT-001
  Priority   : —

#### AC-INT-063 — [REQ-INT-057]
  Given  : the operations the frontend calls are listed from its network traffic
  When   : each is compared with the published API document of the service
  Then   : every operation the frontend calls appears in the published API document

### REQ-INT-058 — No server-rendered report page
  Pattern    : ubiquitous
  Statement  : The system shall offer no server-rendered report page and shall offer the report only as data through the REST API.
  Traces     : US-INT-011
  Entities   : —
  Rationale  : The employee frontend replaces the report page as the display path.
  Source     : POL-INT-016; [KB:raw-idea.md §15 A1]; domain-profile D6; ADR-INT-001
  Priority   : —

#### AC-INT-064 — [REQ-INT-058]
  Given  : Check 717 exists
  When   : a caller requests `/api/v1/checks/717/view`
  Then   : the answer is HTTP 404 and no HTML report is returned

### REQ-INT-059 — Host Integration keeps nothing
  Pattern    : ubiquitous
  Statement  : The system shall keep no request data, uploaded file, report or decision in Host Integration after a request has been answered.
  Traces     : US-INT-012
  Entities   : —
  Rationale  : Each fact lives once, with its owner; nothing is carried from one Check to another.
  Source     : POL-INT-017; [KB:raw-idea.md §12]; domain-profile §5 G9; ADR-INT-007
  Priority   : —

#### AC-INT-065 — [REQ-INT-059]
  Given  : the employee uploaded a TRANSCRIPT for Check 718 and recorded a decision on Check 719
  When   : both requests have been answered
  Then   : Host Integration holds no copy of the file, the request data or the decision; they exist only in Document Access and the Report Store

### REQ-INT-060 — No host database reached
  Pattern    : ubiquitous
  Statement  : The system shall reach no host database from Host Integration; its only outbound host call is the Approval API.
  Traces     : US-INT-012
  Entities   : —
  Rationale  : All access to host data is read-only and belongs to the modules that run Checks.
  Source     : [KB:raw-idea.md §12] "All access to host data uses a read-only database user"; domain-profile §5 G3; POL-INT-017
  Priority   : —

#### AC-INT-066 — [REQ-INT-060]
  Given  : the service runs a full decision with the Approval API on Check 720
  When   : the outbound connections opened by Host Integration are listed
  Then   : the only host connection is the HTTP call to the Approval API; no database connection is opened by Host Integration

## A5 — Business rules

### RULE-INT-001 — Uploads only while the Check waits for documents
  Scope      : ENT-RPT-001
  Trigger    : on upload
  Statement  : The system shall prevent handing an upload to Document Access when the Check's status is not AWAITING_DOCUMENTS.
  Message    : Documents can be uploaded only while Check {checkId} is waiting for documents; its status is {status}.
  Traces     : REQ-INT-011
  Data source: ENT-RPT-001.checkStatus
  Source     : POL-INT-005; ADR-INT-005
  Test-Hint  : a RUNNING, a COMPLETED and a FAILED Check each refuse the upload

### RULE-INT-002 — A decision is complete before any Approval API call
  Scope      : ENT-RPT-001
  Trigger    : on record decision (before the Approval API call)
  Statement  : The system shall prevent calling the Approval API when the decision request carries no decision code of EMPLOYEE_DECISION or no deciding employee.
  Message    : The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing.
  Traces     : REQ-INT-034
  Data source: ENT-RPT-001.employeeDecision, ENT-RPT-001.decidedBy
  Source     : POL-INT-008; RULE-RPT-013 (same code and message — ADR-INT-010)

### RULE-INT-003 — Approval only on a completed, undecided Check
  Scope      : ENT-RPT-001
  Trigger    : on record decision (before the Approval API call)
  Statement  : The system shall prevent calling the Approval API when the Check's status is not COMPLETED or the Check already holds an Employee Decision.
  Message    : Check {checkId} is not completed; a decision can only be recorded on a completed Check. / Check {checkId} already has an Employee Decision.
  Traces     : REQ-INT-035
  Data source: ENT-RPT-001.checkStatus, ENT-RPT-001.employeeDecision
  Source     : POL-INT-008, POL-INT-009; RULE-RPT-011, RULE-RPT-012 (same codes and messages — ADR-INT-010)

### RULE-INT-004 — The frontend is opened for one request
  Scope      : ENT-RPT-001
  Trigger    : on opening the employee frontend
  Statement  : The system shall prevent showing any Check when the frontend was opened without a service code, a request number or an employee identity.
  Message    : Open this screen from the host system for one request.
  Traces     : REQ-INT-041
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.requestNumber, ENT-RPT-001.employeeId
  Source     : POL-INT-012; ADR-INT-011

## A6 — Lookups
```yaml name=lookups
lookups: []
```
Host Integration owns no lookup (ADR-INT-013). Consumed, not redefined: CHECK_STATUS, OVERALL_STATUS, FINDING_OUTCOME, CHECK_FAILURE_REASON (CHK); FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON (DOC); EMPLOYEE_DECISION (RPT); SERVICE_CODE, DOCUMENT_TYPE (REG).

## A7 — Status lifecycle
Not applicable — Host Integration owns no entity with a status. The Check status it reads moves only forward as the Check Engine and the Report Store define it (CON-CHK-001; ADR-RPT-002); INT's rules read it (RULE-INT-001, RULE-INT-003).

## A8 — Module dependencies
```yaml name=module-dependencies
consumes:
  - {module: RPT, entity: ENT-RPT-001, type: SOFT-READ}
  - {module: REG, entity: ENT-REG-002, type: SOFT-READ}
```
Both are read through the owners' published contracts over the in-process interfaces — the Check Run through CON-RPT-003 (read) and CON-RPT-006 (record a decision), the Service Package Version's approval API through CON-REG-012 — and no foreign key crosses modules (ADR-INT-013). The INT → REG read is the finer edge ADR-INT-008 declares; the owner's platform row INT depends_on [CHK, RPT, DOC] is unchanged. CHK and DOC promise no entity INT reads: INT calls their operations (CON-CHK-004, CON-CHK-005, CON-DOC-003), so the INT → CHK and INT → DOC edges are the platform edges.

| External service | Purpose | Integration kind |
|---|---|---|
| Host systems (Oracle ADF, others) | call the REST API; open the employee frontend in the host screen with the launch context | inbound REST; embedding |
| Host Approval API (per service version, optional) | executes an APPROVED decision the employee confirmed | outbound HTTP, one call, configured timeout (ADR-INT-009, ADR-INT-012) |
| Check Engine (CHK, in-process) | start a Check; confirm the uploads | CON-CHK-004, CON-CHK-005 |
| Document Access (DOC, in-process) | hand over an uploaded file | CON-DOC-003 |
| Report Store (RPT, in-process) | read a Check; record a decision | CON-RPT-003, CON-RPT-006 |
| Service Registry (REG, in-process) | the approval API of a version | CON-REG-012 |

# PART B — SCREEN REQUIREMENTS

## SCR-REQ-INT-001 — Checks of a request
### B1 — Definition
  Purpose      : See the Checks of the request the host screen is showing, open one, and start a new one.
  Entities     : ENT-RPT-001
  Operations   : list, create (start a Check)
  Users        : Employee
  Navigation   : INT → host screen (embedded) → Checks of a request; from: host screen; to: SCR-REQ-INT-002
  Content shape: flat list of records
  Traces       : REQ-INT-040, REQ-INT-041, REQ-INT-042, REQ-INT-043, REQ-INT-044
### B2 — Search / list
  No filter — the list is scoped by the launch context (service code, request number) the host passes; applies RULE-INT-004. Columns: Check identifier, status (CHECK_STATUS), Overall Status (OVERALL_STATUS), start time, end time, Employee Decision (EMPLOYEE_DECISION) (REQ-INT-042); newest first, with the total when not all are listed (REQ-INT-043).
### B3 — Input
  No field is typed. Action "Start a Check" → start a Check with the launch context (REQ-INT-044, REQ-INT-001); refusals per REQ-INT-006. Selecting a Check → SCR-REQ-INT-002.
### B4 — Access
  Employee — list, start. No role check in this version (raw-idea A2).
### B5 — API expectations
| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| list the Checks of a request (RPT's read) | GET | /api/v1/checks | serviceCode, requestNumber | up to 100 Checks newest first + total | — | REQ-INT-040, REQ-INT-042, REQ-INT-043 |
| start a Check | POST | /api/v1/checks | serviceCode, requestNumber, employeeId | accepted: checkId, status, address of the Check's read | — | REQ-INT-001 … REQ-INT-006, REQ-INT-044 |

## SCR-REQ-INT-002 — Check report
### B1 — Definition
  Purpose      : Follow a Check until it ends and read its report — the Overall Status, each finding beside its evidence, the documents read, missing or unreadable, the unread queries — or its failure, and any recorded decision.
  Entities     : ENT-RPT-001
  Operations   : read
  Users        : Employee
  Navigation   : INT → host screen (embedded) → Check report; from: SCR-REQ-INT-001; to: SCR-REQ-INT-003, SCR-REQ-INT-004 (while AWAITING_DOCUMENTS), SCR-REQ-INT-005 (while COMPLETED and undecided)
  Content shape: header + repeating lines (findings, documents, unread queries)
  Traces       : REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, REQ-INT-049, REQ-INT-050, REQ-INT-051, REQ-INT-052, REQ-INT-053, REQ-INT-054, REQ-INT-055, REQ-INT-056
### B2 — Search / list
  Not applicable — one Check, addressed by its identifier.
### B3 — Input
  No input. Header per REQ-INT-045; findings per REQ-INT-046; documents per REQ-INT-047; unread queries per REQ-INT-048; status presentation per REQ-INT-049; failure per REQ-INT-050; texts per REQ-INT-051; decision per REQ-INT-052. Actions: "Upload documents" and "Confirm uploads" while AWAITING_DOCUMENTS (REQ-INT-053); "Record decision" while COMPLETED and undecided (REQ-INT-054). Refresh per REQ-INT-055, REQ-INT-056.
### B4 — Access
  Employee — read. No role check in this version (raw-idea A2).
### B5 — API expectations
| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read a Check and its report (RPT's read) | GET | /api/v1/checks/{checkId} | checkId | Check with status and, once ended, report or failure, and decision | — | REQ-INT-045 … REQ-INT-056 |

## SCR-REQ-INT-003 — Document upload
### B1 — Definition
  Purpose      : Upload, one at a time, the documents of a Check whose service obtains them from the employee.
  Entities     : ENT-RPT-001
  Operations   : create (hand over an upload), list (uploaded documents)
  Users        : Employee
  Navigation   : INT → host screen (embedded) → Document upload; from: SCR-REQ-INT-002; to: SCR-REQ-INT-004, SCR-REQ-INT-002
  Content shape: flat record (one upload) + list of uploaded documents
  Traces       : REQ-INT-009, REQ-INT-010, REQ-INT-011, REQ-INT-012, REQ-INT-013, REQ-INT-014, REQ-INT-015, REQ-INT-016, REQ-INT-017
### B2 — Search / list
  Uploaded documents of the Check: document type (DOCUMENT_TYPE), file name, size (REQ-INT-017). No filter.
### B3 — Input
  Document type — choice of the required document types of the Check's service (DOCUMENT_TYPE; REQ-INT-016); file — one file (REQ-INT-009). Action "Upload" → hand over an upload; applies RULE-INT-001; the oversized notice per REQ-INT-013; the size limit per REQ-INT-014; refusals per REQ-INT-006, REQ-INT-012. The upload never confirms (REQ-INT-019).
### B4 — Access
  Employee — upload. No role check in this version (raw-idea A2).
### B5 — API expectations
| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| hand over an upload | POST | /api/v1/checks/{checkId}/documents | checkId, documentType, file | uploaded document (identifier, type, file name, size, oversized, notice) | RULE-INT-001 | REQ-INT-009 … REQ-INT-015 |
| list the uploaded documents of a Check (DOC's read) | GET | /api/v1/uploaded-documents | checkId | uploaded documents | — | REQ-INT-017 |
| read the service's required document types (REG's read) | GET | /api/v1/services/{serviceCode} | serviceCode | service summary with required document types | — | REQ-INT-016 |

## SCR-REQ-INT-004 — Upload confirmation
### B1 — Definition
  Purpose      : Confirm, as a separate action, that every document of a `manual` Check has been uploaded, so that the Check continues.
  Entities     : ENT-RPT-001
  Operations   : custom (confirm uploads)
  Users        : Employee
  Navigation   : INT → host screen (embedded) → Upload confirmation; from: SCR-REQ-INT-002, SCR-REQ-INT-003; to: SCR-REQ-INT-002
  Content shape: flat record
  Traces       : REQ-INT-018, REQ-INT-019, REQ-INT-020
### B2 — Search / list
  Not applicable — the uploaded documents and the required types without an upload are shown read-only (REQ-INT-020).
### B3 — Input
  No field. Action "Confirm uploads" → confirm the uploads (REQ-INT-018); refusals per REQ-INT-006.
### B4 — Access
  Employee — confirm. No role check in this version (raw-idea A2).
### B5 — API expectations
| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| confirm the uploads | POST | /api/v1/checks/{checkId}/upload-confirmation | checkId | accepted: checkId, status | — | REQ-INT-018, REQ-INT-019 |
| list the uploaded documents of a Check (DOC's read) | GET | /api/v1/uploaded-documents | checkId | uploaded documents | — | REQ-INT-020 |

## SCR-REQ-INT-005 — Employee decision
### B1 — Definition
  Purpose      : Record the approve or reject decision on a completed Check — executed through the host's Approval API where the Check's version enables it.
  Entities     : ENT-RPT-001, ENT-REG-002
  Operations   : create (record a decision)
  Users        : Employee
  Navigation   : INT → host screen (embedded) → Employee decision; from: SCR-REQ-INT-002; to: SCR-REQ-INT-002
  Content shape: flat record
  Traces       : REQ-INT-021, REQ-INT-022, REQ-INT-023, REQ-INT-024, REQ-INT-025, REQ-INT-026, REQ-INT-027, REQ-INT-028, REQ-INT-034, REQ-INT-035, REQ-INT-036, REQ-INT-037, REQ-INT-038
### B2 — Search / list
  Not applicable.
### B3 — Input
  Decision — EMPLOYEE_DECISION (APPROVED / REJECTED); deciding employee — the launch identity, not typed (REQ-INT-023). Action "Record decision" → record a decision; applies RULE-INT-002, RULE-INT-003; Approval API per REQ-INT-025 … REQ-INT-028; failures per REQ-INT-036, REQ-INT-037 (the employee may submit again — REQ-INT-038); refusals per REQ-INT-006. The decision is the only submit of the screen (REQ-INT-024).
### B4 — Access
  Employee — decide. No role check in this version (raw-idea A2).
### B5 — API expectations
| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| record a decision | POST | /api/v1/checks/{checkId}/decision | checkId, employeeDecision, decidedBy | recorded decision (decision, decided by, decided at, executed through the Approval API) | RULE-INT-002, RULE-INT-003 | REQ-INT-021 … REQ-INT-039 |

# STANDALONE

## Traceability matrix
| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-INT-001 | REQ-INT-001, REQ-INT-002, REQ-INT-003, REQ-INT-004, REQ-INT-005, REQ-INT-044 | AC-INT-001, AC-INT-002, AC-INT-003, AC-INT-004, AC-INT-005, AC-INT-006, AC-INT-049 | — | ENT-RPT-001 | SCR-REQ-INT-001 |
| US-INT-002 | REQ-INT-006, REQ-INT-007, REQ-INT-008 | AC-INT-007, AC-INT-008, AC-INT-009, AC-INT-010, AC-INT-011, AC-INT-012 | — | ENT-RPT-001 | SCR-REQ-INT-001, SCR-REQ-INT-003, SCR-REQ-INT-004, SCR-REQ-INT-005 |
| US-INT-003 | REQ-INT-009, REQ-INT-010, REQ-INT-011, REQ-INT-012, REQ-INT-013, REQ-INT-014, REQ-INT-015, REQ-INT-016, REQ-INT-017, REQ-INT-053 | AC-INT-013, AC-INT-014, AC-INT-015, AC-INT-016, AC-INT-017, AC-INT-018, AC-INT-019, AC-INT-020, AC-INT-021, AC-INT-059 | RULE-INT-001 | ENT-RPT-001 | SCR-REQ-INT-003 |
| US-INT-004 | REQ-INT-018, REQ-INT-019, REQ-INT-020, REQ-INT-053 | AC-INT-022, AC-INT-023, AC-INT-024, AC-INT-059 | — | ENT-RPT-001 | SCR-REQ-INT-004 |
| US-INT-005 | REQ-INT-021, REQ-INT-022, REQ-INT-023, REQ-INT-024, REQ-INT-054 | AC-INT-025, AC-INT-026, AC-INT-027, AC-INT-028, AC-INT-060 | — | ENT-RPT-001 | SCR-REQ-INT-005 |
| US-INT-006 | REQ-INT-025, REQ-INT-026, REQ-INT-027, REQ-INT-028, REQ-INT-029, REQ-INT-030, REQ-INT-031, REQ-INT-032, REQ-INT-033, REQ-INT-034, REQ-INT-035 | AC-INT-029, AC-INT-030, AC-INT-031, AC-INT-032, AC-INT-033, AC-INT-034, AC-INT-035, AC-INT-036, AC-INT-037, AC-INT-038, AC-INT-039, AC-INT-040 | RULE-INT-002, RULE-INT-003 | ENT-RPT-001, ENT-REG-002 | SCR-REQ-INT-005 |
| US-INT-007 | REQ-INT-036, REQ-INT-037, REQ-INT-038, REQ-INT-039 | AC-INT-041, AC-INT-042, AC-INT-043, AC-INT-044 | — | ENT-RPT-001 | SCR-REQ-INT-005 |
| US-INT-008 | REQ-INT-040, REQ-INT-041, REQ-INT-042, REQ-INT-043, REQ-INT-044 | AC-INT-045, AC-INT-046, AC-INT-047, AC-INT-048, AC-INT-049 | RULE-INT-004 | ENT-RPT-001 | SCR-REQ-INT-001 |
| US-INT-009 | REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, REQ-INT-049, REQ-INT-050, REQ-INT-051, REQ-INT-052 | AC-INT-050, AC-INT-051, AC-INT-052, AC-INT-053, AC-INT-054, AC-INT-055, AC-INT-056, AC-INT-057, AC-INT-058 | — | ENT-RPT-001 | SCR-REQ-INT-002 |
| US-INT-010 | REQ-INT-055, REQ-INT-056 | AC-INT-061, AC-INT-062 | — | ENT-RPT-001 | SCR-REQ-INT-002 |
| US-INT-011 | REQ-INT-057, REQ-INT-058 | AC-INT-063, AC-INT-064 | — | — | — |
| US-INT-012 | REQ-INT-059, REQ-INT-060 | AC-INT-065, AC-INT-066 | — | — | — |

Raw-idea §12 guardrails at INT's surface (AIAS-1; same approach as ADR-REG-008, ADR-RPT-008): (1) the LLM never triggers approval → REQ-INT-029 · (2) approval only as a result of the employee's action → REQ-INT-025, REQ-INT-027, REQ-INT-029, REQ-INT-035 · (3) read-only host access → REQ-INT-060 · (4) bound or typed parameters, nothing built from free text → REQ-INT-031 · (5) file paths inside the storage root → REQ-INT-015 · (6) nothing skipped silently → REQ-INT-013, REQ-INT-047, REQ-INT-048 · (7) document content is data → REQ-INT-051, REQ-INT-015 · (8) limits → REQ-INT-014, REQ-INT-037 · (9) nothing carried between Checks → REQ-INT-059.

## Decisions applied
| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| ADR-REG-001 | RPT owns the run records; INT owns none | P0 (REG) | ACCEPTED by owner |
| ADR-REG-006 | Limits are platform configuration (pattern for INT's limits) | P0 (REG) | ACCEPTED by owner |
| ADR-REG-008 | Each module states the §12 guardrails at its own surface | P1 (REG) | ACCEPTED |
| ADR-CHK-018 | CHK's in-process refusal codes INT passes through | P3.1 (CHK) | ACCEPTED |
| ADR-DOC-006 | INT passes the Check's service code and version with every upload | DOC | ACCEPTED by owner |
| ADR-DOC-012 | DOC's in-process refusal codes INT passes through | P3.1 (DOC) | ACCEPTED |
| ADR-RPT-003 | Decision on the Check Run; call the Approval API first; a failed call records nothing | P0 (RPT) | Confirmed at RPT prd-approval |
| ADR-RPT-005, ADR-RPT-006 | RPT serves the reads over HTTP; its writes are in-process | RPT | ACCEPTED |
| ADR-RPT-013 | RPT's decision refusal codes INT maps to ProblemDetail | P3.1 (RPT) | ACCEPTED |
| ADR-INT-001 | Four write operations; reads stay with their owners; no report page | P0 | Confirmed at prd-approval |
| ADR-INT-002 | Asynchronous start; identity as sent; no caller authentication | P0 | Confirmed at prd-approval |
| ADR-INT-003 | Owner's refusal codes passed through; INT codes for INT's own decisions | P0 | Confirmed at prd-approval |
| ADR-INT-004 | Approval API: APPROVED only, COMPLETED and undecided, where enabled; call first; failure records nothing | P0 | Confirmed at prd-approval |
| ADR-INT-005 | One file per upload, only while AWAITING_DOCUMENTS; confirmation separate | P0 | Confirmed at prd-approval |
| ADR-INT-006 | The employee frontend: launch context, four jobs, REST API only | P0 | Confirmed at prd-approval |
| ADR-INT-007 | INT keeps no records | P0 | Confirmed at prd-approval |
| ADR-INT-008 | INT reads CON-REG-012; platform row unchanged; entity-level edge | P0 | Confirmed at prd-approval |
| ADR-INT-009 | Approval API call shape: method + path, encoded request number, base address per environment, one call | P0 | Confirmed at prd-approval |
| ADR-INT-010 | Pre-call checks refuse with the Report Store's own codes | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-INT-011 | Frontend launch context, stored status with the MISSING safeguard, plain text, 5-second refresh, owners' reads | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-INT-012 | Approval timeout 10 s, upload request limit 50 MB, approval base address per environment | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-INT-013 | No ENT-INT; rules read the owners' fields; no lookup owned | P1 (this stage) | ACCEPTED — non-breaking |
| DEFAULT — approval timeout 10 seconds | The Approval API call waits at most 10 seconds | ADR-INT-012; domain best practice | Override: set the approval timeout in the platform configuration |
| DEFAULT — upload request limit 50 MB | An upload request above 50 MB is refused before it is read | ADR-INT-012 | Override: set the upload request limit (never below the maximum file size) |
| DEFAULT — polling interval 5 seconds | A Check that has not ended is read again every 5 seconds | ADR-INT-011; [KB:raw-idea.md §5] | Override: set the polling interval in the frontend configuration |
| DEFAULT — any 2xx answer is a successful approval | The Approval API succeeded when it answers 200–299 | ADR-INT-009 | Override: a per-host success rule in a later version |

## Access summary
| Role | Screens | Operations |
|---|---|---|
| Employee | SCR-REQ-INT-001 … SCR-REQ-INT-005 | start a Check, upload, confirm uploads, record a decision (INT); read Checks and reports (RPT), uploaded documents (DOC), required document types (REG) |
| Host System | — (embeds the frontend) | the same REST API: start a Check, upload, confirm, record a decision, and the owners' reads |
Caller authentication and who may view stored reports are deferred (raw-idea A2, domain-profile D4); no role check is specified in this version.
══════════════════════════════════════════════════════════════════
