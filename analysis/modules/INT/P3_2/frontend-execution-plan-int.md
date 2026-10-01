# FRONTEND EXECUTION PLAN — Host Integration (INT) — employee frontend
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod) · lazy chunk per screen
Inputs : srs-int.md · prd-int.md · api-spec-int.yaml (`_state/current-api-spec.yaml`) · registry-srs-int.md · registry-exec-be-int.md (all eight operations the screens call — ADR-INT-020, ADR-INT-021) · ui-ux-spec-int.md · flow-diagram-int.md
Screens : SCR-INT-001 … SCR-INT-005   Cross-module : UXD-INT-001 … UXD-INT-008   ADRs : ADR-INT-001, ADR-INT-006, ADR-INT-011, ADR-INT-013, ADR-INT-017, ADR-INT-018, ADR-INT-020, ADR-INT-021 (0 BLOCKED)
Security : no permission model — screens open per the SRS (caller authentication deferred, raw-idea A2; REQ-INT-004); no SEC-FE phase in the profile
══════════════════════════════════════════════════════════════════

## 3.0 Binding to the API document

The plan is bound to `api-spec-int.yaml` alone: INT's four write operations and its four frontend-facing reads,
which relay the owners' published operations (ADR-INT-020; ADR-INT-021 supersedes ADR-INT-018 (1), (2), (3), (7)).
Shapes — method, path, parameters, request/response schemas, error responses and their `x-error-codes` — are read
in the document by `API-*` id (`x-api-id`) and never restated here. The frontend executor's mock server serves
`api-spec-int.yaml`.

```yaml name=api-surface
mock: api-spec-int.yaml
bindings:
  - {req: REQ-INT-001, api: [API-INT-001]}
  - {req: REQ-INT-002, api: [API-INT-001]}
  - {req: REQ-INT-003, api: [API-INT-001]}
  - {req: REQ-INT-004, api: [API-INT-001]}
  - {req: REQ-INT-005, api: [API-INT-001]}
  - {req: REQ-INT-006, api: [API-INT-001, API-INT-002, API-INT-003, API-INT-004]}
  - {req: REQ-INT-007, api: [API-INT-001, API-INT-002, API-INT-003, API-INT-004]}
  - {req: REQ-INT-008, api: [API-INT-001, API-INT-002, API-INT-003, API-INT-004]}
  - {req: REQ-INT-009, api: [API-INT-002]}
  - {req: REQ-INT-010, api: [API-INT-002]}
  - {req: REQ-INT-011, api: [API-INT-002, API-INT-005]}
  - {req: REQ-INT-012, api: [API-INT-002]}
  - {req: REQ-INT-013, api: [API-INT-002]}
  - {req: REQ-INT-014, api: [API-INT-002]}
  - {req: REQ-INT-015, api: [API-INT-002]}
  - {req: REQ-INT-016, api: [API-INT-008, API-INT-005]}
  - {req: REQ-INT-017, api: [API-INT-007]}
  - {req: REQ-INT-018, api: [API-INT-003]}
  - {req: REQ-INT-019, api: [API-INT-002, API-INT-003]}
  - {req: REQ-INT-020, api: [API-INT-007, API-INT-008]}
  - {req: REQ-INT-021, api: [API-INT-004]}
  - {req: REQ-INT-022, api: [API-INT-004]}
  - {req: REQ-INT-023, api: [API-INT-004]}
  - {req: REQ-INT-024, api: [API-INT-004]}
  - {req: REQ-INT-025, api: [API-INT-004]}
  - {req: REQ-INT-026, api: [API-INT-004]}
  - {req: REQ-INT-027, api: [API-INT-004]}
  - {req: REQ-INT-028, api: [API-INT-004]}
  - {req: REQ-INT-029, api: [API-INT-004]}
  - {req: REQ-INT-030, api: [API-INT-004]}
  - {req: REQ-INT-031, api: [API-INT-004]}
  - {req: REQ-INT-032, api: [API-INT-004]}
  - {req: REQ-INT-033, api: [API-INT-004]}
  - {req: REQ-INT-034, api: [API-INT-004]}
  - {req: REQ-INT-035, api: [API-INT-004]}
  - {req: REQ-INT-036, api: [API-INT-004]}
  - {req: REQ-INT-037, api: [API-INT-004]}
  - {req: REQ-INT-038, api: [API-INT-004]}
  - {req: REQ-INT-039, api: [API-INT-004]}
  - {req: REQ-INT-040, api: [API-INT-006]}
  - {req: REQ-INT-042, api: [API-INT-006]}
  - {req: REQ-INT-043, api: [API-INT-006]}
  - {req: REQ-INT-044, api: [API-INT-001]}
  - {req: REQ-INT-045, api: [API-INT-005]}
  - {req: REQ-INT-046, api: [API-INT-005]}
  - {req: REQ-INT-047, api: [API-INT-005]}
  - {req: REQ-INT-048, api: [API-INT-005]}
  - {req: REQ-INT-049, api: [API-INT-005]}
  - {req: REQ-INT-050, api: [API-INT-005]}
  - {req: REQ-INT-051, api: [API-INT-005]}
  - {req: REQ-INT-052, api: [API-INT-005]}
  - {req: REQ-INT-053, api: [API-INT-005]}
  - {req: REQ-INT-054, api: [API-INT-005]}
  - {req: REQ-INT-055, api: [API-INT-005]}
  - {req: REQ-INT-056, api: [API-INT-005]}
  - {req: REQ-INT-061, api: [API-INT-005]}
  - {req: REQ-INT-062, api: [API-INT-006]}
  - {req: REQ-INT-063, api: [API-INT-007]}
  - {req: REQ-INT-064, api: [API-INT-008]}
  - {req: REQ-INT-057, api: [API-INT-001, API-INT-002, API-INT-003, API-INT-004, API-INT-005, API-INT-006, API-INT-008, API-INT-007]}
unmapped:
  - "REQ-INT-041 — no operation: the launch-context check (RULE-INT-004) runs in the frontend before any call"
  - "REQ-INT-058, REQ-INT-059, REQ-INT-060 — no operation: no report page, nothing kept, no host database (backend CORE)"
codes:
  - {code: "INT-409-CHECK-NOT-AWAITING-DOCUMENTS", rule: RULE-INT-001}
  - {code: "RPT-400-DECISION-INCOMPLETE", rule: RULE-INT-002}
  - {code: "RPT-409-CHECK-NOT-COMPLETED", rule: RULE-INT-003}
  - {code: "RPT-409-DECISION-ALREADY-RECORDED", rule: RULE-INT-003}
```

Reconciliation against the SRS (once, before any phase):
- Every REQ needing an operation has one: REQ-INT-001 … REQ-INT-040, REQ-INT-042 … REQ-INT-057 above.
  REQ-INT-041 (RULE-INT-004) is decided in the frontend before any call — no operation needed.
- Every INT operation maps to a REQ (API-INT-001 … API-INT-004 — `x-traces` of the document).
- Every operation of the document maps to a REQ: API-INT-005 … API-INT-008 are the reads REQ-INT-061 … REQ-INT-064 add
  (ADR-INT-020); no operation is unused.
- No value outside the document: the required document types are the Check's own version's (API-INT-008 — ADR-INT-021 (3)).
- RULE-INT-004 has no code: it is the frontend's own check; its text is the SRS message.

Message binding — every text a screen shows for a refusal, by reference (en per SRS/catalogue; ar `PENDING ADR-INT-017`
for every row). Pass-through codes show the server's ProblemDetail `detail` as received — the owner's words (REQ-INT-006):
- `RULE-INT-004` → page message (no Check, no action), text: RULE-INT-004 message (SRS) "Open this screen from the host system for one request."
- `INT-400-REQUEST-INVALID` → page message on the submitting screen, text: catalogue INT-400-REQUEST-INVALID (server detail)
- `INT-500` → generic message on the submitting screen, text: catalogue INT-500 "The request could not be completed because of an unexpected error."
- `CHK-400-START-INCOMPLETE` → page message above the Checks list, text: catalogue CHK-400-START-INCOMPLETE (server detail)
- `CHK-422-SERVICE-NOT-AVAILABLE` → page message above the Checks list, text: catalogue CHK-422-SERVICE-NOT-AVAILABLE (server detail)
- `CHK-422-CONNECTION-NOT-ACTIVATED` → page message above the Checks list, text: catalogue CHK-422-CONNECTION-NOT-ACTIVATED (server detail)
- `INT-409-CHECK-NOT-AWAITING-DOCUMENTS` → form message on the upload form, text: RULE-INT-001 message (SRS) "Documents can be uploaded only while Check {checkId} is waiting for documents; its status is {status}." (server detail)
- `INT-413-UPLOAD-TOO-LARGE` → inline on file, text: catalogue INT-413-UPLOAD-TOO-LARGE (server detail)
- `DOC-400-INCOMPLETE-UPLOAD` → inline on file, text: catalogue DOC-400-INCOMPLETE-UPLOAD (server detail)
- `DOC-404-SERVICE-VERSION-NOT-FOUND` → form message on the upload form, text: catalogue DOC-404-SERVICE-VERSION-NOT-FOUND (server detail)
- `DOC-422-FETCH-MODE-NOT-MANUAL` → form message on the upload form, text: catalogue DOC-422-FETCH-MODE-NOT-MANUAL (server detail)
- `DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE` → inline on documentType, text: catalogue DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE (server detail)
- `RPT-404-CHECK-NOT-FOUND` → page message with a link back to the Checks of the request, text: catalogue RPT-404-CHECK-NOT-FOUND "Check {checkId} was not found." (server detail)
- `CHK-404-CHECK-NOT-FOUND` → page message on the confirmation screen, text: catalogue CHK-404-CHECK-NOT-FOUND (server detail)
- `CHK-409-CHECK-NOT-AWAITING-DOCUMENTS` → page message on the confirmation screen, text: catalogue CHK-409-CHECK-NOT-AWAITING-DOCUMENTS (server detail)
- `RPT-400-DECISION-INCOMPLETE` → inline on employeeDecision, text: RULE-INT-002 message (SRS) (server detail)
- `RPT-409-CHECK-NOT-COMPLETED` → form message on the decision form, text: RULE-INT-003 message (SRS) "Check {checkId} is not completed; a decision can only be recorded on a completed Check." (server detail)
- `RPT-409-DECISION-ALREADY-RECORDED` → form message on the decision form, text: RULE-INT-003 message (SRS) "Check {checkId} already has an Employee Decision." (server detail)
- `RPT-422-APPROVAL-FLAG-ON-REJECTION` → form message on the decision form, text: catalogue RPT-422-APPROVAL-FLAG-ON-REJECTION (server detail)
- `INT-502-APPROVAL-API-FAILED` → form message on the decision form, decision kept, submit enabled again, text: catalogue INT-502-APPROVAL-API-FAILED "The approval was not executed: the host Approval API answered {status}. Nothing was recorded; you can try again." (server detail)
- `INT-504-APPROVAL-API-TIMED-OUT` → form message on the decision form, decision kept, submit enabled again, text: catalogue INT-504-APPROVAL-API-TIMED-OUT "The approval was not executed: the host Approval API did not answer within {timeout} seconds. Nothing was recorded; you can try again." (server detail)
- `RPT-400-REQUEST-KEYS-MISSING` (API-INT-006) → page message above the Checks list, text: catalogue RPT-400-REQUEST-KEYS-MISSING "Both a service code and a request number are needed to list Checks." (server detail)
- A read refused with `INT-400-REQUEST-INVALID` or `INT-500` (API-INT-005 … API-INT-008) → the screen's (or the pane's) error state with retry, texts bound above

<!-- PHASE:F1:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-057,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,AC-INT-069,AC-INT-070,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-001,API-INT-002,API-INT-003,API-INT-004,API-INT-005,API-INT-006,API-INT-007,API-INT-008,UXD-INT-001,UXD-INT-002,UXD-INT-003,UXD-INT-004,UXD-INT-005,UXD-INT-006,UXD-INT-007,UXD-INT-008,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005 -->
## PHASE F1 — F1 — Models & Types

Role RF1. Field/DTO binding: see `api-spec-int.yaml` — the request/response schemas
of the operations each screen calls are the source, generated into TypeScript types and never re-typed by hand.
Frontend-only types are listed per screen. Lookup codes stay string unions taken from the document enums
(INT owns no lookup — ADR-INT-013); host identifiers stay strings exactly as sent.

<!-- SUB:F1-SCR-INT-001:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-057,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-069,AC-INT-070,API-INT-001,API-INT-006,UXD-INT-001,SCR-INT-001 -->
### F1 — SCR-INT-001 Checks of a request
- From the documents: API-INT-006 → `ChecksOfRequestResponse` (`total`, `checks[]`); API-INT-001 → `StartCheckRequest`, `StartedCheckResponse`; `ProblemDetail` (every error response).
- Frontend-only: `LaunchContext` {serviceCode, requestNumber, employeeId} (strings, as the host passed them — ADR-INT-018 (4), kept by ADR-INT-021 (6)).
- Cross-module: UXD-INT-001 (RPT fields of `checks[]` and `total`).
<!-- SUB:F1-SCR-INT-001:END -->

<!-- SUB:F1-SCR-INT-002:START traces=REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-061,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,API-INT-005,UXD-INT-002,UXD-INT-003,UXD-INT-004,SCR-INT-002 -->
### F1 — SCR-INT-002 Check report
- From the documents: API-INT-005 → `CheckReportResponse` with `findings[]`, `documents[]`, `unreadQueries[]`, `decision | null`; `ProblemDetail`.
- Frontend-only: `PresentedOverallStatus` = the OVERALL_STATUS codes of the document | `NOT_VERIFIED_MISSING` (REQ-INT-049 — ADR-INT-011 (2)); `ReportPhase` = `following` (AWAITING_DOCUMENTS, RUNNING) | `ended` (COMPLETED, FAILED).
- Cross-module: UXD-INT-002, UXD-INT-003, UXD-INT-004 (RPT).
<!-- SUB:F1-SCR-INT-002:END -->

<!-- SUB:F1-SCR-INT-003:START traces=REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-002,API-INT-005,API-INT-007,API-INT-008,UXD-INT-005,UXD-INT-006,SCR-INT-003 -->
### F1 — SCR-INT-003 Document upload
- From the documents: API-INT-002 → `UploadRequest` (multipart: documentType, file), `UploadReceiptResponse` (incl. optional `notice`); API-INT-008 → `RequiredDocumentTypesResponse` (`requiredDocumentTypes` of the Check's version); API-INT-007 → `UploadedDocumentResponse[]`; API-INT-005 → `CheckReportResponse` (status only is used); `ProblemDetail`.
- Frontend-only: `UploadFormValues` {documentType: string, file: File}.
- Cross-module: UXD-INT-005 (REG), UXD-INT-006 (DOC).
<!-- SUB:F1-SCR-INT-003:END -->

<!-- SUB:F1-SCR-INT-004:START traces=REQ-INT-006,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-071,AC-INT-073,API-INT-003,API-INT-005,API-INT-007,API-INT-008,UXD-INT-007,UXD-INT-008,SCR-INT-004 -->
### F1 — SCR-INT-004 Upload confirmation
- From the documents: API-INT-003 → `UploadConfirmationRequest` (empty object), `ConfirmedCheckResponse`; API-INT-007 → `UploadedDocumentResponse[]`; API-INT-008 → `RequiredDocumentTypesResponse`; API-INT-005 → `CheckReportResponse` (status); `ProblemDetail`.
- Frontend-only: `MissingTypes` = string[] (required types with no upload — derived in F3).
- Cross-module: UXD-INT-007 (DOC), UXD-INT-008 (REG).
<!-- SUB:F1-SCR-INT-004:END -->

<!-- SUB:F1-SCR-INT-005:START traces=REQ-INT-006,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-054,REQ-INT-061,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,API-INT-004,API-INT-005,SCR-INT-005 -->
### F1 — SCR-INT-005 Employee decision
- From the documents: API-INT-004 → `DecisionRequest` {employeeDecision, decidedBy}, `RecordedDecisionResponse`; API-INT-005 → `CheckReportResponse` (status, decision); `ProblemDetail`.
- Frontend-only: `DecisionFormValues` {employeeDecision: 'APPROVED' | 'REJECTED'} — `decidedBy` is never a form value, it is the launch identity (REQ-INT-023).
- Cross-module: none rendered.
<!-- SUB:F1-SCR-INT-005:END -->
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-057,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,AC-INT-069,AC-INT-070,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-001,API-INT-002,API-INT-003,API-INT-004,API-INT-005,API-INT-006,API-INT-007,API-INT-008,UXD-INT-001,UXD-INT-002,UXD-INT-003,UXD-INT-004,UXD-INT-005,UXD-INT-006,UXD-INT-007,UXD-INT-008,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005 -->
## PHASE F2 — F2 — Data Hooks

Role RF2 — WHAT each screen needs from the API, not hook code. Server state: `tanstack-query`. Components use
the facade only; the facade uses the declared queries only. Cache policy: library defaults except where a row
says otherwise. Loading: LOCAL everywhere (no call is declared slow by the SRS; the decision's Approval API
wait is LOCAL to its submit). Shared hooks — one per operation, reused by every screen that reads it:
CHECK-QUERY (API-INT-005, key `['check', checkId]`), UPLOADED-DOCUMENTS-QUERY (API-INT-007, key
`['uploaded-documents', checkId]`), REQUIRED-TYPES-QUERY (API-INT-008, key `['required-document-types', checkId]`, long-lived
cache — a Check's version never changes). Errors route per the Message binding list of §3.0 (codes → control, text bound there). No hook
retries a mutation (REQ-INT-033: a retry is the employee's own new submit).

<!-- SUB:F2-SCR-INT-001:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-057,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-069,AC-INT-070,API-INT-001,API-INT-006,UXD-INT-001,SCR-INT-001 -->
### F2 — SCR-INT-001 Checks of a request

```yaml name=screen-hooks
screen: SCR-INT-001
hooks:
  - {hook: LAUNCH-CONTEXT-INIT, kind: init, errors: "launch context incomplete → RULE-INT-004 page message, no query enabled", loading: NONE, invalidation: "—"}
  - {hook: CHECKS-QUERY, kind: read, api: [API-INT-006], cache_key: "['checks-of-request', {serviceCode, requestNumber}]", errors: "RPT-400-REQUEST-KEYS-MISSING → page message; INT-500 → error state with retry", loading: LOCAL, invalidation: "—"}
  - {hook: START-CHECK-SAVE, kind: mutation, api: [API-INT-001], errors: "CHK-400-START-INCOMPLETE, CHK-422-SERVICE-NOT-AVAILABLE, CHK-422-CONNECTION-NOT-ACTIVATED, INT-400-REQUEST-INVALID, INT-500 → page message above the list", loading: LOCAL, invalidation: "['checks-of-request', {serviceCode, requestNumber}]"}
  - {hook: CHECKS-FACADE, kind: facade, api: [API-INT-006, API-INT-001], loading: LOCAL}
```

### LAUNCH-CONTEXT-INIT — SCR-INT-001            traces=REQ-INT-041
Reads serviceCode, requestNumber, employeeId from the route's query string (ADR-INT-018 (4)); validated by the F3
launch schema; incomplete → no query is enabled, the RULE-INT-004 message is shown. No permission read (no model).
### CHECKS-QUERY — API-INT-006            traces=API-INT-006,REQ-INT-040,REQ-INT-042,REQ-INT-043
Kind read query — the shape is the document's (API-INT-006 in `api-spec-int.yaml`).
Cache key    : ['checks-of-request', {serviceCode, requestNumber}] — both filters change the response; no page/size (at most 100, newest first, with `total`)
Errors       : RPT-400-REQUEST-KEYS-MISSING → page message (text bound in §3.0) · INT-500 → the screen's error state with retry
Loading      : LOCAL · Cache policy : defaults · Invalidation : —
### START-CHECK-SAVE — API-INT-001            traces=API-INT-001,REQ-INT-044,REQ-INT-001,REQ-INT-006
Kind mutation — body is the launch context as sent (REQ-INT-044, REQ-INT-003); 202 → the new Check appears first.
Errors       : CHK-400-START-INCOMPLETE · CHK-422-SERVICE-NOT-AVAILABLE · CHK-422-CONNECTION-NOT-ACTIVATED · INT-400-REQUEST-INVALID · INT-500 → page message above the list (texts bound in §3.0)
Invalidation : ['checks-of-request', {serviceCode, requestNumber}]
### CHECKS-FACADE — SCR-INT-001
Composes LAUNCH-CONTEXT-INIT, CHECKS-QUERY, START-CHECK-SAVE · owns: the list from query data, `total` and the
"more than listed" flag (`total` > `checks.length`), derived loading · operation: `startCheck()` (no argument —
the launch context is the body).
<!-- SUB:F2-SCR-INT-001:END -->

<!-- SUB:F2-SCR-INT-002:START traces=REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-061,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,API-INT-005,UXD-INT-002,UXD-INT-003,UXD-INT-004,SCR-INT-002 -->
### F2 — SCR-INT-002 Check report

```yaml name=screen-hooks
screen: SCR-INT-002
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-INT-005], cache_key: "['check', checkId]", errors: "RPT-404-CHECK-NOT-FOUND → page message with a way back; INT-400-REQUEST-INVALID, INT-500 → error state with retry", loading: LOCAL, invalidation: "—"}
  - {hook: REPORT-FACADE, kind: facade, api: [API-INT-005], loading: LOCAL}
```

### CHECK-QUERY — API-INT-005            traces=API-INT-005,REQ-INT-045,REQ-INT-055,REQ-INT-056
Kind read query — the shape is the document's (API-INT-005 in `api-spec-int.yaml`).
Cache key    : ['check', checkId]
Errors       : RPT-404-CHECK-NOT-FOUND → page message, text bound in §3.0, link back to SCR-INT-001 · INT-400-REQUEST-INVALID / INT-500 → error state with retry
Loading      : LOCAL on the first read only; the repeated reads are background refetches (no placeholder)
Cache policy : refetch interval 5 000 ms while `status` is AWAITING_DOCUMENTS or RUNNING; no interval once COMPLETED or FAILED (REQ-INT-055, REQ-INT-056 — polling interval is frontend configuration, ADR-INT-011 (4)); deviation from defaults recorded in ADR-INT-021 (1)
Invalidation : —
### REPORT-FACADE — SCR-INT-002
Composes CHECK-QUERY · owns: `presentedOverallStatus` (F3 presenter), the panes from query data in `position`
order, `canUpload` (status AWAITING_DOCUMENTS — REQ-INT-053), `canDecide` (status COMPLETED and `decision` null —
REQ-INT-054), `isFollowing` (AWAITING_DOCUMENTS or RUNNING) · no imperative operation (the screen navigates only).
<!-- SUB:F2-SCR-INT-002:END -->

<!-- SUB:F2-SCR-INT-003:START traces=REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-002,API-INT-005,API-INT-007,API-INT-008,UXD-INT-005,UXD-INT-006,SCR-INT-003 -->
### F2 — SCR-INT-003 Document upload

```yaml name=screen-hooks
screen: SCR-INT-003
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-INT-005], cache_key: "['check', checkId]", errors: "RPT-404-CHECK-NOT-FOUND → page message; INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: REQUIRED-TYPES-QUERY, kind: read, api: [API-INT-008], cache_key: "['required-document-types', checkId]", errors: "RPT-404-CHECK-NOT-FOUND, INT-500 → no choice offered, error state", loading: LOCAL, invalidation: "—"}
  - {hook: UPLOADED-DOCUMENTS-QUERY, kind: read, api: [API-INT-007], cache_key: "['uploaded-documents', checkId]", errors: "INT-400-REQUEST-INVALID, INT-500 → list error state", loading: LOCAL, invalidation: "—"}
  - {hook: UPLOAD-SAVE, kind: mutation, api: [API-INT-002], errors: "INT-409-CHECK-NOT-AWAITING-DOCUMENTS, INT-413-UPLOAD-TOO-LARGE, DOC-400-INCOMPLETE-UPLOAD, DOC-404-SERVICE-VERSION-NOT-FOUND, DOC-422-FETCH-MODE-NOT-MANUAL, DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE, RPT-404-CHECK-NOT-FOUND, INT-400-REQUEST-INVALID, INT-500 → per §3.0", loading: LOCAL, invalidation: "['uploaded-documents', checkId], ['check', checkId]"}
  - {hook: UPLOAD-FACADE, kind: facade, api: [API-INT-005, API-INT-008, API-INT-007, API-INT-002], loading: LOCAL}
```

### CHECK-QUERY — API-INT-005            traces=API-INT-005,REQ-INT-011,REQ-INT-016
The shared read of the Check: its `status` decides whether the form is offered
(AWAITING_DOCUMENTS). No refetch interval on this screen.
### REQUIRED-TYPES-QUERY — API-INT-008     traces=API-INT-008,REQ-INT-016,REQ-INT-064
Kind read query — the required document types of the version the Check runs on (API-INT-008 in `api-spec-int.yaml`) ·
key ['required-document-types', checkId] · options shape: the `requiredDocumentTypes` codes (DOCUMENT_TYPE, shown as
codes) · ONE hook shared with SCR-INT-004 · long-lived cache (stale time = session — a Check's version never changes).
### UPLOADED-DOCUMENTS-QUERY — API-INT-007     traces=API-INT-007,REQ-INT-017
Kind read query — the shape is the document's (API-INT-007 in `api-spec-int.yaml`) · Cache key ['uploaded-documents', checkId] · Errors → the list's error state · Loading LOCAL · defaults.
### UPLOAD-SAVE — API-INT-002            traces=API-INT-002,REQ-INT-009,REQ-INT-011,REQ-INT-013,REQ-INT-014,REQ-INT-019
Kind mutation — multipart body (documentType, file); the service code and version are never sent (REQ-INT-010).
201 → the receipt's `notice` (when `oversized`) is shown as received (REQ-INT-013); the upload never confirms (REQ-INT-019).
Errors       : per §3.0 — INT-409 (RULE-INT-001) form message; INT-413 / DOC-400 inline on file; DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE inline on documentType
Invalidation : ['uploaded-documents', checkId], ['check', checkId]
### UPLOAD-FACADE — SCR-INT-003
Composes the four hooks · owns: the choices, the list, the last receipt notice, `canUpload` (status AWAITING_DOCUMENTS
and choices loaded), derived loading · operation: `upload(values)` → resets the file field on success, keeps it on refusal.
<!-- SUB:F2-SCR-INT-003:END -->

<!-- SUB:F2-SCR-INT-004:START traces=REQ-INT-006,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-071,AC-INT-073,API-INT-003,API-INT-005,API-INT-007,API-INT-008,UXD-INT-007,UXD-INT-008,SCR-INT-004 -->
### F2 — SCR-INT-004 Upload confirmation

```yaml name=screen-hooks
screen: SCR-INT-004
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-INT-005], cache_key: "['check', checkId]", errors: "RPT-404-CHECK-NOT-FOUND → page message; INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: UPLOADED-DOCUMENTS-QUERY, kind: read, api: [API-INT-007], cache_key: "['uploaded-documents', checkId]", errors: "INT-400-REQUEST-INVALID, INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: REQUIRED-TYPES-QUERY, kind: read, api: [API-INT-008], cache_key: "['required-document-types', checkId]", errors: "RPT-404-CHECK-NOT-FOUND, INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: CONFIRM-SAVE, kind: mutation, api: [API-INT-003], errors: "CHK-404-CHECK-NOT-FOUND, CHK-409-CHECK-NOT-AWAITING-DOCUMENTS, INT-400-REQUEST-INVALID, INT-500 → page message per §3.0", loading: LOCAL, invalidation: "['check', checkId], ['checks-of-request', {serviceCode, requestNumber}]"}
  - {hook: CONFIRMATION-FACADE, kind: facade, api: [API-INT-005, API-INT-007, API-INT-008, API-INT-003], loading: LOCAL}
```

### CONFIRM-SAVE — API-INT-003            traces=API-INT-003,REQ-INT-018,REQ-INT-019
Kind mutation — body `{}` (UploadConfirmationRequest); 202 → navigate to SCR-INT-002, whose CHECK-QUERY follows the
Check again (RUNNING). Invalidation: ['check', checkId], ['checks-of-request', {serviceCode, requestNumber}].
### CONFIRMATION-FACADE — SCR-INT-004       traces=REQ-INT-020
Composes CHECK-QUERY, UPLOADED-DOCUMENTS-QUERY, REQUIRED-TYPES-QUERY (shared hooks of SCR-INT-003), CONFIRM-SAVE ·
owns: the uploaded list, `missingTypes` (F3 derivation), `canConfirm` (lists loaded) · operation: `confirm()`.
<!-- SUB:F2-SCR-INT-004:END -->

<!-- SUB:F2-SCR-INT-005:START traces=REQ-INT-006,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-054,REQ-INT-061,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,API-INT-004,API-INT-005,SCR-INT-005 -->
### F2 — SCR-INT-005 Employee decision

```yaml name=screen-hooks
screen: SCR-INT-005
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-INT-005], cache_key: "['check', checkId]", errors: "RPT-404-CHECK-NOT-FOUND → page message; INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: DECISION-SAVE, kind: mutation, api: [API-INT-004], errors: "RPT-400-DECISION-INCOMPLETE, RPT-409-CHECK-NOT-COMPLETED, RPT-409-DECISION-ALREADY-RECORDED, RPT-422-APPROVAL-FLAG-ON-REJECTION, RPT-404-CHECK-NOT-FOUND, INT-502-APPROVAL-API-FAILED, INT-504-APPROVAL-API-TIMED-OUT, INT-400-REQUEST-INVALID, INT-500 → per §3.0", loading: LOCAL, invalidation: "['check', checkId], ['checks-of-request', {serviceCode, requestNumber}]"}
  - {hook: DECISION-FACADE, kind: facade, api: [API-INT-005, API-INT-004], loading: LOCAL}
```

### DECISION-SAVE — API-INT-004            traces=API-INT-004,REQ-INT-021,REQ-INT-023,REQ-INT-036,REQ-INT-037,REQ-INT-038
Kind mutation — body {employeeDecision, decidedBy = launch employeeId} (REQ-INT-023). 201 → invalidate and navigate to
SCR-INT-002, which shows the recorded decision (REQ-INT-022, REQ-INT-052). INT-502 / INT-504 → nothing was recorded:
no invalidation, the form keeps its value and the submit is enabled again — a new submit is a new request
(REQ-INT-038); the hook never retries on its own (REQ-INT-033).
Invalidation : ['check', checkId], ['checks-of-request', {serviceCode, requestNumber}] (on 201 only)
### DECISION-FACADE — SCR-INT-005
Composes CHECK-QUERY, DECISION-SAVE · owns: `offered` (status COMPLETED and `decision` null — REQ-INT-054), the last
refusal · operation: `decide(values)`.
<!-- SUB:F2-SCR-INT-005:END -->
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-057,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,AC-INT-069,AC-INT-070,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-001,API-INT-002,API-INT-003,API-INT-004,API-INT-005,API-INT-006,API-INT-007,API-INT-008,UXD-INT-001,UXD-INT-002,UXD-INT-003,UXD-INT-004,UXD-INT-005,UXD-INT-006,UXD-INT-007,UXD-INT-008,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005 -->
## PHASE F3 — F3 — Forms & Validators

Role: forms (`react-hook-form`) with `zod` schemas built from the document's request schemas, plus the screen's
pure validators/presenters. A form never validates a business rule the server owns (RULE-INT-001 … RULE-INT-003
are decided by the backend); it binds the server's refusal to the control named in §3.0. One form, one submit.

<!-- SUB:F3-SCR-INT-001:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-057,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-069,AC-INT-070,API-INT-001,API-INT-006,UXD-INT-001,SCR-INT-001 -->
### F3 — SCR-INT-001 Checks of a request
- `launchContextSchema` — serviceCode, requestNumber, employeeId each present and not blank; any one missing → RULE-INT-004 (REQ-INT-041), text: RULE-INT-004 message (SRS) "Open this screen from the host system for one request." Values are passed on unchanged — never trimmed or reformatted (REQ-INT-003).
- Start form — no field; its submit sends the validated launch context (StartCheckRequest of API-INT-001). Submit disabled while pending (one start per press).
<!-- SUB:F3-SCR-INT-001:END -->

<!-- SUB:F3-SCR-INT-002:START traces=REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-061,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,API-INT-005,UXD-INT-002,UXD-INT-003,UXD-INT-004,SCR-INT-002 -->
### F3 — SCR-INT-002 Check report
- `presentOverallStatus(report)` — returns the stored `overallStatus`, except `NOT_VERIFIED_MISSING` when it is COMPLIANT and any `documents[].readStatus` is MISSING (REQ-INT-049), shown as "Not verified — a required document is missing" (AC-INT-055; ar PENDING ADR-INT-017). Never derives a status of its own (ADR-INT-011 (2)); returns nothing for a FAILED Check (REQ-INT-050).
- `reportSchema` — the zod mirror of `CheckReportResponse` (API-INT-005) used to parse the response; an unparseable response → the error state, never a partial report.
- Plain-text rule — every report text is rendered as a text node: no HTML injection API, no markdown, no auto-linking (REQ-INT-051).
- No form, no submit.
<!-- SUB:F3-SCR-INT-002:END -->

<!-- SUB:F3-SCR-INT-003:START traces=REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-002,API-INT-005,API-INT-007,API-INT-008,UXD-INT-005,UXD-INT-006,SCR-INT-003 -->
### F3 — SCR-INT-003 Document upload
- `uploadFormSchema` — documentType: required, one of REQUIRED-TYPES-QUERY's `requiredDocumentTypes` (REQ-INT-016); file: required, exactly one file (UploadRequest of API-INT-002). No client size limit (the server decides — REQ-INT-013, REQ-INT-014).
- Server refusals bound to controls: `DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE` → inline on documentType; `INT-413-UPLOAD-TOO-LARGE`, `DOC-400-INCOMPLETE-UPLOAD` → inline on file; `INT-409-CHECK-NOT-AWAITING-DOCUMENTS` → form message (texts bound in §3.0).
- One submit ("Upload"); the confirmation is never part of this form (REQ-INT-019).
<!-- SUB:F3-SCR-INT-003:END -->

<!-- SUB:F3-SCR-INT-004:START traces=REQ-INT-006,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-071,AC-INT-073,API-INT-003,API-INT-005,API-INT-007,API-INT-008,UXD-INT-007,UXD-INT-008,SCR-INT-004 -->
### F3 — SCR-INT-004 Upload confirmation
- `confirmFormSchema` — the empty object of UploadConfirmationRequest (API-INT-003); no field.
- `missingTypes(required, uploaded)` — the required document types (REQUIRED-TYPES-QUERY) with no uploaded document of that type (UPLOADED-DOCUMENTS-QUERY), in the order the version lists them (REQ-INT-020).
- One submit ("Confirm uploads").
<!-- SUB:F3-SCR-INT-004:END -->

<!-- SUB:F3-SCR-INT-005:START traces=REQ-INT-006,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-054,REQ-INT-061,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,API-INT-004,API-INT-005,SCR-INT-005 -->
### F3 — SCR-INT-005 Employee decision
- `decisionFormSchema` — employeeDecision: required, APPROVED or REJECTED (DecisionRequest of API-INT-004); the submit adds `decidedBy` = the launch employeeId exactly as passed (REQ-INT-023).
- Server refusals bound: `RPT-400-DECISION-INCOMPLETE` → inline on employeeDecision; `RPT-409-CHECK-NOT-COMPLETED`, `RPT-409-DECISION-ALREADY-RECORDED`, `RPT-422-APPROVAL-FLAG-ON-REJECTION`, `INT-502-APPROVAL-API-FAILED`, `INT-504-APPROVAL-API-TIMED-OUT` → form message (texts bound in §3.0); the chosen decision is kept.
- One submit ("Record decision"); disabled while pending so one press sends one request (REQ-INT-033).
<!-- SUB:F3-SCR-INT-005:END -->
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-057,REQ-INT-061,REQ-INT-062,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,AC-INT-069,AC-INT-070,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-001,API-INT-002,API-INT-003,API-INT-004,API-INT-005,API-INT-006,API-INT-007,API-INT-008,UXD-INT-001,UXD-INT-002,UXD-INT-003,UXD-INT-004,UXD-INT-005,UXD-INT-006,UXD-INT-007,UXD-INT-008,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005 -->
## PHASE F4 — F4 — Screens & Routes

Role RF4. Router: `react-router`; one lazy chunk per screen. Guard: no permission model — screens open per the
SRS (raw-idea A2; REQ-INT-004); the only route guard is the launch-context check (RULE-INT-004), applied to every
route before any read. Every route carries the launch context in its query string (ADR-INT-018 (4)). Pages call
facades only. Every cross-module field cites its UXD-*.

<!-- SUB:F4-SCR-INT-001:START traces=REQ-INT-001,REQ-INT-006,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,REQ-INT-044,REQ-INT-057,REQ-INT-062,AC-INT-045,AC-INT-046,AC-INT-047,AC-INT-048,AC-INT-049,AC-INT-069,AC-INT-070,API-INT-001,API-INT-006,UXD-INT-001,SCR-INT-001 -->
### CHECKS-SCREEN — SCR-INT-001
Routes       : `/?serviceCode=&requestNumber=&employeeId=` (the host's launch address)
Guard        : launch-context check (RULE-INT-004) — no permission
Facade       : CHECKS-FACADE
Cross-module : UXD-INT-001 (RPT — API-INT-006)
Composition  : container page · Checks collection → inline list (newest first as received; "This request has {total} Checks." when `total` exceeds the listed count — REQ-INT-043) · a row opens `/checks/:checkId` seeding that ONE checkId
Saves        : ONE — "Start a Check" (API-INT-001); on 202 the list is refreshed and the new Check is first (REQ-INT-044)
<!-- SUB:F4-SCR-INT-001:END -->

<!-- SUB:F4-SCR-INT-002:START traces=REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-061,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,API-INT-005,UXD-INT-002,UXD-INT-003,UXD-INT-004,SCR-INT-002 -->
### REPORT-SCREEN — SCR-INT-002
Routes       : `/checks/:checkId` (+ launch query string)
Guard        : launch-context check (RULE-INT-004) — no permission
Facade       : REPORT-FACADE
Cross-module : UXD-INT-002, UXD-INT-003, UXD-INT-004 (RPT — API-INT-005)
Composition  : container page · header · findings collection → inline pane (each Finding one entry: condition, outcome, evidence, note side by side — REQ-INT-046) · documents collection → inline pane (UNREADABLE with reason and detail — REQ-INT-047) · service queries not read → inline pane (REQ-INT-048) · decision pane when recorded (REQ-INT-052) · failure pane when FAILED, no Overall Status (REQ-INT-050) · actions as links: "Upload documents" → `/checks/:checkId/documents`, "Confirm uploads" → `/checks/:checkId/upload-confirmation` (AWAITING_DOCUMENTS only — REQ-INT-053), "Record decision" → `/checks/:checkId/decision` (COMPLETED and undecided only — REQ-INT-054)
Saves        : none — the screen reads and navigates; it follows the Check every 5 seconds until it ends (REQ-INT-055, REQ-INT-056)
<!-- SUB:F4-SCR-INT-002:END -->

<!-- SUB:F4-SCR-INT-003:START traces=REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-002,API-INT-005,API-INT-007,API-INT-008,UXD-INT-005,UXD-INT-006,SCR-INT-003 -->
### UPLOAD-SCREEN — SCR-INT-003
Routes       : `/checks/:checkId/documents` (+ launch query string)
Guard        : launch-context check (RULE-INT-004) — no permission; the form is offered only while the Check is AWAITING_DOCUMENTS
Facade       : UPLOAD-FACADE
Cross-module : UXD-INT-005 (REG — API-INT-008), UXD-INT-006 (DOC — API-INT-007)
Composition  : container page · upload form (document type, file) · uploaded documents collection → inline pane beneath the form, read-only · "Confirm uploads" is a link to `/checks/:checkId/upload-confirmation`, not a submit
Saves        : ONE — "Upload" (API-INT-002); it never confirms (REQ-INT-019)
<!-- SUB:F4-SCR-INT-003:END -->

<!-- SUB:F4-SCR-INT-004:START traces=REQ-INT-006,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-071,AC-INT-073,API-INT-003,API-INT-005,API-INT-007,API-INT-008,UXD-INT-007,UXD-INT-008,SCR-INT-004 -->
### CONFIRMATION-SCREEN — SCR-INT-004
Routes       : `/checks/:checkId/upload-confirmation` (+ launch query string)
Guard        : launch-context check (RULE-INT-004) — no permission
Facade       : CONFIRMATION-FACADE
Cross-module : UXD-INT-007 (DOC — API-INT-007), UXD-INT-008 (REG — API-INT-008)
Composition  : container page · uploaded documents collection → inline pane · missing types collection → inline pane (read-only, shown before the submit — REQ-INT-020)
Saves        : ONE — "Confirm uploads" (API-INT-003); on 202 → `/checks/:checkId`
<!-- SUB:F4-SCR-INT-004:END -->

<!-- SUB:F4-SCR-INT-005:START traces=REQ-INT-006,REQ-INT-021,REQ-INT-022,REQ-INT-023,REQ-INT-024,REQ-INT-025,REQ-INT-026,REQ-INT-027,REQ-INT-028,REQ-INT-034,REQ-INT-035,REQ-INT-036,REQ-INT-037,REQ-INT-038,REQ-INT-054,REQ-INT-061,AC-INT-025,AC-INT-026,AC-INT-027,AC-INT-038,AC-INT-039,AC-INT-040,AC-INT-041,AC-INT-042,AC-INT-043,API-INT-004,API-INT-005,SCR-INT-005 -->
### DECISION-SCREEN — SCR-INT-005
Routes       : `/checks/:checkId/decision` (+ launch query string)
Guard        : launch-context check (RULE-INT-004) — no permission; the form is offered only while the Check is COMPLETED with no Employee Decision (REQ-INT-054)
Facade       : DECISION-FACADE
Cross-module : none rendered (the Check read decides `offered` only)
Composition  : container page · decision form (APPROVED / REJECTED; decided by = launch identity, read-only) · no secondary detail
Saves        : ONE — "Record decision" (API-INT-004), one call; on 201 → `/checks/:checkId`; on INT-502 / INT-504 the form stays and may be submitted again (REQ-INT-038)
<!-- SUB:F4-SCR-INT-005:END -->
<!-- PHASE:F4:END -->

<!-- PHASE:ALIGN-FE:START traces=REQ-INT-049,REQ-INT-051,REQ-INT-057,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005 -->
## PHASE ALIGN-FE — ALIGN-FE

RF5 — Security (frontend half): no permission model — screens open per the SRS (REQ-INT-004; caller
authentication deferred, raw-idea A2). AIAS-11: every Finding shows its Evidence beside it (REQ-INT-046 — F4
SCR-INT-002) and a report with a MISSING document is never presented as COMPLIANT (REQ-INT-049 — F3 presenter);
an UNREADABLE document is shown with its reason beside the stored Overall Status (ADR-INT-011 (2), ADR-INT-018 (5) — kept by ADR-INT-021 (6)).

```
ALIGN — INT v1
row           backing check       mark   assertion
SCREENS       orphans             ✓      every SCR is referenced by a plan block
COMPOSITION   screen-composition  ✓      every SCR names where its secondary detail sits and that it saves once
CONTAINER     composition-rule    ✓      every SCR names its container, and a child collection sits where that container puts it
READS         ux-reads-spec       ✓      every read a screen binds is an operation of api-spec-int.yaml, and the plan names the document its mock server serves
UXD           orphans             ✓      every UXD is cited by a plan block — this is where a UX decision closes
TRACES        traces              ✓      every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces              ✓      every API this plan cites is an operation of api-spec-int.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface        ✓      every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree      ✓      every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages           ✓      labels and messages in en + ar (profile require_all false; labels en/ar in the ui-ux-spec, messages ar PENDING ADR-INT-017)
MARKERS       markers             ✓      the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist          ✓      every ADR this plan cites exists on disk in analysis/decisions/INT/
COVERAGE      (the report)        none   the clauses the analyze report lists as having examined nothing — none of this stage's
```
```yaml name=self-check
findings: 0
clean: true
```
<!-- PHASE:ALIGN-FE:END -->

## Operations coverage

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| Start a Check | API-INT-001 | SCR-INT-001 "Start a Check" | `/` | ✓ |
| Hand over an uploaded document | API-INT-002 | SCR-INT-003 "Upload" | `/checks/:checkId/documents` | ✓ |
| Confirm the uploads | API-INT-003 | SCR-INT-004 "Confirm uploads" | `/checks/:checkId/upload-confirmation` | ✓ |
| Record an Employee Decision | API-INT-004 | SCR-INT-005 "Record decision" | `/checks/:checkId/decision` | ✓ |
| Read a Check and its report | API-INT-005 | SCR-INT-002 read + 5 s follow; SCR-INT-003/004/005 status read | `/checks/:checkId` (and sub-routes) | ✓ |
| List the Checks of a request | API-INT-006 | SCR-INT-001 list | `/` | ✓ |
| Read the required document types of a Check's version | API-INT-008 | SCR-INT-003 choices; SCR-INT-004 missing types | `/checks/:checkId/documents`, `/checks/:checkId/upload-confirmation` | ✓ |
| List the uploaded documents of a Check | API-INT-007 | SCR-INT-003 list; SCR-INT-004 list | `/checks/:checkId/documents`, `/checks/:checkId/upload-confirmation` | ✓ |

Every operation of `api-spec-int.yaml` is bound by a screen (8/8).
Registry: `registry-exec-fe-int.md`.
