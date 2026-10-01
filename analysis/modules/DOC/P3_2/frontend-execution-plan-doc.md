# FRONTEND EXECUTION PLAN — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module : DOC   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod; lazy chunk per screen)
Inputs : srs-doc.md · prd-doc.md · api-spec-doc.yaml · registry-srs-doc.md · registry-exec-be-doc.md · ui-ux-spec-doc.md · flow-diagram-doc.md
Screens : 0 (DOC has no screen — ADR-DOC-003, ADR-DOC-013)   UXD : 0   Open ADRs : 0 BLOCKED — decisions applied: ADR-DOC-003, ADR-DOC-011, ADR-DOC-012, ADR-DOC-013
══════════════════════════════════════════════════════════════════

## Scope of this plan

DOC owns no screen. The employee frontend (raw-idea amendment A1) belongs to INT, whose Document upload and Upload confirmation screens show the files handed over for a Check. DOC's frontend surface is therefore only what that frontend consumes from DOC's API: the one read API-DOC-001 (`GET /api/v1/uploaded-documents?checkId=`), its model, its validators, one shared query with its facade, and the module entry exporting them. The sub-bearing phases F1–F4 carry no SUB — a SUB is qualified by a screen, and there is none — and each says so (ADR-DOC-013). No screen, route, form, permission or field is added here.

## API binding — §3.0

Bound to `api-spec-doc.yaml` (the document the backend plan derived; served by the frontend executor's mock server until the backend is delivered). Shapes are read there by `x-api-id`, never restated.

```yaml name=api-surface
mock: api-spec-doc.yaml
bindings:
  - {req: REQ-DOC-017, api: [API-DOC-001]}
  - {req: REQ-DOC-018, api: [API-DOC-001]}
  - {req: REQ-DOC-043, api: [API-DOC-001]}
  - {req: REQ-DOC-056, api: [API-DOC-001]}
  - {req: REQ-DOC-064, api: [API-DOC-001]}
unmapped: []
```

Reconciliation against the SRS:
- Every REQ that needs an HTTP operation has one: the five REQs above (the Uploaded Documents of a Check — created by the handover, read only for that Check, oversized without content, never another Check's, listed without content — REQ-DOC-064, whose in-process form CON-DOC-006 INT calls, ADR-DOC-017). The other 59 REQs are fulfilled by the in-process `DocumentAccess` operations called by CHK and INT and need no HTTP operation (ADR-DOC-011).
- Every operation of the document maps to a REQ: API-DOC-001 → REQ-DOC-017, REQ-DOC-018, REQ-DOC-043, REQ-DOC-056, REQ-DOC-064. `unmapped` is empty.
- Runtime codes → RULE: none. API-DOC-001's two codes, `DOC-400-CHECK-ID-REQUIRED` and `DOC-500`, are PLATFORM-STD rows with no RULE (ADR-DOC-012), so the `codes` list is omitted. The upload-handover rule codes (RULE-DOC-001 … RULE-DOC-003, RULE-DOC-005, RULE-DOC-009, RULE-DOC-010) reach the frontend through INT's upload endpoint and are bound in INT's plan.

<!-- PHASE:F1:START traces=REQ-DOC-017,REQ-DOC-018,REQ-DOC-043,REQ-DOC-056,API-DOC-001 -->
## PHASE F1 — Models & Types

Per-screen SUBs: none — DOC has no screen (ADR-DOC-013). The phase carries the module-level model consumed by INT's screens.

### UPLOADED-DOCUMENT-SUMMARY — API-DOC-001            traces=API-DOC-001,REQ-DOC-017,REQ-DOC-043
Field/DTO binding : the 200 response of API-DOC-001 — an array of `UploadedDocumentSummary` (`api-spec-doc.yaml` → components.schemas); the frontend type is generated from / checked against that schema, never hand-copied.
Error model       : `ProblemDetail` of the same document (`application/problem+json`), carrying `code`.
Content           : the model has no content member — the document never returns the file (REQ-DOC-043); an oversized upload is a summary with `oversized = true` (RULE-DOC-005).
Request model     : the query parameter `checkId` of API-DOC-001 (integer, int64, required).
Labels            : en from the SRS (Uploaded document id · Document type · File name · File size · Oversized · Created at); ar PENDING ADR-DOC-012 — the consuming screens render them.
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=REQ-DOC-017,REQ-DOC-018,REQ-DOC-043,REQ-DOC-056,API-DOC-001 -->
## PHASE F2 — Data Hooks

Per-screen SUBs: none — DOC has no screen (ADR-DOC-013). The one read DOC offers is declared below at module level. The two `screen-hooks` blocks that follow are **consumer bindings**, not DOC screens: they record, for each INT screen the SRS of INT declares as reading DOC's API (SCR-REQ-INT-003 Document upload, SCR-REQ-INT-004 Upload confirmation), which DOC operation that screen reads through DOC-FRONTEND-ENTRY. INT's plan owns those screens, their SUBs and their full hook tables.

```yaml name=screen-hooks
screen: SCR-REQ-INT-003
hooks:
  - {hook: UPLOADED-DOCUMENTS-QUERY, kind: read, api: [API-DOC-001], cache_key: "[uploaded-documents, {checkId}]", errors: "DOC-400-CHECK-ID-REQUIRED → generic · DOC-500 → generic", loading: LOCAL, invalidation: "—"}
  - {hook: UPLOADED-DOCUMENTS-FACADE, kind: facade, api: [API-DOC-001]}
```

```yaml name=screen-hooks
screen: SCR-REQ-INT-004
hooks:
  - {hook: UPLOADED-DOCUMENTS-QUERY, kind: read, api: [API-DOC-001], cache_key: "[uploaded-documents, {checkId}]", errors: "DOC-400-CHECK-ID-REQUIRED → generic · DOC-500 → generic", loading: LOCAL, invalidation: "—"}
  - {hook: UPLOADED-DOCUMENTS-FACADE, kind: facade, api: [API-DOC-001]}
```

### UPLOADED-DOCUMENTS-QUERY — API-DOC-001            traces=API-DOC-001,REQ-DOC-018,REQ-DOC-056
Kind         : read query — method, path and response schema are cited by API-DOC-001 (an operation of api-spec-doc.yaml), never restated
Cache key    : [uploaded-documents, {checkId}] — `checkId` is the only filter that changes the response; one cache entry per Check, so no Check's list is ever served for another (REQ-DOC-018, REQ-DOC-056, RULE-DOC-008). The read is not paginated (no page / size in the key).
Errors       : DOC-400-CHECK-ID-REQUIRED → generic error (the F3 validator prevents the call without a numeric checkId, so the code is not user-reachable), text: catalogue DOC-400-CHECK-ID-REQUIRED · DOC-500 → generic error, text: catalogue DOC-500 · no unauthenticated / forbidden routing (no permission model, raw-idea A2)
Loading      : LOCAL — the consuming screen's list region; the SRS states no slow call
Cache policy : defaults
Invalidation : — (read). INT's upload mutation invalidates [uploaded-documents, {checkId}] for its own Check on success — declared in INT's plan.
Empty result : 200 with an empty array is the empty state (no upload yet, or the Check has ended and its uploads were deleted — REQ-DOC-054), never an error.

### UPLOADED-DOCUMENTS-FACADE — module level            traces=API-DOC-001,REQ-DOC-017,REQ-DOC-023,REQ-DOC-043
Composes     : UPLOADED-DOCUMENTS-QUERY only — components and INT's screens use the facade, never the query directly
State it owns: the filter object {checkId}; the list from the query data in upload order as returned (createdAt ascending — several uploads of one document type are all listed, the earlier one unchanged, REQ-DOC-023); derived loading / error / empty flags
Operations   : none — DOC exposes no frontend mutation (ADR-DOC-011); uploading is INT's
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=REQ-DOC-017,REQ-DOC-018,REQ-DOC-043,API-DOC-001 -->
## PHASE F3 — Forms & Validators

Per-screen SUBs: none — DOC has no screen (ADR-DOC-013). DOC has no form: the upload form is on INT's Document upload screen.

### UPLOADED-DOCUMENTS-VALIDATORS — API-DOC-001            traces=API-DOC-001,REQ-DOC-017,REQ-DOC-043
Request      : `checkId` — integer, int64, required (API-DOC-001 parameter); the facade issues no call while it is absent or not an integer.
Response     : each item checked against `UploadedDocumentSummary` of the document — required uploadedDocumentId, documentType, fileName, fileSize (≥ 1, matching RULE-DOC-003's non-empty file), oversized, createdAt; no content member is accepted (REQ-DOC-043). A response failing the schema → generic error (DOC-500 routing of F2).
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=REQ-DOC-017,REQ-DOC-018,REQ-DOC-054,API-DOC-001 -->
## PHASE F4 — Screens & Routes

Per-screen SUBs: none — DOC has no screen and registers no route (ADR-DOC-003, ADR-DOC-013).

### DOC-FRONTEND-ENTRY — module entry            traces=API-DOC-001,REQ-DOC-017,REQ-DOC-018,REQ-DOC-054
Exports      : the F2 facade (UPLOADED-DOCUMENTS-FACADE) and the F1 model — the only DOC symbols INT's screens import
Routes       : none — 0 routes, 0 lazy chunks (lazy chunk per screen; DOC has no screen)
Guard        : none — no permission model, screens open per the SRS (raw-idea A2)
Cross-module : none — DOC renders no foreign field (no UXD); INT's screens cite their own UXD for DOC's fields
Composition  : none — no screen
Saves        : none
<!-- PHASE:F4:END -->

<!-- PHASE:ALIGN-FE:START traces=REQ-DOC-017,REQ-DOC-018,REQ-DOC-043,REQ-DOC-056,API-DOC-001 -->
## PHASE ALIGN-FE — ALIGN-FE

R5 — Security (frontend half): no permission model — screens open per the SRS (caller authentication deferred, raw-idea A2; REQ-DOC-017, REQ-DOC-018 name no role check).

```
ALIGN — DOC v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block — examined nothing (0 SCR)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once — examined nothing (0 SCR)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it — examined nothing (0 SCR)
READS         ux-reads-spec   every read a screen binds is an operation of api-spec-doc.yaml, and the plan names the document its mock server serves — examined nothing (0 screen SUBs; the mock line names api-spec-doc.yaml)
UXD           orphans         every UXD is cited by a plan block — this is where a UX decision closes — examined nothing (0 UXD)
TRACES        traces          every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD (UXD/SCR clauses examined nothing — 0 of each)
API           traces          every API this plan cites is an operation of api-spec-doc.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface    every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree  every UXD and SCR defined here is in the stage registry, and nothing else is — examined nothing (0 UXD, 0 SCR)
LANGUAGES     languages       labels and messages in en + ar
MARKERS       markers         the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in analysis/decisions/DOC/
COVERAGE      (the report)    C9.3, C9.4, C9.6, C9.7, C9.8, C9.15, C9.17, C9.22, C9.24 examined nothing (DOC has no screen)
```
```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C9.15
- C9.17
- C9.22
- C9.24
- C9.3
- C9.4
- C9.6
- C9.7
- C9.8
```

Operations coverage:

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| List the Uploaded Documents of a Check | API-DOC-001 | none in DOC — consumed by INT's Document upload and Upload confirmation screens through DOC-FRONTEND-ENTRY | — (DOC has no route; the route is INT's) | ✓ bound (ADR-DOC-013) |
<!-- PHASE:ALIGN-FE:END -->

## Index

| Phase | Blocks | SUBs |
|---|---|---|
| F1 | UPLOADED-DOCUMENT-SUMMARY | none (0 screens) |
| F2 | UPLOADED-DOCUMENTS-QUERY, UPLOADED-DOCUMENTS-FACADE | none (0 screens) |
| F3 | UPLOADED-DOCUMENTS-VALIDATORS | none (0 screens) |
| F4 | DOC-FRONTEND-ENTRY | none (0 screens) |
| ALIGN-FE | ALIGN table, operations coverage | never split |

## Hand-off

The implementer reads this plan in phase order, `api-spec-doc.yaml` for every shape (served by its mock server), and INT's frontend plan for the screens that consume DOC-FRONTEND-ENTRY. No route, component, permission or field is added beyond the blocks above; a gap is an ADR.
