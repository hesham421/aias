<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
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
  - {req: REQ-INT-065, api: [API-INT-002, API-INT-007]}
  - {req: REQ-INT-066, api: [API-INT-005]}
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
- `DOC-409-CHECK-ENDED` → form message on the upload form, the form then not offered (the Check has ended), text: catalogue DOC-409-CHECK-ENDED (server detail — ADR-INT-025)
- `DOC-422-UPLOAD-LIMIT-REACHED` → form message on the upload form, text: catalogue DOC-422-UPLOAD-LIMIT-REACHED (server detail — ADR-INT-025)
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
