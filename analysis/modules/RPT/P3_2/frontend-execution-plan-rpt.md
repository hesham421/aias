# FRONTEND EXECUTION PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod)
Inputs : srs-rpt.md · prd-rpt.md · api-spec-rpt.yaml (the backend plan's document — served by the mock server) · registry-srs-rpt.md · registry-exec-be-rpt.md
Screens : 0 (SCR-REQ 0)   UXD : 0   Open ADRs : 0 BLOCKED — applied ADR-RPT-013, ADR-RPT-014
══════════════════════════════════════════════════════════════════

RPT has no screen of its own (ADR-RPT-014). This plan states RPT's frontend surface as what the embedded employee frontend consumes from RPT's API: the client models (F1), the read hooks (F2) and the parameter validators (F3) of API-RPT-001 and API-RPT-002. The screens and routes that compose them are Host Integration's, which binds its screens' hook tables to these operations. The sub-bearing phases therefore carry no per-screen SUB. No SEC-FE phase and no authentication: security is deferred (raw-idea A2).

## 3.0 Binding to the API document

```yaml name=api-surface
mock: api-spec-rpt.yaml
bindings:
  - {req: REQ-RPT-023, api: [API-RPT-001]}
  - {req: REQ-RPT-024, api: [API-RPT-001]}
  - {req: REQ-RPT-025, api: [API-RPT-001]}
  - {req: REQ-RPT-026, api: [API-RPT-001]}
  - {req: REQ-RPT-027, api: [API-RPT-001]}
  - {req: REQ-RPT-028, api: [API-RPT-002]}
  - {req: REQ-RPT-029, api: [API-RPT-002]}
  - {req: REQ-RPT-030, api: [API-RPT-002]}
  - {req: REQ-RPT-031, api: [API-RPT-002]}
  - {req: REQ-RPT-050, api: [API-RPT-002]}
unmapped:
  - "API-RPT-003 (REQ-RPT-040, REQ-RPT-041) — no employee-frontend consumer; the service administrator reads it directly, administration UI out of scope (ADR-RPT-014)"
codes:
  - {code: "RPT-400-REQUEST-KEYS-MISSING", rule: RULE-RPT-009}
  - {code: "RPT-400-SERVICE-CODE-MISSING", rule: RULE-RPT-015}
```

Reconciliation against the SRS: every REQ that the employee frontend needs has an operation (REQ-RPT-023 … REQ-RPT-031, REQ-RPT-050 → API-RPT-001 / API-RPT-002); every operation maps to a REQ; the result port and the decision are in-process (ADR-RPT-006) and have no HTTP operation — the decision the employee records goes through Host Integration's operation, not RPT's. The other catalog codes (RPT-400-CHECK-ID-INVALID, RPT-404-CHECK-NOT-FOUND, RPT-500) are PLATFORM-STD rows (ADR-RPT-013) and carry no RULE.

<!-- PHASE:F1:START traces=REQ-RPT-023,REQ-RPT-024,REQ-RPT-026,REQ-RPT-027,REQ-RPT-028,REQ-RPT-031,AC-RPT-027,AC-RPT-028,AC-RPT-031,API-RPT-001,API-RPT-002 -->
## PHASE F1 — Models & Types

No per-screen SUB: RPT has no screen (ADR-RPT-014). The models below are RPT's client types, generated from api-spec-rpt.yaml and shared by every consuming screen.

Field/DTO binding : see api-spec-rpt.yaml — the response schemas of API-RPT-001 (`CheckReport` with `FindingView`, `DocumentView`, `UnreadQueryView`, `DecisionView`) and API-RPT-002 (`ChecksOfRequest` with `CheckSummary`), and `ProblemDetail` for every error, are the source; they are not restated here.

| Model | From | Obligation (from the SRS, not a new field) |
|---|---|---|
| CheckReport | API-RPT-001 200 | status-discriminated reading: `overallStatus`, `comparisonModel` present exactly when COMPLETED; `failureReason`, `failureDetail` exactly when FAILED; `findings`, `documents`, `unreadQueries` empty until COMPLETED; `decision` null until recorded (REQ-RPT-023, REQ-RPT-026; AC-RPT-027, AC-RPT-028, AC-RPT-031) |
| FindingView | API-RPT-001 200 | one entry holding condition, outcome, evidence, note — never split across models (REQ-RPT-024) |
| DocumentView | API-RPT-001 200 | one entry holding documentType, sourceMode, readStatus, unreadableReason, detail; `unreadableReason` present exactly when readStatus is UNREADABLE (REQ-RPT-017, RULE-RPT-008) |
| text fields (condition, evidence, note, detail, queryName, requestNumber, employeeId, decidedBy) | API-RPT-001 / API-RPT-002 | typed `string`, kept exactly as received — no trimming, case change or parsing (REQ-RPT-027) |
| ChecksOfRequest | API-RPT-002 200 | `total` kept beside `checks` (≤ 100) so the cut stays visible (REQ-RPT-028, REQ-RPT-031) |
| closed codes | api-spec enums | CHECK_STATUS, OVERALL_STATUS, FINDING_OUTCOME, CHECK_FAILURE_REASON, FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON, EMPLOYEE_DECISION as string-literal unions of the document's enums; labels en per SRS, ar PENDING ADR-RPT-013 |
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=REQ-RPT-023,REQ-RPT-025,REQ-RPT-028,REQ-RPT-029,REQ-RPT-031,REQ-RPT-050,AC-RPT-030,AC-RPT-033,AC-RPT-034,AC-RPT-035,API-RPT-001,API-RPT-002 -->
## PHASE F2 — Data Hooks

No per-screen SUB: RPT has no screen (ADR-RPT-014). The two read hooks below are RPT's; Host Integration's screen SUBs list them in their own hook tables against the same operations. The two `screen-hooks` blocks name the Host Integration screen requirement that consumes each read (SCR-REQ-INT-001 Checks of a request, SCR-REQ-INT-002 Check report — the consumer, cited by its SRS id, not a screen of this plan) and declare only RPT's reads. Server-state library `tanstack-query`; components use the consuming screen's facade only.

```yaml name=screen-hooks
screen: SCR-REQ-INT-001
hooks:
  - {hook: CHECKS-OF-REQUEST-QUERY, kind: read, api: [API-RPT-002], cache_key: "[rpt-checks-of-request, {serviceCode, requestNumber}]", errors: "RPT-400-REQUEST-KEYS-MISSING → user message (RULE-RPT-009 text) · RPT-500 → generic", loading: LOCAL, invalidation: "—"}
```

```yaml name=screen-hooks
screen: SCR-REQ-INT-002
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-RPT-001], cache_key: "[rpt-check, {checkId}]", errors: "RPT-404-CHECK-NOT-FOUND → not-found state (REQ-RPT-025 text) · RPT-400-CHECK-ID-INVALID / RPT-500 → generic", loading: LOCAL, invalidation: "—"}
```

### CHECK-QUERY — API-RPT-001            traces=API-RPT-001,REQ-RPT-023,REQ-RPT-025
Kind         : read query — method, path and response schema are cited by API-RPT-001 (an operation of api-spec-rpt.yaml), never restated
Cache key    : ["rpt-check", {checkId}]
Errors       : `RPT-400-CHECK-ID-INVALID` → generic error state, text: RPT-400-CHECK-ID-INVALID catalogue detail (ADR-RPT-013) · `RPT-404-CHECK-NOT-FOUND` → not-found state (told apart from a running Check), text: RPT-404-CHECK-NOT-FOUND catalogue detail "Check {checkId} was not found." (SRS REQ-RPT-025; ar PENDING ADR-RPT-013) · `RPT-500` → generic error state, text: RPT-500 catalogue detail
Loading      : LOCAL
Cache policy : defaults; while `status` is AWAITING_DOCUMENTS or RUNNING the consuming screen may refetch on an interval (the host polls — [KB:raw-idea.md §5]); once COMPLETED or FAILED the report never changes (REQ-RPT-020), so no refetch
Invalidation : — (read); a decision recorded through Host Integration's operation invalidates ["rpt-check", {checkId}] and ["rpt-checks-of-request", …] in Host Integration's mutation

### CHECKS-OF-REQUEST-QUERY — API-RPT-002            traces=API-RPT-002,REQ-RPT-028,REQ-RPT-029,REQ-RPT-031,REQ-RPT-050
Kind         : read query — cited by API-RPT-002, never restated
Cache key    : ["rpt-checks-of-request", {serviceCode, requestNumber}] — both filters that change the response are in the key; no page or size (fixed cap of 100, REQ-RPT-031)
Errors       : `RPT-400-REQUEST-KEYS-MISSING` → user message, text: RULE-RPT-009 message "Both a service code and a request number are needed to list Checks." (SRS RULE-RPT-009; ar PENDING ADR-RPT-013) · `RPT-500` → generic error state, text: RPT-500 catalogue detail
Loading      : LOCAL
Cache policy : defaults
Invalidation : — (read)

The service code and request number come from the frontend's launch context (Host Integration) and are passed exactly as received — bound parameters on the server (REQ-RPT-050).
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=REQ-RPT-029,REQ-RPT-050,REQ-RPT-025,AC-RPT-035,AC-RPT-058,API-RPT-001,API-RPT-002 -->
## PHASE F3 — Forms & Validators

No form: RPT's HTTP surface is read-only (ADR-RPT-006) and RPT has no screen; the employee's decision form is Host Integration's. RPT's validators are the `zod` schemas of the two read parameter sets, applied before a call is sent:

| Schema | Operation | Rule | On failure |
|---|---|---|---|
| CheckIdParam — integer, int64 | API-RPT-001 | the path parameter is a number (the document's parameter schema) | the call is not sent; the generic error state — the server's RPT-400-CHECK-ID-INVALID never needs to be provoked |
| ChecksOfRequestParams — serviceCode and requestNumber present, not blank, ≤ 100 characters | API-RPT-002 | RULE-RPT-009 | the call is not sent; the user message, text: RULE-RPT-009 message "Both a service code and a request number are needed to list Checks." (SRS RULE-RPT-009) |

No validator transforms a value: no trim, no case change, no escaping — the request number `1001' OR '1'='1` is sent as typed and the server binds it (REQ-RPT-050, AC-RPT-058).
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=REQ-RPT-010,REQ-RPT-024,REQ-RPT-027,REQ-RPT-032,REQ-RPT-036,REQ-RPT-037,AC-RPT-013,AC-RPT-029,AC-RPT-032,AC-RPT-038,AC-RPT-043,AC-RPT-044,API-RPT-001 -->
## PHASE F4 — Screens & Routes

No screen and no route: RPT declares none (ADR-RPT-014); every route of the embedded frontend is Host Integration's. Security (frontend half): no permission model — screens open per the SRS (REQ-RPT-023, REQ-RPT-028; raw-idea A2).

Rendering obligations any consuming screen owes RPT's data (from the SRS; not components):
- condition, evidence, note, detail and query name are rendered as text nodes — never as HTML, markdown or a link built from their content (REQ-RPT-027, AC-RPT-032);
- each finding is one visual entry holding its condition, outcome, evidence and note together (REQ-RPT-024, AC-RPT-029);
- findings, documents and unread queries are rendered in `position` order as returned (REQ-RPT-010, AC-RPT-013);
- each Check Document is one visual entry showing documentType, sourceMode and readStatus; READ, MISSING and UNREADABLE carry distinct text labels (never colour alone), an UNREADABLE document shows its unreadableReason and detail, and a missing or unreadable document is never shown as satisfied (REQ-RPT-023, REQ-RPT-017; ui-ux-spec design intent);
- a document path quoted in a detail is shown as text; nothing is opened or fetched from it (REQ-RPT-048);
- the Employee Decision block renders only when `decision` is not null — before that the screen shows "no decision yet", never a default decision; once recorded, employeeDecision, decidedBy (as text) and decidedAt are shown together with approvalApiExecuted as its own distinct text label (executed through the Approval API / not executed through it), beside — never in place of — the Overall Status and findings, and no wording suggests the Report Store approved on its own (REQ-RPT-032, REQ-RPT-036, REQ-RPT-037; AC-RPT-038, AC-RPT-043, AC-RPT-044; ui-ux-spec design intent).
<!-- PHASE:F4:END -->

<!-- PHASE:ALIGN-FE:START traces=REQ-RPT-023,REQ-RPT-028,API-RPT-001,API-RPT-002 -->
## PHASE ALIGN-FE — ALIGN-FE

```
ALIGN — RPT v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block — examined nothing (0 SCR, ADR-RPT-014)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once — examined nothing (0 SCR)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it — examined nothing (0 SCR)
READS         ux-reads-spec   every read a screen binds is an operation of api-spec-rpt.yaml, and the plan names the document its mock server serves
UXD           orphans         every UXD is cited by a plan block — examined nothing (0 UXD)
TRACES        traces          every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces          every API this plan cites is an operation of api-spec-rpt.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface    every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree  every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages       labels and messages in en + ar
MARKERS       markers         the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in analysis/decisions/RPT/
COVERAGE      (the report)    as stamped by the orchestrator from the analyze report
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
| Read a Check and its report | API-RPT-001 | none in RPT — read by Host Integration's Check report screen | Host Integration's route | consumed (CHECK-QUERY) — no RPT route by design (ADR-RPT-014) |
| List the Checks of a request | API-RPT-002 | none in RPT — read by Host Integration's Checks of a request screen | Host Integration's route | consumed (CHECKS-OF-REQUEST-QUERY) — no RPT route by design (ADR-RPT-014) |
| Read the decision agreement of a service | API-RPT-003 | none | none | unused by the employee frontend — administration UI out of scope (ADR-RPT-014) |
<!-- PHASE:ALIGN-FE:END -->

## Hand-off

Implementer reads F1 → F4 in order and api-spec-rpt.yaml for every shape (served by the mock server until the backend is delivered). It adds no RPT route, screen, component, permission or field; Host Integration's plan composes these hooks into its screens.
