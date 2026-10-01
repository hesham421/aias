<!-- source: PHASE:SVC-API / SUB:SVC-API-COMMAND -->
<!-- context: SVC-API-HEADER.md — phase-level preamble -->
<!-- traces: DBF-INT-001, DBF-INT-002, DBF-INT-003, DBF-INT-004, DBF-INT-005, DBF-INT-006, DBF-INT-007, DBF-INT-008, DBF-INT-009, DBF-INT-010, REQ-INT-001, REQ-INT-002, REQ-INT-003, REQ-INT-004, REQ-INT-005, REQ-INT-006, REQ-INT-007, REQ-INT-008, REQ-INT-009, REQ-INT-010, REQ-INT-011, REQ-INT-012, REQ-INT-013, REQ-INT-014, REQ-INT-015, REQ-INT-016, REQ-INT-017, REQ-INT-018, REQ-INT-019, REQ-INT-020, REQ-INT-021, REQ-INT-022, REQ-INT-023, REQ-INT-024, REQ-INT-025, REQ-INT-026, REQ-INT-027, REQ-INT-028, REQ-INT-029, REQ-INT-030, REQ-INT-031, REQ-INT-032, REQ-INT-033, REQ-INT-034, REQ-INT-035, REQ-INT-036, REQ-INT-037, REQ-INT-038, REQ-INT-039, REQ-INT-044, REQ-INT-053, REQ-INT-054, REQ-INT-057, REQ-INT-058, REQ-INT-059, REQ-INT-060, REQ-INT-065 -->
<!-- SUB:SVC-API-COMMAND:START traces=REQ-INT-001,REQ-INT-002,REQ-INT-003,REQ-INT-004,REQ-INT-005,REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-029,REQ-INT-030,REQ-INT-031,REQ-INT-032,REQ-INT-033,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-039,REQ-INT-044,REQ-INT-053,REQ-INT-054,REQ-INT-057,REQ-INT-058,REQ-INT-059,REQ-INT-060,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-006,DBF-INT-007,DBF-INT-008,DBF-INT-009,DBF-INT-010,REQ-INT-065 -->
### SVC-API-COMMAND — the four write operations

<!-- API:API-INT-001:START traces=REQ-INT-001,REQ-INT-002,REQ-INT-003,REQ-INT-004,REQ-INT-005,REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-044,REQ-INT-057,REQ-INT-058,REQ-INT-059,REQ-INT-060,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-005,DBF-INT-006 -->
### API-INT-001 — Start a Check
Entity       : the Report Store's Check Run (operation: create — starts a Check whose run the Report Store keeps)
Endpoint     : /api/v1/checks   verb: POST
Layers       : controller CheckIntakeController.start → service CheckIntakeService.start
Request      : body (application/json) StartCheckRequest {serviceCode (DBF-INT-003, string ≤ 100, required), requestNumber (DBF-INT-005, string ≤ 100, required), employeeId (DBF-INT-006, string ≤ 100, required)} — passed exactly as received, never trimmed or checked against a directory (REQ-INT-003, REQ-INT-004); presence is decided by the Check Engine (CHK-400-START-INCOMPLETE); no path or query parameter
Response     : 202 · StartedCheckResponse {checkId (DBF-INT-001, int64), status (DBF-INT-002 — RUNNING or AWAITING_DOCUMENTS), checkUrl (`/api/v1/checks/{checkId}`)} and header `Location: /api/v1/checks/{checkId}` (REQ-INT-005) · not paginated · no envelope
Validations  : body readable as JSON (PLATFORM-STD, ADR-INT-017)
Errors       : INT-400-REQUEST-INVALID (400) · CHK-400-START-INCOMPLETE (400, PASS-THROUGH) · CHK-422-SERVICE-NOT-AVAILABLE (422, PASS-THROUGH) · CHK-422-CONNECTION-NOT-ACTIVATED (422, PASS-THROUGH) · INT-500 (500)
Orchestration : parse → `CheckEnginePort.start` (PORTS) → answer 202 at once; the pipeline runs in the background inside the Check Engine (REQ-INT-002). INT writes no column: DBF-INT-001 and DBF-INT-002 come back from the call
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE — INT allocates nothing and decides on nothing it writes; the Check identifier is allocated by the Report Store through the Check Engine, and every start is a new, independent Check
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; REQ-INT-004)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-001
Covers       : REQ-INT-044 (the frontend's start uses this endpoint with its launch context); REQ-INT-057 … REQ-INT-060 hold for every INT endpoint (CORE).
<!-- API:API-INT-001:END -->

<!-- API:API-INT-002:START traces=REQ-INT-065,REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-053,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004 -->
### API-INT-002 — Hand over an uploaded document
Entity       : the Report Store's Check Run (operation: create — one uploaded document handed over for the Check)
Endpoint     : /api/v1/checks/{checkId}/documents   verb: POST
Layers       : controller CheckUploadController.upload → service UploadService.upload
Request      : path checkId (DBF-INT-001, integer int64, required); body (multipart/form-data) documentType (string ≤ 100, required — one of the service's required document types, checked by Document Access), file (binary, exactly one part, required); the service code and version are NOT accepted from the caller (REQ-INT-010)
Response     : 201 · UploadReceiptResponse {uploadedDocumentId (int64), documentType, fileName, fileSize (int64), oversized (boolean), notice (string, present only when oversized — REQ-INT-013)} · not paginated · no envelope
Validations  : request within `aias.integration.upload.request-limit` (REQ-INT-014, PLATFORM-STD) · RULE-INT-001 — Uploads only while the Check waits for documents · trigger: on upload · statement: The system shall prevent handing an upload to Document Access when the Check's status is not AWAITING_DOCUMENTS. · message (en): Documents can be uploaded only while Check {checkId} is waiting for documents; its status is {status}. · message (ar): PENDING ADR-INT-017
Errors       : INT-400-REQUEST-INVALID (400) · RPT-404-CHECK-NOT-FOUND (404, PASS-THROUGH) · INT-409-CHECK-NOT-AWAITING-DOCUMENTS (409, RULE-INT-001) · INT-413-UPLOAD-TOO-LARGE (413) · DOC-400-INCOMPLETE-UPLOAD (400, PASS-THROUGH) · DOC-404-SERVICE-VERSION-NOT-FOUND (404, PASS-THROUGH) · DOC-422-FETCH-MODE-NOT-MANUAL (422, PASS-THROUGH) · DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE (422, PASS-THROUGH) · DOC-409-CHECK-ENDED (409, PASS-THROUGH — ADR-INT-025) · DOC-422-UPLOAD-LIMIT-REACHED (422, PASS-THROUGH — ADR-INT-025) · INT-500 (500)
Orchestration : size limit (container, INT-413) → parse → load the Check: `CheckRecordPort.read(checkId)` (PORTS; unknown → RPT-404-CHECK-NOT-FOUND) → RULE-INT-001 on DBF-INT-002 → `DocumentAccessPort.handOver` with serviceCode DBF-INT-003 and versionNumber DBF-INT-004 of the Check → 201 with the receipt. INT writes no column; the Uploaded Document is Document Access's record. The upload never confirms the uploads (REQ-INT-019)
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE of INT's own allocation. Read-then-act: the status read and the hand-over are not atomic. If the Check leaves AWAITING_DOCUMENTS in between (a confirmation commits, or the Check Engine ends the Check and sends Document Access its end-of-Check notice), Document Access refuses the handover synchronously: its CheckEndedException becomes DOC-409-CHECK-ENDED, which INT passes through (ADR-INT-025). Document Access does not accept the file and delete it later. A handover that passed DOC's check just before the notice committed is swept by Document Access at the next end of any Check (Document Access's own safety net — ADR-INT-025). INT upholds its side of the ordering rule (PF-6) by handing over only after reading the Check as AWAITING_DOCUMENTS (RULE-INT-001). Concurrent uploads for one Check may exceed DOC's upload limit by at most the number running at once (Document Access's counting rule — ADR-INT-025); INT adds no guard
Security     : none — no permission model (raw-idea A2)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-002
Covers       : REQ-INT-065 (each same-type upload is its own handover). REQ-INT-016, REQ-INT-017 and REQ-INT-053 are the frontend's use of this endpoint with the owners' reads of the service and of the uploaded documents (P3.2).
<!-- API:API-INT-002:END -->

<!-- API:API-INT-003:START traces=REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-053,DBF-INT-001,DBF-INT-002 -->
### API-INT-003 — Confirm the uploads
Entity       : the Report Store's Check Run (operation: custom — confirm that the uploads of a `manual` Check are complete)
Endpoint     : /api/v1/checks/{checkId}/upload-confirmation   verb: POST
Layers       : controller CheckUploadController.confirm → service UploadConfirmationService.confirm
Request      : path checkId (DBF-INT-001, integer int64, required); body (application/json) UploadConfirmationRequest {} — an empty object, no field
Response     : 202 · ConfirmedCheckResponse {checkId (DBF-INT-001, int64), status (DBF-INT-002 — RUNNING)} · not paginated · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-INT-017)
Errors       : INT-400-REQUEST-INVALID (400) · CHK-404-CHECK-NOT-FOUND (404, PASS-THROUGH) · CHK-409-CHECK-NOT-AWAITING-DOCUMENTS (409, PASS-THROUGH) · INT-500 (500)
Orchestration : parse → `CheckEnginePort.confirm(checkId)` (PORTS) → 202; the pipeline continues in the background inside the Check Engine. INT writes no column
Repository   : none (no QR — ADR-INT-017)
Concurrency  : NONE in INT — the Check Engine's locking read serialises a confirmation against the upload-window deadline and a second confirmation (its own guard); the loser is refused CHK-409-CHECK-NOT-AWAITING-DOCUMENTS
Security     : none — no permission model (raw-idea A2)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-003
Covers       : REQ-INT-020 is the confirmation screen's use of the uploaded-documents read before this call (P3.2).
<!-- API:API-INT-003:END -->

<!-- API:API-INT-004:START traces=REQ-INT-006,REQ-INT-007,REQ-INT-008,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-029,REQ-INT-030,REQ-INT-031,REQ-INT-032,REQ-INT-033,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-039,REQ-INT-054,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-007,DBF-INT-008,DBF-INT-009,DBF-INT-010 -->
### API-INT-004 — Record an Employee Decision
Entity       : the Report Store's Check Run (operation: create — the Employee Decision recorded beside the Check's result)
Endpoint     : /api/v1/checks/{checkId}/decision   verb: POST
Layers       : controller DecisionController.decide → service DecisionService.decide
Request      : path checkId (DBF-INT-001, integer int64, required); body (application/json) DecisionRequest {employeeDecision (DBF-INT-007, string, `APPROVED` | `REJECTED`, required), decidedBy (DBF-INT-008, string ≤ 100, required — exactly as the host sent it)}; approvalApiExecuted is never accepted from the caller — INT sets it (REQ-INT-026)
Response     : 201 · RecordedDecisionResponse {checkId (int64), employeeDecision, decidedBy, decidedAt (date-time), approvalApiExecuted (boolean)} · not paginated · no envelope
Validations  : body readable (PLATFORM-STD) · RULE-INT-002 — A decision is complete before any Approval API call · trigger: on record decision (before the Approval API call) · statement: The system shall prevent calling the Approval API when the decision request carries no decision code of EMPLOYEE_DECISION or no deciding employee. · message (en): The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. · message (ar): PENDING ADR-INT-017 · RULE-INT-003 — Approval only on a completed, undecided Check · trigger: on record decision (before the Approval API call) · statement: The system shall prevent calling the Approval API when the Check's status is not COMPLETED or the Check already holds an Employee Decision. · message (en): Check {checkId} is not completed; a decision can only be recorded on a completed Check. / Check {checkId} already has an Employee Decision. · message (ar): PENDING ADR-INT-017
Errors       : INT-400-REQUEST-INVALID (400) · RPT-404-CHECK-NOT-FOUND (404, PASS-THROUGH) · RPT-400-DECISION-INCOMPLETE (400, RULE-INT-002 / PASS-THROUGH) · RPT-409-CHECK-NOT-COMPLETED (409, RULE-INT-003 / PASS-THROUGH) · RPT-409-DECISION-ALREADY-RECORDED (409, RULE-INT-003 / PASS-THROUGH) · RPT-422-APPROVAL-FLAG-ON-REJECTION (422, PASS-THROUGH) · INT-502-APPROVAL-API-FAILED (502) · INT-504-APPROVAL-API-TIMED-OUT (504) · INT-500 (500)
Orchestration : 
  1. parse (INT-400-REQUEST-INVALID); load the Check: `CheckRecordPort.read(checkId)` (unknown → RPT-404-CHECK-NOT-FOUND).
  2. When employeeDecision (DBF-INT-007) is `APPROVED`: integrate (XM-INT-001) — the approval definition of the Check's own version, by serviceCode DBF-INT-003 and versionNumber DBF-INT-004 (REQ-INT-030): enabled DBF-INT-009, method + path DBF-INT-010.
  3. Approval path — APPROVED and enabled (REQ-INT-025): take the per-Check lock (below); re-read the Check; RULE-INT-002 on the request (DBF-INT-007, DBF-INT-008) → RPT-400-DECISION-INCOMPLETE; RULE-INT-003 on DBF-INT-002 and DBF-INT-007 → RPT-409-CHECK-NOT-COMPLETED / RPT-409-DECISION-ALREADY-RECORDED — in every refusal the host receives no call (REQ-INT-034, REQ-INT-035). Then `HostApprovalPort.approve(definition, requestNumber DBF-INT-005, checkId, decidedBy)` once (REQ-INT-031 … REQ-INT-033): failure → INT-502-APPROVAL-API-FAILED, timeout → INT-504-APPROVAL-API-TIMED-OUT, nothing recorded and the employee may submit again as a new request (REQ-INT-036 … REQ-INT-038). Success → `CheckRecordPort.handOverDecision(checkId, APPROVED, decidedBy, approvalApiExecuted = true)` (REQ-INT-026). A refusal of the hand-over after a successful call → answer that refusal unchanged and log at WARN "Approval executed but decision not recorded: Check {checkId}, request {requestNumber}" (REQ-INT-039). Release the lock.
  4. Every other case — REJECTED (REQ-INT-027), a version that does not enable the Approval API (REQ-INT-028), or a code that is not APPROVED: no Approval API call; `CheckRecordPort.handOverDecision(checkId, employeeDecision, decidedBy, approvalApiExecuted = false)` (REQ-INT-021); the Report Store's refusals pass through unchanged (REQ-INT-006).
  5. 201 with the recorded decision (REQ-INT-022). INT writes no column: DBF-INT-007 and DBF-INT-008 are written by the Report Store from the hand-over. The Approval API is called from this step 3 only (REQ-INT-029).
Repository   : none (no QR — ADR-INT-017)
Concurrency  : guard — a per-Check-identifier in-process lock (`ConcurrentHashMap<Long, ReentrantLock>`, removed when released) held across step 3 from the re-read to the hand-over: two simultaneous APPROVED decisions on one approval-enabled Check cannot both call the host; the second waits, re-reads, finds the decision and is refused RPT-409-DECISION-ALREADY-RECORDED before any call. The Report Store's conditional update stays the final guard for every path (one decision per Check). A multi-instance deployment is not covered by the in-process lock (ADR-INT-017 (6))
Security     : none — no permission model (raw-idea A2); `HostApprovalPort` and `ApprovalDefinitionPort` are injected into `DecisionService` only (AIAS-4)
Localization : messages en per SRS; ar PENDING ADR-INT-017
Honours      : CON-INT-004
Covers       : REQ-INT-023 (the frontend sends its launch identity as decidedBy) and REQ-INT-054 (the frontend offers the decision only on a COMPLETED, undecided Check) are the frontend's use of this endpoint (P3.2); REQ-INT-024: no other endpoint carries a decision.
<!-- API:API-INT-004:END -->
<!-- SUB:SVC-API-COMMAND:END -->
