# UI/UX SPEC — Host Integration (INT) — employee frontend
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias   Stage : P3.2 (Part A)
Inputs : srs-int.md (PART B — SCR-REQ-INT-001 … SCR-REQ-INT-005) · prd-int.md · api-spec-int.yaml (writes API-INT-001 … API-INT-004, reads API-INT-005 … API-INT-008 — ADR-INT-020, ADR-INT-021)
Mints  : SCR-INT-001 … SCR-INT-005 · UXD-INT-001 … UXD-INT-008   ADRs : ADR-INT-006, ADR-INT-011, ADR-INT-018, ADR-INT-020, ADR-INT-021, ADR-INT-025, ADR-INT-026
══════════════════════════════════════════════════════════════════

Common to every screen
- Embedded in the host screen; launch context = service code, request number, employee identity, carried
  in the route's query string (ADR-INT-018 (4)). Without all three: no Check, no read, and the message of
  RULE-INT-004 "Open this screen from the host system for one request." (en; ar PENDING ADR-INT-017).
- Every text that comes from a report or a file (condition, Evidence, note, detail, file name, failure
  detail, query detail) is rendered as plain text — never interpreted as markup, never a link (REQ-INT-051).
- Lookup codes (CHECK_STATUS, OVERALL_STATUS, FINDING_OUTCOME, CHECK_FAILURE_REASON, FETCH_MODE,
  DOCUMENT_READ_STATUS, UNREADABLE_REASON, EMPLOYEE_DECISION, DOCUMENT_TYPE) are shown as their codes —
  INT owns no lookup (ADR-INT-013).
- No role check, no permission (raw-idea A2): Permissions lines reference the SRS B4 rows only.
- A refusal is shown with the `detail` the server sent, in the owner's words (REQ-INT-006); the screen keeps
  its form state.

## SCR-INT-001 — Checks of a request                 traces=REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,UXD-INT-001
UI pattern        : flat list of records (SCR-REQ-INT-001 — content shape "flat list of records")
Sub-views         : none — one list scoped by the launch context; no filter (B2)
Fields shown      : Check identifier (en "Check" / ar "الفحص") · status (en "Status" / ar "الحالة") · Overall Status (en "Overall Status" / ar "الحالة العامة") · start time (en "Started" / ar "وقت البدء") · end time (en "Ended" / ar "وقت الانتهاء") · Employee Decision (en "Decision" / ar "قرار الموظف") · total when not every Check is listed (en "This request has {total} Checks." / ar "لهذا الطلب {total} فحصًا.") — all read-only
Composition       : container page · Checks collection → inline (the list is the page body, read-only rows) · submits: one ("Start a Check" — en "Start a Check" / ar "بدء فحص"; no field is typed — the launch context is sent)
Permissions       : Employee — list, start (SRS B4; no role check — raw-idea A2)
Cross-module data : the Checks of the request (checkId, status, overallStatus, startedAt, endedAt, employeeDecision, total) → UXD-INT-001 (RPT)
Navigation        : from the Host System screen (launch: serviceCode, requestNumber, employeeId) · to SCR-INT-002 by selecting a row, seeding checkId of that ONE Check (launch context kept)
States            : empty (no Check yet for the request: en "No Check has been run for this request yet." / ar "لم يُجرَ أي فحص لهذا الطلب بعد." with "Start a Check" still offered) · loading (list placeholder; "Start a Check" disabled while the start is sent) · error (the read failed: generic message with retry; a refused start shows the server's detail above the list) · launch context incomplete (RULE-INT-004 message, no list, no action)

## SCR-INT-002 — Check report                        traces=REQ-INT-061,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-066,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-078,UXD-INT-002,UXD-INT-003,UXD-INT-004
UI pattern        : header + repeating lines (SCR-REQ-INT-002 — findings, documents, unread queries)
Sub-views         : header · findings pane · documents pane · service queries not read pane · decision pane (when recorded) · failure pane (when FAILED) — all on one page, stacked
Fields shown      : header — status (en "Status" / ar "الحالة"), service code (en "Service" / ar "الخدمة"), service package version (en "Version" / ar "الإصدار"), fetch mode (en "Fetch mode" / ar "طريقة جلب المستندات"), request number (en "Request" / ar "رقم الطلب"), employee (en "Employee" / ar "الموظف"), start time (en "Started" / ar "وقت البدء"), end time once ended (en "Ended" / ar "وقت الانتهاء"), Overall Status once COMPLETED (en "Overall Status" / ar "الحالة العامة"; presented "Not verified — a required document is missing" instead of COMPLIANT when any document outcome is MISSING — REQ-INT-049), comparison model once COMPLETED (en "Model" / ar "النموذج") · findings — one entry per Finding holding condition (en "Condition" / ar "الشرط"), outcome (en "Outcome" / ar "النتيجة"), Evidence (en "Evidence" / ar "الدليل") and note (en "Note" / ar "ملاحظة") side by side · documents — document type (en "Document" / ar "المستند"), source mode (en "Source" / ar "المصدر"), read status (en "Read status" / ar "حالة القراءة"), and for UNREADABLE its reason (en "Reason" / ar "السبب") and detail (en "Detail" / ar "التفاصيل") · service queries not read — query name (en "Query" / ar "الاستعلام") and detail (en "Detail" / ar "التفاصيل") · failure (FAILED only) — failure reason (en "Failure reason" / ar "سبب الإخفاق") and failure detail (en "Failure detail" / ar "تفاصيل الإخفاق"), no Overall Status · decision (when recorded) — Employee Decision (en "Decision" / ar "القرار"), decided by (en "Decided by" / ar "صاحب القرار"), decided at (en "Decided at" / ar "وقت القرار"), executed through the Approval API (en "executed through the Approval API" / ar "نُفّذ عبر واجهة الاعتماد" — shown only when true) — all read-only
Composition       : container page · findings collection → inline · documents collection → inline · service queries collection → inline (read-only panes beneath the header, scrolling with the page body) · submits: none (actions navigate: "Upload documents" and "Confirm uploads" while AWAITING_DOCUMENTS — REQ-INT-053; "Record decision" while COMPLETED with no Employee Decision — REQ-INT-054)
Permissions       : Employee — read (SRS B4; no role check — raw-idea A2)
Cross-module data : header, failure and decision → UXD-INT-002 (RPT) · findings → UXD-INT-003 (RPT) · documents and service queries not read → UXD-INT-004 (RPT)
Navigation        : from SCR-INT-001 (checkId of one Check) · to SCR-INT-003 and SCR-INT-004 (same checkId; offered only while AWAITING_DOCUMENTS) · to SCR-INT-005 (same checkId; offered only while COMPLETED with no Employee Decision) · back to SCR-INT-001 (launch context kept)
States            : empty (a COMPLETED report with no findings / documents / unread queries shows the pane heading with en "None" / ar "لا يوجد") · loading (first read: header placeholder; later reads every 5 seconds are silent — no placeholder) · error (the first read failed: generic message with retry; RPT-404-CHECK-NOT-FOUND: the server's detail and a way back to SCR-INT-001) · refresh failed (a later background read fails while a Check is shown: the last Check read successfully stays on screen unchanged with a non-blocking notice en "Refresh failed; retrying" / ar "تعذّر التحديث؛ جارٍ إعادة المحاولة"; the next 5-second read retries and a successful read removes the notice — REQ-INT-066, ADR-INT-026 (2)) · running (AWAITING_DOCUMENTS / RUNNING: header only, status kept current every 5 seconds until COMPLETED or FAILED — REQ-INT-055, REQ-INT-056)

## SCR-INT-003 — Document upload                     traces=REQ-INT-063,REQ-INT-064,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-065,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-075,AC-INT-076,AC-INT-077,UXD-INT-005,UXD-INT-006
UI pattern        : flat record (one upload) + list of uploaded documents (SCR-REQ-INT-003)
Sub-views         : upload form · uploaded documents list (beneath the form)
Fields shown      : input — document type (en "Document type" / ar "نوع المستند"; a choice of the required document types of the Check's service only — REQ-INT-016; a type that already has an upload is marked en "already uploaded" / ar "تم رفعه" and stays selectable — a further upload of it is a separate document, REQ-INT-065), file (en "File" / ar "الملف"; exactly one file) · list — document type (en "Document type" / ar "نوع المستند"), file name (en "File name" / ar "اسم الملف"), size (en "Size" / ar "الحجم"), oversized marker (en "Too large — will be reported unreadable" / ar "يتجاوز الحد — سيُبلَّغ بتعذر قراءته") — list read-only; after an oversized upload the server's notice is shown as received (REQ-INT-013)
Composition       : container page · uploaded documents collection → inline (read-only pane beneath the upload form; no save of its own) · submits: one ("Upload" — en "Upload" / ar "رفع"; it never confirms the uploads — REQ-INT-019; "Confirm uploads" is a link to SCR-INT-004, not a second submit)
Permissions       : Employee — upload (SRS B4; no role check — raw-idea A2)
Cross-module data : document type choices (requiredDocumentTypes) → UXD-INT-005 (REG) · uploaded documents (documentType, fileName, fileSize, oversized) → UXD-INT-006 (DOC)
Navigation        : from SCR-INT-002 (checkId; offered while AWAITING_DOCUMENTS) · to SCR-INT-004 (same checkId) · back to SCR-INT-002 (same checkId)
States            : empty (no document uploaded yet: en "No document has been uploaded for this Check yet." / ar "لم يُرفع أي مستند لهذا الفحص بعد.") · loading (choices and list placeholders; "Upload" disabled while the file is sent) · error (a read failed: generic message with retry; a refused upload shows the server's detail on the form and keeps the chosen type — including Document Access's ended-Check refusal DOC-409-CHECK-ENDED and upload-limit refusal DOC-422-UPLOAD-LIMIT-REACHED, ADR-INT-025) · not awaiting documents (the Check left AWAITING_DOCUMENTS: the form is not offered and the screen links back to SCR-INT-002)

## SCR-INT-004 — Upload confirmation                 traces=REQ-INT-063,REQ-INT-064,REQ-INT-018,REQ-INT-019,REQ-INT-020,AC-INT-022,AC-INT-023,AC-INT-024,UXD-INT-007,UXD-INT-008
UI pattern        : flat record (SCR-REQ-INT-004)
Sub-views         : uploaded documents (read-only) · required document types with no upload (read-only)
Fields shown      : uploaded documents — document type (en "Document type" / ar "نوع المستند"), file name (en "File name" / ar "اسم الملف"), size (en "Size" / ar "الحجم") · required document types with no upload (en "No upload — will be reported missing" / ar "لم يُرفع — سيُبلَّغ بأنه مفقود") — all read-only
Composition       : container page · uploaded documents collection → inline · missing types collection → inline (read-only panes, no save of their own) · submits: one ("Confirm uploads" — en "Confirm uploads" / ar "تأكيد اكتمال الرفع"; no field)
Permissions       : Employee — confirm (SRS B4; no role check — raw-idea A2)
Cross-module data : uploaded documents (documentType, fileName, fileSize) → UXD-INT-007 (DOC) · required document types with no upload (requiredDocumentTypes minus the uploaded types) → UXD-INT-008 (REG)
Navigation        : from SCR-INT-002 or SCR-INT-003 (checkId) · to SCR-INT-002 after the confirmation is accepted (same checkId) · back to SCR-INT-003 (same checkId)
States            : empty (no document uploaded: every required type is listed as having no upload; "Confirm uploads" still offered — the Check then reports them missing) · loading (lists placeholder; "Confirm uploads" disabled while sent) · error (a read failed: generic message with retry; a refused confirmation shows the server's detail and keeps the screen)

## SCR-INT-005 — Employee decision                   traces=REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-054,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-041,AC-INT-042,AC-INT-043
UI pattern        : flat record (SCR-REQ-INT-005)
Sub-views         : none
Fields shown      : input — Employee Decision (en "Decision" / ar "القرار"; APPROVED or REJECTED, required) · the deciding employee is the launch identity, sent as decidedBy and never typed (REQ-INT-023) — shown read-only (en "Decided by" / ar "صاحب القرار")
Composition       : container page · none (no secondary detail) · submits: one ("Record decision" — en "Record decision" / ar "تسجيل القرار"; the only submit of the screen — REQ-INT-024)
Permissions       : Employee — decide (SRS B4; no role check — raw-idea A2)
Cross-module data : none rendered — the Check's status and decision are read only to decide whether the decision is offered (REQ-INT-054)
Navigation        : from SCR-INT-002 (checkId; offered while COMPLETED with no Employee Decision) · to SCR-INT-002 after the decision is recorded (same checkId) · back to SCR-INT-002
States            : empty (not applicable — one form) · loading (the Check read; "Record decision" disabled while sent — the Approval API may take up to its timeout) · error (a refusal shows the server's detail and keeps the chosen decision; after INT-502-APPROVAL-API-FAILED or INT-504-APPROVAL-API-TIMED-OUT nothing was recorded and "Record decision" may be submitted again — REQ-INT-038) · not offered (the Check is not COMPLETED or already holds an Employee Decision: no form, a link back to SCR-INT-002)

## Cross-module display dependencies

### UXD-INT-001 — Checks of a request on SCR-INT-001          traces=REQ-INT-040,REQ-INT-042,REQ-INT-043,AC-INT-045,AC-INT-047,AC-INT-048
Screen    : SCR-INT-001
Fields    : checkId, status, overallStatus, startedAt, endedAt, employeeDecision (each Check); total
Owner     : RPT — the Report Store's list of the Checks of a request, served through INT's API-INT-006 (ADR-INT-020, ADR-INT-021)
Empty/err : empty list → the screen's empty state; read failure → the screen's error state

### UXD-INT-002 — Report header, failure and recorded decision on SCR-INT-002   traces=REQ-INT-045,REQ-INT-050,REQ-INT-052,REQ-INT-055,REQ-INT-056,REQ-INT-066,AC-INT-050,AC-INT-056,AC-INT-058,AC-INT-061,AC-INT-062,AC-INT-078
Screen    : SCR-INT-002
Fields    : status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt, endedAt, overallStatus, comparisonModel, failureReason, failureDetail, decision (employeeDecision, decidedBy, decidedAt, approvalApiExecuted)
Owner     : RPT — the Report Store's read of a Check and its report, served through INT's API-INT-005
Empty/err : fields absent by status are not shown (no end time before the Check ends, no Overall Status unless COMPLETED, no failure unless FAILED, no decision pane when none); read failure → the screen's error state

### UXD-INT-003 — Findings beside their Evidence on SCR-INT-002   traces=REQ-INT-046,REQ-INT-051,AC-INT-051,AC-INT-057
Screen    : SCR-INT-002
Fields    : findings[] (position, condition, outcome, Evidence, note) — in position order, plain text
Owner     : RPT — served through INT's API-INT-005
Empty/err : no findings → the pane's empty state; read failure → the screen's error state

### UXD-INT-004 — Documents and service queries not read on SCR-INT-002   traces=REQ-INT-047,REQ-INT-048,REQ-INT-049,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055
Screen    : SCR-INT-002
Fields    : documents[] (position, documentType, sourceMode, readStatus, unreadableReason, detail) · unreadQueries[] (position, queryName, detail); a MISSING readStatus drives the Overall Status safeguard (REQ-INT-049, ADR-INT-011 (2))
Owner     : RPT — served through INT's API-INT-005
Empty/err : none → the pane's empty state; read failure → the screen's error state

### UXD-INT-005 — Document type choices on SCR-INT-003          traces=REQ-INT-016,REQ-INT-065,AC-INT-020,AC-INT-077
Screen    : SCR-INT-003
Fields    : requiredDocumentTypes of the service package version the Check runs on; each marked "already uploaded" when UXD-INT-006 lists an upload of that type (REQ-INT-065)
Owner     : REG — the version's required document types, served through INT's API-INT-008, keyed by checkId
Empty/err : read failure → no choice offered, "Upload" disabled, the screen's error state

### UXD-INT-006 — Uploaded documents on SCR-INT-003             traces=REQ-INT-017,AC-INT-021
Screen    : SCR-INT-003
Fields    : uploadedDocumentId (row key only), documentType, fileName, fileSize, oversized
Owner     : DOC — Document Access's uploaded documents of a Check, served through INT's API-INT-007
Empty/err : empty → the screen's empty state; read failure → the list's error state (the upload form stays usable)

### UXD-INT-007 — Uploaded documents on SCR-INT-004             traces=REQ-INT-020,AC-INT-024
Screen    : SCR-INT-004
Fields    : documentType, fileName, fileSize
Owner     : DOC — served through INT's API-INT-007
Empty/err : empty → every required type listed as having no upload; read failure → the screen's error state ("Confirm uploads" disabled until the lists load)

### UXD-INT-008 — Required document types with no upload on SCR-INT-004   traces=REQ-INT-020,AC-INT-024
Screen    : SCR-INT-004
Fields    : requiredDocumentTypes of the Check's version minus the documentType values of UXD-INT-007
Owner     : REG — served through INT's API-INT-008, keyed by checkId
Empty/err : every required type uploaded → the pane's empty state (en "Every required document has an upload." / ar "لكل مستند مطلوب ملف مرفوع."); read failure → the screen's error state
