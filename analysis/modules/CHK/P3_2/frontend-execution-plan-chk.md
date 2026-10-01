# FRONTEND EXECUTION PLAN — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module : CHK   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod; lazy chunk per screen)
Inputs : srs-chk.md · prd-chk.md · api-spec-chk.yaml (OPENAPI 3.1.0, 1 operation) · registry-srs-chk.md · registry-exec-be-chk.md
Screens: 0 (SRS PART B not applicable) · UXD 0 · API bound 1 (API-CHK-001)   Open ADRs : 0 BLOCKED — ADR-CHK-019 (ACCEPTED)
══════════════════════════════════════════════════════════════════

## 3.0 Binding to the API document

This plan is bound to `api-spec-chk.yaml`, the document the backend plan derived (ADR-CHK-017). The
frontend executor's mock server serves that document. Shapes are read there by `x-api-id` and are not
restated here.

```yaml name=api-surface
mock: api-spec-chk.yaml
bindings:
  - {req: REQ-CHK-076, api: [API-CHK-001]}
  - {req: REQ-CHK-077, api: [API-CHK-001]}
  - {req: REQ-CHK-080, api: [API-CHK-001]}
unmapped: []
codes: []
```

Reconciliation against the SRS:
- Every operation maps to a REQ. API-CHK-001 maps to REQ-CHK-076, REQ-CHK-077 and REQ-CHK-080 (its `x-traces`).
- No REQ of CHK needs an HTTP operation it lacks. The SRS gives CHK no HTTP operation (ADR-CHK-013): start and
  upload confirmation are in-process operations behind INT's endpoints, and the pipeline has no caller. So
  `unmapped` is empty.
- `codes` is empty. The three error codes of API-CHK-001 (`CHK-400-CHECK-ID-INVALID`, `CHK-404-ACTIVE-CHECK-NOT-FOUND`,
  `CHK-500`) are PLATFORM-STD and carry no RULE (ADR-CHK-018). Their routing is in F2.
- Screens: none. CHK's SRS has no screen entry, so no phase below has a per-screen SUB. Each F-phase states only what a
  consumer of API-CHK-001 needs. The one `screen-hooks` block (F2) is keyed to the consumer screen entry of INT
  that the read is offered to, because the block's schema needs a screen id. INT's screens (SCR-REQ-INT-001 … SCR-REQ-INT-005) are the consumers that may use it
  (ADR-CHK-019).
- Security: no permission model — screens open per the SRS. Caller authentication is deferred (raw-idea A2),
  and the profile has no SEC-FE phase (REQ-CHK-076, REQ-CHK-077, REQ-CHK-080 name no role).

<!-- PHASE:F1:START traces=REQ-CHK-076,REQ-CHK-077,REQ-CHK-080,REQ-CHK-082,AC-CHK-079,AC-CHK-080,AC-CHK-085,API-CHK-001 -->
## PHASE F1 — F1 — Models & Types

No SUB: CHK has no `SCR-*` (ADR-CHK-019). The phase delivers the types of CHK's one consumable operation.

### RF1 — Models & types — API-CHK-001            traces=API-CHK-001,REQ-CHK-076,REQ-CHK-077,REQ-CHK-080,REQ-CHK-082
- Field/DTO binding: see `api-spec-chk.yaml`. The 200 response schema `ActiveCheckView` and the error schema
  `ProblemDetail` of API-CHK-001 are the source and are not restated here.
- `ActiveCheckView` carries the three fields of the Active Check and nothing of the request (REQ-CHK-082):
  checkId (label en "Check"), checkStatus (label en "Status"), deadlineAt (label en "Deadline"). ar labels are
  PENDING ADR-CHK-018.
- checkStatus is a closed union of the two unfinished CHECK_STATUS codes, AWAITING_DOCUMENTS and RUNNING
  (RULE-CHK-009). Display labels en: "Awaiting documents" · "Running". ar: PENDING ADR-CHK-018. COMPLETED and
  FAILED are not representable: an ended Check has no Active Check (REQ-CHK-078).
- deadlineAt is an ISO-8601 date-time string, kept as received. It means the end of the upload window while
  AWAITING_DOCUMENTS (REQ-CHK-076) and the end of the Check timeout while RUNNING (REQ-CHK-077).
- checkId is an int64 number, passed as received from the consumer's own state (a Check identifier from INT's
  read). No CHK type is created for the Check run, report or findings: those are RPT's, read through INT.
- Lookups: none fetched. CHECK_STATUS is a closed enum of CHK, and its two values are part of the type.
<!-- PHASE:F1:END -->

<!-- PHASE:F2:START traces=REQ-CHK-076,REQ-CHK-077,REQ-CHK-078,REQ-CHK-080,AC-CHK-079,AC-CHK-080,AC-CHK-081,AC-CHK-083,API-CHK-001 -->
## PHASE F2 — F2 — Data Hooks

No SUB: there is no CHK screen (ADR-CHK-019). The phase declares the ONE read a consumer needs. No
mutation, lookup, screen-init or facade hook exists for CHK, because those belong to a screen. The
`screen-hooks` block below is keyed to the consumer the read is offered to: INT's Check report screen entry,
which follows a Check until it ends (SCR-REQ-INT-002). It is not a CHK screen and not a SUB of this plan, and
it does not decide INT's binding. Whether that screen calls the hook is stated in INT's plan.

```yaml name=screen-hooks
screen: SCR-REQ-INT-002
hooks:
  - {hook: ACTIVE-CHECK-QUERY, kind: read, api: [API-CHK-001], cache_key: "[active-check, checkId]", errors: "CHK-404-ACTIVE-CHECK-NOT-FOUND → state ended, no refusal text · CHK-400-CHECK-ID-INVALID → generic (prevented by the F3 guard) · CHK-500 → generic", loading: LOCAL, invalidation: "—"}
```

### ACTIVE-CHECK-QUERY — API-CHK-001            traces=API-CHK-001,REQ-CHK-076,REQ-CHK-077,REQ-CHK-078,REQ-CHK-080
Kind         : read query. The method, path and request/response schema are cited by API-CHK-001 (an operation of api-spec-chk.yaml) and are not restated
Cache key    : ["active-check", checkId]. checkId is the only input that changes the response (no filter, no paging)
Errors       : CHK-404-ACTIVE-CHECK-NOT-FOUND → not an error for the consumer. The Check is no longer unfinished (ended COMPLETED or FAILED, or unknown — REQ-CHK-078), so the hook reports the state `ended`, renders no refusal text, and the consumer reads the ended Check from its own Check read · CHK-400-CHECK-ID-INVALID → generic error (never sent: the F3 checkId guard runs before the call) · CHK-500 → generic error · network failure → generic error
Loading      : LOCAL
Cache policy : defaults. The refetch interval is the consuming screen's choice: CHK states none, and the SRS states no polling period for the Active Check (a deviation needs an ADR)
Invalidation : — (read only). The key `["active-check", checkId]` is the one a consumer's confirm-uploads mutation refreshes on success (REQ-CHK-077). That mutation is INT's to declare
Returns      : `{state: "unfinished", view: ActiveCheckView} | {state: "ended"} | {state: "error"}`. A view's checkStatus is AWAITING_DOCUMENTS or RUNNING, and its deadlineAt is the stored deadline (REQ-CHK-076, REQ-CHK-077, REQ-CHK-080)

Rules of use: components use this hook only through the facade of the screen that renders it (INT's). The
hook calls API-CHK-001 only (server-state library `tanstack-query`). A deadline in the past is still
shown as received: the Check is ended by the server's deadline check (REQ-CHK-080), and the next read
answers `ended`. The frontend never computes the ending itself.
<!-- PHASE:F2:END -->

<!-- PHASE:F3:START traces=REQ-CHK-076,REQ-CHK-077,REQ-CHK-082,AC-CHK-085,API-CHK-001 -->
## PHASE F3 — F3 — Forms & Validators

No SUB and no form. CHK takes no input from the employee through the frontend: starting a Check and
confirming uploads are INT's screens and endpoints (ADR-CHK-013, ADR-CHK-017). The phase delivers only the
two runtime validators the F2 read uses (validation library `zod`):

### ACTIVE-CHECK-VIEW-SCHEMA — API-CHK-001          traces=API-CHK-001,REQ-CHK-076,REQ-CHK-077,REQ-CHK-082
- Parses the 200 body of API-CHK-001 as exactly the `ActiveCheckView` of api-spec-chk.yaml. checkId is an
  integer, checkStatus one of AWAITING_DOCUMENTS · RUNNING (RULE-CHK-009), and deadlineAt an ISO-8601 date-time.
  A body that does not parse is treated as the generic error, never shown half-read.
- No field beyond the three is accepted into the view (REQ-CHK-082: the Active Check holds no request data).

### CHECK-ID-GUARD — API-CHK-001                    traces=API-CHK-001,REQ-CHK-076
- The checkId path parameter is sent only when it is a positive integer within int64 (the parameter schema of
  API-CHK-001). Otherwise the read is not issued and the hook reports the generic error. This is why
  CHK-400-CHECK-ID-INVALID is never expected from a well-formed consumer.
<!-- PHASE:F3:END -->

<!-- PHASE:F4:START traces=API-CHK-001,REQ-CHK-076,REQ-CHK-077,REQ-CHK-080 -->
## PHASE F4 — F4 — Screens & Routes

Empty: CHK has no screen, so this phase holds no SUB, route, lazy chunk or component (SRS PART B not
applicable; ADR-CHK-019). The surface of F1–F3 (API-CHK-001: status and deadline of an unfinished Check) is
rendered, if at all, by a screen of INT, which owns the five screens of the employee frontend. That screen's
guard, facade, composition and single save are specified in INT's plan, and so is any UXD for the
Active Check.
<!-- PHASE:F4:END -->

<!-- PHASE:ALIGN-FE:START traces=API-CHK-001,REQ-CHK-076,REQ-CHK-077,REQ-CHK-080 -->
## PHASE ALIGN-FE — ALIGN-FE

```
ALIGN — CHK v1
row           backing check       assertion
SCREENS       orphans             every SCR is referenced by a plan block — examined nothing (0 SCR)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once — examined nothing (0 SCR)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it — examined nothing (0 SCR)
READS         ux-reads-spec       every read a screen binds is an operation of api-spec-chk.yaml, and the plan names the document its mock server serves
UXD           orphans             every UXD is cited by a plan block — examined nothing (0 UXD)
TRACES        traces              every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces              every API this plan cites is an operation of api-spec-chk.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface        every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree      every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages           labels and messages in en + ar
MARKERS       markers             the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist          every ADR this plan cites exists on disk in analysis/decisions/CHK/
COVERAGE      (the report)        as stamped by the orchestrator from the analyze report
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
| Read the Active Check of a Check | API-CHK-001 | none in CHK — read by the F2 hook for a screen of INT | — (no CHK route) | ✗ by the template's rule (empty route); accepted by ADR-CHK-019, because CHK has no screen |

R5 — Security (frontend half): no permission model — screens open per the SRS (REQ-CHK-076, REQ-CHK-077,
REQ-CHK-080; caller authentication deferred, raw-idea A2).
<!-- PHASE:ALIGN-FE:END -->

## Hand-off

The plan and registry are split into `frontend-execution/` after the P4 verdict. The implementer builds F1–F3
(types, one read hook, two validators) against `api-spec-chk.yaml` served by its mock server. It builds no
route, screen or component for CHK (F4 is empty). Using the read on INT's screens is a decision of
INT's plan (ADR-CHK-019).
