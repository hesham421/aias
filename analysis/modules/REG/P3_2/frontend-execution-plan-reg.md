# FRONTEND EXECUTION PLAN — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod)
Inputs : srs-reg.md · prd-reg.md · api-spec-reg.yaml · registry-srs-reg.md · registry-exec-be-reg.md
Screens : 0 SCR · 0 UXD — REG has no screen (ADR-REG-012)
Security : no permission model — screens open per the SRS; caller authentication deferred (raw idea A2), so the profile has no SEC-FE phase
Open ADRs : 0 BLOCKED — decisions applied: ADR-REG-005, ADR-REG-007, ADR-REG-011, ADR-REG-012, ADR-REG-016 (analysis/decisions/REG/; header regenerated from the ADRs this file's body cites — gate-analysis REVISE round 2)
══════════════════════════════════════════════════════════════════

## 3.0 Binding to the API document

REG's frontend surface is only what a frontend consumes from REG's read API: the three GET operations of `api-spec-reg.yaml`. Method, path, parameters and response schemas (`ServiceSummary`, `LoadResult`, `ProblemDetail`) are read in the document by `x-api-id`, never restated here. The document has no security scheme (raw idea A2).

```yaml name=api-surface
mock: api-spec-reg.yaml
bindings:
  - {req: REQ-REG-013, api: [API-REG-001]}
  - {req: REQ-REG-014, api: [API-REG-002]}
  - {req: REQ-REG-015, api: [API-REG-002]}
  - {req: REQ-REG-008, api: [API-REG-003]}
unmapped: []
codes:
  - {code: "REG-404-SERVICE-NOT-FOUND", rule: RULE-REG-016}
```

Reconciliation against the SRS: every REQ of the SRS "API expectations" table that has an HTTP operation is bound above (REQ-REG-013, REQ-REG-014, REQ-REG-015, REQ-REG-008); every operation of the document maps to a REQ. The in-process operations (supply service package, resolve version, supply connection, supply approval API) have no HTTP operation by design (ADR-REG-011) and are never called by a frontend. `REG-500` is PLATFORM-STD (no RULE) and is routed to the generic server error. No ADR is pending against the document.

<!-- PHASE:F1:START traces=REQ-REG-008,REQ-REG-013,REQ-REG-014,AC-REG-008,AC-REG-014,AC-REG-015,API-REG-001,API-REG-002,API-REG-003 -->
## PHASE F1 — F1 — Models & Types

No screen SUB: REG has no `SCR-*` (SRS PART B not applicable; administration UI out of scope; the employee frontend's screens are INT's — ADR-REG-012). This phase holds REG's client types only.

**RF1 — Models & types.**
Field/DTO binding : see `api-spec-reg.yaml` — the response schemas of API-REG-001, API-REG-002 (`ServiceSummary`) and API-REG-003 (`LoadResult`), and the error body `ProblemDetail`, are the source, not restated here. Types are generated from the document, never hand-written.

| Type | Operation(s) | Rule |
|---|---|---|
| `ServiceSummary` | API-REG-001 (array), API-REG-002 | exactly the document's properties, including the required `available` flag (false for a withdrawn service — ADR-REG-016); it carries no SQL text and no connection setting (REQ-REG-013, AC-REG-014) |
| `LoadResult` | API-REG-003 (array) | exactly the document's properties; nullable `serviceCode`, `versionNumber`, `reason` stay nullable (AC-REG-008) |
| `ProblemDetail` | every error response | `code` carries the catalog code (`REG-404-SERVICE-NOT-FOUND`, `REG-500`) |
| `FetchMode` | `ServiceSummary.fetchMode` | the document's closed enum path · blob · manual (ADR-REG-005) |
| `LoadOutcome`, `LoadSubject` | `LoadResult.outcome`, `LoadResult.subjectKind` | the document's closed enums |

No request type: REG exposes no create, update or delete (REQ-REG-017, ADR-REG-007).
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=REQ-REG-008,REQ-REG-013,REQ-REG-014,REQ-REG-015,AC-REG-008,AC-REG-014,AC-REG-015,AC-REG-016,API-REG-001,API-REG-002,API-REG-003 -->
## PHASE F2 — F2 — Data Hooks

No screen SUB: REG has no `SCR-*` (ADR-REG-012). This phase declares the three read queries a consuming screen (INT's) reuses; INT's own plan binds them in its screens' hook tables. No mutation exists (REQ-REG-017).

The one consumer the SRSs name is INT's Document upload screen requirement (SCR-REQ-INT-003, REQ-INT-016: the document type choices are the required document types of the Check's service, read with GET /api/v1/services/{serviceCode}). The block below names that consuming screen requirement — REG mints no screen — and binds the read it reuses to API-REG-002 (ADR-REG-012):

```yaml name=screen-hooks
screen: SCR-REQ-INT-003
hooks:
  - {hook: SERVICE-QUERY, kind: read, api: [API-REG-002], cache_key: "[reg-service, serviceCode]", errors: "REG-404-SERVICE-NOT-FOUND → user message, text: RULE-REG-016 message (SRS) · REG-500 → generic server error", loading: LOCAL, invalidation: "—"}
```

### SERVICES-QUERY — API-REG-001            traces=API-REG-001,REQ-REG-013
Kind         : read query — method, path and response schema are cited by API-REG-001 (an operation of api-spec-reg.yaml), never restated
Cache key    : ["reg-services"] — the operation takes no filter
Errors       : REG-500 → generic server error
Loading      : LOCAL
Cache policy : defaults
Invalidation : — (read only; the registry changes only at the next start of the service — ADR-REG-007)

### SERVICE-QUERY — API-REG-002             traces=API-REG-002,REQ-REG-014,REQ-REG-015
Kind         : read query — cited by API-REG-002 (an operation of api-spec-reg.yaml), never restated
Cache key    : ["reg-service", serviceCode] — the path parameter is the only input that changes the response
Errors       : REG-404-SERVICE-NOT-FOUND → user message (business rule, no field), text: RULE-REG-016 message (SRS) en "The service "{serviceCode}" is not available." · ar PENDING ADR-REG-011 · REG-500 → generic server error
Loading      : LOCAL
Cache policy : defaults
Invalidation : —
Consumer     : INT's Document upload (SCR-REQ-INT-003, REQ-INT-016)

### LOAD-REPORT-QUERY — API-REG-003         traces=API-REG-003,REQ-REG-008
Kind         : read query — cited by API-REG-003 (an operation of api-spec-reg.yaml), never restated
Cache key    : ["reg-load-results"] — the operation takes no filter
Errors       : REG-500 → generic server error
Loading      : LOCAL
Cache policy : defaults
Invalidation : —
Consumer     : no v1 screen (the Service Administrator reads the load report directly — no administration UI; ADR-REG-012)

No LOOKUP hook: REG's lookup values reach a frontend only inside these responses. No SCREEN-INIT and no FACADE: there is no REG screen to compose them.
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=REQ-REG-013,REQ-REG-014,AC-REG-014,AC-REG-015,API-REG-001,API-REG-002,API-REG-003 -->
## PHASE F3 — F3 — Forms & Validators

No screen SUB and no form: REG has no `SCR-*` and no write operation, so nothing is entered or submitted (REQ-REG-017, ADR-REG-012). The validators of this phase are the response validators of the three reads:

| Validator | Operation(s) | Behaviour |
|---|---|---|
| `ServiceSummary` schema (strict) | API-REG-001, API-REG-002 | built from the document's schema; a response carrying a property the document does not declare (e.g. `sqlText`, a connection setting) fails validation and is treated as a generic server error — REG responses never expose SQL or connection settings (REQ-REG-013, AC-REG-014) |
| `LoadResult` schema (strict) | API-REG-003 | built from the document's schema; same handling |
| `FetchMode` enum | API-REG-001, API-REG-002 | only path · blob · manual are accepted (AC-REG-015 reads `path`) |
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=REQ-REG-017,AC-REG-018 -->
## PHASE F4 — F4 — Screens & Routes

Empty: REG has no `SCR-*`, so it has no screen, no route, no guard, no facade, no composition and no save (SRS PART B not applicable; administration UI out of scope; the employee frontend's five screens are INT's — ADR-REG-012). No `UXD-*` is cited because none exists.

The frontend holds no affordance and sends no call that creates or changes a REG resource (a service package, a version or a connection): REG exposes read operations only (REQ-REG-017, AC-REG-018, ADR-REG-007).

**RF5 — Security (frontend half).** No permission model — screens open per the SRS (REQ-REG-013, REQ-REG-014, REQ-REG-008 name no role check; caller authentication deferred, raw idea A2).
<!-- PHASE:F4:END -->

<!-- PHASE:ALIGN-FE:START traces=REQ-REG-008,REQ-REG-013,REQ-REG-014,REQ-REG-015,REQ-REG-017,API-REG-001,API-REG-002,API-REG-003 -->
## PHASE ALIGN-FE — ALIGN-FE

```
ALIGN — REG v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block — examined nothing (0 SCR)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once — examined nothing (0 SCR)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it — examined nothing (0 SCR)
READS         ux-reads-spec   every read a screen binds is an operation of api-spec-reg.yaml, and the plan names the document its mock server serves — examined nothing (0 screen SUB)
UXD           orphans         every UXD is cited by a plan block — this is where a UX decision closes — examined nothing (0 UXD)
TRACES        traces          every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces          every API this plan cites is an operation of api-spec-reg.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface    every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree  every UXD and SCR defined here is in the stage registry, and nothing else is — examined nothing (0 SCR, 0 UXD)
LANGUAGES     languages       labels and messages in en + ar
MARKERS       markers         the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in analysis/decisions/REG/
COVERAGE      (the report)    examined nothing: C9.3, C9.4, C9.6, C9.7, C9.8, C9.15, C9.17, C9.22, C9.24 — as stamped by the orchestrator from the analyze report
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

### Operations coverage
| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| List services | API-REG-001 | none — no REG screen; read query SERVICES-QUERY for consuming INT screens | — | n/a — no REG screen (ADR-REG-012) |
| Read one service | API-REG-002 | none — no REG screen; read query SERVICE-QUERY reused by INT's SCR-REQ-INT-003 | — | n/a — no REG screen (ADR-REG-012) |
| Read the load report | API-REG-003 | none — no v1 consumer (no administration UI) | — | n/a — no REG screen (ADR-REG-012) |
<!-- PHASE:ALIGN-FE:END -->

## Hand-off

The implementer reads this plan in profile-phase order, `api-spec-reg.yaml` for shapes (served by its mock server until the backend is delivered), and adds no REG route, component, permission or field — none is traceable to an F-block. INT's frontend plan owns every screen and cites any REG field it renders as its own `UXD-*`.
