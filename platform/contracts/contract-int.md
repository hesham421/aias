# CONTRACT — Host Integration (INT) — outbound promise
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias
Inputs : srs-int.md, registry-srs-int.md
Items  : CON 4 (entity promises 0 · lookup promises 0 · operations 4)
Read by: host systems (REST API) and INT's employee frontend track; no platform module consumes INT (tier 4 — ADR-INT-014)
══════════════════════════════════════════════════════════════════

INT is tier 4 (depends on CHK, RPT and DOC; reads REG's approval API — ADR-INT-008). It declares no entity (ADR-INT-007, ADR-INT-013), so it promises no entity and no lookup: every code below is owned and promised by CHK (CON-CHK-001 … CON-CHK-005), DOC (CON-DOC-001 … CON-DOC-003), RPT (CON-RPT-001 … CON-RPT-006) or REG (CON-REG-012). What hosts and the employee frontend build against are the four write operations below. Operations name no path and no verb; P3.1 chooses the implementation and states `Honours: CON-…` on it. Refusals of CHK, DOC and RPT keep their owner's code, HTTP status and message (ADR-INT-003, ADR-INT-010); INT's own codes follow the profile format `{MOD}-{http}[-{SLUG}]`. Every error is answered as a ProblemDetail (RFC 9457) → {type, title, status, detail, code}.

Identifier rule (profile `conventions.identifiers`): the Check identifier is the Report Store's `NUMBER(19)` (CON-RPT-001), passed by value. Host identifiers — the request number, the employee identity and the deciding employee — are strings exactly as the host sent them (a consumer that keeps them stores `VARCHAR2(100 CHAR)`); INT checks them against no directory (raw-idea A2). No caller authentication in this version.

## Operations (host systems / employee frontend → INT)

### CON-INT-001 — Start a Check
Signature : startCheck(serviceCode, requestNumber, employeeId) → accepted {checkId (`NUMBER(19)`), status (CON-CHK-001: RUNNING or AWAITING_DOCUMENTS), address of the Check's read} · errors: request unreadable (INT-400-REQUEST-INVALID), incomplete start (CHK-400-START-INCOMPLETE), service not available (CHK-422-SERVICE-NOT-AVAILABLE), connection not activated (CHK-422-CONNECTION-NOT-ACTIVATED), unexpected failure (INT-500)
Entity    : ENT-RPT-001
Notes     : answers as soon as the Check Engine has created the Check; the report is read later through the Report Store's read of that Check (CON-RPT-003). Request number and employee identity are handed on exactly as sent.
Traces    : REQ-INT-001, REQ-INT-002, REQ-INT-003, REQ-INT-004, REQ-INT-005, REQ-INT-006, REQ-INT-007

### CON-INT-002 — Hand over a file uploaded for a Check
Signature : uploadDocument(checkId, documentType, file) → {uploadedDocumentId, documentType, fileName, fileSize, oversized, notice (present only when oversized)} · errors: request unreadable (INT-400-REQUEST-INVALID), Check not found (RPT-404-CHECK-NOT-FOUND), Check not waiting for documents (INT-409-CHECK-NOT-AWAITING-DOCUMENTS — RULE-INT-001), upload larger than the upload request limit (INT-413-UPLOAD-TOO-LARGE), incomplete upload (DOC-400-INCOMPLETE-UPLOAD), service package version not found (DOC-404-SERVICE-VERSION-NOT-FOUND), fetch mode not manual (DOC-422-FETCH-MODE-NOT-MANUAL), document type not of the service (DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE), unexpected failure (INT-500)
Entity    : ENT-RPT-001
Notes     : one file of one document type per call; the service code and version handed to Document Access are the Check's own (REQ-INT-010); the file is passed as content, its name as text (REQ-INT-015). The upload never continues the Check (REQ-INT-019).
Traces    : REQ-INT-009, REQ-INT-010, REQ-INT-011, REQ-INT-012, REQ-INT-013, REQ-INT-014, REQ-INT-015

### CON-INT-003 — Confirm the uploads of a `manual` Check
Signature : confirmUploads(checkId) → accepted {checkId, status (RUNNING)} · errors: request unreadable (INT-400-REQUEST-INVALID), Check not found (CHK-404-CHECK-NOT-FOUND), Check not waiting for documents (CHK-409-CHECK-NOT-AWAITING-DOCUMENTS), unexpected failure (INT-500)
Entity    : ENT-RPT-001
Notes     : the only way a `manual` Check continues (REQ-INT-019); the pipeline then runs in the background.
Traces    : REQ-INT-018, REQ-INT-019

### CON-INT-004 — Record an Employee Decision
Signature : recordDecision(checkId, employeeDecision (CON-RPT-002), decidedBy) → {checkId, employeeDecision, decidedBy, decidedAt, approvalApiExecuted} · errors: request unreadable (INT-400-REQUEST-INVALID), Check not found (RPT-404-CHECK-NOT-FOUND), incomplete decision (RPT-400-DECISION-INCOMPLETE), Check not completed (RPT-409-CHECK-NOT-COMPLETED), decision already recorded (RPT-409-DECISION-ALREADY-RECORDED), Approval API execution on a REJECTED decision (RPT-422-APPROVAL-FLAG-ON-REJECTION), Approval API failed (INT-502-APPROVAL-API-FAILED), Approval API timed out (INT-504-APPROVAL-API-TIMED-OUT), unexpected failure (INT-500)
Entity    : ENT-RPT-001, ENT-REG-002
Notes     : where the Check's service package version enables the Approval API (CON-REG-012) and the decision is APPROVED, INT checks the decision is complete and the Check is COMPLETED and undecided (RULE-INT-002, RULE-INT-003), calls the Approval API once within the configured timeout, and only after it succeeds records the decision with approvalApiExecuted true; a failed or timed-out call records nothing and the caller may submit again. A REJECTED decision, or any decision on a version without the Approval API, is recorded with approvalApiExecuted false and no call (REQ-INT-027, REQ-INT-028). The Approval API is called from no other operation (REQ-INT-029).
Traces    : REQ-INT-021, REQ-INT-022, REQ-INT-025, REQ-INT-026, REQ-INT-027, REQ-INT-028, REQ-INT-029, REQ-INT-030, REQ-INT-034, REQ-INT-035, REQ-INT-036, REQ-INT-037, REQ-INT-038, REQ-INT-039

## Stability
All 4 items are ADDITIVE in v1. Changing or removing one a consumer depends on requires a BREAKING version with an ADR.
══════════════════════════════════════════════════════════════════
