# PASS 2 — module RPT v1 — bundled session (2 stages, one commit per stage)

- `P3.2` Frontend — UX Design + Execution Plan — questions forbidden
- `P4` Test Plan — questions forbidden

==============================================================================
# BRIEF — stage `P3.2` (Frontend — UX Design + Execution Plan) · module RPT · v1 · profile `aias`

Lane `analysis` · implementer ['claude:opus'] · effort high · round 1

## Rules that bind this run
- Questions: **forbidden**. A `[QUESTION]` block is refused. Ambiguity → ADR in `analysis/decisions/RPT/` (`ADR-{MOD}-{seq:03d}.md`): non-breaking → continue; breaking → status BLOCKED and stop.
- Owns IDs: UXD, SCR — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-RPT-014`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared/analysis/modules/RPT/P3_2/flow-diagram-rpt.md`
- `governance-shared/analysis/modules/RPT/P3_2/ui-ux-spec-rpt.md`
- `governance-shared/analysis/modules/RPT/P3_2/frontend-execution-plan-rpt.md`
- `governance-shared/analysis/modules/RPT/P3_2/registry-exec-fe-rpt.md` (registry)
- Write the files directly into the project checkout (you are the operator); the orchestrator reads them on `--complete`.

## Contracts checked by `gov.py analyze` after this stage
- **C8** API document (the backend plan's endpoints) → frontend: C8.1 exists {'artifact': 'api-spec'} [CRITICAL]
- **C9** frontend design + execution plan → split: C9.1 markers {'artifact': 'frontend-execution-plan', 'track': 'frontend', 'plan': 'exec'} [CRITICAL]; C9.2 traces {'from': 'frontend-execution-plan', 'blocks': ['PHASE', 'SUB'], 'min': 1} [MAJOR]; C9.3 traces {'from': 'UXD', 'to': ['REQ', 'AC'], 'min': 1} [MAJOR]; C9.4 traces {'from': 'SCR', 'to': ['REQ', 'UXD'], 'min': 1, 'mode': 'any'} [MAJOR]; C9.5 traces {'from': 'frontend-execution-plan', 'to': ['API'], 'defined_in': 'api-spec'} [CRITICAL]; C9.6 orphans {'kind': 'UXD', 'referenced_by': ['frontend-execution-plan'], 'min': 1} [MAJOR]; C9.7 orphans {'kind': 'SCR', 'referenced_by': ['frontend-execution-plan'], 'min': 1} [MAJOR]; C9.8 registry-agree {'artifact': ['ui-ux-spec', 'frontend-execution-plan'], 'registry': 'registry-exec-fe', 'kinds': ['UXD', 'SCR']} [MAJOR]; C9.9 ids-owned {'stage': 'P3.2'} [CRITICAL]; C9.10 no-questions {'stage': 'P3.2'} [CRITICAL]; C9.11 ids-continue {'stage': 'P3.2'} [CRITICAL]; C9.13 xref-surface {'artifact': ['frontend-execution-plan'], 'locator': 'stack.backend.api.base_path', 'kinds': ['API']} [MAJOR]; C9.14 languages {'stage': 'P3.2'} [MAJOR]; C9.15 screen-states {'artifact': 'ui-ux-spec', 'kind': 'SCR', 'spec': 'analyze.maturity.screen_states'} [MINOR]; C9.17 screen-composition {'artifact': 'ui-ux-spec', 'kind': 'SCR', 'spec': 'analyze.maturity.screen_composition', 'when': 'profile.conventions.screen_composition'} [MINOR]; C9.16 glossary {'artifact': ['flow-diagram', 'ui-ux-spec'], 'glossary': 'vocabulary.glossary', 'synonyms': 'vocabulary.glossary_synonyms'} [MINOR]; C9.24 ux-reads-spec {'plan': 'frontend-execution-plan', 'spec': 'api-spec', 'reads': 'analyze.plan_reads', 'kind': 'API', 'screen': 'SCR'} [CRITICAL]; C9.22 composition-rule {'artifact': 'ui-ux-spec', 'plan': 'frontend-execution-plan', 'kind': 'SCR', 'spec': 'analyze.maturity.screen_composition', 'reads': 'analyze.plan_reads', 'when': 'profile.conventions.screen_composition'} [MAJOR]; C9.23 publication-fresh {'when': 'factory.publications'} [MAJOR]; C9.12 verdict-agrees {'artifact': ['frontend-execution-plan'], 'spec': 'self_check', 'when': 'profile.self_check'} [CRITICAL]

## Blocks this stage emits — each a ```yaml name={name} fence, held to its schema by C0.1 (`gov.py analyze`)
- `self-check` in `backend-execution-plan`, `frontend-execution-plan` (optional) — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"self-check","description":"an execution plan's verdict about itself — written by the orchestrator from the analyze report, never by the author (analyze: verdict-agrees)","type":"object","required":["findings","clean"],"additionalProperties":false,"properties":{"findings":{"type":"integer","minimum":0},"clean":{"type":"boolean"},"examined_nothing":{"type":"array","items":{"type":"string"}}}}
  ```
- `api-surface` in `frontend-execution-plan` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"api-surface","description":"P3.2 frontend plan §3.0: the API document the plan is bound to and a mock server serves (`mock`), the REQ → API bindings, the operations mapped to nothing, and the runtime code → RULE links","type":"object","required":["mock"],"additionalProperties":false,"properties":{"mock":{"type":"string","minLength":1},"bindings":{"type":"array","items":{"type":"object","required":["req","api"],"additionalProperties":false,"properties":{"req":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"api":{"type":"array","items":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}}}}},"unmapped":{"type":"array","items":{"type":"string"}},"codes":{"type":"array","items":{"type":"object","required":["code","rule"],"additionalProperties":false,"properties":{"code":{"type":"string"},"rule":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}}}}}}
  ```
- `screen-hooks` in `frontend-execution-plan` (one per screen) — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"screen-hooks","description":"P3.2 frontend plan, one per screen SUB of the data phase: what the screen reads and mutates, each hook bound to the API operation(s) it calls (analyze: ux-reads-spec)","type":"object","required":["screen","hooks"],"additionalProperties":false,"properties":{"screen":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"hooks":{"type":"array","items":{"type":"object","required":["hook","kind"],"additionalProperties":false,"properties":{"hook":{"type":"string","minLength":1},"kind":{"type":"string","enum":["read","mutation","lookup","init","facade"]},"api":{"type":"array","items":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}},"cache_key":{"type":"string"},"errors":{"type":"string"},"loading":{"type":"string"},"invalidation":{"type":"string"}}}}}}
  ```

---
# ENGINE
```
ENGINE        : P3.2 — Frontend — UX Design + Execution Plan
PASS / TRACK  : pass 2 · track frontend · lane analysis · questions forbidden
MODULE        : RPT · v1 · profile aias (Request Verification Service)
READS         : srs · prd · api-spec · registry-srs · registry-exec-be
                (_state/ for current state — `_state/current-api-spec.yaml` is the API surface: the backend PLAN's document, not a delivered backend)
PRODUCES      : flow-diagram-rpt.md · ui-ux-spec-rpt.md · frontend-execution-plan-rpt.md · registry-exec-fe-rpt.md   — ONE run, ONE input set
OWNS IDS      : UXD, SCR
NEXT          : P4   (the orchestrator owns the completion protocol — shared/GOVERNANCE-CORE.md)
BOUNDARY      : analysis-only — design artifacts and specifications, not a build [G]
```

# Frontend — UX Design + Execution Plan — engine reference

## 0. Position and authority

One engine, one run, two internal parts that share the same input set:

- **Part A — UX design** (§2): from the SRS (functional ceiling) and the PRD (priority and
  intent) it produces the flow diagram and the ui-ux-spec, minting `SCR-*` and `UXD-*`.
- **Part B — frontend execution plan** (§3): bound to the API surface in `_state/current-api-spec.yaml` —
  the OPENAPI 3.1.0 document the backend stage DERIVED from its plan
  (`factory.api_spec`, proved against the plan by C7.29–C7.31). No backend is built, delivered
  or fetched before this stage runs: the frontend executor serves the same document from a
  mock server, and `api-verify` later holds what the backend publishes to it.

Authority order: SRS `REQ/AC` are the functional ceiling; the API document is the only source [C:C9.5]
for endpoint shape; the PRD informs sequencing and priority; Part A's spec is strong design
intent for Part B — not a licence to add a field, rule or permission the SRS does not [G]
have. A conflict is a finding (ADR — §7), not a silent resolution. The backend execution [G]
plan's prose is **never** read as an API source (it may be opened for `DBF`/catalog code [C:C9.5]
lookup only) — the document is the one statement of an endpoint's shape. [C:C9.5]

Questions are `forbidden`. Ambiguity → `factory.yaml → ambiguity` (§7). No human
approval sits inside this engine: the human decision is the `P4` gate.

**Delta versions** (v2+) emit only ADDED / MODIFIED / REMOVED blocks + [C:C12.2]
`change-manifest.md` against `_state/`; `SCR/UXD` sequences continue,
never renumbered. Rules: shared/VERSIONING.md. [C:C9.11]

## 1. Inputs and entry check

| Input | Read from | Use |
|---|---|---|
| `srs` | `_state/current-srs.md` | REQ/AC (EARS + Given/When/Then), ENT, RULE with messages, screen entries, permission matrix, lookup keys |
| `prd` | `_state/current-prd.md` | US-* priority, intent, navigation expectations |
| `api-spec` | `_state/current-api-spec.yaml` | every operation by `x-api-id`: method, path, parameters, request/response schemas, error responses with their catalog codes (`x-error-codes`), paging (`x-paginated`), the security scheme |
| `registry-srs` | `_state/current-registry-srs.md` | ID ranges, screen entries, shared entities |
| `registry-exec-be` | `_state/current-registry-exec-be.md` | API-* ranges, catalog codes, XM status, permission names declared by the backend plan |

Entry: the orchestrator refuses the stage while `api-spec` is absent (C8.1) — this [T:inputs-missing]
engine does not re-gate. It does run one **reconciliation of the API document against the
SRS** (§3.0) before any F-content.

## 2. Part A — UX design (flow diagram + ui-ux-spec)

### A.0 Role

Translate the approved functional truth into navigation and component **intent**. Never
invent scope, business rules or permissions; do not fix an SRS↔PRD contradiction by choosing [G]
an interpretation (§7 decides). Final component names, code and routing are Part B's.

### A.1 Screens — `SCR-*`

Mint one `SCR-*` per screen the SRS declares (`SCR-RPT-{seq}`,
traces → REQ + UXD).
`profile.conventions.composite_screen` is not set: each SRS screen entry is one `SCR-*`.

### A.2 Flow diagram — `flow-diagram-rpt.md`

One flow block per navigation path (identified by its starting `SCR-*` + a short name; flows
carry no atom of their own):
```
FLOW — <name>                                   traces=US-RPT-<seq>,REQ-RPT-<seq>,SCR-RPT-<seq>
Screens   : SCR-RPT-<seq> [, SCR-RPT-<seq> …]
Sequence  : <entry> → <screen A> → <screen B> → <exit>
Trigger   : <what gets the user here>
Priority  : <from the PRD, if stated>
```
Every flow cites a `US-*` **and** an `SCR-*`. A flow with no SRS-backed screen is inventing
navigation → ADR, not silently included.

### A.3 UI/UX spec — `ui-ux-spec-rpt.md`

One block per `SCR-*`, fields and permissions copied from the SRS (no additions, no omissions):
```
## SCR-RPT-<seq> — <name>                 traces=REQ-RPT-<seq>,AC-RPT-<seq>[,UXD-RPT-<seq>]
UI pattern        : <from the SRS screen entry — do not change>
Sub-views         : as the SRS declares
Fields shown      : <every SRS field of the owning ENT the screen RENDERS — label per language (en/ar), read-only flags; an id carried only to navigate to another screen goes on the Navigation line, not here> [G]
Composition       : container <page | drawer> · <part> → <none | inline | summary row + second level> · submits: one
 [G]Permissions       : <SRS matrix rows for this screen — reference only>
Cross-module data : <field → UXD-RPT-<seq> (owner module)> | none
Navigation        : <routes; every jump to or from another SCR with the exact filter it seeds> | none
States            : empty · loading · error (generic — catalog codes are Part B's) · offline (if the SRS says so)
```

### A.4 One screen, one job, one submit — the `Composition` line

`profile.conventions.screen_composition` is set, so every `SCR-*` block commits to a placement.
A screen opened to create or edit ONE record has ONE save. Secondary detail — an entity picker,
a repeating child-row editor, a permissions matrix, an attachment list, a tag selector — is a
SECOND job, and this line says where it goes:

- `none` — the screen has no secondary detail (a report, a search, a confirmation). Still an answer.
- `inline` — only when it is SHORTER than the primary field group and adds no save of its own. [G]
- `summary row + second level` — otherwise. The record's surface keeps a label, an edit trigger and
  a capped preview; the picking happens in a second-level view opened from that row. Four
  properties that view must have, stated here because each one failed in the field:
  1. it RETURNS A VALUE and does not save — confirm feeds the screen's own form state, cancel [G]
     discards a local draft, and the ONE save stays where the screen's primary action already is;
  2. its open state lives where the rest of the application's navigation state lives, so going back
     closes it, dismissing closes only it, and a deep link opens it; [G]
  3. it carries no scroll region of its own — the list scrolls with the body it sits in;
  4. it renders as a SIBLING of the first level, not inside its element. [G]

A screen whose parts differ names more than one: a full-page form can hold its child collection
`inline` and still open a `second level` for a picker over an unbounded set. Name each part.

**The container decides the collection's place.** The line opens with `container <kind>`
— where the record is created and edited — and every part that is a child collection
takes the place that container prescribes:
- `page` → the collection is `inline`: a record page keeps its header inline and its child collection in a pane beneath it; one child line is added in a one-line drawer. A screen whose routes carry `/new` or `/:` is a `page`.
- `drawer` → the collection is `summary row`: an entry drawer shows the collection as a summary row and edits it in a second-level drawer that returns the set.
A journal entry edited on a route page was once given the drawer shape (summary row + lines drawer);
the implementer kept the project's page rule and recorded the contradiction. A departure from the
container's shape is an ADR, never a silent choice; `analyze` (composition-rule) refuses the mismatch. [C:C9.22]

Never two saves on one screen; never a height-capped scrolling region inside a surface that already [C:C9.17]
scrolls; no inline control taller than the fields beside it. These three are one defect wearing [G]
three faces, and the third is the one a user loses work to: an administrator set a subject's
assignments in an inline picker that carried its own save, pressed the screen's Save, and left
believing both had been saved.

Collapsing two saves into one can leave a single action owning TWO calls (update the record, then
replace its child set). Part B (RF2) declares them ORDERED — the second sent only after the first [G]
succeeded, so a rejected update cannot leave the record carrying children not saved with it — and [G]
declares that the second is not sent at all when the child set is unchanged.


### A.5 Cross-module display dependencies — `UXD-*`

`UXD-*` (`UXD-RPT-{seq}`, traces → REQ + AC)
names an application-layer need: a screen owned by **this** module displays data whose
authoritative source is another module's real API. It is minted the moment such a field is
drafted, keyed by the module owning the **screen**, recorded in the spec block and in the
registry. It is not a DB constraint, shares nothing with `XM-*`, and never appears in [C:C7.7]
backend artifacts.

Lifecycle: minted here → cited (never reassigned) by the F-blocks of Part B → verified at the [C:C9.9]
`P4` gate by `gov.py analyze`: every `UXD-*` must be referenced by an F-block and
every referenced one must exist (unreferenced or dangling = MAJOR).

### A.6 Reconciliation self-check (SRS B1–B4 ↔ draft) — no human gate

Run before Part B, on the whole draft:
```
RECONCILIATION — RPT v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → none: ADR (no invented screen), flow excluded
B2 no RULE-* contradicts a flow/spec outcome                           → contradiction: both texts verbatim in an ADR (breaking → BLOCKED)
B3 every field/permission on a screen exists in the SRS               → extra: removed; missing: added
B4 every screen entry of the SRS has exactly one SCR-* block          → gap: block added [G]
RESULT  reconciled <n> · reworked <n> (bounded to flagged blocks) · ADRs <list>
```

## 3. Part B — frontend execution plan

### 3.0 Binding to the API document

Before writing any phase, bind this plan to `_state/current-api-spec.yaml`. Record only what exists in [G]
neither source alone — the shapes stay in the document, cited by `API-*` id (its
`x-api-id`), not restated: [G]
ONE block (data, not prose — `gov.py analyze` → `ux-reads-spec` reads `mock`; C0.1 holds it to its schema):
```yaml name=api-surface
mock: api-spec-rpt.yaml                         # the document the frontend executor's mock server serves (C9.24)
bindings:                                       # the binding only; method, path, request/response schema, paging and envelope are read in the document, not copied here [G]
  - {req: REQ-RPT-<seq>, api: [API-RPT-<seq>]}
unmapped: []                                    # a REQ needing an operation that has none · an operation mapping to no REQ → ADR
codes:                                          # runtime error code (x-error-codes) → RULE — the link neither the document nor the SRS carries
  - {code: "<code>", rule: RULE-RPT-<seq>}
```
Reconcile once against the SRS: every REQ that needs an operation has one (missing/renamed
→ ADR — naming diffs continue, a missing core operation is breaking); every operation maps
to a REQ (unknown → ADR, not silently used). A value not in the document is not [G]
invented — mark `PENDING ADR-<id>`: the gap belongs to the backend plan the document was
derived from, and the ADR names it.

### 3.1 Markers, thresholds, traces

Grammar: `factory.markers` (schema v2, syntax `html-comment`) —
`<!-- KIND:ID:START [traces=…] -->` … `<!-- KIND:ID:END -->`. Kinds allowed in a `frontend` execution plan:

| Kind | Level | Allowed parents | Notes |
|---|---|---|---|
| `PHASE` | 1 | — (top level) [T:marker-foreign-kind] | keys from `profile.tracks.<track>.plans.<plan>.phases` |
| `SUB` | 2 | PHASE [T:marker-foreign-kind] | id = `{PHASE-KEY}-{SCR-ID}` — always phase-qualified |

- No atom kind is carried by this track: `API-*` and `XM-*` are backend-owned and only cited. [C:C9.9]
  The unit of addressing here is the **SUB per screen** in `sub_bearing` phases.
- `traces=` on **every** PHASE and SUB block: the
  `REQ/AC/API/UXD/SCR` IDs the block implements (grammar `{prefix}-{MOD}-{seq}`,
  3-digit seq). A PHASE traces to the union of its SUBs.
- The same screen legitimately appears under several phases; without the `{PHASE-KEY}-`
  prefix the SUB ids would collide — the prefix is mandatory, always. [T:sub-unqualified]
- First line of a phase = its START marker; last = END. Threshold checked **while** writing.
  Unknown key → the toolkit refuses (`refuse`). [T:phase-unknown]
- Headings with the word PHASE use a profile key only; index, ALIGN table (unless a phase), [T:phase-unknown]
  registry and hand-off are trailing content after the last END. Protocol: shared/MARKER-PROTOCOL.md.

Phase table for `profile.tracks.frontend.plans.exec` (plan order):

| # | Key | Display | Split rule | Per-screen SUB |
|---|---|---|---|---|
| 1 | `F1` | F1 — Models & Types [T:never-split] | always — one SUB per screen | yes — `SUB:F1-SCR-RPT-<seq>` |
| 2 | `F2` | F2 — Data Hooks [T:never-split] | always — one SUB per screen | yes — `SUB:F2-SCR-RPT-<seq>` |
| 3 | `F3` | F3 — Forms & Validators [T:never-split] | always — one SUB per screen | yes — `SUB:F3-SCR-RPT-<seq>` |
| 4 | `F4` | F4 — Screens & Routes [T:never-split] | always — one SUB per screen | yes — `SUB:F4-SCR-RPT-<seq>` |
| 5 | `ALIGN-FE` | ALIGN-FE [T:never-split] | never split | no |


### 3.2 Content roles

The profile names the phases; the engine supplies content **by role**, matched on the words
in the phase display ("Models & Types", "Data Hooks", "Screens & Routes",
security, alignment). A phase matching no role is filled as the profile describes it. Stack
facts come from `profile.stack.frontend`: framework `react-ts-vite`; libraries — routing: `react-router`, server-state: `tanstack-query`, forms: `react-hook-form`, validation: `zod`; lazy chunk per `screen`.

**RF1 — Models & types.**
Field/DTO binding : see `_state/current-api-spec.yaml` — the request/response schemas of this module's
                     operations are the source, not restated here.

**RF2 — Data hooks.** Declares WHAT each screen needs from the API — not hook code. One `screen-hooks`
block per screen SUB (data, not prose — `gov.py analyze` → `ux-reads-spec` reads it; C0.1 holds it to its schema);
a hook whose `kind` is `read` binds an operation of the document:
```yaml name=screen-hooks
screen: SCR-RPT-<seq>
hooks:
  - {hook: <role>-QUERY, kind: read, api: [API-RPT-<seq>], cache_key: "[resource, filters]", errors: "<code → routing>", loading: NONE, invalidation: "—"}
  - {hook: <role>-SAVE, kind: mutation, api: [API-RPT-<seq>], invalidation: "<keys refreshed on success>"}
```
The prose beside each block explains the row roles:
```
### <role>-QUERY — API-RPT-<seq>            traces=API-…,REQ-…
Kind (read query | mutation) — method, path and request/response schema are cited by the API-* id above (an operation of api-spec-rpt.yaml — C9.24), never restated [C:C9.24]
Cache key    : [resource, filters] — every filter that changes the response is in the key
Errors       : catalog code → routing (field validation → inline · business rule → user message · unauthenticated → login · forbidden → unauthorized · server → generic) [G]
Loading      : NONE | LOCAL | GLOBAL (GLOBAL only when the SRS says the call is slow → ADR) [G]
Cache policy : defaults | <stale/gc values> (deviation → ADR)
Invalidation : keys refreshed on success (mutations declare this) [G]
### <role>-LOOKUP — <lookup key>     endpoint · key · options shape (code + label per language) · ONE hook per key, shared across screens · long-lived cache
### <role>-SCREEN-INIT — SCR-RPT-<seq>   permission read for the screen · lookups used · entity-by-id when editing
### <role>-FACADE — SCR-RPT-<seq>         composes the queries above · state it owns (list from query data, selection, filters incl. page/size, derived loading) · imperative operations (create/update/deactivate with usage check first)
```
**What a screen must be able to READ.** Every read row binds an operation of the document
(`ux-reads-spec`): a control that chooses a foreign record has the read that feeds it in the same
table, a foreign id the screen renders has the read that labels it, a child option list names the
parent it is scoped by, a cross-screen jump seeds a filter that identifies ONE record. None of
these four is checked mechanically any more (ADR-FACTORY-003) — state them in the table, and the
gate's adversarial reading looks for them.
5. **A routed refusal carries its words** (msg-bound). Every server code routed to a control in the
   `Errors` column is bound to its text by reference, in every language the catalogue carries:
   `CODE → inline on <field>, text: <RULE-RPT-<seq> message (SRS) | catalogue CODE>`. Routing
   without the text leaves each implementer to write it — and a test quoting the SRS to disagree.

State rule: page and page size live **inside** the filter object that forms the cache key —
not as independent state. Components use the facade only; the facade uses the declared [G]
queries only (server-state library: `tanstack-query`). [G]

**RF4 — Screens & routes.** One block per `SCR-*`:
```
### <role>-SCREEN — SCR-RPT-<seq>            traces=REQ-…,UXD-…,API-…
Guard        : every route element guarded by its permission [G]
Facade       : the RF2 facade of this screen · pages do not call queries directly [G]
Cross-module : UXD-* cited for every foreign-data field (missing → ADR, never minted here) [C:C9.9]
Composition  : the spec's `Composition` line resolved to components — a `second level` is its own
               component, a SIBLING of the first, opened from navigation state and not from a [G]
               boolean the screen holds; its value returns to the screen's form state
Saves        : ONE. When it owns two calls, they are ordered and the second is skipped unchanged
```

**RF5 — Security (frontend half).** [G] No security model in the profile: write "no permission model — screens open per the SRS" and cite the REQs.

**RF6 — Alignment.** The ALIGN table (§4) as the alignment-role phase content (never [T:never-split]
split); trailing content if the profile has no such phase.

### 3.3 Phase-by-phase

#### PHASE 1 — `F1` (F1 — Models & Types)
- `<!-- PHASE:F1:START traces=… -->` … `<!-- PHASE:F1:END -->`; content = the roles whose words appear in "F1 — Models & Types", else as the profile describes.
- [C:C9.7] Per-screen SUB: `<!-- SUB:F1-SCR-RPT-<seq>:START traces=… -->` for **every** `SCR-*`.

#### PHASE 2 — `F2` (F2 — Data Hooks)
- `<!-- PHASE:F2:START traces=… -->` … `<!-- PHASE:F2:END -->`; content = the roles whose words appear in "F2 — Data Hooks", else as the profile describes.
- [C:C9.7] Per-screen SUB: `<!-- SUB:F2-SCR-RPT-<seq>:START traces=… -->` for **every** `SCR-*`.

#### PHASE 3 — `F3` (F3 — Forms & Validators)
- `<!-- PHASE:F3:START traces=… -->` … `<!-- PHASE:F3:END -->`; content = the roles whose words appear in "F3 — Forms & Validators", else as the profile describes.
- [C:C9.7] Per-screen SUB: `<!-- SUB:F3-SCR-RPT-<seq>:START traces=… -->` for **every** `SCR-*`.

#### PHASE 4 — `F4` (F4 — Screens & Routes)
- `<!-- PHASE:F4:START traces=… -->` … `<!-- PHASE:F4:END -->`; content = the roles whose words appear in "F4 — Screens & Routes", else as the profile describes.
- [C:C9.7] Per-screen SUB: `<!-- SUB:F4-SCR-RPT-<seq>:START traces=… -->` for **every** `SCR-*`.

#### PHASE 5 — `ALIGN-FE` (ALIGN-FE)
- `<!-- PHASE:ALIGN-FE:START traces=… -->` … `<!-- PHASE:ALIGN-FE:END -->`; content = the roles whose words appear in "ALIGN-FE", else as the profile describes.
- [C:C9.7] Never split — level-1 only.

### 3.4 Mutual consistency rule

Every `SCR-*` in the ui-ux-spec has an F-block in **each** `sub_bearing` phase
(`F1`, `F2`, `F3`, `F4`) and every F-block names an
`SCR-*` that exists in the spec; every `UXD-*` in the spec is cited by an F-block. `gov.py
analyze` checks this at the gate — a mismatch is MAJOR.

## 4. ALIGN self-check

Against the plan itself, the API document and the SRS ceiling (cross-artifact = `gov.py analyze`).

**Every row names the check that backs it, and there are no other rows.** The block used to
assert screen coverage, validation, routing and security in prose no check could falsify, and a
sibling plan shipped four such rows false under a verdict that read clean. A row
nothing can falsify manufactures confidence and is worse than no row, so every unbacked row was
**deleted** rather than softened — if a dimension matters and no check covers it, the fix is a
clause in `shared/ARTIFACT-CONTRACTS.md`, not a sentence here. Each mark is the analyze report's
result for that check, copied; a clause the report says examined nothing is written
`— examined nothing`, never ✓. [C:C9.12]

```
ALIGN — RPT v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it
READS         ux-reads-spec   every read a screen binds is an operation of api-spec-rpt.yaml, and the plan names the document its mock server serves
UXD           orphans         every UXD is cited by a plan block — this is where a UX decision closes
TRACES        traces          every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces          every API this plan cites is an operation of api-spec-rpt.yaml — never a line of the backend plan's prose [C:C9.5]
FOREIGN       xref-surface    every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree  every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages       labels and messages in en + ar
MARKERS       markers         the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in analysis/decisions/RPT/
COVERAGE      (the report)    the clauses the analyze report lists as having examined nothing — verbatim, or `none`
```
```yaml name=self-check
findings: 0          # written by the orchestrator from the analyze report — leave it alone
clean: true
```
Operations coverage table (operation │ API │ SCR action │ route │ status) closes the section —
a row with an empty route is a ✗.

## 5. Registry update — `registry-exec-fe-rpt.md`

```
REGISTRY — P3.2 — RPT v1
ID RANGES     UXD-RPT-<first>..<last> · SCR-RPT-<first>..<last>
SCREENS       SCR │ name │ owning ENT │ permissions
UXD INDEX     UXD │ screen │ field │ owner module · API used
API COVERAGE  documented endpoints used / unused (with ADR)
ALIGN      verdict as stamped · findings fixed
ADRs          decisions/RPT/ADR-RPT-<seq> … (status)
TRACEABILITY  REQ covered by ≥1 SCR/F-block: <n>/<total> · orphan REQ: <list — a gate blocker>
```

## 6. Structural self-check (toolkit)

```
[ ] every profile key has exactly one PHASE START/END pair, in profile order [T:marker-duplicate]
[ ] every SUB id is {PHASE-KEY}-SCR-…; the same screen under different phases carries different prefixes
[ ] every PHASE/SUB carries traces=
[ ] no heading repeats; trailing content sits after the last PHASE END
[ ] §3.4 mutual consistency holds
```
Then (non-zero exit is blocking):
```
gov.py split --track frontend --module RPT --version 1 --dry-run
```
`gov.py analyze` runs before `P4`; CRITICAL keeps the gate closed.

## 7. Ambiguity rule

`factory.yaml → ambiguity` (shared/GOVERNANCE-CORE.md): non-breaking → ADR
`analysis/decisions/RPT/ADR-RPT-{seq:03d}.md` and
**continue**; breaking (contradicts a locked decision or a
REQ, including an SRS↔PRD contradiction) → ADR `BLOCKED`,
**stop**. Use `profile.knowledge.files` and `analysis/domain/` steering
for the best-practice choice. No question is raised at this stage.

## 8. Boundaries and hand-off

| Owns (mints) | References (read-only) | Never touches |
|---|---|---|
| `UXD-*`, `SCR-*`; flow diagram; ui-ux-spec; F-blocks; ALIGN; ADRs it raises | `REQ/AC/ENT/RULE` (P1), `API` (P3.1 — shape from api-spec-rpt.yaml), catalog codes, permission names, `US` (P0.5) | `DBF/XM` (P2 — backend-only), `QR`, `TC` (P4), any code, any build |

Hand-off (the orchestrator prints it): plan + registry split by the toolkit into
`/frontend-execution/` inside the
shared repo after the `P4` verdict, then tagged `{mod}-v{version}`. Nothing is
copied anywhere: the implementer reads it where it was written, at the commit its own repo pins. The implementer reads the plan in profile-phase order, the spec for
intent, `api-spec-rpt.yaml` for shapes (served by its mock server until the backend is delivered), and does not invent a route, component, permission or field [G]
not traceable to an F-block (a gap → ADR, not an invention).


---
# INPUTS (generated current state)

<<<INPUT: srs>>>
# SRS — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias
Inputs : prd, domain-profile, project-registry (PRD approved 2026-10-01)
Counts : REQ 52 · AC 60 · ENT 4 · RULE 15 · SCR-REQ 0 · ADR 5 (new: ADR-RPT-006 … ADR-RPT-010; applied: ADR-RPT-001 … ADR-RPT-010, ADR-REG-001, ADR-REG-002, ADR-REG-006, ADR-CHK-001, ADR-CHK-002, ADR-CHK-005, ADR-CHK-007, ADR-CHK-011, ADR-CHK-014, ADR-CHK-015, ADR-CHK-017, ADR-DOC-002, ADR-DOC-007, ADR-DOC-008, ADR-DOC-011)
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information
| Item | Value |
|---|---|
| Module | RPT — Report Store |
| Feature code | RPT |
| Version | v1 |
| Date | 2026-10-01 |
| Status | DRAFT — P1 output, PRD approved 2026-10-01 (gate prd-approval) |
| Prepared by | P1 SRS engine (operator run, lane analysis) |
| Decisions applied | 10 RPT ADRs (ADR-RPT-001 … ADR-RPT-010, of which 5 new), 3 REG ADRs, 8 CHK ADRs, 4 DOC ADRs and 4 DEFAULTs — see Decisions applied |

## A2 — Functional context

### In scope
- Implementing all six operations of the Check result port CHK declares (CON-CHK-006 … CON-CHK-011): create a Check run and return its identifier, mark it RUNNING, store a completed report whole, store a failure with its reason, read one Check, list the unfinished Checks (POL-RPT-001, POL-RPT-004, POL-RPT-006, POL-RPT-010; ADR-RPT-001).
- Keeping host identifiers exactly as sent and only the codes of the closed lists of CHK and DOC (POL-RPT-002, POL-RPT-005).
- Moving a Check's status only forward and never changing an ended report (POL-RPT-003, POL-RPT-009; ADR-RPT-002).
- Keeping every unread document and unread service query in the report, and no document content or query results (POL-RPT-007, POL-RPT-008).
- Serving, read-only, a Check's status and report, the Checks of a request and the decision agreement of a service (POL-RPT-011, POL-RPT-012, POL-RPT-017; ADR-RPT-005, ADR-RPT-006).
- Recording the Employee Decision handed over by INT beside the result (POL-RPT-013 … POL-RPT-016, POL-RPT-018; ADR-RPT-003, ADR-RPT-009).
- Retention and the hard-delete purge (POL-RPT-019 … POL-RPT-022; ADR-RPT-004, ADR-RPT-010).
- The raw-idea §12 guardrails at RPT's surface (ADR-RPT-008).

### Out of scope
- Running a Check, deriving the Overall Status, the Check timeout and upload window — CHK (ADR-CHK-001, ADR-CHK-002, ADR-CHK-005).
- Fetching and reading documents, the storage root, the maximum file size, uploaded files — DOC (ADR-DOC-001, ADR-DOC-008).
- Starting a Check, uploading documents, confirming uploads, the decision endpoint and the call to the Approval API — INT (G2; ADR-RPT-003, ADR-RPT-006).
- Who may view stored reports and caller authentication — deferred (domain-profile D4, D7; raw-idea A2); no role check is specified.
- Changing or withdrawing a decision, per-service retention, archiving, cross-service dashboards (scope exceptions of the business policies).
- Multi-tenancy, conversation memory, RAG, multi-agent orchestration, an administration UI.

### Module function
The Report Store keeps the record of every Check: what it was asked to verify, how far it got, what it found and what the employee decided. It is written by the Check Engine through the Check result port and by Host Integration for the Employee Decision; it serves the host, the employee frontend and the service administrator read-only views of that record; and it removes each record, whole, once the configured retention period has passed since the Check ended.

### Detailed description
When the Check Engine starts a Check, it asks the Report Store to create the Check run — service code, service package version, fetch mode, request number, employee identity, initial status and start time — and receives the Check's identifier. As the pipeline advances, the Check Engine marks the Check RUNNING and finally either completes it — handing over the Overall Status, the findings with condition, outcome, evidence and note, the document outcomes with type, source mode, read status and reason, the service queries that could not be read and the metadata — or fails it with one failure reason and a detail text. The Report Store stores a completed report in one piece or not at all, refuses a code outside its closed list and refuses any change to an ended Check. At start-up and on its schedule the Check Engine asks for the unfinished Checks, and when the employee confirms the uploads of a `manual` Check it reads that Check back. Host systems and the embedded employee frontend read a Check's status while it runs and its whole report once it has ended, and list the Checks of a request. When the employee decides, Host Integration — after calling the Approval API where the service enables it — hands the decision to the Report Store, which records it once, beside the result of a COMPLETED Check. The service administrator reads, per service package version, how the decided Checks of each Overall Status were approved or rejected. A purge, on the platform configuration's schedule, deletes every Check run that ended longer ago than the report retention period, with all its records. Roles: the Employee (follows Checks, reads reports, takes the decision) and the Service Administrator (retention, accuracy measure).

### Current situation
| Step | Party | Notes |
|---|---|---|
| Employee checks the request by hand and approves or rejects it in the host system | Employee | No record of what was checked, on which evidence, or whether a check would have agreed with the decision [KB:raw-idea.md §1, §9] |

### Current difficulties
Nothing records which conditions were verified, on what evidence, or how often the employee's decision differs from what the evidence shows, so neither the employees' work nor a future automated check can be measured [KB:raw-idea.md §1, §9].

### Proposed system and benefits
Every Check leaves a complete, unchangeable report (US-RPT-003, US-RPT-006) that the employee reads with each finding beside its evidence (US-RPT-008); the decision is kept beside the result (US-RPT-010), so the service administrator can see where the service and the employees disagree (US-RPT-011); reports leave only by a deliberate, configured purge (US-RPT-012).

### General notes
- Logical types only; physical types and tables belong to P2.
- The Check identifier carried by the Check result port (`checkId`) is the identifier of the Check Run (ENT-RPT-001.checkRunId); CHK, DOC and INT hold it as a value only (CON-CHK-006).
- The report retention period, in whole days, and the purge schedule are platform configuration (ADR-RPT-004, ADR-RPT-010) — not entities.
- Every write comes from in-process callers: CHK through the result port, INT for the decision; RPT's HTTP surface is read-only (ADR-RPT-006).
- No role check is specified in this version (raw-idea A2).

## A3 — Entities and fields

Standard fields — per profile: kind `transactional` carries `createdAt, updatedAt` (system-filled, never accepted from a client). Every identifier field of the service's own schema is a number key generated by identity (profile `conventions.identifiers`); host identifiers (request number, employee identity) are kept as text exactly as the host sent them and are never foreign keys.

### ENT-RPT-001 — Check Run
Kind reason: transactional — one row per Check, created when the Check starts, ended once, removed only by the purge.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | SHARED (owner) — the identifier travels by value to CHK, DOC and INT; no other module stores its rows | no | create (REQ-RPT-001), read (REQ-RPT-021, REQ-RPT-023, REQ-RPT-028, REQ-RPT-040), update (REQ-RPT-005, REQ-RPT-008, REQ-RPT-018, REQ-RPT-032), delete (purge — REQ-RPT-043) | CHK writes it through the result port; INT records the decision; DOC and INT hold `checkId` by value | POL-RPT-001; ADR-REG-001; ADR-RPT-001, ADR-RPT-003 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| checkRunId | number (identifier) | yes | identity | the Check identifier (`checkId`) of the result port | Check |
| serviceCode | text | yes | SERVICE_CODE (REG, by value) | as carried by CHK; never a foreign key | Service |
| versionNumber | number | yes | service package version (REG, by value) | with serviceCode, the version the report was built on (G11) | Service version |
| fetchMode | lookup | yes | FETCH_MODE | | Document source mode |
| requestNumber | text | yes | host | exactly as the host sent it (REQ-RPT-002) | Request number |
| employeeId | text | yes | host | the employee who started the Check, exactly as sent | Employee |
| checkStatus | lookup | yes | CHECK_STATUS | forward only (RULE-RPT-003) | Status |
| startedAt | date-time | yes | CHK | | Started at |
| runningSince | date-time | no | CHK | set by the first mark RUNNING | Running since |
| endedAt | date-time | no | CHK | set when COMPLETED or FAILED | Ended at |
| overallStatus | lookup | no | OVERALL_STATUS | present exactly when COMPLETED (REQ-RPT-008, REQ-RPT-018) | Overall Status |
| comparisonModel | text | no | CHK metadata | present exactly when COMPLETED | Model used |
| failureReason | lookup | no | CHECK_FAILURE_REASON | present exactly when FAILED | Failure reason |
| failureDetail | text | no | CHK | present exactly when FAILED | Failure detail |
| employeeDecision | lookup | no | EMPLOYEE_DECISION | at most once, only on a COMPLETED Check (RULE-RPT-011, RULE-RPT-012) | Employee Decision |
| decidedBy | text | no | host, through INT | exactly as sent; present exactly when employeeDecision is | Decided by |
| decidedAt | date-time | no | system | time the decision was recorded (ADR-RPT-009) | Decided at |
| approvalApiExecuted | flag | no | INT | true when the decision was carried out through the Approval API; present exactly when employeeDecision is | Executed through Approval API |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

### ENT-RPT-002 — Finding
Kind reason: transactional — one row per condition of a completed report, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-004; [KB:raw-idea.md §9 CHECK_FINDING]; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| findingId | number (identifier) | yes | identity | | Finding |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order of the finding in the report as handed over, from 1 | Position |
| conditionText | text | yes | CHK | the condition the finding is about | Condition |
| findingOutcome | lookup | yes | FINDING_OUTCOME | | Outcome |
| evidence | text | yes | CHK | the actual value found (G10) | Evidence |
| note | text | yes | CHK | the note for the employee | Note |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

### ENT-RPT-003 — Check Document
Kind reason: transactional — one row per document outcome of a completed report, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-007; [KB:raw-idea.md §9 CHECK_DOCUMENT]; ADR-REG-001; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| checkDocumentId | number (identifier) | yes | identity | | Check Document |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order of the outcome as handed over, from 1 | Position |
| documentType | text | yes | DOCUMENT_TYPE (REG, by value) | | Document type |
| sourceMode | lookup | yes | FETCH_MODE | | Source mode |
| readStatus | lookup | yes | DOCUMENT_READ_STATUS | | Read status |
| unreadableReason | lookup | no | UNREADABLE_REASON | present exactly when readStatus is UNREADABLE (RULE-RPT-008) | Reason |
| detail | text | no | CHK / DOC | | Detail |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

No document content field exists (REQ-RPT-019).

### ENT-RPT-004 — Unread Query
Kind reason: transactional — one row per service query whose data could not be read, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-007; CON-CHK-008; ADR-CHK-014; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| unreadQueryId | number (identifier) | yes | identity | | Unread query |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order as handed over, from 1 | Position |
| queryName | text | yes | CHK | the service query's name in the service definition | Query |
| detail | text | yes | CHK | why its data was not read | Detail |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

Consumed: none. RPT consumes no entity of another module: the service code, version number, document type and every closed code arrive by value through the Check result port (CON-CHK-006, CON-CHK-008) and are stored as values; RPT never reads REG, DOC or CHK at run time (ADR-RPT-001).

## A4 — Functional requirements (EARS) and acceptance criteria

### REQ-RPT-001 — Check run created, identifier returned
  Pattern    : event
  Statement  : When the Check Engine asks to create a Check run with a service code, a version number, a fetch mode, a request number, an employee identity, an initial status and a start time, the system shall store the Check run and return its identifier.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : One row per check is where the report and the decision are kept.
  Source     : POL-RPT-001; CON-CHK-006; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-001 — [REQ-RPT-001]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run for `scholarship-request` version 3, fetch mode `path`, request number `1001`, employee `E-2041`, status RUNNING, start time 2026-10-01T09:00:00Z
  Then   : 1 Check run is stored with exactly those values and its new identifier is returned

### REQ-RPT-002 — Host identifiers kept exactly as sent
  Pattern    : ubiquitous
  Statement  : The system shall keep the request number and the employee identity of a Check run as text exactly as received, without trimming, case change or reformatting.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : The host data lives outside the service's schema; the report must show the identifiers the host knows.
  Source     : POL-RPT-002; profile `conventions.identifiers`
  Priority   : HIGH

#### AC-RPT-002 — [REQ-RPT-002]
  Given  : the Check Engine creates a Check run with request number `00-1001/A` and employee identity ` e.2041 `
  When   : the Check run is read back
  Then   : the request number is "00-1001/A" and the employee identity is " e.2041 ", unchanged

### REQ-RPT-003 — Incomplete Check run refused
  Pattern    : unwanted
  Statement  : If a Check run to be created lacks its service code, version number, fetch mode, request number, employee identity, initial status or start time, then the system shall refuse to store it.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : A Check run without these values cannot be traced to what it verified.
  Source     : POL-RPT-001; RULE-RPT-001; CON-CHK-006 "errors: not stored"
  Priority   : HIGH

#### AC-RPT-003 — [REQ-RPT-003]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run whose employee identity is blank
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: employeeId is missing."

### REQ-RPT-004 — Initial status agrees with the fetch mode
  Pattern    : unwanted
  Statement  : If a Check run to be created has the initial status AWAITING_DOCUMENTS with a fetch mode other than `manual`, or the initial status RUNNING with the fetch mode `manual`, then the system shall refuse to store it.
  Traces     : US-RPT-001, US-RPT-002
  Entities   : ENT-RPT-001
  Rationale  : Only a `manual` Check waits for documents; a contradictory record would mislead the host polling it.
  Source     : RULE-RPT-002; ADR-CHK-004; ADR-RPT-007
  Priority   : —

#### AC-RPT-004 — [REQ-RPT-004]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run with fetch mode `path` and initial status AWAITING_DOCUMENTS
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual."

#### AC-RPT-005 — [REQ-RPT-004]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run with fetch mode `manual` and initial status RUNNING
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS."

### REQ-RPT-005 — Check marked RUNNING
  Pattern    : event
  Statement  : When the Check Engine marks a Check that is AWAITING_DOCUMENTS or RUNNING as running, the system shall set its status to RUNNING and, if no running time is stored yet, store the running time received.
  Traces     : US-RPT-002
  Entities   : ENT-RPT-001
  Rationale  : The host sees that the Check's pipeline is under way.
  Source     : POL-RPT-003; CON-CHK-007; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-006 — [REQ-RPT-005]
  Given  : Check 501 is AWAITING_DOCUMENTS with no running time
  When   : the Check Engine marks Check 501 running with running time 2026-10-01T09:05:00Z
  Then   : Check 501 has status RUNNING and running time 2026-10-01T09:05:00Z

#### AC-RPT-007 — [REQ-RPT-005]
  Given  : Check 502 is RUNNING with running time 2026-10-01T09:01:00Z
  When   : the Check Engine marks Check 502 running with running time 2026-10-01T09:02:00Z
  Then   : Check 502 stays RUNNING and its running time stays 2026-10-01T09:01:00Z

### REQ-RPT-006 — Status never moves backwards
  Pattern    : unwanted
  Statement  : If a status change would move a Check out of COMPLETED or FAILED, or complete a Check that is not RUNNING, then the system shall refuse the change and keep the stored status.
  Traces     : US-RPT-002, US-RPT-006
  Entities   : ENT-RPT-001
  Rationale  : COMPLETED and FAILED are final; a Check ends exactly once.
  Source     : POL-RPT-003; RULE-RPT-003; CON-CHK-001; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-008 — [REQ-RPT-006]
  Given  : Check 503 is COMPLETED
  When   : the Check Engine marks Check 503 running
  Then   : Check 503 stays COMPLETED and the call is refused with "Check 503 has already ended; its status cannot change."

#### AC-RPT-009 — [REQ-RPT-006]
  Given  : Check 504 is AWAITING_DOCUMENTS
  When   : the Check Engine completes Check 504
  Then   : nothing is stored for Check 504, it stays AWAITING_DOCUMENTS and the call is refused with "Check 504 is not running; it cannot be completed."

### REQ-RPT-007 — Unknown Check on the result port
  Pattern    : unwanted
  Statement  : If the Check Engine marks running, completes, fails or reads a Check that has no stored Check run, then the system shall refuse the call as not found.
  Traces     : US-RPT-002, US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : A result for a Check that was never created has nowhere to go.
  Source     : CON-CHK-007, CON-CHK-009, CON-CHK-010 "errors: not found"
  Priority   : —

#### AC-RPT-010 — [REQ-RPT-007]
  Given  : no Check run 999 exists
  When   : the Check Engine fails Check 999
  Then   : nothing is stored and the call is refused with "Check 999 was not found."

### REQ-RPT-008 — Completed report stored
  Pattern    : event
  Statement  : When the Check Engine completes a RUNNING Check, the system shall store its status COMPLETED, its Overall Status, its comparison model, its end time, every finding, every document outcome and every unread service query.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The report has a fixed structure for every service and is stored as data.
  Source     : POL-RPT-004; CON-CHK-008; [KB:raw-idea.md §7]; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-011 — [REQ-RPT-008]
  Given  : Check 505 is RUNNING
  When   : the Check Engine completes it with Overall Status NOT_COMPLIANT, comparison model `gemini-flash-lite`, end time 2026-10-01T09:10:00Z, 3 findings, 2 document outcomes and 1 unread query
  Then   : Check 505 is COMPLETED with Overall Status NOT_COMPLIANT, model `gemini-flash-lite`, end time 2026-10-01T09:10:00Z, and 3 Findings, 2 Check Documents and 1 Unread Query are stored for it

### REQ-RPT-009 — A report is stored whole or not at all
  Pattern    : unwanted
  Statement  : If any part of a completed report cannot be stored, then the system shall store no part of it, keep the Check RUNNING and refuse the call as not stored.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A half-stored report would show a result without the findings that justify it; the Check Engine then fails the Check INTERNAL_ERROR.
  Source     : POL-RPT-004; CON-CHK-008 "errors: not stored (CHK then fails the Check with INTERNAL_ERROR)"; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-012 — [REQ-RPT-009]
  Given  : Check 506 is RUNNING
  When   : the Check Engine completes it with 3 findings of which the third has outcome `PASSED`
  Then   : Check 506 stays RUNNING with no Overall Status, 0 Findings, 0 Check Documents and 0 Unread Queries are stored for it, and the call is refused as not stored

### REQ-RPT-010 — Report order kept
  Pattern    : ubiquitous
  Statement  : The system shall keep the findings, the document outcomes and the unread service queries of a report in the order in which the Check Engine handed them over.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The employee reads the report in the order the conditions were assessed.
  Source     : POL-RPT-004; [KB:raw-idea.md §7]
  Priority   : —

#### AC-RPT-013 — [REQ-RPT-010]
  Given  : Check 507 is completed with findings on conditions "GPA at least 3.0", "TRANSCRIPT present", "ID_CARD present" in that order
  When   : the report of Check 507 is read
  Then   : the findings are returned at positions 1, 2, 3 in that same order

### REQ-RPT-011 — Missing and unreadable documents kept with their reason
  Pattern    : ubiquitous
  Statement  : The system shall keep every document outcome of a completed report, including each MISSING document and each UNREADABLE document with its reason and detail.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-003
  Rationale  : Anything that could not be read appears in the report; it is never skipped silently.
  Source     : POL-RPT-007; [KB:raw-idea.md §7, §12]; domain-profile §5 G6; ADR-DOC-002, ADR-DOC-007
  Priority   : HIGH

#### AC-RPT-014 — [REQ-RPT-011]
  Given  : Check 508 is RUNNING
  When   : the Check Engine completes it with document outcomes TRANSCRIPT READ, ID_CARD MISSING and a second TRANSCRIPT UNREADABLE with reason TOO_LARGE and detail "file of 31 MB exceeds 20 MB"
  Then   : 3 Check Documents are stored for Check 508, the third with reason TOO_LARGE and detail "file of 31 MB exceeds 20 MB"

### REQ-RPT-012 — Unread service queries kept
  Pattern    : ubiquitous
  Statement  : The system shall keep every unread service query of a completed report with its query name and detail.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-004
  Rationale  : Data that could not be read must stay visible beside the result it weakened.
  Source     : POL-RPT-007; CON-CHK-008; ADR-CHK-014; domain-profile §5 G6
  Priority   : HIGH

#### AC-RPT-015 — [REQ-RPT-012]
  Given  : Check 509 is RUNNING
  When   : the Check Engine completes it with Overall Status NEEDS_MANUAL_REVIEW and unread query `request_details` with detail "more than 500 rows"
  Then   : 1 Unread Query `request_details` with detail "more than 500 rows" is stored for Check 509

### REQ-RPT-013 — Metadata agrees with the Check run
  Pattern    : unwanted
  Statement  : If the metadata of a completed report lacks the comparison model or the end time, names a service code, version number, fetch mode, employee identity or start time different from the stored Check run, or ends before the Check started, then the system shall refuse to store the report.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001
  Rationale  : A report whose metadata contradicts its Check run cannot be traced to what produced it.
  Source     : RULE-RPT-004; [KB:raw-idea.md §7] metadata; domain-profile §5 G11; ADR-RPT-007
  Priority   : —

#### AC-RPT-016 — [REQ-RPT-013]
  Given  : Check 510 is RUNNING for `scholarship-request` version 3
  When   : the Check Engine completes it with metadata version number 4
  Then   : nothing is stored for Check 510 and the call is refused with "The report of Check 510 was not stored: its metadata versionNumber 4 differs from the Check run (3)."

### REQ-RPT-014 — COMPLIANT only with every finding satisfied
  Pattern    : unwanted
  Statement  : If a completed report has the Overall Status COMPLIANT while any of its findings is not SATISFIED or any service query was not read, then the system shall refuse to store the report.
  Traces     : US-RPT-003, US-RPT-004
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-004
  Rationale  : A stored report must never claim more than was verified.
  Source     : RULE-RPT-005; CON-CHK-001 derivation; domain-profile §5 G6; ADR-RPT-007
  Priority   : HIGH

#### AC-RPT-017 — [REQ-RPT-014]
  Given  : Check 511 is RUNNING
  When   : the Check Engine completes it with Overall Status COMPLIANT and a finding "ID_CARD present" with outcome NOT_SATISFIED
  Then   : nothing is stored for Check 511 and the call is refused with "The report of Check 511 was not stored: COMPLIANT needs every finding SATISFIED and every service query read."

### REQ-RPT-015 — Only the codes of the closed lists
  Pattern    : unwanted
  Statement  : If a Check status, Overall Status, finding outcome, failure reason, document read status, unreadable reason or fetch mode received is not a code of its closed list, then the system shall refuse to store it.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003
  Rationale  : The closed lists are owned by the service; a code outside them is meaningless to every reader.
  Source     : POL-RPT-005; RULE-RPT-006; CON-CHK-001 … CON-CHK-003, CON-DOC-001, CON-DOC-002
  Priority   : —

#### AC-RPT-018 — [REQ-RPT-015]
  Given  : Check 512 is RUNNING
  When   : the Check Engine fails it with failure reason `CRASHED`
  Then   : Check 512 stays RUNNING and the call is refused with "Not stored: `CRASHED` is not a code of CHECK_FAILURE_REASON."

### REQ-RPT-016 — A finding is complete
  Pattern    : unwanted
  Statement  : If a finding of a completed report lacks its condition, outcome, evidence or note, then the system shall refuse to store the report.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-002
  Rationale  : Every finding carries its evidence so the employee can verify it.
  Source     : POL-RPT-004; RULE-RPT-007; CON-CHK-002; domain-profile §5 G10
  Priority   : HIGH

#### AC-RPT-019 — [REQ-RPT-016]
  Given  : Check 513 is RUNNING
  When   : the Check Engine completes it with a finding "GPA at least 3.0" whose evidence is blank
  Then   : nothing is stored for Check 513 and the call is refused with "The report of Check 513 was not stored: finding 1 has no evidence."

### REQ-RPT-017 — Reason exactly on UNREADABLE documents
  Pattern    : unwanted
  Statement  : If a document outcome is UNREADABLE without a reason, or READ or MISSING with a reason, then the system shall refuse to store the report.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-003
  Rationale  : The reason tells the employee why a document could not be read; it means nothing on a document that was read or absent.
  Source     : RULE-RPT-008; CON-DOC-001
  Priority   : —

#### AC-RPT-020 — [REQ-RPT-017]
  Given  : Check 514 is RUNNING
  When   : the Check Engine completes it with a document outcome ID_CARD UNREADABLE with no reason
  Then   : nothing is stored for Check 514 and the call is refused with "The report of Check 514 was not stored: document 1 is UNREADABLE without a reason."

### REQ-RPT-018 — Failed Check stored with its reason
  Pattern    : event
  Statement  : When the Check Engine fails a Check that is AWAITING_DOCUMENTS or RUNNING, the system shall store its status FAILED, its failure reason, its detail and its end time, with no Overall Status and no findings.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001
  Rationale  : A failed Check must be visible as failed, never with a result the pipeline did not reach.
  Source     : POL-RPT-006; CON-CHK-009; CON-CHK-003; ADR-CHK-005
  Priority   : HIGH

#### AC-RPT-021 — [REQ-RPT-018]
  Given  : Check 515 is RUNNING
  When   : the Check Engine fails it with reason TIMED_OUT, detail "Check exceeded 300 s", end time 2026-10-01T09:06:00Z
  Then   : Check 515 is FAILED with reason TIMED_OUT, detail "Check exceeded 300 s", end time 2026-10-01T09:06:00Z, no Overall Status and 0 Findings

### REQ-RPT-019 — No document content or query results kept
  Pattern    : ubiquitous
  Statement  : The system shall keep no document content and no query result rows in a stored report beyond the condition, evidence, note, outcome and detail texts the Check Engine hands over.
  Traces     : US-RPT-005
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The report needs the evidence the employee verifies, not a copy of the request's files and data.
  Source     : POL-RPT-008; CON-CHK-008 "Carries no document content"; ADR-DOC-008
  Priority   : —

#### AC-RPT-022 — [REQ-RPT-019]
  Given  : Check 516 is completed with a document outcome TRANSCRIPT READ
  When   : the Check Document of TRANSCRIPT is read
  Then   : it holds document type, source mode, read status and detail only — no field holds the transcript's text or file

### REQ-RPT-020 — An ended report never changes
  Pattern    : state
  Statement  : While a Check is COMPLETED or FAILED, the system shall refuse every change to its status, result, failure, findings, Check Documents, unread queries and metadata.
  Traces     : US-RPT-006
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The decision is measured against the report the employee saw.
  Source     : POL-RPT-009; RULE-RPT-003; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-023 — [REQ-RPT-020]
  Given  : Check 517 is COMPLETED with Overall Status NOT_COMPLIANT and 3 findings
  When   : the Check Engine completes Check 517 again with Overall Status COMPLIANT
  Then   : Check 517 keeps Overall Status NOT_COMPLIANT and its 3 findings, and the call is refused with "Check 517 has already ended; its status cannot change."

### REQ-RPT-021 — One Check read for the Check Engine
  Pattern    : event
  Statement  : When the Check Engine reads one Check, the system shall return its identifier, status, service code, version number, fetch mode, request number, employee identity and start time.
  Traces     : US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : The Check Engine resumes a `manual` Check on the version it recorded.
  Source     : CON-CHK-010; POL-RPT-010
  Priority   : —

#### AC-RPT-024 — [REQ-RPT-021]
  Given  : Check 518 is AWAITING_DOCUMENTS for `scholarship-request` version 3, fetch mode `manual`, request `1001`, employee `E-2041`
  When   : the Check Engine reads Check 518
  Then   : it receives 518, AWAITING_DOCUMENTS, `scholarship-request`, 3, `manual`, `1001`, `E-2041` and the start time

### REQ-RPT-022 — Unfinished Checks listed
  Pattern    : event
  Statement  : When the Check Engine asks for the unfinished Checks, the system shall return the identifier, status and start time of every Check that is AWAITING_DOCUMENTS or RUNNING, oldest first.
  Traces     : US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : Checks interrupted by a restart or whose upload window elapsed must be ended.
  Source     : POL-RPT-010; CON-CHK-011; ADR-CHK-005
  Priority   : —

#### AC-RPT-025 — [REQ-RPT-022]
  Given  : Checks 519 (RUNNING, started 09:00), 520 (AWAITING_DOCUMENTS, started 08:30) and 521 (COMPLETED) exist
  When   : the Check Engine asks for the unfinished Checks
  Then   : it receives 520 then 519, and not 521

#### AC-RPT-026 — [REQ-RPT-022]
  Given  : every stored Check is COMPLETED or FAILED
  When   : the Check Engine asks for the unfinished Checks
  Then   : it receives an empty list

### REQ-RPT-023 — A Check's status and report read
  Pattern    : event
  Statement  : When a host system or the employee frontend reads a Check, the system shall return its identifier, status, service code, version number, fetch mode, request number, employee identity and times and, once it is COMPLETED, its Overall Status, model used, findings, Check Documents, unread queries and Employee Decision.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The host polls the status; the employee reads the whole report.
  Source     : POL-RPT-011; [KB:raw-idea.md §8] `GET /checks/{id}`; §15 A1; ADR-RPT-005, ADR-RPT-006
  Priority   : HIGH

#### AC-RPT-027 — [REQ-RPT-023]
  Given  : Check 522 is RUNNING
  When   : the employee frontend reads Check 522
  Then   : it receives status RUNNING with the Check's service, version, request and times, and no Overall Status, findings or documents

#### AC-RPT-028 — [REQ-RPT-023]
  Given  : Check 523 is COMPLETED with Overall Status NOT_COMPLIANT, 3 findings, 2 Check Documents and 0 unread queries, with no decision
  When   : the employee frontend reads Check 523
  Then   : it receives status COMPLETED, Overall Status NOT_COMPLIANT, the model used, 3 findings, 2 documents, 0 unread queries and no Employee Decision

### REQ-RPT-024 — Each finding returned with its evidence
  Pattern    : ubiquitous
  Statement  : The system shall return every finding of a report as one entry holding its condition, outcome, evidence and note together.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-002
  Rationale  : Every finding carries its evidence so the employee can verify it.
  Source     : POL-RPT-011; [KB:raw-idea.md §7]; domain-profile §5 G10
  Priority   : HIGH

#### AC-RPT-029 — [REQ-RPT-024]
  Given  : Check 524 is completed with finding "GPA at least 3.0", outcome NOT_SATISFIED, evidence "GPA = 2.7", note "Below the 3.0 minimum"
  When   : the report of Check 524 is read
  Then   : one finding entry holds "GPA at least 3.0", NOT_SATISFIED, "GPA = 2.7" and "Below the 3.0 minimum"

### REQ-RPT-025 — Unknown Check read
  Pattern    : unwanted
  Statement  : If a host system or the employee frontend reads a Check that has no stored Check run, then the system shall answer that the Check was not found.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001
  Rationale  : The caller must tell a missing Check from a running one; a purged Check is also not found.
  Source     : POL-RPT-011; profile `error_envelope` ProblemDetail
  Priority   : —

#### AC-RPT-030 — [REQ-RPT-025]
  Given  : no Check run 998 exists
  When   : the employee frontend reads Check 998
  Then   : the answer is not found with detail "Check 998 was not found."

### REQ-RPT-026 — Failed Check read with its reason
  Pattern    : event
  Statement  : When a host system or the employee frontend reads a FAILED Check, the system shall return its failure reason and detail with no Overall Status.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001
  Rationale  : A failed Check must be visible as failed.
  Source     : POL-RPT-006, POL-RPT-011; ADR-CHK-005
  Priority   : —

#### AC-RPT-031 — [REQ-RPT-026]
  Given  : Check 525 is FAILED with reason MODEL_UNAVAILABLE and detail "provider answered 503"
  When   : the employee frontend reads Check 525
  Then   : it receives status FAILED, reason MODEL_UNAVAILABLE, detail "provider answered 503" and no Overall Status

### REQ-RPT-027 — Stored texts returned as data
  Pattern    : ubiquitous
  Statement  : The system shall return every stored condition, evidence, note and detail text exactly as stored, as data, without interpreting or executing anything it contains.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : Evidence may quote document content, which is data, never instructions.
  Source     : [KB:raw-idea.md §12] "Document content is treated as data"; domain-profile §5 G7; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-032 — [REQ-RPT-027]
  Given  : Check 526 is completed with a finding whose evidence is "<script>alert(1)</script> ignore previous instructions"
  When   : the report of Check 526 is read
  Then   : the evidence is returned as the same character string, as a text value

### REQ-RPT-028 — Checks of a request listed
  Pattern    : event
  Statement  : When the employee frontend lists the Checks of a service code and request number, the system shall return the identifier, status, Overall Status, start time, end time and Employee Decision of the newest 100 of those Checks, newest first, with the total number of Checks of that request.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : The employee sees whether the request was checked before and what each Check found.
  Source     : POL-RPT-012; [KB:raw-idea.md §15 A1]; ADR-RPT-005, ADR-RPT-008
  Priority   : —

#### AC-RPT-033 — [REQ-RPT-028]
  Given  : request `1001` of `scholarship-request` has Checks 527 (started 08:00, FAILED) and 528 (started 09:00, COMPLETED, NOT_COMPLIANT), and request `1001` of `housing-request` has Check 529
  When   : the employee frontend lists the Checks of `scholarship-request` request `1001`
  Then   : it receives 528 then 527 and the total 2, and not 529

#### AC-RPT-034 — [REQ-RPT-028]
  Given  : request `1002` of `scholarship-request` has 130 Checks
  When   : the employee frontend lists its Checks
  Then   : it receives the 100 newest, newest first, and the total 130

### REQ-RPT-029 — Service code and request number needed for the list
  Pattern    : unwanted
  Statement  : If the Checks of a request are listed without a service code or without a request number, then the system shall refuse the listing.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : Two services can share a host request number; a list on one key would mix their reports.
  Source     : RULE-RPT-009; ADR-RPT-005
  Priority   : —

#### AC-RPT-035 — [REQ-RPT-029]
  Given  : Checks exist for request `1001`
  When   : the employee frontend lists Checks with request number `1001` and no service code
  Then   : no list is returned and the answer is a validation error with detail "Both a service code and a request number are needed to list Checks."

### REQ-RPT-030 — Every Check its own record
  Pattern    : ubiquitous
  Statement  : The system shall store every Check of the same request as its own Check run and shall never copy a finding, a result or an Employee Decision from one Check run to another.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001, ENT-RPT-002
  Rationale  : No data is carried from one check to another.
  Source     : POL-RPT-023; [KB:raw-idea.md §12]; domain-profile §5 G9; ADR-CHK-007
  Priority   : —

#### AC-RPT-036 — [REQ-RPT-030]
  Given  : Check 530 of request `1001` is COMPLETED with 3 findings and decision APPROVED
  When   : the Check Engine creates a new Check run for request `1001` of the same service
  Then   : a new Check run with a new identifier is stored with no findings and no Employee Decision, and Check 530 is unchanged

### REQ-RPT-031 — Bounded list of a request's Checks
  Pattern    : ubiquitous
  Statement  : The system shall return at most 100 Checks in one listing of the Checks of a request.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : Every read has a limit; the total number keeps the cut visible, never silent.
  Source     : [KB:raw-idea.md §12] "Each check has limits"; domain-profile §5 G8; ADR-RPT-008
  Priority   : —

#### AC-RPT-037 — [REQ-RPT-031]
  Given  : request `1003` of `scholarship-request` has 101 Checks
  When   : the employee frontend lists its Checks
  Then   : exactly 100 entries are returned with the total 101

### REQ-RPT-032 — Employee Decision recorded
  Pattern    : event
  Statement  : When Host Integration hands over an Employee Decision on a COMPLETED Check with no decision, the system shall record the decision, the deciding employee's identity exactly as sent, whether it was executed through the Approval API and the recording time.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : Keeping the decision beside the result shows where the two disagree.
  Source     : POL-RPT-013; [KB:raw-idea.md §9, §11]; ADR-RPT-003, ADR-RPT-009
  Priority   : HIGH

#### AC-RPT-038 — [REQ-RPT-032]
  Given  : Check 531 is COMPLETED with Overall Status COMPLIANT and no decision
  When   : Host Integration hands over decision APPROVED by employee `E-3307`, not executed through the Approval API
  Then   : Check 531 holds decision APPROVED, decided by "E-3307", executed through Approval API false, and a recording time; its Overall Status and findings are unchanged

### REQ-RPT-033 — One decision per Check
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that already has one, then the system shall refuse it and keep the decision already recorded.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The decision is a fact about one report; a later change belongs to the host system's own record.
  Source     : POL-RPT-014; RULE-RPT-011; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-039 — [REQ-RPT-033]
  Given  : Check 532 is COMPLETED with decision REJECTED by `E-3307`
  When   : Host Integration hands over decision APPROVED by `E-4410`
  Then   : Check 532 keeps decision REJECTED by `E-3307` and the call is refused with "Check 532 already has an Employee Decision."

### REQ-RPT-034 — Decisions only on a completed Check
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that is not COMPLETED, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : A running or failed Check has no result to stand beside.
  Source     : POL-RPT-015; RULE-RPT-012; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-040 — [REQ-RPT-034]
  Given  : Check 533 is FAILED
  When   : Host Integration hands over decision APPROVED by `E-3307`
  Then   : no decision is recorded and the call is refused with "Check 533 is not completed; a decision can only be recorded on a completed Check."

### REQ-RPT-035 — A decision is complete
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over without a decision code of EMPLOYEE_DECISION, without the deciding employee's identity or without saying whether it was executed through the Approval API, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The record must show what was decided, by whom and how.
  Source     : POL-RPT-013, POL-RPT-016; RULE-RPT-013; ADR-RPT-009
  Priority   : —

#### AC-RPT-041 — [REQ-RPT-035]
  Given  : Check 534 is COMPLETED with no decision
  When   : Host Integration hands over decision `MAYBE` by `E-3307`
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: `MAYBE` is not APPROVED or REJECTED."

#### AC-RPT-042 — [REQ-RPT-035]
  Given  : Check 535 is COMPLETED with no decision
  When   : Host Integration hands over decision APPROVED with a blank employee identity
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: the deciding employee is missing."

### REQ-RPT-036 — Execution through the Approval API recorded
  Pattern    : optional
  Statement  : Where an Employee Decision was executed through the service's Approval API, the system shall record it as executed through the Approval API.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The record shows which approvals the service carried out and on which report.
  Source     : POL-RPT-016; [KB:raw-idea.md §11]; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-043 — [REQ-RPT-036]
  Given  : Check 536 is COMPLETED with no decision
  When   : Host Integration hands over decision APPROVED by `E-3307`, executed through the Approval API
  Then   : Check 536 holds decision APPROVED with executed through Approval API true

### REQ-RPT-037 — The Report Store never approves
  Pattern    : ubiquitous
  Statement  : The system shall never call an Approval API and never set an Employee Decision other than one handed over by Host Integration.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The employee stays the decision maker; approval happens only as a result of the employee's action.
  Source     : POL-RPT-018; [KB:raw-idea.md §12]; domain-profile §5 G2; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-044 — [REQ-RPT-037]
  Given  : Check 537 is completed with Overall Status COMPLIANT for a service whose Approval API is enabled
  When   : 24 hours pass with no decision handed over
  Then   : Check 537 has no Employee Decision and the Report Store has sent 0 calls to any Approval API

### REQ-RPT-038 — Unknown Check for a decision
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that has no stored Check run, then the system shall refuse it as not found.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : A decision must stand beside an existing report.
  Source     : POL-RPT-013; ADR-RPT-003
  Priority   : —

#### AC-RPT-045 — [REQ-RPT-038]
  Given  : no Check run 997 exists
  When   : Host Integration hands over decision APPROVED by `E-3307` for Check 997
  Then   : nothing is recorded and the call is refused with "Check 997 was not found."

### REQ-RPT-039 — Approval API execution only with an approval
  Pattern    : unwanted
  Statement  : If an Employee Decision REJECTED is handed over as executed through the Approval API, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The Approval API carries out approvals; a rejection is never executed through it.
  Source     : RULE-RPT-014; [KB:raw-idea.md §11]; ADR-RPT-009
  Priority   : —

#### AC-RPT-046 — [REQ-RPT-039]
  Given  : Check 538 is COMPLETED with no decision
  When   : Host Integration hands over decision REJECTED by `E-3307`, executed through the Approval API
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: only an APPROVED decision is executed through the Approval API."

### REQ-RPT-040 — Decision agreement of a service
  Pattern    : event
  Statement  : When the decision agreement of a service code is read, the system shall return, for each service package version with a decided Check, the number of decided Checks for each pair of Overall Status and Employee Decision.
  Traces     : US-RPT-011
  Entities   : ENT-RPT-001
  Rationale  : Where the result and the decision disagree, the service knowledge or a check needs attention.
  Source     : POL-RPT-017; [KB:raw-idea.md §9]; ADR-RPT-005
  Priority   : —

#### AC-RPT-047 — [REQ-RPT-040]
  Given  : `scholarship-request` version 3 has decided Checks: 4 COMPLIANT + APPROVED, 1 COMPLIANT + REJECTED, 2 NOT_COMPLIANT + REJECTED; version 2 has 1 NEEDS_MANUAL_REVIEW + APPROVED; and 3 undecided Checks exist
  When   : the decision agreement of `scholarship-request` is read
  Then   : it returns version 3: (COMPLIANT, APPROVED) 4, (COMPLIANT, REJECTED) 1, (NOT_COMPLIANT, REJECTED) 2; version 2: (NEEDS_MANUAL_REVIEW, APPROVED) 1; the undecided Checks are not counted

#### AC-RPT-048 — [REQ-RPT-040]
  Given  : no decided Check exists for `housing-request`
  When   : the decision agreement of `housing-request` is read
  Then   : it returns an empty list

### REQ-RPT-041 — Service code needed for the decision agreement
  Pattern    : unwanted
  Statement  : If the decision agreement is read without a service code, then the system shall refuse the read.
  Traces     : US-RPT-011
  Entities   : ENT-RPT-001
  Rationale  : The measure is per service; mixing services hides where attention is needed.
  Source     : RULE-RPT-015; ADR-RPT-005
  Priority   : —

#### AC-RPT-049 — [REQ-RPT-041]
  Given  : decided Checks exist
  When   : the decision agreement is read with no service code
  Then   : nothing is returned and the answer is a validation error with detail "A service code is needed to read the decision agreement."

### REQ-RPT-042 — Reports kept for the retention period
  Pattern    : ubiquitous
  Statement  : The system shall keep every Check run, with its findings, Check Documents, unread queries and Employee Decision, until it has been ended for longer than the report retention period of the platform configuration.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A report is kept as long as the host keeps the request it verified.
  Source     : POL-RPT-019; domain-profile §8 D4; ADR-RPT-004
  Priority   : —

#### AC-RPT-050 — [REQ-RPT-042]
  Given  : the report retention period is 365 days and Check 539 ended 364 days ago
  When   : the purge runs
  Then   : Check 539 and all its records are still stored

### REQ-RPT-043 — Purge of expired Check runs
  Pattern    : event
  Statement  : When the purge runs on the schedule of the platform configuration, the system shall permanently delete every Check run that has been ended for longer than the report retention period, together with its findings, Check Documents and unread queries.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A purge removes older runs with their findings and documents by hard delete.
  Source     : POL-RPT-020; domain-profile §8 D4; profile `delete_semantics: hard`; ADR-RPT-004, ADR-RPT-010
  Priority   : —

#### AC-RPT-051 — [REQ-RPT-043]
  Given  : the report retention period is 365 days and Check 540 ended 366 days ago with 3 findings, 2 Check Documents, 1 unread query and decision APPROVED
  When   : the purge runs
  Then   : Check 540, its 3 Findings, 2 Check Documents and 1 Unread Query no longer exist, and reading Check 540 answers not found

### REQ-RPT-044 — No retention period, no purge
  Pattern    : unwanted
  Statement  : If the report retention period is not configured or is not a whole number of days greater than zero, then the system shall delete no Check run when the purge runs and shall log that the purge was skipped.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : A missing or wrong setting must never destroy records.
  Source     : POL-RPT-021; ADR-RPT-004, ADR-RPT-010
  Priority   : —

#### AC-RPT-052 — [REQ-RPT-044]
  Given  : no report retention period is configured and Check 541 ended 1000 days ago
  When   : the purge runs
  Then   : Check 541 is still stored and the log holds "Report purge skipped: no valid report retention period is configured."

### REQ-RPT-045 — Unfinished Checks never purged
  Pattern    : unwanted
  Statement  : If a Check is AWAITING_DOCUMENTS or RUNNING, then the system shall not delete it when the purge runs, whatever its start time.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : The Check Engine still writes to an unfinished Check and ends it.
  Source     : POL-RPT-022; CON-CHK-011; ADR-RPT-004
  Priority   : —

#### AC-RPT-053 — [REQ-RPT-045]
  Given  : the report retention period is 30 days and Check 542 is RUNNING, started 40 days ago
  When   : the purge runs
  Then   : Check 542 is still stored

### REQ-RPT-046 — Purge outcome logged
  Pattern    : event
  Statement  : When a purge run ends, the system shall log the number of Check runs it deleted and the cut-off time it applied.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : A deletion of records must leave a trace of how much was removed and on what basis.
  Source     : POL-RPT-020; ADR-RPT-010
  Priority   : —

#### AC-RPT-054 — [REQ-RPT-046]
  Given  : the report retention period is 365 days and 7 Check runs ended more than 365 days ago
  When   : the purge runs at 2026-10-02T02:00:00Z
  Then   : the log holds "Report purge deleted 7 Check runs ended before 2025-10-02T02:00:00Z."

### REQ-RPT-047 — No access to host data
  Pattern    : ubiquitous
  Statement  : The system shall run no query on host data and hold no connection to a host database.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : All access to host data is read-only and belongs to the Check Engine and Document Access; the Report Store has no need of it.
  Source     : [KB:raw-idea.md §12] read-only host access; domain-profile §5 G3; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-055 — [REQ-RPT-047]
  Given  : the service runs with a Report Store and an activated `main-db` connection
  When   : the Report Store's operations are exercised end to end
  Then   : 0 queries are sent through any host connection by the Report Store

### REQ-RPT-048 — No file opened
  Pattern    : ubiquitous
  Statement  : The system shall open no file of host storage and no uploaded file.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : File paths are validated by Document Access; the Report Store keeps no document and has no path to open.
  Source     : [KB:raw-idea.md §12] storage root; domain-profile §5 G5; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-056 — [REQ-RPT-048]
  Given  : a completed report holds a Check Document whose detail names the path "/data/att/1001/t.pdf"
  When   : the report is read
  Then   : the path is returned as text and no file is opened

### REQ-RPT-049 — No model call
  Pattern    : ubiquitous
  Statement  : The system shall call no model and give no model a tool or any access to stored reports.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : The LLM analyses and summarises inside the Check Engine only; it never reaches the record.
  Source     : [KB:raw-idea.md §12] "The LLM analyses and summarises"; domain-profile §5 G1; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-057 — [REQ-RPT-049]
  Given  : a comparison model is configured for the service
  When   : a Check run is created, completed, read, decided and purged
  Then   : the Report Store sends 0 requests to any model

### REQ-RPT-050 — Read filters bound as parameters
  Pattern    : ubiquitous
  Statement  : The system shall pass the service code, the request number and the Check identifier of every read to its own store as bound parameters, never as part of the query text.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : SQL is never built from free text, including the service's own store.
  Source     : [KB:raw-idea.md §12] "Query parameters are bound"; domain-profile §5 G4; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-058 — [REQ-RPT-050]
  Given  : a Check of request `1001` exists
  When   : the employee frontend lists the Checks of `scholarship-request` and request number `1001' OR '1'='1`
  Then   : 0 Checks and the total 0 are returned, and no other request's Check is returned

### REQ-RPT-051 — Incomplete failure refused
  Pattern    : unwanted
  Statement  : If a failure handed over by the Check Engine lacks its failure reason, its detail or its end time, then the system shall refuse to store it.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001
  Rationale  : Every FAILED Check carries exactly one reason and a detail text.
  Source     : POL-RPT-006; RULE-RPT-010; CON-CHK-003
  Priority   : —

#### AC-RPT-059 — [REQ-RPT-051]
  Given  : Check 543 is RUNNING
  When   : the Check Engine fails it with reason INTERNAL_ERROR and a blank detail
  Then   : Check 543 stays RUNNING and the call is refused with "The failure of Check 543 was not stored: detail is missing."

### REQ-RPT-052 — Purge deletes each Check run whole
  Pattern    : unwanted
  Statement  : If the deletion of any record of an expired Check run fails during the purge, then the system shall keep that Check run with all its records and continue with the next expired Check run.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A report is removed completely or not at all; one failure must not stop the purge.
  Source     : POL-RPT-020; ADR-RPT-010
  Priority   : —

#### AC-RPT-060 — [REQ-RPT-052]
  Given  : Checks 544 and 545 have expired and the deletion of a Finding of Check 544 fails
  When   : the purge runs
  Then   : Check 544 is still stored with all its records, Check 545 no longer exists, and the log counts 1 deleted Check run

## A5 — Business rules

### RULE-RPT-001 — A Check run is complete
  Scope      : ENT-RPT-001
  Trigger    : on create Check run
  Statement  : The system shall require a service code, a version number, a fetch mode, a request number, an employee identity, an initial status and a start time, each present and not blank, to create a Check run.
  Message    : The Check run was not stored: {field} is missing.
  Traces     : REQ-RPT-003
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.versionNumber, ENT-RPT-001.fetchMode, ENT-RPT-001.requestNumber, ENT-RPT-001.employeeId, ENT-RPT-001.checkStatus, ENT-RPT-001.startedAt
  Source     : POL-RPT-001; CON-CHK-006

### RULE-RPT-002 — Initial status agrees with the fetch mode
  Scope      : ENT-RPT-001
  Trigger    : on create Check run
  Statement  : The system shall require the initial status AWAITING_DOCUMENTS when the fetch mode is `manual` and RUNNING when it is `path` or `blob`.
  Message    : The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual. / The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS.
  Traces     : REQ-RPT-004
  Data source: ENT-RPT-001.fetchMode, ENT-RPT-001.checkStatus
  Source     : ADR-CHK-004; ADR-RPT-007

### RULE-RPT-003 — Status moves forward only
  Scope      : ENT-RPT-001
  Trigger    : on mark RUNNING, complete, fail
  Statement  : The system shall allow only the changes AWAITING_DOCUMENTS → RUNNING, RUNNING → RUNNING, RUNNING → COMPLETED, AWAITING_DOCUMENTS → FAILED and RUNNING → FAILED, and shall prevent every change from COMPLETED or FAILED.
  Message    : Check {checkId} has already ended; its status cannot change. / Check {checkId} is not running; it cannot be completed.
  Traces     : REQ-RPT-005, REQ-RPT-006, REQ-RPT-020
  Data source: ENT-RPT-001.checkStatus
  Source     : POL-RPT-003, POL-RPT-009; CON-CHK-001; ADR-RPT-002
  Test-Hint  : walk every pair of the four statuses

### RULE-RPT-004 — Report metadata agrees with its Check run
  Scope      : ENT-RPT-001
  Trigger    : on complete
  Statement  : The system shall require the report metadata to carry a comparison model and an end time not earlier than the start time, and its service code, version number, fetch mode, employee identity and start time to equal those stored on the Check run.
  Message    : The report of Check {checkId} was not stored: its metadata {field} {value} differs from the Check run ({stored}). / The report of Check {checkId} was not stored: {field} is missing.
  Traces     : REQ-RPT-013
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.versionNumber, ENT-RPT-001.fetchMode, ENT-RPT-001.employeeId, ENT-RPT-001.startedAt, ENT-RPT-001.comparisonModel, ENT-RPT-001.endedAt
  Source     : POL-RPT-004; domain-profile §5 G11; ADR-RPT-007

### RULE-RPT-005 — COMPLIANT only when fully verified
  Scope      : ENT-RPT-001
  Trigger    : on complete
  Statement  : The system shall prevent storing the Overall Status COMPLIANT when any finding of the report is not SATISFIED or the report has any unread service query.
  Message    : The report of Check {checkId} was not stored: COMPLIANT needs every finding SATISFIED and every service query read.
  Traces     : REQ-RPT-014
  Data source: ENT-RPT-001.overallStatus, ENT-RPT-002.findingOutcome, ENT-RPT-004.queryName
  Source     : CON-CHK-001; domain-profile §5 G6; ADR-RPT-007

### RULE-RPT-006 — Closed codes only
  Scope      : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003
  Trigger    : on create Check run, complete, fail
  Statement  : The system shall require every Check status, Overall Status, failure reason, fetch mode, source mode, finding outcome, document read status and unreadable reason to be a code of its closed list in A6.
  Message    : Not stored: `{value}` is not a code of {lookupKey}.
  Traces     : REQ-RPT-015
  Data source: ENT-RPT-001.checkStatus, ENT-RPT-001.overallStatus, ENT-RPT-001.failureReason, ENT-RPT-001.fetchMode, ENT-RPT-002.findingOutcome, ENT-RPT-003.sourceMode, ENT-RPT-003.readStatus, ENT-RPT-003.unreadableReason
  Source     : POL-RPT-005; CON-CHK-001 … CON-CHK-003; CON-DOC-001, CON-DOC-002

### RULE-RPT-007 — A finding is complete
  Scope      : ENT-RPT-002
  Trigger    : on complete
  Statement  : The system shall require every finding to carry a condition, an outcome, an evidence and a note, each present and not blank.
  Message    : The report of Check {checkId} was not stored: finding {position} has no {field}.
  Traces     : REQ-RPT-016
  Data source: ENT-RPT-002.conditionText, ENT-RPT-002.findingOutcome, ENT-RPT-002.evidence, ENT-RPT-002.note
  Source     : POL-RPT-004; CON-CHK-002; domain-profile §5 G10

### RULE-RPT-008 — Reason exactly on UNREADABLE
  Scope      : ENT-RPT-003
  Trigger    : on complete
  Statement  : The system shall require an unreadable reason on every UNREADABLE document outcome and prevent one on a READ or MISSING outcome, and shall require a document type and source mode on every outcome.
  Message    : The report of Check {checkId} was not stored: document {position} is UNREADABLE without a reason. / The report of Check {checkId} was not stored: document {position} is {readStatus} and cannot carry a reason.
  Traces     : REQ-RPT-017
  Data source: ENT-RPT-003.readStatus, ENT-RPT-003.unreadableReason, ENT-RPT-003.documentType, ENT-RPT-003.sourceMode
  Source     : CON-DOC-001

### RULE-RPT-009 — A request's Checks need both keys
  Scope      : ENT-RPT-001
  Trigger    : on list Checks of a request
  Statement  : The system shall require a service code and a request number, both present and not blank, to list the Checks of a request.
  Message    : Both a service code and a request number are needed to list Checks.
  Traces     : REQ-RPT-029
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.requestNumber
  Source     : ADR-RPT-005

### RULE-RPT-010 — A failure is complete
  Scope      : ENT-RPT-001
  Trigger    : on fail
  Statement  : The system shall require a failure reason, a detail not blank and an end time not earlier than the start time to store a failure.
  Message    : The failure of Check {checkId} was not stored: {field} is missing.
  Traces     : REQ-RPT-051
  Data source: ENT-RPT-001.failureReason, ENT-RPT-001.failureDetail, ENT-RPT-001.endedAt, ENT-RPT-001.startedAt
  Source     : POL-RPT-006; CON-CHK-003, CON-CHK-009

### RULE-RPT-011 — One decision per Check
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording an Employee Decision on a Check run that already holds one.
  Message    : Check {checkId} already has an Employee Decision.
  Traces     : REQ-RPT-033
  Data source: ENT-RPT-001.employeeDecision
  Source     : POL-RPT-014; ADR-RPT-003

### RULE-RPT-012 — Decision only on a completed Check
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording an Employee Decision on a Check run whose status is not COMPLETED.
  Message    : Check {checkId} is not completed; a decision can only be recorded on a completed Check.
  Traces     : REQ-RPT-034
  Data source: ENT-RPT-001.checkStatus
  Source     : POL-RPT-015; ADR-RPT-003

### RULE-RPT-013 — A decision is complete
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall require a decision code of EMPLOYEE_DECISION, a deciding employee's identity not blank and a yes / no value for execution through the Approval API to record an Employee Decision.
  Message    : The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. / The decision was not recorded: say whether it was executed through the Approval API.
  Traces     : REQ-RPT-035
  Data source: ENT-RPT-001.employeeDecision, ENT-RPT-001.decidedBy, ENT-RPT-001.approvalApiExecuted
  Source     : POL-RPT-013, POL-RPT-016; ADR-RPT-009

### RULE-RPT-014 — Approval API execution only with APPROVED
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording a decision REJECTED as executed through the Approval API.
  Message    : The decision was not recorded: only an APPROVED decision is executed through the Approval API.
  Traces     : REQ-RPT-039
  Data source: ENT-RPT-001.employeeDecision, ENT-RPT-001.approvalApiExecuted
  Source     : [KB:raw-idea.md §11]; ADR-RPT-009

### RULE-RPT-015 — Decision agreement needs a service code
  Scope      : ENT-RPT-001
  Trigger    : on read decision agreement
  Statement  : The system shall require a service code, present and not blank, to read the decision agreement.
  Message    : A service code is needed to read the decision agreement.
  Traces     : REQ-RPT-041
  Data source: ENT-RPT-001.serviceCode
  Source     : ADR-RPT-005

## A6 — Lookups

```yaml name=lookups
lookups:
  - {key: EMPLOYEE_DECISION, seeded: [APPROVED, REJECTED], open: false, values: [APPROVED, REJECTED], fields: [employeeDecision], entity: ENT-RPT-001, control: lookup, source: "domain-profile §7.1 Employee Decision 'approve / reject'; ADR-RPT-003"}
  - {key: CHECK_STATUS, seeded: [], open: false, values: [AWAITING_DOCUMENTS, RUNNING, COMPLETED, FAILED], fields: [checkStatus], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-001; by value through the Check result port"}
  - {key: OVERALL_STATUS, seeded: [], open: false, values: [COMPLIANT, NOT_COMPLIANT, NEEDS_MANUAL_REVIEW], fields: [overallStatus], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-001; profile closed enum"}
  - {key: CHECK_FAILURE_REASON, seeded: [], open: false, values: [TIMED_OUT, MODEL_UNAVAILABLE, MODEL_OUTPUT_INVALID, MODEL_NOT_PERMITTED, UPLOAD_WINDOW_EXPIRED, INTERRUPTED, INTERNAL_ERROR], fields: [failureReason], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-003"}
  - {key: FINDING_OUTCOME, seeded: [], open: false, values: [SATISFIED, NOT_SATISFIED, UNDETERMINED], fields: [findingOutcome], owner: CHK, entity: ENT-RPT-002, control: lookup, source: "consumed — CHK CON-CHK-002"}
  - {key: FETCH_MODE, seeded: [], open: false, values: [path, blob, manual], fields: [fetchMode, sourceMode], owner: DOC, entity: ENT-RPT-001, control: lookup, source: "consumed — DOC CON-DOC-002; profile closed enum; also ENT-RPT-003.sourceMode"}
  - {key: DOCUMENT_READ_STATUS, seeded: [], open: false, values: [READ, MISSING, UNREADABLE], fields: [readStatus], owner: DOC, entity: ENT-RPT-003, control: lookup, source: "consumed — DOC CON-DOC-001; by value through the Check result port"}
  - {key: UNREADABLE_REASON, seeded: [], open: false, values: [OUTSIDE_STORAGE_ROOT, NOT_FOUND, TOO_LARGE, UNSUPPORTED_FORMAT, READING_FAILED, OUT_OF_TIME, SOURCE_QUERY_FAILED, MODEL_NOT_PERMITTED], fields: [unreadableReason], owner: DOC, entity: ENT-RPT-003, control: lookup, source: "consumed — DOC CON-DOC-001"}
  - {key: SERVICE_CODE, seeded: [], open: true, values: [scholarship-request], fields: [serviceCode], owner: REG, entity: ENT-RPT-001, control: reference, source: "consumed — REG by value through CHK; never hardcoded, never a foreign key"}
  - {key: DOCUMENT_TYPE, seeded: [], open: true, values: [TRANSCRIPT, ID_CARD], fields: [documentType], owner: REG, entity: ENT-RPT-003, control: lookup, source: "consumed — REG by value through CHK"}
```

| Key | Labels (en) | Rationale |
|---|---|---|
| EMPLOYEE_DECISION | Approved · Rejected | Closed: the approve / reject decision of the glossary (ADR-RPT-003) |
| CHECK_STATUS, OVERALL_STATUS, CHECK_FAILURE_REASON, FINDING_OUTCOME | as CHK labels them | Consumed from CHK by value; never redefined |
| FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON | as DOC labels them | Consumed from DOC by value through CHK; never redefined |
| SERVICE_CODE, DOCUMENT_TYPE | as REG labels them | Open lists of the service registry; stored as text values |

RPT owns one lookup, EMPLOYEE_DECISION. Every consumed closed list backs an RPT field and is enforced by RULE-RPT-006 (P2 states the codes as CHECK constraints — ADR-CHK-016 hands them to RPT).

## A7 — Status lifecycle

Check status (CHECK_STATUS, CHK's list) as stored on ENT-RPT-001.checkStatus:

```
   create (fetch mode manual)    ──► AWAITING_DOCUMENTS ──mark RUNNING──► RUNNING
   create (fetch mode path|blob) ──────────────────────────────────────► RUNNING ──mark RUNNING (no change)──► RUNNING
                                                                          RUNNING ──complete──► COMPLETED  (final)
   AWAITING_DOCUMENTS ──fail──► FAILED  (final)
   RUNNING            ──fail──► FAILED  (final)
```

Constrained transitions: create → RULE-RPT-001, RULE-RPT-002; every change → RULE-RPT-003; complete → RULE-RPT-004 … RULE-RPT-008; fail → RULE-RPT-010. On COMPLETED the Employee Decision may be recorded once (RULE-RPT-011 … RULE-RPT-014); it is not a status. No approval flow: the Report Store never approves (REQ-RPT-037).

## A8 — Module dependencies
```yaml name=module-dependencies
consumes: []
```
RPT consumes no entity of another module. CHK promises no entity (its Active Check is PRIVATE — CON-CHK contract); RPT implements CHK's Check result port (CON-CHK-006 … CON-CHK-011), so the RPT → CHK edge is the platform edge (ADR-REG-002). The service code, version number, document type and DOC's codes are stored as values with no runtime read of REG or DOC (CON-DOC-001, CON-DOC-002; ADR-RPT-001).

| External service | Purpose | Integration kind |
|---|---|---|
| Check Engine (CHK, in-process) | creates, advances, completes and fails Check runs; reads one Check; lists unfinished Checks | calls RPT's implementation of the Check result port (ADR-REG-002, ADR-CHK-011) |
| Host Integration (INT, in-process) | hands over the Employee Decision | RPT's in-process decision operation (ADR-RPT-003, ADR-RPT-006) |
| Platform configuration | report retention period, purge schedule | read at start-up (ADR-RPT-010) |

# PART B — SCREEN REQUIREMENTS

Not applicable: RPT has no screen of its own (module-registry AUTO-DECISION; [KB:raw-idea.md §15 A1]). The employee reads reports and records decisions in the embedded frontend, which uses RPT's read operations and INT's decision operation.

## API expectations (module level — no screen carries them)
Base path : /api/v1/{resource}
Verbs     : GET only — RPT's HTTP surface is read-only (ADR-RPT-006); every write is in-process
Errors    : ProblemDetail (RFC 9457) → {type, title, status, detail, code}; in-process rejections are typed with the RULE messages above, which INT maps to ProblemDetail

| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read a Check and its report | GET | /api/v1/checks/{checkId} | checkId | Check with status and, once ended, report or failure | — | REQ-RPT-023 … REQ-RPT-027 |
| list the Checks of a request | GET | /api/v1/checks | serviceCode, requestNumber | up to 100 Checks newest first + total | RULE-RPT-009 | REQ-RPT-028 … REQ-RPT-031, REQ-RPT-050 |
| read the decision agreement of a service | GET | /api/v1/decision-agreement | serviceCode | rows (versionNumber, overallStatus, employeeDecision, count) | RULE-RPT-015 | REQ-RPT-040, REQ-RPT-041 |
| record an Employee Decision (in-process, called by INT) | — | — | checkId, employeeDecision, decidedBy, approvalApiExecuted | Check with the recorded decision or rejection | RULE-RPT-011 … RULE-RPT-014 | REQ-RPT-032 … REQ-RPT-039 |
| Check result port — create a Check run (implements CON-CHK-006) | — | — | serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt | checkId | RULE-RPT-001, RULE-RPT-002, RULE-RPT-006 | REQ-RPT-001 … REQ-RPT-004 |
| Check result port — mark RUNNING (implements CON-CHK-007) | — | — | checkId, runningSince | — | RULE-RPT-003 | REQ-RPT-005, REQ-RPT-006, REQ-RPT-007 |
| Check result port — complete a Check (implements CON-CHK-008) | — | — | checkId, overallStatus, findings, documentOutcomes, unreadQueries, metadata | — | RULE-RPT-003 … RULE-RPT-008 | REQ-RPT-008 … REQ-RPT-017, REQ-RPT-020 |
| Check result port — fail a Check (implements CON-CHK-009) | — | — | checkId, failureReason, detail, endedAt | — | RULE-RPT-003, RULE-RPT-006, RULE-RPT-010 | REQ-RPT-018, REQ-RPT-051 |
| Check result port — read one Check (implements CON-CHK-010) | — | — | checkId | checkId, status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt | — | REQ-RPT-021, REQ-RPT-007 |
| Check result port — list unfinished Checks (implements CON-CHK-011) | — | — | — | list of checkId, status, startedAt | — | REQ-RPT-022 |
| purge (scheduled, no caller) | — | — | platform configuration | count deleted (log) | — | REQ-RPT-042 … REQ-RPT-046, REQ-RPT-052 |

# STANDALONE

## Traceability matrix
| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-RPT-001 | REQ-RPT-001, REQ-RPT-002, REQ-RPT-003, REQ-RPT-004 | AC-RPT-001, AC-RPT-002, AC-RPT-003, AC-RPT-004, AC-RPT-005 | RULE-RPT-001, RULE-RPT-002 | ENT-RPT-001 | — |
| US-RPT-002 | REQ-RPT-004, REQ-RPT-005, REQ-RPT-006, REQ-RPT-007 | AC-RPT-006, AC-RPT-007, AC-RPT-008, AC-RPT-009, AC-RPT-010 | RULE-RPT-003 | ENT-RPT-001 | — |
| US-RPT-003 | REQ-RPT-008, REQ-RPT-009, REQ-RPT-010, REQ-RPT-011, REQ-RPT-012, REQ-RPT-013, REQ-RPT-014 | AC-RPT-011, AC-RPT-012, AC-RPT-013, AC-RPT-014, AC-RPT-015, AC-RPT-016, AC-RPT-017 | RULE-RPT-004, RULE-RPT-005 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-004 | REQ-RPT-014, REQ-RPT-015, REQ-RPT-016, REQ-RPT-017, REQ-RPT-018, REQ-RPT-051 | AC-RPT-017, AC-RPT-018, AC-RPT-019, AC-RPT-020, AC-RPT-021, AC-RPT-059 | RULE-RPT-005, RULE-RPT-006, RULE-RPT-007, RULE-RPT-008, RULE-RPT-010 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003 | — |
| US-RPT-005 | REQ-RPT-019, REQ-RPT-047, REQ-RPT-048, REQ-RPT-049 | AC-RPT-022, AC-RPT-055, AC-RPT-056, AC-RPT-057 | — | ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-006 | REQ-RPT-006, REQ-RPT-020 | AC-RPT-008, AC-RPT-009, AC-RPT-023 | RULE-RPT-003 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-007 | REQ-RPT-007, REQ-RPT-021, REQ-RPT-022 | AC-RPT-010, AC-RPT-024, AC-RPT-025, AC-RPT-026 | — | ENT-RPT-001 | — |
| US-RPT-008 | REQ-RPT-023, REQ-RPT-024, REQ-RPT-025, REQ-RPT-026, REQ-RPT-027 | AC-RPT-027, AC-RPT-028, AC-RPT-029, AC-RPT-030, AC-RPT-031, AC-RPT-032 | — | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-009 | REQ-RPT-028, REQ-RPT-029, REQ-RPT-030, REQ-RPT-031, REQ-RPT-050 | AC-RPT-033, AC-RPT-034, AC-RPT-035, AC-RPT-036, AC-RPT-037, AC-RPT-058 | RULE-RPT-009 | ENT-RPT-001, ENT-RPT-002 | — |
| US-RPT-010 | REQ-RPT-032, REQ-RPT-033, REQ-RPT-034, REQ-RPT-035, REQ-RPT-036, REQ-RPT-037, REQ-RPT-038, REQ-RPT-039 | AC-RPT-038, AC-RPT-039, AC-RPT-040, AC-RPT-041, AC-RPT-042, AC-RPT-043, AC-RPT-044, AC-RPT-045, AC-RPT-046 | RULE-RPT-011, RULE-RPT-012, RULE-RPT-013, RULE-RPT-014 | ENT-RPT-001 | — |
| US-RPT-011 | REQ-RPT-040, REQ-RPT-041 | AC-RPT-047, AC-RPT-048, AC-RPT-049 | RULE-RPT-015 | ENT-RPT-001 | — |
| US-RPT-012 | REQ-RPT-042, REQ-RPT-043, REQ-RPT-044, REQ-RPT-045, REQ-RPT-046, REQ-RPT-052 | AC-RPT-050, AC-RPT-051, AC-RPT-052, AC-RPT-053, AC-RPT-054, AC-RPT-060 | — | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |

Raw-idea §12 guardrails at RPT's surface (AIAS-1, ADR-RPT-008): (1) LLM analyses only → REQ-RPT-049 · (2) approval only on the employee's action → REQ-RPT-037, REQ-RPT-039 · (3) read-only host access → REQ-RPT-047 · (4) bound parameters → REQ-RPT-050 · (5) storage root → REQ-RPT-048 · (6) nothing skipped silently → REQ-RPT-011, REQ-RPT-012, REQ-RPT-014 · (7) content is data → REQ-RPT-019, REQ-RPT-027 · (8) limits → REQ-RPT-031 · (9) nothing carried between Checks → REQ-RPT-030.

## Decisions applied
| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| ADR-REG-001 | RPT owns the Check run, findings and Check Document rows | P0 (REG) | ACCEPTED by owner |
| ADR-REG-002 | CHK declares the result port; RPT implements it | P0 (REG) | ACCEPTED by owner |
| ADR-REG-006 | Limits are platform configuration (pattern for the retention period) | P0 (REG) | ACCEPTED by owner |
| ADR-CHK-001 | Closed lists carried by value; RPT stores the codes | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-002 | Overall Status derivation (basis of RULE-RPT-005) | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-005 | FAILED with a reason and no Overall Status; start-up closes unfinished Checks | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-007 | Several independent Checks per request | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-011 | The six result port operations | P1 (CHK) | ACCEPTED |
| ADR-CHK-014 | Unread queries as query name + detail | P1 (CHK) | ACCEPTED |
| ADR-CHK-015 | Every Check ends exactly once on CHK's side | P1 (CHK) | ACCEPTED |
| ADR-CHK-017 | Pattern: a module's own HTTP surface is read-only | P3.1 (CHK) | ACCEPTED |
| ADR-DOC-002, ADR-DOC-007 | Read status and unreadable reason lists | DOC | ACCEPTED by owner |
| ADR-DOC-008 | Fetched content lives only for the fetching call | DOC | ACCEPTED by owner |
| ADR-DOC-011 | Pattern: read-only HTTP surface, writes in-process | DOC | ACCEPTED |
| ADR-RPT-001 | Result port implemented; four records; stored whole; codes by value | P0 | Confirmed at prd-approval |
| ADR-RPT-002 | Forward-only status; ended report final | P0 | Confirmed at prd-approval |
| ADR-RPT-003 | Employee Decision on the Check Run, once, COMPLETED only, Approval API flag | P0 | Confirmed at prd-approval |
| ADR-RPT-004 | Retention period and hard-delete purge | P0 | Confirmed at prd-approval |
| ADR-RPT-005 | Three reads; no viewer restriction | P0 | Confirmed at prd-approval |
| ADR-RPT-006 | RPT serves its three reads over HTTP itself; all writes in-process | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-007 | Store-side shape guards: initial status vs fetch mode, metadata agreement, COMPLIANT consistency | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-008 | Every §12 guardrail stated at RPT's surface; listing capped at 100 with a total | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-009 | Decision inputs; recording time is RPT's clock; Approval API flag only with APPROVED | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-010 | Retention and purge configuration: whole days, schedule default daily 02:00, one transaction per Check run, logged | P1 (this stage) | ACCEPTED — non-breaking |
| DEFAULT — purge schedule daily at 02:00 server time | The purge runs once a day at 02:00 | ADR-RPT-010; domain best practice | Override: set the purge schedule in the platform configuration |
| DEFAULT — age measured from the end time | A Check run's age for the purge counts from endedAt | ADR-RPT-004 | Override: count from startedAt |
| DEFAULT — listing cap 100 | The Checks of a request are returned 100 at most, newest first, with the total | ADR-RPT-008 | Override: add paging in a later version |
| DEFAULT — decidedAt is the recording time | The decision time is RPT's clock when INT hands the decision over | ADR-RPT-009 | Override: INT passes the employee's confirmation time |

## Access summary
| Role | Screens | Operations |
|---|---|---|
| Employee | none in RPT (embedded frontend) | reads a Check and its report, lists the Checks of a request (RPT HTTP); records the decision (INT → RPT in-process) |
| Service Administrator | none | reads the decision agreement (RPT HTTP); sets the report retention period and purge schedule (platform configuration) |
| CHK, INT (in-process) | — | CHK: the six result port operations · INT: record an Employee Decision |
Caller authentication and who may view stored reports are deferred (raw-idea A2, domain-profile D4); no role check is specified in this version.
══════════════════════════════════════════════════════════════════

<<<END INPUT>>>

<<<INPUT: prd>>>
# PRD — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module          : RPT     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : 12   Policies covered : 23/23   Deferred : 0
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

## USER STORIES

US-RPT-001
  Title          : Every Check I start is on record at once
  Story          : As an employee, I need every Check I start from the host screen to be on record straight away — for the service and request I asked about, under my identity as the host knows me, on the service package version it runs on — so that I can follow it by its identifier and later see exactly what it was built on.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-001, POL-RPT-002
  Source         : [KB:raw-idea.md §5] "the host starts it and then polls"; §9 `CHECK_RUN`; §8 "passes the employee's identity, which is recorded with the check"; profile `conventions.identifiers`; ADR-RPT-001
  Status         : DRAFT

US-RPT-002
  Title          : The status I poll tells the truth
  Story          : As an employee, I need the status of a Check I am following to move only forward — waiting for my documents, running, then completed or failed once — so that a status I have seen as ended never changes under me.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-003
  Source         : [KB:raw-idea.md §5] "the host starts it and then polls for the result"; CON-CHK-001; ADR-RPT-002
  Status         : DRAFT

US-RPT-003
  Title          : The whole report, or none of it
  Story          : As an employee, I need a completed report to be kept complete — the Overall Status together with every finding, every document read, missing or unreadable, every service query that could not be read and the metadata — so that I never see a result without the findings that justify it.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-004, POL-RPT-007
  Source         : [KB:raw-idea.md §7] report model; §12 "Anything that could not be read appears in the report. It is never skipped silently"; domain-profile §5 G6; ADR-RPT-001
  Status         : DRAFT

US-RPT-004
  Title          : Results I can read the same way every time
  Story          : As an employee, I need every stored Check to speak the service's fixed vocabulary — a completed Check with exactly one Overall Status, a failed Check with its reason and no result, and every finding and document in their fixed outcomes — so that every report reads the same way whatever the service.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-005, POL-RPT-006
  Source         : [KB:raw-idea.md §7] "The report has a fixed structure for every service"; profile `conventions.lookups`; CON-CHK-001 … CON-CHK-003; CON-DOC-001, CON-DOC-002
  Status         : DRAFT

US-RPT-005
  Title          : Evidence kept, not the request's files
  Story          : As a service administrator, I need the Report Store to keep the evidence, notes and outcomes of a report but never the content of the request's documents or the data its queries returned, so that the service holds no more of a citizen's request than the report needs.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-008
  Source         : CON-CHK-008 "Carries no document content"; ADR-DOC-008; domain-profile §5 G9, G10
  Status         : DRAFT

US-RPT-006
  Title          : The report I decided on stays as it was
  Story          : As an employee, I need a report that has ended to stay exactly as it was when I read it, so that my decision always stands beside the report it was based on.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-009
  Source         : [KB:raw-idea.md §9] decision beside the result; §11 "records the report the approval was based on"; ADR-RPT-002
  Status         : DRAFT

US-RPT-007
  Title          : No Check left hanging
  Story          : As an employee, I need the Check Engine to be able to find every Check that is still waiting or running, so that a Check interrupted by a restart or abandoned before its uploads were confirmed is ended and shown as failed instead of running for ever.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-010
  Source         : CON-CHK-011; ADR-CHK-004; ADR-CHK-005
  Status         : DRAFT

US-RPT-008
  Title          : See a Check and its report
  Story          : As an employee, I need to see, in the frontend embedded in my host screen, the status of a Check and, once it has ended, its report with each finding beside its evidence, so that I can verify every finding before I decide.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-011
  Source         : [KB:raw-idea.md §7] "Every finding carries its evidence so the employee can verify it"; §8 `GET /checks/{id}`; §15 A1; domain-profile §5 G10
  Status         : DRAFT

US-RPT-009
  Title          : See all the Checks of a request
  Story          : As an employee, I need to see all the Checks of the request on my screen, newest first, each standing on its own, so that I know whether the request was checked before and what each Check found.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-012, POL-RPT-023
  Source         : [KB:raw-idea.md §15 A1] "the checks of a request"; §12 "No data is carried from one check to another"; ADR-CHK-007; ADR-RPT-005
  Status         : DRAFT

US-RPT-010
  Title          : My decision kept beside the result
  Story          : As an employee, I need my approve / reject decision on a completed Check to be kept beside its result — once, under my identity, with the time, and noting whether the service carried it out through the Approval API — while the service itself never decides or approves anything, so that the record shows what I decided, on which report, and that the decision was mine.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-RPT-013, POL-RPT-014, POL-RPT-015, POL-RPT-016, POL-RPT-018
  Source         : [KB:raw-idea.md §1] "The employee stays the decision maker"; §9; §11 approval options; §12 "Approval is executed only as a result of the employee's action"; domain-profile §5 G2; ADR-RPT-003
  Status         : DRAFT

US-RPT-011
  Title          : Know where the service and the employees disagree
  Story          : As a service administrator, I need to see, for a service and each of its package versions, how the decided Checks of each Overall Status were approved or rejected, so that I know where the service knowledge or a check needs attention.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-017
  Source         : [KB:raw-idea.md §9] "gives a direct measure of accuracy: where the two disagree, the service knowledge or a check needs attention"; domain-profile §5 G11; ADR-RPT-005
  Status         : DRAFT

US-RPT-012
  Title          : Reports kept as long as the request, then removed
  Story          : As a service administrator, I need each report kept for as long as the host keeps the request it verified — a period I configure — and then removed completely, never while its Check is still unfinished and never when no period is set, so that reports are neither lost early nor kept for ever.
  Priority       : —
  Success metric : —
  Traces         : POL-RPT-019, POL-RPT-020, POL-RPT-021, POL-RPT-022
  Source         : domain-profile §8 D4; profile `delete_semantics: hard`; ADR-RPT-004
  Status         : DRAFT

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-RPT-001 | POL-RPT-001, POL-RPT-002 | [KB:raw-idea.md §5, §8, §9]; profile conventions.identifiers |
| US-RPT-002 | POL-RPT-003 | [KB:raw-idea.md §5]; CON-CHK-001; ADR-RPT-002 |
| US-RPT-003 | POL-RPT-004, POL-RPT-007 | [KB:raw-idea.md §7, §12]; G6 |
| US-RPT-004 | POL-RPT-005, POL-RPT-006 | [KB:raw-idea.md §7]; profile conventions.lookups |
| US-RPT-005 | POL-RPT-008 | CON-CHK-008; G9, G10 |
| US-RPT-006 | POL-RPT-009 | [KB:raw-idea.md §9, §11]; ADR-RPT-002 |
| US-RPT-007 | POL-RPT-010 | CON-CHK-011; ADR-CHK-005 |
| US-RPT-008 | POL-RPT-011 | [KB:raw-idea.md §7, §8, §15 A1] |
| US-RPT-009 | POL-RPT-012, POL-RPT-023 | [KB:raw-idea.md §12, §15 A1]; ADR-CHK-007 |
| US-RPT-010 | POL-RPT-013, POL-RPT-014, POL-RPT-015, POL-RPT-016, POL-RPT-018 | [KB:raw-idea.md §1, §9, §11, §12]; G2 |
| US-RPT-011 | POL-RPT-017 | [KB:raw-idea.md §9] |
| US-RPT-012 | POL-RPT-019, POL-RPT-020, POL-RPT-021, POL-RPT-022 | domain-profile D4 |

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Which roles the RPT stories speak for | The employee (follows Checks, reads reports, takes the decision) and the service administrator (retention, accuracy measure, data held); host systems, CHK and INT are callers of RPT, not story roles — the same choice as the CHK and DOC PRDs | yes — owner standing instruction "do all with recommended" (prd-approval) | domain-profile §7.1 (Employee, Service Administrator); CHK PRD decision 1 |
| 2 | Which stories carry a priority | HIGH for the stories of report integrity and visibility the employee relies on (US-RPT-001, US-RPT-002, US-RPT-003, US-RPT-006, US-RPT-008) and for the decision record (US-RPT-010, G2); every other story "—" | yes — owner standing instruction (prd-approval) | [KB:raw-idea.md §1, §7, §12] |
| 3 | Whether the accuracy measure is a story of v1 or left to ad-hoc queries | A story of v1 (US-RPT-011): the raw idea names it as the reason the decision is stored beside the result (ADR-RPT-005) | yes — owner standing instruction (prd-approval) | [KB:raw-idea.md §9] |
| 4 | Whether "who may view stored reports" becomes a story | No — deferred with caller authentication (D4, A2); kept as a scope exception of the business policies, not a DEFERRED story, as the owner already deferred it | yes — owner D4, A2 | domain-profile §8 D4, D7 |

## DEFERRED
| US | Reason | Activation trigger |
|---|---|---|
| None | No RPT story is deferred. Out-of-scope items (viewer restriction, changing a decision, per-service retention, archiving) stay in the RPT SCOPE EXCEPTIONS, not as stories | — |

## APPROVAL
Approved by : —   Date : —
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════

<<<END INPUT>>>

<<<INPUT: api-spec>>>
openapi: 3.1.0
info:
  title: Report Store (RPT) API
  version: 1.0.0
  description: 'Derived from backend-execution-plan-rpt.md (API-RPT-001 … API-RPT-003). Read-only; the Check result port and
    the Employee Decision are in-process (ADR-RPT-006). No security scheme: caller authentication is deferred (raw-idea A2).'
paths:
  /api/v1/checks/{checkId}:
    get:
      operationId: readCheck
      summary: Read a Check and its report
      x-api-id: API-RPT-001
      x-traces:
      - REQ-RPT-023
      - REQ-RPT-024
      - REQ-RPT-025
      - REQ-RPT-026
      - REQ-RPT-027
      - DBF-RPT-001
      - DBF-RPT-002
      - DBF-RPT-003
      - DBF-RPT-004
      - DBF-RPT-005
      - DBF-RPT-006
      - DBF-RPT-007
      - DBF-RPT-008
      - DBF-RPT-009
      - DBF-RPT-010
      - DBF-RPT-011
      - DBF-RPT-012
      - DBF-RPT-013
      - DBF-RPT-014
      - DBF-RPT-015
      - DBF-RPT-016
      - DBF-RPT-017
      - DBF-RPT-018
      - DBF-RPT-022
      - DBF-RPT-023
      - DBF-RPT-024
      - DBF-RPT-025
      - DBF-RPT-026
      - DBF-RPT-031
      - DBF-RPT-032
      - DBF-RPT-033
      - DBF-RPT-034
      - DBF-RPT-035
      - DBF-RPT-036
      - DBF-RPT-041
      - DBF-RPT-042
      - DBF-RPT-043
      parameters:
      - name: checkId
        in: path
        required: true
        description: The Check identifier (DBF-RPT-001)
        schema:
          type: integer
          format: int64
      responses:
        '200':
          description: The Check with, once COMPLETED, its report
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CheckReport'
        '400':
          description: The checkId path parameter is not a number
          content:
            application/problem+json:
              schema: &id001
                $ref: '#/components/schemas/ProblemDetail'
          x-error-codes:
          - RPT-400-CHECK-ID-INVALID
        '404':
          description: No Check Run exists for the checkId — never created or purged
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-404-CHECK-NOT-FOUND
        '500':
          description: Unexpected server failure
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-500
  /api/v1/checks:
    get:
      operationId: listChecksOfRequest
      summary: List the Checks of a request
      x-api-id: API-RPT-002
      x-traces:
      - REQ-RPT-028
      - REQ-RPT-029
      - REQ-RPT-030
      - REQ-RPT-031
      - REQ-RPT-050
      - DBF-RPT-001
      - DBF-RPT-002
      - DBF-RPT-005
      - DBF-RPT-007
      - DBF-RPT-008
      - DBF-RPT-010
      - DBF-RPT-011
      - DBF-RPT-015
      parameters:
      - name: serviceCode
        in: query
        required: true
        description: DBF-RPT-002, matched exactly
        schema: &id002
          type: string
          maxLength: 100
      - name: requestNumber
        in: query
        required: true
        description: DBF-RPT-005, matched exactly as sent
        schema: *id002
      responses:
        '200':
          description: At most 100 Checks, newest first, with the total
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ChecksOfRequest'
        '400':
          description: serviceCode or requestNumber absent or blank
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-400-REQUEST-KEYS-MISSING
        '500':
          description: Unexpected server failure
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-500
  /api/v1/decision-agreement:
    get:
      operationId: readDecisionAgreement
      summary: Read the decision agreement of a service
      x-api-id: API-RPT-003
      x-traces:
      - REQ-RPT-040
      - REQ-RPT-041
      - DBF-RPT-002
      - DBF-RPT-003
      - DBF-RPT-011
      - DBF-RPT-015
      parameters:
      - name: serviceCode
        in: query
        required: true
        description: DBF-RPT-002
        schema: *id002
      responses:
        '200':
          description: Counts of decided Checks per version, Overall Status and Employee Decision
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/AgreementRow'
        '400':
          description: serviceCode absent or blank
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-400-SERVICE-CODE-MISSING
        '500':
          description: Unexpected server failure
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-500
components:
  schemas:
    FindingView:
      type: object
      required:
      - position
      - condition
      - outcome
      - evidence
      - note
      properties:
        position:
          type: integer
          description: DBF-RPT-022
        condition:
          type: string
          description: DBF-RPT-023
        outcome:
          type: string
          enum:
          - SATISFIED
          - NOT_SATISFIED
          - UNDETERMINED
          maxLength: 30
          description: DBF-RPT-024 — FINDING_OUTCOME
        evidence:
          type: string
          description: DBF-RPT-025 — returned exactly as stored, as data
        note:
          type: string
          description: DBF-RPT-026
    DocumentView:
      type: object
      required:
      - position
      - documentType
      - sourceMode
      - readStatus
      properties:
        position:
          type: integer
          description: DBF-RPT-031
        documentType:
          type: string
          maxLength: 100
          description: DBF-RPT-032
        sourceMode:
          type: string
          enum: &id003
          - path
          - blob
          - manual
          maxLength: 10
          description: DBF-RPT-033 — FETCH_MODE
        readStatus:
          type: string
          enum:
          - READ
          - MISSING
          - UNREADABLE
          maxLength: 30
          description: DBF-RPT-034 — DOCUMENT_READ_STATUS
        unreadableReason:
          type:
          - string
          - 'null'
          enum:
          - OUTSIDE_STORAGE_ROOT
          - NOT_FOUND
          - TOO_LARGE
          - UNSUPPORTED_FORMAT
          - READING_FAILED
          - OUT_OF_TIME
          - SOURCE_QUERY_FAILED
          - MODEL_NOT_PERMITTED
          - null
          maxLength: 30
          description: DBF-RPT-035 — UNREADABLE_REASON, UNREADABLE only
        detail:
          type:
          - string
          - 'null'
          description: DBF-RPT-036
    UnreadQueryView:
      type: object
      required:
      - position
      - queryName
      - detail
      properties:
        position:
          type: integer
          description: DBF-RPT-041
        queryName:
          type: string
          maxLength: 100
          description: DBF-RPT-042
        detail:
          type: string
          description: DBF-RPT-043
    DecisionView:
      type: object
      required:
      - employeeDecision
      - decidedBy
      - decidedAt
      - approvalApiExecuted
      properties:
        employeeDecision:
          type: string
          enum: &id005
          - APPROVED
          - REJECTED
          maxLength: 30
          description: DBF-RPT-015 — EMPLOYEE_DECISION
        decidedBy:
          type: string
          maxLength: 100
          description: DBF-RPT-016 — exactly as sent
        decidedAt:
          type: string
          format: date-time
          description: DBF-RPT-017
        approvalApiExecuted:
          type: boolean
          description: DBF-RPT-018
    CheckReport:
      type: object
      required:
      - checkId
      - status
      - serviceCode
      - versionNumber
      - fetchMode
      - requestNumber
      - employeeId
      - startedAt
      - findings
      - documents
      - unreadQueries
      properties:
        checkId:
          type: integer
          format: int64
          description: DBF-RPT-001
        status:
          type: string
          enum: &id004
          - AWAITING_DOCUMENTS
          - RUNNING
          - COMPLETED
          - FAILED
          maxLength: 30
          description: DBF-RPT-007 — CHECK_STATUS
        serviceCode:
          type: string
          maxLength: 100
          description: DBF-RPT-002
        versionNumber:
          type: integer
          description: DBF-RPT-003
        fetchMode:
          type: string
          enum: *id003
          maxLength: 10
          description: DBF-RPT-004 — FETCH_MODE
        requestNumber:
          type: string
          maxLength: 100
          description: DBF-RPT-005 — exactly as sent
        employeeId:
          type: string
          maxLength: 100
          description: DBF-RPT-006 — exactly as sent
        startedAt:
          type: string
          format: date-time
          description: DBF-RPT-008
        runningSince:
          type:
          - string
          - 'null'
          format: date-time
          description: DBF-RPT-009
        endedAt:
          type:
          - string
          - 'null'
          format: date-time
          description: DBF-RPT-010
        overallStatus:
          type:
          - string
          - 'null'
          enum:
          - COMPLIANT
          - NOT_COMPLIANT
          - NEEDS_MANUAL_REVIEW
          - null
          maxLength: 30
          description: DBF-RPT-011 — OVERALL_STATUS, COMPLETED only
        comparisonModel:
          type:
          - string
          - 'null'
          maxLength: 200
          description: DBF-RPT-012 — COMPLETED only
        failureReason:
          type:
          - string
          - 'null'
          enum:
          - TIMED_OUT
          - MODEL_UNAVAILABLE
          - MODEL_OUTPUT_INVALID
          - MODEL_NOT_PERMITTED
          - UPLOAD_WINDOW_EXPIRED
          - INTERRUPTED
          - INTERNAL_ERROR
          - null
          maxLength: 30
          description: DBF-RPT-013 — CHECK_FAILURE_REASON, FAILED only
        failureDetail:
          type:
          - string
          - 'null'
          description: DBF-RPT-014 — FAILED only
        findings:
          type: array
          items:
            $ref: '#/components/schemas/FindingView'
        documents:
          type: array
          items:
            $ref: '#/components/schemas/DocumentView'
        unreadQueries:
          type: array
          items:
            $ref: '#/components/schemas/UnreadQueryView'
        decision:
          oneOf:
          - $ref: '#/components/schemas/DecisionView'
          - type: 'null'
    CheckSummary:
      type: object
      required:
      - checkId
      - status
      - startedAt
      properties:
        checkId:
          type: integer
          format: int64
          description: DBF-RPT-001
        status:
          type: string
          enum: *id004
          maxLength: 30
          description: DBF-RPT-007
        overallStatus:
          type:
          - string
          - 'null'
          enum:
          - COMPLIANT
          - NOT_COMPLIANT
          - NEEDS_MANUAL_REVIEW
          - null
          maxLength: 30
          description: DBF-RPT-011
        startedAt:
          type: string
          format: date-time
          description: DBF-RPT-008
        endedAt:
          type:
          - string
          - 'null'
          format: date-time
          description: DBF-RPT-010
        employeeDecision:
          type:
          - string
          - 'null'
          enum:
          - APPROVED
          - REJECTED
          - null
          maxLength: 30
          description: DBF-RPT-015
    ChecksOfRequest:
      type: object
      required:
      - total
      - checks
      properties:
        total:
          type: integer
          description: every Check of the service code and request number
        checks:
          type: array
          maxItems: 100
          items:
            $ref: '#/components/schemas/CheckSummary'
    AgreementRow:
      type: object
      required:
      - versionNumber
      - overallStatus
      - employeeDecision
      - count
      properties:
        versionNumber:
          type: integer
          description: DBF-RPT-003
        overallStatus:
          type: string
          enum:
          - COMPLIANT
          - NOT_COMPLIANT
          - NEEDS_MANUAL_REVIEW
          maxLength: 30
          description: DBF-RPT-011
        employeeDecision:
          type: string
          enum: *id005
          maxLength: 30
          description: DBF-RPT-015
        count:
          type: integer
          minimum: 1
    ProblemDetail:
      type: object
      required:
      - type
      - title
      - status
      - code
      properties:
        type:
          type: string
        title:
          type: string
        status:
          type: integer
        detail:
          type: string
        code:
          type: string

<<<END INPUT>>>

<<<INPUT: registry-srs>>>
## REGISTRY — P1 — RPT v1

### Entities
| ENT | Name | Kind | Ownership | Status |
|---|---|---|---|---|
| ENT-RPT-001 | Check Run | transactional | SHARED (owner) | REGISTERED |
| ENT-RPT-002 | Finding | transactional | PRIVATE | REGISTERED |
| ENT-RPT-003 | Check Document | transactional | PRIVATE | REGISTERED |
| ENT-RPT-004 | Unread Query | transactional | PRIVATE | REGISTERED |

### Consumed
None — `consumes: []` (RPT implements CHK's Check result port; codes stored by value; ADR-RPT-001).

### Lookups owned
| Key | ENT | Values |
|---|---|---|
| EMPLOYEE_DECISION | ENT-RPT-001 | 2 |

### Lookups consumed
| Key | Owner |
|---|---|
| CHECK_STATUS | CHK |
| OVERALL_STATUS | CHK |
| CHECK_FAILURE_REASON | CHK |
| FINDING_OUTCOME | CHK |
| FETCH_MODE | DOC |
| DOCUMENT_READ_STATUS | DOC |
| UNREADABLE_REASON | DOC |
| SERVICE_CODE | REG |
| DOCUMENT_TYPE | REG |

### Screens
None — no SCR-REQ in this version (RPT has no screen).

### Requirements
REQ count 52 · AC count 60 · RULE count 15 · last sequence per atom (REQ: 52, AC: 60, ENT: 4, RULE: 15, SCR-REQ: 0)

| REQ | AC |
|---|---|
| REQ-RPT-001 | AC-RPT-001 |
| REQ-RPT-002 | AC-RPT-002 |
| REQ-RPT-003 | AC-RPT-003 |
| REQ-RPT-004 | AC-RPT-004, AC-RPT-005 |
| REQ-RPT-005 | AC-RPT-006, AC-RPT-007 |
| REQ-RPT-006 | AC-RPT-008, AC-RPT-009 |
| REQ-RPT-007 | AC-RPT-010 |
| REQ-RPT-008 | AC-RPT-011 |
| REQ-RPT-009 | AC-RPT-012 |
| REQ-RPT-010 | AC-RPT-013 |
| REQ-RPT-011 | AC-RPT-014 |
| REQ-RPT-012 | AC-RPT-015 |
| REQ-RPT-013 | AC-RPT-016 |
| REQ-RPT-014 | AC-RPT-017 |
| REQ-RPT-015 | AC-RPT-018 |
| REQ-RPT-016 | AC-RPT-019 |
| REQ-RPT-017 | AC-RPT-020 |
| REQ-RPT-018 | AC-RPT-021 |
| REQ-RPT-019 | AC-RPT-022 |
| REQ-RPT-020 | AC-RPT-023 |
| REQ-RPT-021 | AC-RPT-024 |
| REQ-RPT-022 | AC-RPT-025, AC-RPT-026 |
| REQ-RPT-023 | AC-RPT-027, AC-RPT-028 |
| REQ-RPT-024 | AC-RPT-029 |
| REQ-RPT-025 | AC-RPT-030 |
| REQ-RPT-026 | AC-RPT-031 |
| REQ-RPT-027 | AC-RPT-032 |
| REQ-RPT-028 | AC-RPT-033, AC-RPT-034 |
| REQ-RPT-029 | AC-RPT-035 |
| REQ-RPT-030 | AC-RPT-036 |
| REQ-RPT-031 | AC-RPT-037 |
| REQ-RPT-032 | AC-RPT-038 |
| REQ-RPT-033 | AC-RPT-039 |
| REQ-RPT-034 | AC-RPT-040 |
| REQ-RPT-035 | AC-RPT-041, AC-RPT-042 |
| REQ-RPT-036 | AC-RPT-043 |
| REQ-RPT-037 | AC-RPT-044 |
| REQ-RPT-038 | AC-RPT-045 |
| REQ-RPT-039 | AC-RPT-046 |
| REQ-RPT-040 | AC-RPT-047, AC-RPT-048 |
| REQ-RPT-041 | AC-RPT-049 |
| REQ-RPT-042 | AC-RPT-050 |
| REQ-RPT-043 | AC-RPT-051 |
| REQ-RPT-044 | AC-RPT-052 |
| REQ-RPT-045 | AC-RPT-053 |
| REQ-RPT-046 | AC-RPT-054 |
| REQ-RPT-047 | AC-RPT-055 |
| REQ-RPT-048 | AC-RPT-056 |
| REQ-RPT-049 | AC-RPT-057 |
| REQ-RPT-050 | AC-RPT-058 |
| REQ-RPT-051 | AC-RPT-059 |
| REQ-RPT-052 | AC-RPT-060 |

| RULE | Traces |
|---|---|
| RULE-RPT-001 | REQ-RPT-003 |
| RULE-RPT-002 | REQ-RPT-004 |
| RULE-RPT-003 | REQ-RPT-005, REQ-RPT-006, REQ-RPT-020 |
| RULE-RPT-004 | REQ-RPT-013 |
| RULE-RPT-005 | REQ-RPT-014 |
| RULE-RPT-006 | REQ-RPT-015 |
| RULE-RPT-007 | REQ-RPT-016 |
| RULE-RPT-008 | REQ-RPT-017 |
| RULE-RPT-009 | REQ-RPT-029 |
| RULE-RPT-010 | REQ-RPT-051 |
| RULE-RPT-011 | REQ-RPT-033 |
| RULE-RPT-012 | REQ-RPT-034 |
| RULE-RPT-013 | REQ-RPT-035 |
| RULE-RPT-014 | REQ-RPT-039 |
| RULE-RPT-015 | REQ-RPT-041 |

### Decisions
ADR-RPT-006, ADR-RPT-007, ADR-RPT-008, ADR-RPT-009, ADR-RPT-010 (new, ACCEPTED); applied ADR-RPT-001 … ADR-RPT-005, ADR-REG-001, ADR-REG-002, ADR-REG-006, ADR-CHK-001, ADR-CHK-002, ADR-CHK-005, ADR-CHK-007, ADR-CHK-011, ADR-CHK-014, ADR-CHK-015, ADR-CHK-017, ADR-DOC-002, ADR-DOC-007, ADR-DOC-008, ADR-DOC-011. BLOCKED: none.

### Event
"P1 completed: RPT v1 — REQ 52 · AC 60 · ENT 4 · RULE 15 · SCR-REQ 0 · ADR 5"

<<<END INPUT>>>

<<<INPUT: registry-exec-be>>>
## REGISTRY — P3.1 — RPT v1

ID RANGES        API-RPT-001, API-RPT-002, API-RPT-003 · QR-RPT-001, QR-RPT-002, QR-RPT-003, QR-RPT-004, QR-RPT-005, QR-RPT-006, QR-RPT-007
ENTITIES / TABLES bound: ENT-RPT-001 RPT_CHECK_RUN · ENT-RPT-002 RPT_FINDING · ENT-RPT-003 RPT_CHECK_DOCUMENT · ENT-RPT-004 RPT_UNREAD_QUERY · lookups reused: CHECK_STATUS, OVERALL_STATUS, FINDING_OUTCOME, CHECK_FAILURE_REASON (Check Engine enums, by value), FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON (Document Access enums, by value), SERVICE_CODE, DOCUMENT_TYPE (strings) · new: EMPLOYEE_DECISION
INTEGRATION      none — 0 XM (RPT implements the Check Engine's result port; no dependency on Check Engine data)
CATALOG          5 codes (RPT-400-CHECK-ID-INVALID, RPT-404-CHECK-NOT-FOUND, RPT-400-REQUEST-KEYS-MISSING, RPT-400-SERVICE-CODE-MISSING, RPT-500) · 16 in-process rejection codes · ar messages PENDING ADR-RPT-013
API DOCUMENT     api-spec-rpt.yaml · operations 3 = API blocks 3 · error responses 5 = catalog rows 5
ALIGN            verdict as stamped by the orchestrator
ADRs             ADR-RPT-012 (ACCEPTED), ADR-RPT-013 (ACCEPTED)
CONTRACT         Honours: CON-RPT-001, CON-RPT-002, CON-RPT-003, CON-RPT-004, CON-RPT-005, CON-RPT-006 · Implements the Check result port: CON-CHK-006, CON-CHK-007, CON-CHK-008, CON-CHK-009, CON-CHK-010, CON-CHK-011
TRACEABILITY     REQ covered by ≥1 API/DBF: 52/52 · orphan REQ: none

<<<END INPUT>>>

---
# KNOWLEDGE (profile primary sources — cite as [KB:<file> §n])

<<<KB: profiles/aias/knowledge/raw-idea.md>>>
# Request Verification Service — Raw Idea (project `aias`)

As of 2026-10-01. Author: Hesham Ezzat. Amended 2026-10-01 — see section 15.
Save as: `governance-shared/profiles/aias/knowledge/raw-idea.md` and list it under `knowledge.files` in `profiles/aias.yaml`.

## 0. How the factory should read this file

- Section 13 "Decided" is locked input. `domain-profile`, P0 and the PRD must not reopen those points.
- Section 13 "Open" is the complete set of questions to resolve, by research and a recommended answer.
- Section 12 "Guardrails" are non-negotiable and should become formal requirements in P1.
- YAML and endpoint examples illustrate intent. Final names, schemas and contracts are for the analysis stages to define.

## 1. Purpose

A standalone service that verifies a government service request before an employee approves it. It collects the request's data and attached documents, compares them with the conditions of the service, and returns a short report: compliant or not, and where the problems are.

The employee stays the decision maker. The service informs the decision and never makes it.

The service is generic. It covers many services, each with its own conditions, data queries and documents, all defined in configuration. It supersedes the earlier "Reusable Agentic AI Library" idea, which was broader than the first real need.

## 2. Scope

In scope:

- A standalone Spring Boot service, called by host systems over REST.
- Per-service configuration: a knowledge file plus a structured definition, maintained by the service administrator. Employees never supply knowledge files.
- Read-only access to request data through an MCP server (Oracle expected).
- Document retrieval by file path, by database BLOB, or by manual upload.
- Reading PDF, XLS and image documents (other extensions may appear).
- A structured verification report, stored in a database and shown to the employee inside the host system.
- A web frontend for the employee, embedded in the host screen (amendment A1, section 15).
- Optional execution of an approval API, per service, triggered by the employee.

Out of scope for now:

- Multi-tenancy.
- Conversation memory.
- RAG and a vector store.
- Multi-agent orchestration.
- A full administration UI and an internal permission system.
- Caller authentication and the security phases, deferred to a later version (amendment A2, section 15).

Memory is excluded because a check is one independent run, and carrying state between requests risks leaking one request's data into another. RAG is excluded because each service's knowledge fits whole in the prompt, which is more reliable for compliance than retrieving fragments. RAG becomes relevant only if one service's knowledge grows to hundreds of pages.

## 3. Architecture

Stack: Java 21, Spring Boot 4, Spring AI 2.0.

```text
Host system (Oracle ADF or other)
        |  REST
        v
Request Verification Service
  - Service Registry   loads each service package
  - Check Engine       fixed pipeline (the only fixed logic)
  - Report Store       saves the report and the employee decision
        |
        +--> MCP server        read-only queries on Oracle
        +--> Documents         storage path, BLOB or manual upload
        +--> LLM provider      through Spring AI, replaceable
        +--> Service database  runs, findings, documents
```

Each dependency sits behind an interface so it can be replaced without touching the engine.

## 4. Service package (the variable part)

Everything that differs between services lives in one package per service. Adding a service needs no code as long as it uses existing check types.

```text
services/<service-code>/
  knowledge.md     conditions and rules in natural language (read by the LLM)
  service.yaml     queries, required documents, fetch mode, optional approval API
```

The two files are separate on purpose. SQL and file locations are executed literally by the engine; only the conditions are interpreted by the LLM. Each package carries a version, and every report records the version it was built on.

```yaml
service: scholarship-request
version: 3
input: requestId

queries:
  request_details:
    connection: main-db
    sql: >
      SELECT r.status, r.gpa, s.national_id
      FROM requests r JOIN students s ON s.id = r.student_id
      WHERE r.id = :requestId
  attachments:
    connection: main-db
    sql: >
      SELECT a.doc_type, a.file_path
      FROM request_attachments a
      WHERE a.request_id = :requestId

documents:
  source: attachments
  type_column: doc_type
  fetch: path            # path | blob | manual
  path_column: file_path
  required: [TRANSCRIPT, ID_CARD]

approval:
  enabled: false
  api: POST /requests/{requestId}/approve
```

Connections are defined once, outside the service packages, and set at activation time for each environment.

```yaml
connections:
  main-db:
    type: mcp
    endpoint: ...
    query_tool: run_query
    dialect: oracle
```

## 5. Check flow (the fixed part)

1. The host system sends the service code, the request number and the employee's identity.
2. The engine loads the service package and runs its queries through the MCP connection.
3. It fetches the documents using the service's fetch mode.
4. It reads each document by type: text extraction for PDF, table extraction for XLS, OCR or a vision model for scans and images.
5. It runs the deterministic checks in code: required documents present, explicit values and dates.
6. The LLM compares the data and document content with the service knowledge.
7. The engine stores the structured report and makes it available to the host system.
8. The employee takes the action in the host system, or through the approval API where it is enabled.

A check takes time, so it runs asynchronously: the host starts it and then polls for the result.

## 6. Data and document access

Queries and documents use separate channels, each behind its own interface.

Queries go through `QueryExecutor`. The first implementation, `McpQueryExecutor`, calls the MCP server's query tool using the Spring AI MCP client. The engine calls MCP to run the queries written in `service.yaml`. The LLM is never given a tool that runs SQL.

Documents go through `DocumentFetcher`. All three modes are available, chosen per service.

| Mode | Used when | How |
| --- | --- | --- |
| `path` | The file is in storage and its path is in the database | Read from storage by the path the query returns |
| `blob` | The file is stored inside the database | Read the column directly over JDBC with a read-only user |
| `manual` | The service is not allowed direct access | The employee uploads the files; the report is marked accordingly |

BLOBs are not moved through MCP, because binary content would travel as Base64 text inside a message, which is slow and size-limited.

Requirements for the MCP server chosen at activation: bind-variable support or strict parameter type validation in the engine, structured (JSON) results, a row limit and a timeout.

## 7. Report model

The report has a fixed structure for every service, produced as structured output and stored as data.

| Part | Content |
| --- | --- |
| Overall status | `COMPLIANT`, `NOT_COMPLIANT` or `NEEDS_MANUAL_REVIEW` |
| Findings | One per condition: satisfied or not, the evidence (actual value found), and a note for the employee |
| Documents | What was read, what is missing, what could not be read |
| Metadata | Service version, document source mode, model used, time, employee |

Every finding carries its evidence so the employee can verify it. A required document that is missing or unreadable prevents a `COMPLIANT` status.

## 8. API

| Endpoint | Purpose |
| --- | --- |
| `POST /checks` | Start a check for a service code and request number |
| `GET /checks/{id}` | Return the status and the report as JSON |
| `GET /checks/{id}/view` | Return a ready report page to embed in the host screen |
| `POST /checks/{id}/documents` | Upload documents in manual mode |
| `POST /checks/{id}/decision` | Record the employee's decision; call the approval API if the service enables it |

The calling system authenticates itself (API key or mTLS) and passes the employee's identity, which is recorded with the check.

## 9. Persistence

Reports are stored in database tables in a schema owned by the service, separate from the read-only user that reaches host data.

| Table | Holds |
| --- | --- |
| `CHECK_RUN` | One row per check: service, request number, employee, status, result, service version, model, timestamps, employee decision |
| `CHECK_FINDING` | One row per condition: satisfied flag, evidence, note |
| `CHECK_DOCUMENT` | One row per document: type, source mode, read status |

Storing the employee's decision beside the report result gives a direct measure of accuracy: where the two disagree, the service knowledge or a check needs attention.

## 10. LLM strategy

The provider is cloud-based for now and must be replaceable through configuration alone.

- Testing: a free cloud tier. Gemini Flash-Lite through Google AI Studio is the starting candidate. Free-tier limits change often.
- Test data only: free tiers may use submitted data for model training. Only synthetic or anonymised requests and documents are sent while a free provider is in use.
- Real data: the provider for real requests, cloud or inside the network, is decided before go-live.

Rules that keep the provider replaceable:

- The engine depends on Spring AI's `ChatModel` only, with no provider-specific features.
- Document reading (OCR or vision) is a separate step with its own configurable model.
- A fixed set of test requests with known expected results is run on every model change.

## 11. Host integration

The service is reached from Oracle ADF applications, and from other government systems later, over HTTP only. It is not embedded in ADF.

Display. The first version embeds the ready report page (`/checks/{id}/view`) inside the ADF screen. The same report is available as JSON, so it can later be rendered with ADF components or shown in the request log without changing the service.

Approval. Two options, chosen per service:

1. Default: the employee approves in the host system as today. The host notifies the service of the decision for the record. The service needs no write access to any system.
2. Optional: where the host exposes an approval API, the service calls it after the employee confirms, and records the report the approval was based on.

## 12. Guardrails

- The LLM analyses and summarises. It does not write SQL and does not trigger approval.
- Approval is executed only as a result of the employee's action.
- All access to host data uses a read-only database user, preferably limited to specific views.
- Query parameters are bound or strictly type-validated; SQL is never built from free text.
- File paths are validated to be inside the allowed storage root before opening.
- Anything that could not be read appears in the report. It is never skipped silently.
- Document content is treated as data, never as instructions to the model.
- Each check has limits: timeout, maximum rows, maximum file size.
- No data is carried from one check to another.

## 13. Decisions and open items

Decided:

| Topic | Decision |
| --- | --- |
| Form | A standalone service, not a library |
| Stack | Java 21, Spring Boot 4, Spring AI 2.0 |
| Tenancy | Single tenant |
| Configuration | One package per service, maintained by the administrator |
| Database access | Read-only through an MCP server, specified at activation |
| Documents | `path`, `blob` and `manual` modes all available |
| Decision maker | The employee; approval API is an optional second path |
| Display | The frontend (A1) embedded in the host screen; JSON available for native display or the request log (amended — A1) |
| Storage | Reports kept in the service's own database tables |
| LLM | Cloud, free tier for testing, replaceable by configuration |
| Memory, RAG, vector store | Not included |
| Frontend | A web frontend for the employee, embedded in the host screen (amended — A1) |
| Security | Caller authentication and security phases deferred to a later version; the section 12 guardrails stay (amended — A2) |

Open:

- Which MCP server to use for Oracle, confirmed against the requirements in section 6.
- Which LLM provider is permitted for real request data.
- How the host system authenticates to the service: API key or mTLS. Deferred with A2 — not to be resolved in this version.
- Report retention period and who may view stored reports.
- The first service to implement as the pilot.

## 14. Proposed module split (for `domain-profile` to confirm)

| Code | Module | Scope |
| --- | --- | --- |
| `REG` | Service Registry | Service packages, versions, connections |
| `CHK` | Check Engine | The fixed pipeline, deterministic checks, LLM comparison |
| `DOC` | Document Access | `path`, `blob` and `manual` fetching; reading PDF, XLS and images |
| `RPT` | Report Store | Runs, findings, documents, employee decision |
| `INT` | Host Integration | REST API, optional approval API |

Tracks: backend and frontend (amended — A1). The platform track covers the MCP connection, the LLM provider configuration and the service database.

## 15. Amendments

| # | Date | Change | Supersedes |
| --- | --- | --- | --- |
| A1 | 2026-10-01 | A frontend track is added. A web frontend (React + TypeScript), embedded in the host screen, gives the employee: the checks of a request, the report (overall status, findings with evidence, documents read / missing / unreadable), manual document upload, and recording the decision. It consumes the same REST API as any host. It replaces the server-rendered report page (`GET /checks/{id}/view`) as the display path. A full administration UI stays out of scope. | Section 13 "Frontend: None" and "Display"; the section 14 tracks paragraph; the server-rendered page in sections 8 and 11 |
| A2 | 2026-10-01 | Caller authentication (API key or mTLS) and the security phases are deferred to a later version; the owner already has the solution and adds it then. The section 12 guardrails are NOT deferred: they are part of what the service does. | The auth item under section 13 "Open" |

<<<END KB>>>


==============================================================================
# BRIEF — stage `P4` (Test Plan) · module RPT · v1 · profile `aias`

Lane `test-gen` · implementer ['claude:opus'] · effort high · round 1

## Rules that bind this run
- Questions: **forbidden**. A `[QUESTION]` block is refused. Ambiguity → ADR in `analysis/decisions/RPT/` (`ADR-{MOD}-{seq:03d}.md`): non-breaking → continue; breaking → status BLOCKED and stop.
- Owns IDs: TC — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-RPT-014`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared/analysis/modules/RPT/P4/backend-test-plan-rpt.md`
- `governance-shared/analysis/modules/RPT/P4/frontend-test-plan-rpt.md`
- Write the files directly into the project checkout (you are the operator); the orchestrator reads them on `--complete`.

## Contracts checked by `gov.py analyze` after this stage
- **C10** test plans (every TC an acceptance of one package) → split: C10.1 traces {'from': 'TC', 'to': ['AC', 'XM', 'UXD'], 'min': 1, 'mode': 'any'} [CRITICAL]; C10.2 orphans {'kind': 'AC', 'referenced_by': ['TC'], 'min': 1} [MAJOR]; C10.3 markers {'artifact': 'backend-test-plan', 'track': 'backend', 'plan': 'test'} [CRITICAL]; C10.4 markers {'artifact': 'frontend-test-plan', 'track': 'frontend', 'plan': 'test'} [CRITICAL]; C10.5 ids-owned {'stage': 'P4'} [CRITICAL]; C10.7 msg-bound {'plan': 'frontend-execution-plan', 'tests': ['frontend-test-plan'], 'spec': 'analyze.messages', 'format': 'stack.backend.api.error_code_format', 'rule_kind': 'RULE', 'kind': 'TC'} [MAJOR]; C10.8 tc-data {'srs': 'srs', 'tests': ['frontend-test-plan', 'backend-test-plan'], 'spec': 'analyze.catalogue', 'kind': 'TC'} [MAJOR]; C10.9 tc-consistency {'srs': 'srs', 'tests': ['frontend-test-plan', 'backend-test-plan'], 'spec': 'analyze.catalogue', 'kind': 'TC'} [MAJOR]; C10.10 tc-package-resolves {'tests': ['backend-test-plan', 'frontend-test-plan'], 'spec': 'test_plan', 'kind': 'TC'} [MAJOR]; C10.11 package-has-tests {'tests': ['backend-test-plan', 'frontend-test-plan'], 'spec': 'test_plan', 'kind': 'TC'} [MAJOR]

---
# ENGINE
```
ENGINE        : P4 — Test Plan   (the LAST analysis stage — pass 2, before the one gate)
LANE          : test-gen · questions forbidden · derives from `AC-*` + `XM-*` + `UXD-*` (ids.atoms.TC.traces_to)
MODULE        : RPT · v1 · profile aias (Request Verification Service)
READS         : srs · registry-srs · registry-db · backend-execution-plan · frontend-execution-plan · api-spec · dependency-graph?   (from _state/ — "?" = optional)
PRODUCES      : backend-test-plan-rpt.md · frontend-test-plan-rpt.md
OWNS IDS      : TC
NEXT          : gate:analysis   (the one review gate over backend + frontend + tests)
FRAMEWORK     : backend `agnostic` · frontend `agnostic`   (profile.stack.testing)
BOUNDARY      : analysis-only — test PLANS, not test code; the factory runs nothing [G]
```

# Test Plan — engine reference

## 0. Position

This engine is the **last stage of the analysis** (`factory.passes.2`): it runs after
both execution plans and the API document exist in `_state/`, and the one review gate
(`gate:analysis`) reads its plans together with the backend and frontend plans. Every package
the split emits after that gate carries the test cases derived here as its acceptance
(`Package` line, §6; C10.10). It **invents nothing**: no rule, error, endpoint,
field, screen or cross-module flow — it only adds test cases. The factory runs none of them: [C:C10.5]
a test plan is data for the executor.

Questions are `forbidden`; ambiguity → `factory.yaml → ambiguity` (ADR, then
`continue`; breaking → `BLOCKED`,
`stop`) — shared/GOVERNANCE-CORE.md.

Delta versions: read `_state/` as the baseline, emit only ADDED / MODIFIED / REMOVED [C:C12.2]
test cases, continue the `TC` sequence — shared/VERSIONING.md.

The API document (`_state/current-api-spec.yaml`) is the source of every endpoint shape a backend TC
asserts on (method, path, request/response schema, the error responses' codes): a TC cites
the `API-*` id and the executor reads the shape there.

## 1. Inputs

| Input | Read from | Use |
|---|---|---|
| `srs` [G] | `_state/current-srs.md` | **the derivation source**: every `REQ-*` with its `AC-*` (Given / When / Then), `RULE-*` messages, screens, permissions |
| `registry-srs` [G] | `_state/current-registry-srs.md` | ID ranges, coverage of REQ by API/SCR |
| `registry-db` [G] | `_state/current-registry-db.md` | the module's `XM-*` register (target module, type) — cross-checked against the `XM` blocks above, not restated |
| `backend-execution-plan` [G] | `_state/current-backend-execution-plan.md` | `API-*` (verb, path, request/response, catalog codes) to bind backend steps to endpoints; the integration blocks of its last phase (`XM-*`: target, type, requires, tests, traces — shared/XM-PROTOCOL.md §6) are the **integration derivation source** on the backend track |
| `frontend-execution-plan` [G] | `_state/current-frontend-execution-plan.md` | `SCR-*`, routes, F-blocks to bind frontend steps to screens; its `UXD-*` references (screen, foreign field, owner module) are the **integration derivation source** on the frontend track |
| `api-spec` [G] | `_state/current-api-spec.md` | ID ranges, coverage of REQ by API/SCR |
| `dependency-graph` [G] (optional) | `_state/current-dependency-graph.md` | ID ranges, coverage of REQ by API/SCR |

Both execution plans and the API document are mandatory inputs here: every backend TC binds [T:inputs-missing]
an `API-*` whose shape the document states, every frontend TC a `SCR-*` the frontend plan
places, and every TC names the split unit it is acceptance for — none of that can be
derived from the SRS alone.

## 2. Derivations — this module, both tracks, its own edges

One run, one module, one file per track. Three derivations, each from what THIS module's
artifacts state:

| Derivation | Source | Phases populated |
|---|---|---|
| module (§3) | every `AC-*` of the SRS | the module test phases of each track |
| integration (§4, §5) | every `XM-*` block of the last phase of this module's backend plan (`integration` — the edge, its target, its `requires`, its tests line) and every `UXD-*` its frontend plan cites | the integration phase(s) (`profile…phases[*].integration: true`) — populated when the module has such an edge or such a field, **absent** otherwise: not an empty `PHASE` block [G] |
| platform | — | nothing: the rollup is `analyze.coverage` (`ac-tc`), computed by the factory from the traceability matrix, not written by hand [G] |

Rules:
1. the **declaring** module owns an edge's TCs and the **displaying** module owns a foreign
   field's TCs — this module's plan carries its own integration blocks, so nothing about
   another module has to be selected or waited for; the target's shape is cited by id only; [G]
2. an integration TC exercises this module's own `API-*` / `SCR-*` against the edge's
   `requires` state — present, and absent (the block's `if_not_met` path);
3. every TC names the package it is acceptance for (§6, `Package`).

## 3. Derivation (module) — every `TC-*` comes from an `AC-*`

`TC-*` (`TC-RPT-{seq}`, 3-digit seq,
one continuous sequence across the module — both plans share it, so no TC id repeats) traces → AC + XM + UXD. The
module-scope derivation is **mechanical**:

| AC part | becomes |
|---|---|
| **Given** | preconditions — data state, role/permission, system state (bound to real entities/screens from the plans) |
| **When** | the step list — for backend: the endpoint call (`API-*`, verb, path, payload from the AC); for frontend: navigation + user actions on `SCR-*` |
| **Then** | expected result — status/response (per `ProblemDetail (RFC 9457) → {type, title, status, detail, code}` when a RULE fires) or UI state; message asserted in every language (en, ar) |

Rules:
1. one `TC-*` per `AC-*`, always — an AC without a TC is a coverage gap (✗), never skipped; [C:C10.2]
2. an AC whose Then names a `RULE-*` violation yields the **violation** TC; its happy-path
   twin exists only if another AC states it — do not fabricate happy paths; [G]
3. a **boundary** TC is added only when the AC (or the RULE it cites) states a numeric limit; [G]
4. every TC cites, besides its AC: the `REQ-*`, and the `API-*` (backend) or `SCR-*`
   (frontend) it exercises, plus the `RULE-*` / catalog code when a violation is expected;
5. do not reword a rule, message or endpoint — reference by ID/code; message text is copied [G]
   character-perfect from the SRS in every language. A case that asserts a refusal by its TEXT
   needs the frontend plan to bind that text (`text: <source>` on the row routing the code —
   `analyze` msg-bound); a plan that routes the code without its words is a plan gap to record in an
   ADR, never a message to paraphrase here; [C:C10.7]
6. **catalogue values are seeded or created** (`analyze` tc-data). The SRS lookup section
   (the `lookups` block: `seeded` per key, `open: true` when the host may add values) says which values each key SEEDS and which are host data.
   A case whose data names a value the section does not seed carries a `Host data` line creating it —
   the key, the value, the state the case needs (active / inactive) and the call the OWNER of the
   lookup publishes for adding a value, by verb and path. Host data is site data: it is never moved [C:C10.8]
   into a product seed, and a case never assumes it exists; [C:C10.8]
7. **no two cases demand opposite states of one catalogue value** (`analyze` tc-consistency). One
   case needing a value absent and another holding it inactive, or one asserting an option set that
   leaves out a value another case needs, cannot share a catalogue. Give each such case its OWN host
   value (`<VALUE>_<TC seq>`), created by its own `Host data` line — the one departure from `Test data`'s "the values
   named in the AC" this engine allows, and the case's `Test data` line says so;
8. over-engineering guard: if a track's TC count exceeds ~2× its AC count, review — the
   extra TCs are mostly fabricated variants; remove them. [G]

Scenario tags (one per TC): `HAPPY | VIOLATION | BOUNDARY | PERMISSION | STATE | INTEGRATION`;
data class: `VALID | INVALID | BOUNDARY | EDGE | ATTACK`.


## 4. XM → TC derivation (integration, backend)

Runs for every `XM-*` block of this module's `backend-execution-plan` — its last phase carries
one per edge (XM-PROTOCOL.md §6), with the target, the type, the contract item, `requires`
and the `if_not_met` path. The target module is not read: the block states everything the [G]
test needs, and the executor decides when the edge's package runs.

Source: those blocks (cross-checked against `registry-db`, not restated). [G]
The **declaring module owns the resulting TC** — one continuous `TC-{MOD}-<seq>` sequence,
same rule as module scope (design decision: integration TC ownership follows declaration,
not the target).

| XM type | TC scenario |
|---|---|
| `HARD-FK` | one `EXISTS` TC (the referenced row is present — request/flow succeeds) **and** one `MISSING` TC (the referenced row is absent — the physical constraint is honoured: the documented rejection, not a silent pass) [G] |
| `SOFT-READ` | one `GRACEFUL-DEGRADATION` TC — the target read fails or returns empty and the declaring module's flow still returns a defined result (not a 500 / unhandled state) [G] |

Rules:
1. do not invent the target entity's shape — bind by `ENT`/`DBF` ID only, as the `XM` block [G]
   already does; the TC exercises the declaring module's own `API-*`, not the target's;
2. tag every such TC `INTEGRATION`, data class per the row above;
3. `traces=` carries the `XM-*` id plus the `REQ-*` the XM itself traces to (and the `API-*`
   exercised, when the plan binds one) — never an `AC-*` that does not exist for it; [C:C10.1]
4. one `XM-*` yields at most the two/one TC(s) in the table above — no fabricated [G]
   extra scenarios ("over-engineering guard" of §3 applies here too).

## 5. UXD → TC derivation (integration, frontend)

Runs for every `UXD-*` this module's `frontend-execution-plan` cites — a foreign-owned field
one of its screens renders. The owner module is not read. [G]

Source: the `UXD-*` references of the **displaying** module's `frontend-execution-plan`
F4 blocks (cross-checked against its `registry-exec-fe`, not restated). The **displaying [G]
module owns the resulting TC** (it is the one whose screen renders the foreign field).

| UXD case | TC scenario |
|---|---|
| foreign field rendered | one TC: navigate to the `SCR-*`, the foreign-owned field renders the value the owner module's API returns |
| foreign field empty / owner API failure | one TC: the screen shows its declared empty/error state (§A.3 `States` of the ui-ux-spec) — not a blank crash, no invented copy [G] |

Rules:
1. do not invent the owner module's field shape or a new permission — the UXD block and the [G]
   operation the screen's read binds in `_state/current-api-spec.yaml` are the only sources; [G]
2. tag every such TC `INTEGRATION`; `traces=` carries the `UXD-*` id plus its `REQ-*`/`AC-*`
   and the `SCR-*` it renders on;
3. one `UXD-*` yields at most the two TCs in the table above. [G]

## 6. TC block — framework-agnostic form

```
<!-- TC:TC-RPT-<seq>:START traces=AC-RPT-<seq>,REQ-RPT-<seq>[,API-RPT-<seq>|SCR-RPT-<seq>|XM-RPT-<seq>|UXD-RPT-<seq>] -->
### TC-RPT-<seq> — <title>
Derived from : AC-RPT-<seq>  (REQ-RPT-<seq>)   |   XM-RPT-<seq> (REQ-RPT-<seq>)   |   UXD-RPT-<seq> (REQ-RPT-<seq>, AC-RPT-<seq>)
Exercises    : API-RPT-<seq> <verb path>   |   SCR-RPT-<seq> <route>
Rule / code  : RULE-RPT-<seq> → <catalog code> | —
Package      : <the split unit this TC is acceptance for — see below>
Scenario     : <tag> · data class <class> · language <en|ar|ALL>
Preconditions: <from Given — concrete entities, role, state | for XM/UXD: the target/owner entity present or absent>
Host data    : <KEY VALUE — state — created by <verb path of the owner's add-value call>> | none
Steps        : 1. … 2. … (from When — one observable action per step)
Expected     : <from Then — status / body shape / message per language / UI state>
Test data    : <values named in the AC; placeholders marked, no invented business data> [G]
<!-- TC:TC-RPT-<seq>:END -->
```
**`Package` — the package this TC is acceptance for**, derived from the traces, never chosen: [C:C10.10]
an AC-derived TC names the split unit of the track's execution plan that implements the AC's
REQ — the `SUB` id (`{PHASE-KEY}-{LABEL}` / `{PHASE-KEY}-SCR-*`) holding the `API-*` / the screen it
exercises, or the `PHASE` key when that phase carries no SUB; an XM-derived TC names its
`XM-*` block (the edge's own package); a UXD-derived TC names the frontend SUB of the
screen that renders the field. One unit per TC. `gov.py analyze` refuses a unit the plan does not [C:C10.10]
split into (C10.10), and after the gate `gov.py split` writes every TC into its package's manifest
(`tests`) — a unit no TC names is a finding (C10.11) unless its phase is flagged
`no_tests` in the profile.

Framework: `profile.stack.testing` is **agnostic** on both tracks — the block above is the whole
contract; the consumer repo chooses its tool and turns each TC into a test. No framework
name, annotation or file layout is mentioned anywhere in the plan.

## 7. Test plans — organised by the profile's test phases

Each track with a `test` plan in the profile gets one file per module, wrapped in the
profile's test phases with `TC` atoms (kind `TC`, level 3, parents PHASE/SUB,
plans test). Test-plan SUB ids are **bare** labels
(`factory.markers.rules.sub_unqualified_exempt_plans` = test).
A phase flagged `integration: true` in the profile is populated when this module has an
`XM-*` block (backend) or cites a `UXD-*` (frontend) — §4/§5 — and is **absent** otherwise.

### Track `backend` — `backend-test-plan-rpt.md`

| Phase key | Split rule | SUB labels |
|---|---|---|
| `TEST-PLAN-BE` [T:never-split] | SUB when TC count > 12 — grouped RULE-SCENARIOS / API-SCENARIOS / MODEL-EVAL | `RULE-SCENARIOS`, `API-SCENARIOS`, `MODEL-EVAL` |
| `INT-XM` [T:never-split] _(integration — when the module has an edge / a foreign field)_ | SUB when TC count > 8 — grouped per target module | — |
Layout:
```
<header>   sources (_state files + versions) · framework note (§3) · open ADRs
<!-- PHASE:TEST-PLAN-BE:START traces=<union of the TCs' REQ/AC> -->
  <!-- SUB:RULE-SCENARIOS:START traces=… -->  …TC blocks…  <!-- SUB:RULE-SCENARIOS:END -->
  <!-- SUB:API-SCENARIOS:START traces=… -->  …TC blocks…  <!-- SUB:API-SCENARIOS:END -->
  <!-- SUB:MODEL-EVAL:START traces=… -->  …TC blocks…  <!-- SUB:MODEL-EVAL:END -->
  (SUBs only when the threshold is met — decide WHILE writing, from the TC count) [G]
<!-- PHASE:TEST-PLAN-BE:END -->
<!-- PHASE:INT-XM:START traces=<union of the TCs' REQ/AC/XM/UXD> -->
  (populate ONLY when this module has an edge / cites a foreign field — §4/§5; omit this PHASE entirely when it has none, not an empty block) [G]
  …TC blocks…
<!-- PHASE:INT-XM:END -->
TC TRACEABILITY INDEX   AC → TC · REQ → TC · API → TC · RULE/code → TC · XM → TC · Package → TC
COVERAGE                AC covered <n>/<total> (a gap is ✗ and blocks the run) · REQ covered · API covered · every XM edge covered <n>/<total> (an integration gap is ✗ exactly like an AC gap — recorded, not silently dropped) [G]
```
Backend grouping hint: rule-driven ACs (violations, state transitions) vs endpoint-driven ACs
(happy paths, permission, paging/empty-result per `the documented envelope`); integration TCs (§4) group by target module inside `INT-XM`.

### Track `frontend` — `frontend-test-plan-rpt.md`

| Phase key | Split rule | SUB labels |
|---|---|---|
| `TEST-PLAN-FE` [T:never-split] | SUB when TC count > 8 — grouped UI-FLOWS / INT-FLOW | `UI-FLOWS`, `INT-FLOW` |
| `INT-UXD` [T:never-split] _(integration — when the module has an edge / a foreign field)_ | SUB when TC count > 8 — grouped per source module | — |
Layout:
```
<header>   sources (_state files + versions) · framework note (§3) · open ADRs
<!-- PHASE:TEST-PLAN-FE:START traces=<union of the TCs' REQ/AC> -->
  <!-- SUB:UI-FLOWS:START traces=… -->  …TC blocks…  <!-- SUB:UI-FLOWS:END -->
  <!-- SUB:INT-FLOW:START traces=… -->  …TC blocks…  <!-- SUB:INT-FLOW:END -->
  (SUBs only when the threshold is met — decide WHILE writing, from the TC count) [G]
<!-- PHASE:TEST-PLAN-FE:END -->
<!-- PHASE:INT-UXD:START traces=<union of the TCs' REQ/AC/XM/UXD> -->
  (populate ONLY when this module has an edge / cites a foreign field — §4/§5; omit this PHASE entirely when it has none, not an empty block) [G]
  …TC blocks…
<!-- PHASE:INT-UXD:END -->
TC TRACEABILITY INDEX   AC → TC · REQ → TC · SCR → TC · RULE/code → TC · UXD → TC · Package → TC
COVERAGE                AC covered <n>/<total> (a gap is ✗ and blocks the run) · REQ covered · SCR covered · every UXD covered <n>/<total> (an integration gap is ✗ exactly like an AC gap — recorded, not silently dropped) [G]
```
Frontend grouping hint: per-screen flows (search, create/edit, violation shown on screen,
permission-hidden affordance) vs the single module lifecycle flow (create → search → update →
deactivate → gone from active results); integration TCs (§5) group by source (owner) module inside `INT-UXD`. [G]

## 8. Split

The same toolkit splits test plans, with plan key `test`
(`factory.tracks.<track>.packages.test` → `backend-test`, `frontend-test`), after the `gate:analysis` gate, with the execution plans:
```
gov.py split --track <track> --module <MOD> --version <v> --plan test --dry-run   # validate, non-zero exit = fix first
gov.py split --track <track> --module <MOD> --version <v> --plan test
```
Every TC atom is verified by content hash (`factory.markers.rules.verify` = sha256) after the split,
and every execution package's manifest lists the TCs whose `Package` names it — the package's acceptance.

## 9. Self-check before finishing

```
[ ] every AC-* in the SRS has ≥1 TC-* (coverage ✗ = not done)
[ ] every TC-* carries traces= with its AC-*/XM-*/UXD-* source (+ the upstream ids the plan names) and the atom marker pair
[ ] every TC names one of AC/XM/UXD as its source; no reworded rule/message/endpoint; no invented test data [G]
[ ] phases = the profile's test phases, in order; SUB labels bare; thresholds checked while writing
[ ] framework wording matches §6; every TC carries a `Package` line naming ONE split unit of its track's plan
[ ] every XM-* block and every cited UXD-* has ≥1 TC or is recorded as a gap (✗) — never silently [C:C10.11]
    dropped, not fabricated when absent; the integration phase is absent when there is none [G]
[ ] every host-data value a case names has a `Host data` line with the owner's add-value call;
    no two cases share a host value in opposite states (tc-data, tc-consistency)
[ ] every refusal a case asserts by its text is bound in the frontend plan (msg-bound) — or an ADR
    records the plan gap
[ ] ADRs written for every derivation choice that was not mechanical
```

## 10. Boundaries

| Owns | References (never redefines) | Never [C:C10.5] |
|---|---|---|
| `TC-*`, the test plans | `REQ/AC/RULE` (P1), `API` (P3.1), `SCR/UXD` (P3.2 — `UXD` is `P3.2`'s, cited never redefined), `DBF/XM` (P2 — `XM` is `P2`'s, cited never redefined), catalog codes | test code, framework scaffolding, any edit to a line artifact, any gate or verdict, a TC about another module's own behaviour [C:C10.5] |


---
# INPUTS (generated current state)

<<<INPUT: srs>>>
# SRS — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias
Inputs : prd, domain-profile, project-registry (PRD approved 2026-10-01)
Counts : REQ 52 · AC 60 · ENT 4 · RULE 15 · SCR-REQ 0 · ADR 5 (new: ADR-RPT-006 … ADR-RPT-010; applied: ADR-RPT-001 … ADR-RPT-010, ADR-REG-001, ADR-REG-002, ADR-REG-006, ADR-CHK-001, ADR-CHK-002, ADR-CHK-005, ADR-CHK-007, ADR-CHK-011, ADR-CHK-014, ADR-CHK-015, ADR-CHK-017, ADR-DOC-002, ADR-DOC-007, ADR-DOC-008, ADR-DOC-011)
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information
| Item | Value |
|---|---|
| Module | RPT — Report Store |
| Feature code | RPT |
| Version | v1 |
| Date | 2026-10-01 |
| Status | DRAFT — P1 output, PRD approved 2026-10-01 (gate prd-approval) |
| Prepared by | P1 SRS engine (operator run, lane analysis) |
| Decisions applied | 10 RPT ADRs (ADR-RPT-001 … ADR-RPT-010, of which 5 new), 3 REG ADRs, 8 CHK ADRs, 4 DOC ADRs and 4 DEFAULTs — see Decisions applied |

## A2 — Functional context

### In scope
- Implementing all six operations of the Check result port CHK declares (CON-CHK-006 … CON-CHK-011): create a Check run and return its identifier, mark it RUNNING, store a completed report whole, store a failure with its reason, read one Check, list the unfinished Checks (POL-RPT-001, POL-RPT-004, POL-RPT-006, POL-RPT-010; ADR-RPT-001).
- Keeping host identifiers exactly as sent and only the codes of the closed lists of CHK and DOC (POL-RPT-002, POL-RPT-005).
- Moving a Check's status only forward and never changing an ended report (POL-RPT-003, POL-RPT-009; ADR-RPT-002).
- Keeping every unread document and unread service query in the report, and no document content or query results (POL-RPT-007, POL-RPT-008).
- Serving, read-only, a Check's status and report, the Checks of a request and the decision agreement of a service (POL-RPT-011, POL-RPT-012, POL-RPT-017; ADR-RPT-005, ADR-RPT-006).
- Recording the Employee Decision handed over by INT beside the result (POL-RPT-013 … POL-RPT-016, POL-RPT-018; ADR-RPT-003, ADR-RPT-009).
- Retention and the hard-delete purge (POL-RPT-019 … POL-RPT-022; ADR-RPT-004, ADR-RPT-010).
- The raw-idea §12 guardrails at RPT's surface (ADR-RPT-008).

### Out of scope
- Running a Check, deriving the Overall Status, the Check timeout and upload window — CHK (ADR-CHK-001, ADR-CHK-002, ADR-CHK-005).
- Fetching and reading documents, the storage root, the maximum file size, uploaded files — DOC (ADR-DOC-001, ADR-DOC-008).
- Starting a Check, uploading documents, confirming uploads, the decision endpoint and the call to the Approval API — INT (G2; ADR-RPT-003, ADR-RPT-006).
- Who may view stored reports and caller authentication — deferred (domain-profile D4, D7; raw-idea A2); no role check is specified.
- Changing or withdrawing a decision, per-service retention, archiving, cross-service dashboards (scope exceptions of the business policies).
- Multi-tenancy, conversation memory, RAG, multi-agent orchestration, an administration UI.

### Module function
The Report Store keeps the record of every Check: what it was asked to verify, how far it got, what it found and what the employee decided. It is written by the Check Engine through the Check result port and by Host Integration for the Employee Decision; it serves the host, the employee frontend and the service administrator read-only views of that record; and it removes each record, whole, once the configured retention period has passed since the Check ended.

### Detailed description
When the Check Engine starts a Check, it asks the Report Store to create the Check run — service code, service package version, fetch mode, request number, employee identity, initial status and start time — and receives the Check's identifier. As the pipeline advances, the Check Engine marks the Check RUNNING and finally either completes it — handing over the Overall Status, the findings with condition, outcome, evidence and note, the document outcomes with type, source mode, read status and reason, the service queries that could not be read and the metadata — or fails it with one failure reason and a detail text. The Report Store stores a completed report in one piece or not at all, refuses a code outside its closed list and refuses any change to an ended Check. At start-up and on its schedule the Check Engine asks for the unfinished Checks, and when the employee confirms the uploads of a `manual` Check it reads that Check back. Host systems and the embedded employee frontend read a Check's status while it runs and its whole report once it has ended, and list the Checks of a request. When the employee decides, Host Integration — after calling the Approval API where the service enables it — hands the decision to the Report Store, which records it once, beside the result of a COMPLETED Check. The service administrator reads, per service package version, how the decided Checks of each Overall Status were approved or rejected. A purge, on the platform configuration's schedule, deletes every Check run that ended longer ago than the report retention period, with all its records. Roles: the Employee (follows Checks, reads reports, takes the decision) and the Service Administrator (retention, accuracy measure).

### Current situation
| Step | Party | Notes |
|---|---|---|
| Employee checks the request by hand and approves or rejects it in the host system | Employee | No record of what was checked, on which evidence, or whether a check would have agreed with the decision [KB:raw-idea.md §1, §9] |

### Current difficulties
Nothing records which conditions were verified, on what evidence, or how often the employee's decision differs from what the evidence shows, so neither the employees' work nor a future automated check can be measured [KB:raw-idea.md §1, §9].

### Proposed system and benefits
Every Check leaves a complete, unchangeable report (US-RPT-003, US-RPT-006) that the employee reads with each finding beside its evidence (US-RPT-008); the decision is kept beside the result (US-RPT-010), so the service administrator can see where the service and the employees disagree (US-RPT-011); reports leave only by a deliberate, configured purge (US-RPT-012).

### General notes
- Logical types only; physical types and tables belong to P2.
- The Check identifier carried by the Check result port (`checkId`) is the identifier of the Check Run (ENT-RPT-001.checkRunId); CHK, DOC and INT hold it as a value only (CON-CHK-006).
- The report retention period, in whole days, and the purge schedule are platform configuration (ADR-RPT-004, ADR-RPT-010) — not entities.
- Every write comes from in-process callers: CHK through the result port, INT for the decision; RPT's HTTP surface is read-only (ADR-RPT-006).
- No role check is specified in this version (raw-idea A2).

## A3 — Entities and fields

Standard fields — per profile: kind `transactional` carries `createdAt, updatedAt` (system-filled, never accepted from a client). Every identifier field of the service's own schema is a number key generated by identity (profile `conventions.identifiers`); host identifiers (request number, employee identity) are kept as text exactly as the host sent them and are never foreign keys.

### ENT-RPT-001 — Check Run
Kind reason: transactional — one row per Check, created when the Check starts, ended once, removed only by the purge.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | SHARED (owner) — the identifier travels by value to CHK, DOC and INT; no other module stores its rows | no | create (REQ-RPT-001), read (REQ-RPT-021, REQ-RPT-023, REQ-RPT-028, REQ-RPT-040), update (REQ-RPT-005, REQ-RPT-008, REQ-RPT-018, REQ-RPT-032), delete (purge — REQ-RPT-043) | CHK writes it through the result port; INT records the decision; DOC and INT hold `checkId` by value | POL-RPT-001; ADR-REG-001; ADR-RPT-001, ADR-RPT-003 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| checkRunId | number (identifier) | yes | identity | the Check identifier (`checkId`) of the result port | Check |
| serviceCode | text | yes | SERVICE_CODE (REG, by value) | as carried by CHK; never a foreign key | Service |
| versionNumber | number | yes | service package version (REG, by value) | with serviceCode, the version the report was built on (G11) | Service version |
| fetchMode | lookup | yes | FETCH_MODE | | Document source mode |
| requestNumber | text | yes | host | exactly as the host sent it (REQ-RPT-002) | Request number |
| employeeId | text | yes | host | the employee who started the Check, exactly as sent | Employee |
| checkStatus | lookup | yes | CHECK_STATUS | forward only (RULE-RPT-003) | Status |
| startedAt | date-time | yes | CHK | | Started at |
| runningSince | date-time | no | CHK | set by the first mark RUNNING | Running since |
| endedAt | date-time | no | CHK | set when COMPLETED or FAILED | Ended at |
| overallStatus | lookup | no | OVERALL_STATUS | present exactly when COMPLETED (REQ-RPT-008, REQ-RPT-018) | Overall Status |
| comparisonModel | text | no | CHK metadata | present exactly when COMPLETED | Model used |
| failureReason | lookup | no | CHECK_FAILURE_REASON | present exactly when FAILED | Failure reason |
| failureDetail | text | no | CHK | present exactly when FAILED | Failure detail |
| employeeDecision | lookup | no | EMPLOYEE_DECISION | at most once, only on a COMPLETED Check (RULE-RPT-011, RULE-RPT-012) | Employee Decision |
| decidedBy | text | no | host, through INT | exactly as sent; present exactly when employeeDecision is | Decided by |
| decidedAt | date-time | no | system | time the decision was recorded (ADR-RPT-009) | Decided at |
| approvalApiExecuted | flag | no | INT | true when the decision was carried out through the Approval API; present exactly when employeeDecision is | Executed through Approval API |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

### ENT-RPT-002 — Finding
Kind reason: transactional — one row per condition of a completed report, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-004; [KB:raw-idea.md §9 CHECK_FINDING]; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| findingId | number (identifier) | yes | identity | | Finding |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order of the finding in the report as handed over, from 1 | Position |
| conditionText | text | yes | CHK | the condition the finding is about | Condition |
| findingOutcome | lookup | yes | FINDING_OUTCOME | | Outcome |
| evidence | text | yes | CHK | the actual value found (G10) | Evidence |
| note | text | yes | CHK | the note for the employee | Note |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

### ENT-RPT-003 — Check Document
Kind reason: transactional — one row per document outcome of a completed report, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-007; [KB:raw-idea.md §9 CHECK_DOCUMENT]; ADR-REG-001; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| checkDocumentId | number (identifier) | yes | identity | | Check Document |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order of the outcome as handed over, from 1 | Position |
| documentType | text | yes | DOCUMENT_TYPE (REG, by value) | | Document type |
| sourceMode | lookup | yes | FETCH_MODE | | Source mode |
| readStatus | lookup | yes | DOCUMENT_READ_STATUS | | Read status |
| unreadableReason | lookup | no | UNREADABLE_REASON | present exactly when readStatus is UNREADABLE (RULE-RPT-008) | Reason |
| detail | text | no | CHK / DOC | | Detail |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

No document content field exists (REQ-RPT-019).

### ENT-RPT-004 — Unread Query
Kind reason: transactional — one row per service query whose data could not be read, written once with its Check Run.

| Kind | Ownership | Business number | Operations | Cross-module | Source |
|---|---|---|---|---|---|
| transactional | PRIVATE | no | create (REQ-RPT-008), read (REQ-RPT-023), delete (purge — REQ-RPT-043) | — | POL-RPT-007; CON-CHK-008; ADR-CHK-014; ADR-RPT-001 |

| Field | Logical type | Required | Values / source | Notes | Label |
|---|---|---|---|---|---|
| unreadQueryId | number (identifier) | yes | identity | | Unread query |
| checkRunId | reference | yes | ENT-RPT-001 | owning Check Run | Check |
| position | number | yes | system | order as handed over, from 1 | Position |
| queryName | text | yes | CHK | the service query's name in the service definition | Query |
| detail | text | yes | CHK | why its data was not read | Detail |
| createdAt, updatedAt | date-time | yes | system | standard fields — per profile | Created at, Updated at |

Consumed: none. RPT consumes no entity of another module: the service code, version number, document type and every closed code arrive by value through the Check result port (CON-CHK-006, CON-CHK-008) and are stored as values; RPT never reads REG, DOC or CHK at run time (ADR-RPT-001).

## A4 — Functional requirements (EARS) and acceptance criteria

### REQ-RPT-001 — Check run created, identifier returned
  Pattern    : event
  Statement  : When the Check Engine asks to create a Check run with a service code, a version number, a fetch mode, a request number, an employee identity, an initial status and a start time, the system shall store the Check run and return its identifier.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : One row per check is where the report and the decision are kept.
  Source     : POL-RPT-001; CON-CHK-006; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-001 — [REQ-RPT-001]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run for `scholarship-request` version 3, fetch mode `path`, request number `1001`, employee `E-2041`, status RUNNING, start time 2026-10-01T09:00:00Z
  Then   : 1 Check run is stored with exactly those values and its new identifier is returned

### REQ-RPT-002 — Host identifiers kept exactly as sent
  Pattern    : ubiquitous
  Statement  : The system shall keep the request number and the employee identity of a Check run as text exactly as received, without trimming, case change or reformatting.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : The host data lives outside the service's schema; the report must show the identifiers the host knows.
  Source     : POL-RPT-002; profile `conventions.identifiers`
  Priority   : HIGH

#### AC-RPT-002 — [REQ-RPT-002]
  Given  : the Check Engine creates a Check run with request number `00-1001/A` and employee identity ` e.2041 `
  When   : the Check run is read back
  Then   : the request number is "00-1001/A" and the employee identity is " e.2041 ", unchanged

### REQ-RPT-003 — Incomplete Check run refused
  Pattern    : unwanted
  Statement  : If a Check run to be created lacks its service code, version number, fetch mode, request number, employee identity, initial status or start time, then the system shall refuse to store it.
  Traces     : US-RPT-001
  Entities   : ENT-RPT-001
  Rationale  : A Check run without these values cannot be traced to what it verified.
  Source     : POL-RPT-001; RULE-RPT-001; CON-CHK-006 "errors: not stored"
  Priority   : HIGH

#### AC-RPT-003 — [REQ-RPT-003]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run whose employee identity is blank
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: employeeId is missing."

### REQ-RPT-004 — Initial status agrees with the fetch mode
  Pattern    : unwanted
  Statement  : If a Check run to be created has the initial status AWAITING_DOCUMENTS with a fetch mode other than `manual`, or the initial status RUNNING with the fetch mode `manual`, then the system shall refuse to store it.
  Traces     : US-RPT-001, US-RPT-002
  Entities   : ENT-RPT-001
  Rationale  : Only a `manual` Check waits for documents; a contradictory record would mislead the host polling it.
  Source     : RULE-RPT-002; ADR-CHK-004; ADR-RPT-007
  Priority   : —

#### AC-RPT-004 — [REQ-RPT-004]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run with fetch mode `path` and initial status AWAITING_DOCUMENTS
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual."

#### AC-RPT-005 — [REQ-RPT-004]
  Given  : no Check run exists
  When   : the Check Engine creates a Check run with fetch mode `manual` and initial status RUNNING
  Then   : no Check run is stored and the call is refused with "The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS."

### REQ-RPT-005 — Check marked RUNNING
  Pattern    : event
  Statement  : When the Check Engine marks a Check that is AWAITING_DOCUMENTS or RUNNING as running, the system shall set its status to RUNNING and, if no running time is stored yet, store the running time received.
  Traces     : US-RPT-002
  Entities   : ENT-RPT-001
  Rationale  : The host sees that the Check's pipeline is under way.
  Source     : POL-RPT-003; CON-CHK-007; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-006 — [REQ-RPT-005]
  Given  : Check 501 is AWAITING_DOCUMENTS with no running time
  When   : the Check Engine marks Check 501 running with running time 2026-10-01T09:05:00Z
  Then   : Check 501 has status RUNNING and running time 2026-10-01T09:05:00Z

#### AC-RPT-007 — [REQ-RPT-005]
  Given  : Check 502 is RUNNING with running time 2026-10-01T09:01:00Z
  When   : the Check Engine marks Check 502 running with running time 2026-10-01T09:02:00Z
  Then   : Check 502 stays RUNNING and its running time stays 2026-10-01T09:01:00Z

### REQ-RPT-006 — Status never moves backwards
  Pattern    : unwanted
  Statement  : If a status change would move a Check out of COMPLETED or FAILED, or complete a Check that is not RUNNING, then the system shall refuse the change and keep the stored status.
  Traces     : US-RPT-002, US-RPT-006
  Entities   : ENT-RPT-001
  Rationale  : COMPLETED and FAILED are final; a Check ends exactly once.
  Source     : POL-RPT-003; RULE-RPT-003; CON-CHK-001; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-008 — [REQ-RPT-006]
  Given  : Check 503 is COMPLETED
  When   : the Check Engine marks Check 503 running
  Then   : Check 503 stays COMPLETED and the call is refused with "Check 503 has already ended; its status cannot change."

#### AC-RPT-009 — [REQ-RPT-006]
  Given  : Check 504 is AWAITING_DOCUMENTS
  When   : the Check Engine completes Check 504
  Then   : nothing is stored for Check 504, it stays AWAITING_DOCUMENTS and the call is refused with "Check 504 is not running; it cannot be completed."

### REQ-RPT-007 — Unknown Check on the result port
  Pattern    : unwanted
  Statement  : If the Check Engine marks running, completes, fails or reads a Check that has no stored Check run, then the system shall refuse the call as not found.
  Traces     : US-RPT-002, US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : A result for a Check that was never created has nowhere to go.
  Source     : CON-CHK-007, CON-CHK-009, CON-CHK-010 "errors: not found"
  Priority   : —

#### AC-RPT-010 — [REQ-RPT-007]
  Given  : no Check run 999 exists
  When   : the Check Engine fails Check 999
  Then   : nothing is stored and the call is refused with "Check 999 was not found."

### REQ-RPT-008 — Completed report stored
  Pattern    : event
  Statement  : When the Check Engine completes a RUNNING Check, the system shall store its status COMPLETED, its Overall Status, its comparison model, its end time, every finding, every document outcome and every unread service query.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The report has a fixed structure for every service and is stored as data.
  Source     : POL-RPT-004; CON-CHK-008; [KB:raw-idea.md §7]; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-011 — [REQ-RPT-008]
  Given  : Check 505 is RUNNING
  When   : the Check Engine completes it with Overall Status NOT_COMPLIANT, comparison model `gemini-flash-lite`, end time 2026-10-01T09:10:00Z, 3 findings, 2 document outcomes and 1 unread query
  Then   : Check 505 is COMPLETED with Overall Status NOT_COMPLIANT, model `gemini-flash-lite`, end time 2026-10-01T09:10:00Z, and 3 Findings, 2 Check Documents and 1 Unread Query are stored for it

### REQ-RPT-009 — A report is stored whole or not at all
  Pattern    : unwanted
  Statement  : If any part of a completed report cannot be stored, then the system shall store no part of it, keep the Check RUNNING and refuse the call as not stored.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A half-stored report would show a result without the findings that justify it; the Check Engine then fails the Check INTERNAL_ERROR.
  Source     : POL-RPT-004; CON-CHK-008 "errors: not stored (CHK then fails the Check with INTERNAL_ERROR)"; ADR-RPT-001
  Priority   : HIGH

#### AC-RPT-012 — [REQ-RPT-009]
  Given  : Check 506 is RUNNING
  When   : the Check Engine completes it with 3 findings of which the third has outcome `PASSED`
  Then   : Check 506 stays RUNNING with no Overall Status, 0 Findings, 0 Check Documents and 0 Unread Queries are stored for it, and the call is refused as not stored

### REQ-RPT-010 — Report order kept
  Pattern    : ubiquitous
  Statement  : The system shall keep the findings, the document outcomes and the unread service queries of a report in the order in which the Check Engine handed them over.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The employee reads the report in the order the conditions were assessed.
  Source     : POL-RPT-004; [KB:raw-idea.md §7]
  Priority   : —

#### AC-RPT-013 — [REQ-RPT-010]
  Given  : Check 507 is completed with findings on conditions "GPA at least 3.0", "TRANSCRIPT present", "ID_CARD present" in that order
  When   : the report of Check 507 is read
  Then   : the findings are returned at positions 1, 2, 3 in that same order

### REQ-RPT-011 — Missing and unreadable documents kept with their reason
  Pattern    : ubiquitous
  Statement  : The system shall keep every document outcome of a completed report, including each MISSING document and each UNREADABLE document with its reason and detail.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-003
  Rationale  : Anything that could not be read appears in the report; it is never skipped silently.
  Source     : POL-RPT-007; [KB:raw-idea.md §7, §12]; domain-profile §5 G6; ADR-DOC-002, ADR-DOC-007
  Priority   : HIGH

#### AC-RPT-014 — [REQ-RPT-011]
  Given  : Check 508 is RUNNING
  When   : the Check Engine completes it with document outcomes TRANSCRIPT READ, ID_CARD MISSING and a second TRANSCRIPT UNREADABLE with reason TOO_LARGE and detail "file of 31 MB exceeds 20 MB"
  Then   : 3 Check Documents are stored for Check 508, the third with reason TOO_LARGE and detail "file of 31 MB exceeds 20 MB"

### REQ-RPT-012 — Unread service queries kept
  Pattern    : ubiquitous
  Statement  : The system shall keep every unread service query of a completed report with its query name and detail.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-004
  Rationale  : Data that could not be read must stay visible beside the result it weakened.
  Source     : POL-RPT-007; CON-CHK-008; ADR-CHK-014; domain-profile §5 G6
  Priority   : HIGH

#### AC-RPT-015 — [REQ-RPT-012]
  Given  : Check 509 is RUNNING
  When   : the Check Engine completes it with Overall Status NEEDS_MANUAL_REVIEW and unread query `request_details` with detail "more than 500 rows"
  Then   : 1 Unread Query `request_details` with detail "more than 500 rows" is stored for Check 509

### REQ-RPT-013 — Metadata agrees with the Check run
  Pattern    : unwanted
  Statement  : If the metadata of a completed report lacks the comparison model or the end time, names a service code, version number, fetch mode, employee identity or start time different from the stored Check run, or ends before the Check started, then the system shall refuse to store the report.
  Traces     : US-RPT-003
  Entities   : ENT-RPT-001
  Rationale  : A report whose metadata contradicts its Check run cannot be traced to what produced it.
  Source     : RULE-RPT-004; [KB:raw-idea.md §7] metadata; domain-profile §5 G11; ADR-RPT-007
  Priority   : —

#### AC-RPT-016 — [REQ-RPT-013]
  Given  : Check 510 is RUNNING for `scholarship-request` version 3
  When   : the Check Engine completes it with metadata version number 4
  Then   : nothing is stored for Check 510 and the call is refused with "The report of Check 510 was not stored: its metadata versionNumber 4 differs from the Check run (3)."

### REQ-RPT-014 — COMPLIANT only with every finding satisfied
  Pattern    : unwanted
  Statement  : If a completed report has the Overall Status COMPLIANT while any of its findings is not SATISFIED or any service query was not read, then the system shall refuse to store the report.
  Traces     : US-RPT-003, US-RPT-004
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-004
  Rationale  : A stored report must never claim more than was verified.
  Source     : RULE-RPT-005; CON-CHK-001 derivation; domain-profile §5 G6; ADR-RPT-007
  Priority   : HIGH

#### AC-RPT-017 — [REQ-RPT-014]
  Given  : Check 511 is RUNNING
  When   : the Check Engine completes it with Overall Status COMPLIANT and a finding "ID_CARD present" with outcome NOT_SATISFIED
  Then   : nothing is stored for Check 511 and the call is refused with "The report of Check 511 was not stored: COMPLIANT needs every finding SATISFIED and every service query read."

### REQ-RPT-015 — Only the codes of the closed lists
  Pattern    : unwanted
  Statement  : If a Check status, Overall Status, finding outcome, failure reason, document read status, unreadable reason or fetch mode received is not a code of its closed list, then the system shall refuse to store it.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003
  Rationale  : The closed lists are owned by the service; a code outside them is meaningless to every reader.
  Source     : POL-RPT-005; RULE-RPT-006; CON-CHK-001 … CON-CHK-003, CON-DOC-001, CON-DOC-002
  Priority   : —

#### AC-RPT-018 — [REQ-RPT-015]
  Given  : Check 512 is RUNNING
  When   : the Check Engine fails it with failure reason `CRASHED`
  Then   : Check 512 stays RUNNING and the call is refused with "Not stored: `CRASHED` is not a code of CHECK_FAILURE_REASON."

### REQ-RPT-016 — A finding is complete
  Pattern    : unwanted
  Statement  : If a finding of a completed report lacks its condition, outcome, evidence or note, then the system shall refuse to store the report.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-002
  Rationale  : Every finding carries its evidence so the employee can verify it.
  Source     : POL-RPT-004; RULE-RPT-007; CON-CHK-002; domain-profile §5 G10
  Priority   : HIGH

#### AC-RPT-019 — [REQ-RPT-016]
  Given  : Check 513 is RUNNING
  When   : the Check Engine completes it with a finding "GPA at least 3.0" whose evidence is blank
  Then   : nothing is stored for Check 513 and the call is refused with "The report of Check 513 was not stored: finding 1 has no evidence."

### REQ-RPT-017 — Reason exactly on UNREADABLE documents
  Pattern    : unwanted
  Statement  : If a document outcome is UNREADABLE without a reason, or READ or MISSING with a reason, then the system shall refuse to store the report.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-003
  Rationale  : The reason tells the employee why a document could not be read; it means nothing on a document that was read or absent.
  Source     : RULE-RPT-008; CON-DOC-001
  Priority   : —

#### AC-RPT-020 — [REQ-RPT-017]
  Given  : Check 514 is RUNNING
  When   : the Check Engine completes it with a document outcome ID_CARD UNREADABLE with no reason
  Then   : nothing is stored for Check 514 and the call is refused with "The report of Check 514 was not stored: document 1 is UNREADABLE without a reason."

### REQ-RPT-018 — Failed Check stored with its reason
  Pattern    : event
  Statement  : When the Check Engine fails a Check that is AWAITING_DOCUMENTS or RUNNING, the system shall store its status FAILED, its failure reason, its detail and its end time, with no Overall Status and no findings.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001
  Rationale  : A failed Check must be visible as failed, never with a result the pipeline did not reach.
  Source     : POL-RPT-006; CON-CHK-009; CON-CHK-003; ADR-CHK-005
  Priority   : HIGH

#### AC-RPT-021 — [REQ-RPT-018]
  Given  : Check 515 is RUNNING
  When   : the Check Engine fails it with reason TIMED_OUT, detail "Check exceeded 300 s", end time 2026-10-01T09:06:00Z
  Then   : Check 515 is FAILED with reason TIMED_OUT, detail "Check exceeded 300 s", end time 2026-10-01T09:06:00Z, no Overall Status and 0 Findings

### REQ-RPT-019 — No document content or query results kept
  Pattern    : ubiquitous
  Statement  : The system shall keep no document content and no query result rows in a stored report beyond the condition, evidence, note, outcome and detail texts the Check Engine hands over.
  Traces     : US-RPT-005
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The report needs the evidence the employee verifies, not a copy of the request's files and data.
  Source     : POL-RPT-008; CON-CHK-008 "Carries no document content"; ADR-DOC-008
  Priority   : —

#### AC-RPT-022 — [REQ-RPT-019]
  Given  : Check 516 is completed with a document outcome TRANSCRIPT READ
  When   : the Check Document of TRANSCRIPT is read
  Then   : it holds document type, source mode, read status and detail only — no field holds the transcript's text or file

### REQ-RPT-020 — An ended report never changes
  Pattern    : state
  Statement  : While a Check is COMPLETED or FAILED, the system shall refuse every change to its status, result, failure, findings, Check Documents, unread queries and metadata.
  Traces     : US-RPT-006
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The decision is measured against the report the employee saw.
  Source     : POL-RPT-009; RULE-RPT-003; ADR-RPT-002
  Priority   : HIGH

#### AC-RPT-023 — [REQ-RPT-020]
  Given  : Check 517 is COMPLETED with Overall Status NOT_COMPLIANT and 3 findings
  When   : the Check Engine completes Check 517 again with Overall Status COMPLIANT
  Then   : Check 517 keeps Overall Status NOT_COMPLIANT and its 3 findings, and the call is refused with "Check 517 has already ended; its status cannot change."

### REQ-RPT-021 — One Check read for the Check Engine
  Pattern    : event
  Statement  : When the Check Engine reads one Check, the system shall return its identifier, status, service code, version number, fetch mode, request number, employee identity and start time.
  Traces     : US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : The Check Engine resumes a `manual` Check on the version it recorded.
  Source     : CON-CHK-010; POL-RPT-010
  Priority   : —

#### AC-RPT-024 — [REQ-RPT-021]
  Given  : Check 518 is AWAITING_DOCUMENTS for `scholarship-request` version 3, fetch mode `manual`, request `1001`, employee `E-2041`
  When   : the Check Engine reads Check 518
  Then   : it receives 518, AWAITING_DOCUMENTS, `scholarship-request`, 3, `manual`, `1001`, `E-2041` and the start time

### REQ-RPT-022 — Unfinished Checks listed
  Pattern    : event
  Statement  : When the Check Engine asks for the unfinished Checks, the system shall return the identifier, status and start time of every Check that is AWAITING_DOCUMENTS or RUNNING, oldest first.
  Traces     : US-RPT-007
  Entities   : ENT-RPT-001
  Rationale  : Checks interrupted by a restart or whose upload window elapsed must be ended.
  Source     : POL-RPT-010; CON-CHK-011; ADR-CHK-005
  Priority   : —

#### AC-RPT-025 — [REQ-RPT-022]
  Given  : Checks 519 (RUNNING, started 09:00), 520 (AWAITING_DOCUMENTS, started 08:30) and 521 (COMPLETED) exist
  When   : the Check Engine asks for the unfinished Checks
  Then   : it receives 520 then 519, and not 521

#### AC-RPT-026 — [REQ-RPT-022]
  Given  : every stored Check is COMPLETED or FAILED
  When   : the Check Engine asks for the unfinished Checks
  Then   : it receives an empty list

### REQ-RPT-023 — A Check's status and report read
  Pattern    : event
  Statement  : When a host system or the employee frontend reads a Check, the system shall return its identifier, status, service code, version number, fetch mode, request number, employee identity and times and, once it is COMPLETED, its Overall Status, model used, findings, Check Documents, unread queries and Employee Decision.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : The host polls the status; the employee reads the whole report.
  Source     : POL-RPT-011; [KB:raw-idea.md §8] `GET /checks/{id}`; §15 A1; ADR-RPT-005, ADR-RPT-006
  Priority   : HIGH

#### AC-RPT-027 — [REQ-RPT-023]
  Given  : Check 522 is RUNNING
  When   : the employee frontend reads Check 522
  Then   : it receives status RUNNING with the Check's service, version, request and times, and no Overall Status, findings or documents

#### AC-RPT-028 — [REQ-RPT-023]
  Given  : Check 523 is COMPLETED with Overall Status NOT_COMPLIANT, 3 findings, 2 Check Documents and 0 unread queries, with no decision
  When   : the employee frontend reads Check 523
  Then   : it receives status COMPLETED, Overall Status NOT_COMPLIANT, the model used, 3 findings, 2 documents, 0 unread queries and no Employee Decision

### REQ-RPT-024 — Each finding returned with its evidence
  Pattern    : ubiquitous
  Statement  : The system shall return every finding of a report as one entry holding its condition, outcome, evidence and note together.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-002
  Rationale  : Every finding carries its evidence so the employee can verify it.
  Source     : POL-RPT-011; [KB:raw-idea.md §7]; domain-profile §5 G10
  Priority   : HIGH

#### AC-RPT-029 — [REQ-RPT-024]
  Given  : Check 524 is completed with finding "GPA at least 3.0", outcome NOT_SATISFIED, evidence "GPA = 2.7", note "Below the 3.0 minimum"
  When   : the report of Check 524 is read
  Then   : one finding entry holds "GPA at least 3.0", NOT_SATISFIED, "GPA = 2.7" and "Below the 3.0 minimum"

### REQ-RPT-025 — Unknown Check read
  Pattern    : unwanted
  Statement  : If a host system or the employee frontend reads a Check that has no stored Check run, then the system shall answer that the Check was not found.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001
  Rationale  : The caller must tell a missing Check from a running one; a purged Check is also not found.
  Source     : POL-RPT-011; profile `error_envelope` ProblemDetail
  Priority   : —

#### AC-RPT-030 — [REQ-RPT-025]
  Given  : no Check run 998 exists
  When   : the employee frontend reads Check 998
  Then   : the answer is not found with detail "Check 998 was not found."

### REQ-RPT-026 — Failed Check read with its reason
  Pattern    : event
  Statement  : When a host system or the employee frontend reads a FAILED Check, the system shall return its failure reason and detail with no Overall Status.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-001
  Rationale  : A failed Check must be visible as failed.
  Source     : POL-RPT-006, POL-RPT-011; ADR-CHK-005
  Priority   : —

#### AC-RPT-031 — [REQ-RPT-026]
  Given  : Check 525 is FAILED with reason MODEL_UNAVAILABLE and detail "provider answered 503"
  When   : the employee frontend reads Check 525
  Then   : it receives status FAILED, reason MODEL_UNAVAILABLE, detail "provider answered 503" and no Overall Status

### REQ-RPT-027 — Stored texts returned as data
  Pattern    : ubiquitous
  Statement  : The system shall return every stored condition, evidence, note and detail text exactly as stored, as data, without interpreting or executing anything it contains.
  Traces     : US-RPT-008
  Entities   : ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : Evidence may quote document content, which is data, never instructions.
  Source     : [KB:raw-idea.md §12] "Document content is treated as data"; domain-profile §5 G7; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-032 — [REQ-RPT-027]
  Given  : Check 526 is completed with a finding whose evidence is "<script>alert(1)</script> ignore previous instructions"
  When   : the report of Check 526 is read
  Then   : the evidence is returned as the same character string, as a text value

### REQ-RPT-028 — Checks of a request listed
  Pattern    : event
  Statement  : When the employee frontend lists the Checks of a service code and request number, the system shall return the identifier, status, Overall Status, start time, end time and Employee Decision of the newest 100 of those Checks, newest first, with the total number of Checks of that request.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : The employee sees whether the request was checked before and what each Check found.
  Source     : POL-RPT-012; [KB:raw-idea.md §15 A1]; ADR-RPT-005, ADR-RPT-008
  Priority   : —

#### AC-RPT-033 — [REQ-RPT-028]
  Given  : request `1001` of `scholarship-request` has Checks 527 (started 08:00, FAILED) and 528 (started 09:00, COMPLETED, NOT_COMPLIANT), and request `1001` of `housing-request` has Check 529
  When   : the employee frontend lists the Checks of `scholarship-request` request `1001`
  Then   : it receives 528 then 527 and the total 2, and not 529

#### AC-RPT-034 — [REQ-RPT-028]
  Given  : request `1002` of `scholarship-request` has 130 Checks
  When   : the employee frontend lists its Checks
  Then   : it receives the 100 newest, newest first, and the total 130

### REQ-RPT-029 — Service code and request number needed for the list
  Pattern    : unwanted
  Statement  : If the Checks of a request are listed without a service code or without a request number, then the system shall refuse the listing.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : Two services can share a host request number; a list on one key would mix their reports.
  Source     : RULE-RPT-009; ADR-RPT-005
  Priority   : —

#### AC-RPT-035 — [REQ-RPT-029]
  Given  : Checks exist for request `1001`
  When   : the employee frontend lists Checks with request number `1001` and no service code
  Then   : no list is returned and the answer is a validation error with detail "Both a service code and a request number are needed to list Checks."

### REQ-RPT-030 — Every Check its own record
  Pattern    : ubiquitous
  Statement  : The system shall store every Check of the same request as its own Check run and shall never copy a finding, a result or an Employee Decision from one Check run to another.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001, ENT-RPT-002
  Rationale  : No data is carried from one check to another.
  Source     : POL-RPT-023; [KB:raw-idea.md §12]; domain-profile §5 G9; ADR-CHK-007
  Priority   : —

#### AC-RPT-036 — [REQ-RPT-030]
  Given  : Check 530 of request `1001` is COMPLETED with 3 findings and decision APPROVED
  When   : the Check Engine creates a new Check run for request `1001` of the same service
  Then   : a new Check run with a new identifier is stored with no findings and no Employee Decision, and Check 530 is unchanged

### REQ-RPT-031 — Bounded list of a request's Checks
  Pattern    : ubiquitous
  Statement  : The system shall return at most 100 Checks in one listing of the Checks of a request.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : Every read has a limit; the total number keeps the cut visible, never silent.
  Source     : [KB:raw-idea.md §12] "Each check has limits"; domain-profile §5 G8; ADR-RPT-008
  Priority   : —

#### AC-RPT-037 — [REQ-RPT-031]
  Given  : request `1003` of `scholarship-request` has 101 Checks
  When   : the employee frontend lists its Checks
  Then   : exactly 100 entries are returned with the total 101

### REQ-RPT-032 — Employee Decision recorded
  Pattern    : event
  Statement  : When Host Integration hands over an Employee Decision on a COMPLETED Check with no decision, the system shall record the decision, the deciding employee's identity exactly as sent, whether it was executed through the Approval API and the recording time.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : Keeping the decision beside the result shows where the two disagree.
  Source     : POL-RPT-013; [KB:raw-idea.md §9, §11]; ADR-RPT-003, ADR-RPT-009
  Priority   : HIGH

#### AC-RPT-038 — [REQ-RPT-032]
  Given  : Check 531 is COMPLETED with Overall Status COMPLIANT and no decision
  When   : Host Integration hands over decision APPROVED by employee `E-3307`, not executed through the Approval API
  Then   : Check 531 holds decision APPROVED, decided by "E-3307", executed through Approval API false, and a recording time; its Overall Status and findings are unchanged

### REQ-RPT-033 — One decision per Check
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that already has one, then the system shall refuse it and keep the decision already recorded.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The decision is a fact about one report; a later change belongs to the host system's own record.
  Source     : POL-RPT-014; RULE-RPT-011; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-039 — [REQ-RPT-033]
  Given  : Check 532 is COMPLETED with decision REJECTED by `E-3307`
  When   : Host Integration hands over decision APPROVED by `E-4410`
  Then   : Check 532 keeps decision REJECTED by `E-3307` and the call is refused with "Check 532 already has an Employee Decision."

### REQ-RPT-034 — Decisions only on a completed Check
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that is not COMPLETED, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : A running or failed Check has no result to stand beside.
  Source     : POL-RPT-015; RULE-RPT-012; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-040 — [REQ-RPT-034]
  Given  : Check 533 is FAILED
  When   : Host Integration hands over decision APPROVED by `E-3307`
  Then   : no decision is recorded and the call is refused with "Check 533 is not completed; a decision can only be recorded on a completed Check."

### REQ-RPT-035 — A decision is complete
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over without a decision code of EMPLOYEE_DECISION, without the deciding employee's identity or without saying whether it was executed through the Approval API, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The record must show what was decided, by whom and how.
  Source     : POL-RPT-013, POL-RPT-016; RULE-RPT-013; ADR-RPT-009
  Priority   : —

#### AC-RPT-041 — [REQ-RPT-035]
  Given  : Check 534 is COMPLETED with no decision
  When   : Host Integration hands over decision `MAYBE` by `E-3307`
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: `MAYBE` is not APPROVED or REJECTED."

#### AC-RPT-042 — [REQ-RPT-035]
  Given  : Check 535 is COMPLETED with no decision
  When   : Host Integration hands over decision APPROVED with a blank employee identity
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: the deciding employee is missing."

### REQ-RPT-036 — Execution through the Approval API recorded
  Pattern    : optional
  Statement  : Where an Employee Decision was executed through the service's Approval API, the system shall record it as executed through the Approval API.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The record shows which approvals the service carried out and on which report.
  Source     : POL-RPT-016; [KB:raw-idea.md §11]; ADR-RPT-003
  Priority   : HIGH

#### AC-RPT-043 — [REQ-RPT-036]
  Given  : Check 536 is COMPLETED with no decision
  When   : Host Integration hands over decision APPROVED by `E-3307`, executed through the Approval API
  Then   : Check 536 holds decision APPROVED with executed through Approval API true

### REQ-RPT-037 — The Report Store never approves
  Pattern    : ubiquitous
  Statement  : The system shall never call an Approval API and never set an Employee Decision other than one handed over by Host Integration.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The employee stays the decision maker; approval happens only as a result of the employee's action.
  Source     : POL-RPT-018; [KB:raw-idea.md §12]; domain-profile §5 G2; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-044 — [REQ-RPT-037]
  Given  : Check 537 is completed with Overall Status COMPLIANT for a service whose Approval API is enabled
  When   : 24 hours pass with no decision handed over
  Then   : Check 537 has no Employee Decision and the Report Store has sent 0 calls to any Approval API

### REQ-RPT-038 — Unknown Check for a decision
  Pattern    : unwanted
  Statement  : If an Employee Decision is handed over for a Check that has no stored Check run, then the system shall refuse it as not found.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : A decision must stand beside an existing report.
  Source     : POL-RPT-013; ADR-RPT-003
  Priority   : —

#### AC-RPT-045 — [REQ-RPT-038]
  Given  : no Check run 997 exists
  When   : Host Integration hands over decision APPROVED by `E-3307` for Check 997
  Then   : nothing is recorded and the call is refused with "Check 997 was not found."

### REQ-RPT-039 — Approval API execution only with an approval
  Pattern    : unwanted
  Statement  : If an Employee Decision REJECTED is handed over as executed through the Approval API, then the system shall refuse it.
  Traces     : US-RPT-010
  Entities   : ENT-RPT-001
  Rationale  : The Approval API carries out approvals; a rejection is never executed through it.
  Source     : RULE-RPT-014; [KB:raw-idea.md §11]; ADR-RPT-009
  Priority   : —

#### AC-RPT-046 — [REQ-RPT-039]
  Given  : Check 538 is COMPLETED with no decision
  When   : Host Integration hands over decision REJECTED by `E-3307`, executed through the Approval API
  Then   : no decision is recorded and the call is refused with "The decision was not recorded: only an APPROVED decision is executed through the Approval API."

### REQ-RPT-040 — Decision agreement of a service
  Pattern    : event
  Statement  : When the decision agreement of a service code is read, the system shall return, for each service package version with a decided Check, the number of decided Checks for each pair of Overall Status and Employee Decision.
  Traces     : US-RPT-011
  Entities   : ENT-RPT-001
  Rationale  : Where the result and the decision disagree, the service knowledge or a check needs attention.
  Source     : POL-RPT-017; [KB:raw-idea.md §9]; ADR-RPT-005
  Priority   : —

#### AC-RPT-047 — [REQ-RPT-040]
  Given  : `scholarship-request` version 3 has decided Checks: 4 COMPLIANT + APPROVED, 1 COMPLIANT + REJECTED, 2 NOT_COMPLIANT + REJECTED; version 2 has 1 NEEDS_MANUAL_REVIEW + APPROVED; and 3 undecided Checks exist
  When   : the decision agreement of `scholarship-request` is read
  Then   : it returns version 3: (COMPLIANT, APPROVED) 4, (COMPLIANT, REJECTED) 1, (NOT_COMPLIANT, REJECTED) 2; version 2: (NEEDS_MANUAL_REVIEW, APPROVED) 1; the undecided Checks are not counted

#### AC-RPT-048 — [REQ-RPT-040]
  Given  : no decided Check exists for `housing-request`
  When   : the decision agreement of `housing-request` is read
  Then   : it returns an empty list

### REQ-RPT-041 — Service code needed for the decision agreement
  Pattern    : unwanted
  Statement  : If the decision agreement is read without a service code, then the system shall refuse the read.
  Traces     : US-RPT-011
  Entities   : ENT-RPT-001
  Rationale  : The measure is per service; mixing services hides where attention is needed.
  Source     : RULE-RPT-015; ADR-RPT-005
  Priority   : —

#### AC-RPT-049 — [REQ-RPT-041]
  Given  : decided Checks exist
  When   : the decision agreement is read with no service code
  Then   : nothing is returned and the answer is a validation error with detail "A service code is needed to read the decision agreement."

### REQ-RPT-042 — Reports kept for the retention period
  Pattern    : ubiquitous
  Statement  : The system shall keep every Check run, with its findings, Check Documents, unread queries and Employee Decision, until it has been ended for longer than the report retention period of the platform configuration.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A report is kept as long as the host keeps the request it verified.
  Source     : POL-RPT-019; domain-profile §8 D4; ADR-RPT-004
  Priority   : —

#### AC-RPT-050 — [REQ-RPT-042]
  Given  : the report retention period is 365 days and Check 539 ended 364 days ago
  When   : the purge runs
  Then   : Check 539 and all its records are still stored

### REQ-RPT-043 — Purge of expired Check runs
  Pattern    : event
  Statement  : When the purge runs on the schedule of the platform configuration, the system shall permanently delete every Check run that has been ended for longer than the report retention period, together with its findings, Check Documents and unread queries.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A purge removes older runs with their findings and documents by hard delete.
  Source     : POL-RPT-020; domain-profile §8 D4; profile `delete_semantics: hard`; ADR-RPT-004, ADR-RPT-010
  Priority   : —

#### AC-RPT-051 — [REQ-RPT-043]
  Given  : the report retention period is 365 days and Check 540 ended 366 days ago with 3 findings, 2 Check Documents, 1 unread query and decision APPROVED
  When   : the purge runs
  Then   : Check 540, its 3 Findings, 2 Check Documents and 1 Unread Query no longer exist, and reading Check 540 answers not found

### REQ-RPT-044 — No retention period, no purge
  Pattern    : unwanted
  Statement  : If the report retention period is not configured or is not a whole number of days greater than zero, then the system shall delete no Check run when the purge runs and shall log that the purge was skipped.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : A missing or wrong setting must never destroy records.
  Source     : POL-RPT-021; ADR-RPT-004, ADR-RPT-010
  Priority   : —

#### AC-RPT-052 — [REQ-RPT-044]
  Given  : no report retention period is configured and Check 541 ended 1000 days ago
  When   : the purge runs
  Then   : Check 541 is still stored and the log holds "Report purge skipped: no valid report retention period is configured."

### REQ-RPT-045 — Unfinished Checks never purged
  Pattern    : unwanted
  Statement  : If a Check is AWAITING_DOCUMENTS or RUNNING, then the system shall not delete it when the purge runs, whatever its start time.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : The Check Engine still writes to an unfinished Check and ends it.
  Source     : POL-RPT-022; CON-CHK-011; ADR-RPT-004
  Priority   : —

#### AC-RPT-053 — [REQ-RPT-045]
  Given  : the report retention period is 30 days and Check 542 is RUNNING, started 40 days ago
  When   : the purge runs
  Then   : Check 542 is still stored

### REQ-RPT-046 — Purge outcome logged
  Pattern    : event
  Statement  : When a purge run ends, the system shall log the number of Check runs it deleted and the cut-off time it applied.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001
  Rationale  : A deletion of records must leave a trace of how much was removed and on what basis.
  Source     : POL-RPT-020; ADR-RPT-010
  Priority   : —

#### AC-RPT-054 — [REQ-RPT-046]
  Given  : the report retention period is 365 days and 7 Check runs ended more than 365 days ago
  When   : the purge runs at 2026-10-02T02:00:00Z
  Then   : the log holds "Report purge deleted 7 Check runs ended before 2025-10-02T02:00:00Z."

### REQ-RPT-047 — No access to host data
  Pattern    : ubiquitous
  Statement  : The system shall run no query on host data and hold no connection to a host database.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : All access to host data is read-only and belongs to the Check Engine and Document Access; the Report Store has no need of it.
  Source     : [KB:raw-idea.md §12] read-only host access; domain-profile §5 G3; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-055 — [REQ-RPT-047]
  Given  : the service runs with a Report Store and an activated `main-db` connection
  When   : the Report Store's operations are exercised end to end
  Then   : 0 queries are sent through any host connection by the Report Store

### REQ-RPT-048 — No file opened
  Pattern    : ubiquitous
  Statement  : The system shall open no file of host storage and no uploaded file.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : File paths are validated by Document Access; the Report Store keeps no document and has no path to open.
  Source     : [KB:raw-idea.md §12] storage root; domain-profile §5 G5; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-056 — [REQ-RPT-048]
  Given  : a completed report holds a Check Document whose detail names the path "/data/att/1001/t.pdf"
  When   : the report is read
  Then   : the path is returned as text and no file is opened

### REQ-RPT-049 — No model call
  Pattern    : ubiquitous
  Statement  : The system shall call no model and give no model a tool or any access to stored reports.
  Traces     : US-RPT-005
  Entities   : —
  Rationale  : The LLM analyses and summarises inside the Check Engine only; it never reaches the record.
  Source     : [KB:raw-idea.md §12] "The LLM analyses and summarises"; domain-profile §5 G1; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-057 — [REQ-RPT-049]
  Given  : a comparison model is configured for the service
  When   : a Check run is created, completed, read, decided and purged
  Then   : the Report Store sends 0 requests to any model

### REQ-RPT-050 — Read filters bound as parameters
  Pattern    : ubiquitous
  Statement  : The system shall pass the service code, the request number and the Check identifier of every read to its own store as bound parameters, never as part of the query text.
  Traces     : US-RPT-009
  Entities   : ENT-RPT-001
  Rationale  : SQL is never built from free text, including the service's own store.
  Source     : [KB:raw-idea.md §12] "Query parameters are bound"; domain-profile §5 G4; ADR-RPT-008
  Priority   : HIGH

#### AC-RPT-058 — [REQ-RPT-050]
  Given  : a Check of request `1001` exists
  When   : the employee frontend lists the Checks of `scholarship-request` and request number `1001' OR '1'='1`
  Then   : 0 Checks and the total 0 are returned, and no other request's Check is returned

### REQ-RPT-051 — Incomplete failure refused
  Pattern    : unwanted
  Statement  : If a failure handed over by the Check Engine lacks its failure reason, its detail or its end time, then the system shall refuse to store it.
  Traces     : US-RPT-004
  Entities   : ENT-RPT-001
  Rationale  : Every FAILED Check carries exactly one reason and a detail text.
  Source     : POL-RPT-006; RULE-RPT-010; CON-CHK-003
  Priority   : —

#### AC-RPT-059 — [REQ-RPT-051]
  Given  : Check 543 is RUNNING
  When   : the Check Engine fails it with reason INTERNAL_ERROR and a blank detail
  Then   : Check 543 stays RUNNING and the call is refused with "The failure of Check 543 was not stored: detail is missing."

### REQ-RPT-052 — Purge deletes each Check run whole
  Pattern    : unwanted
  Statement  : If the deletion of any record of an expired Check run fails during the purge, then the system shall keep that Check run with all its records and continue with the next expired Check run.
  Traces     : US-RPT-012
  Entities   : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004
  Rationale  : A report is removed completely or not at all; one failure must not stop the purge.
  Source     : POL-RPT-020; ADR-RPT-010
  Priority   : —

#### AC-RPT-060 — [REQ-RPT-052]
  Given  : Checks 544 and 545 have expired and the deletion of a Finding of Check 544 fails
  When   : the purge runs
  Then   : Check 544 is still stored with all its records, Check 545 no longer exists, and the log counts 1 deleted Check run

## A5 — Business rules

### RULE-RPT-001 — A Check run is complete
  Scope      : ENT-RPT-001
  Trigger    : on create Check run
  Statement  : The system shall require a service code, a version number, a fetch mode, a request number, an employee identity, an initial status and a start time, each present and not blank, to create a Check run.
  Message    : The Check run was not stored: {field} is missing.
  Traces     : REQ-RPT-003
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.versionNumber, ENT-RPT-001.fetchMode, ENT-RPT-001.requestNumber, ENT-RPT-001.employeeId, ENT-RPT-001.checkStatus, ENT-RPT-001.startedAt
  Source     : POL-RPT-001; CON-CHK-006

### RULE-RPT-002 — Initial status agrees with the fetch mode
  Scope      : ENT-RPT-001
  Trigger    : on create Check run
  Statement  : The system shall require the initial status AWAITING_DOCUMENTS when the fetch mode is `manual` and RUNNING when it is `path` or `blob`.
  Message    : The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual. / The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS.
  Traces     : REQ-RPT-004
  Data source: ENT-RPT-001.fetchMode, ENT-RPT-001.checkStatus
  Source     : ADR-CHK-004; ADR-RPT-007

### RULE-RPT-003 — Status moves forward only
  Scope      : ENT-RPT-001
  Trigger    : on mark RUNNING, complete, fail
  Statement  : The system shall allow only the changes AWAITING_DOCUMENTS → RUNNING, RUNNING → RUNNING, RUNNING → COMPLETED, AWAITING_DOCUMENTS → FAILED and RUNNING → FAILED, and shall prevent every change from COMPLETED or FAILED.
  Message    : Check {checkId} has already ended; its status cannot change. / Check {checkId} is not running; it cannot be completed.
  Traces     : REQ-RPT-005, REQ-RPT-006, REQ-RPT-020
  Data source: ENT-RPT-001.checkStatus
  Source     : POL-RPT-003, POL-RPT-009; CON-CHK-001; ADR-RPT-002
  Test-Hint  : walk every pair of the four statuses

### RULE-RPT-004 — Report metadata agrees with its Check run
  Scope      : ENT-RPT-001
  Trigger    : on complete
  Statement  : The system shall require the report metadata to carry a comparison model and an end time not earlier than the start time, and its service code, version number, fetch mode, employee identity and start time to equal those stored on the Check run.
  Message    : The report of Check {checkId} was not stored: its metadata {field} {value} differs from the Check run ({stored}). / The report of Check {checkId} was not stored: {field} is missing.
  Traces     : REQ-RPT-013
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.versionNumber, ENT-RPT-001.fetchMode, ENT-RPT-001.employeeId, ENT-RPT-001.startedAt, ENT-RPT-001.comparisonModel, ENT-RPT-001.endedAt
  Source     : POL-RPT-004; domain-profile §5 G11; ADR-RPT-007

### RULE-RPT-005 — COMPLIANT only when fully verified
  Scope      : ENT-RPT-001
  Trigger    : on complete
  Statement  : The system shall prevent storing the Overall Status COMPLIANT when any finding of the report is not SATISFIED or the report has any unread service query.
  Message    : The report of Check {checkId} was not stored: COMPLIANT needs every finding SATISFIED and every service query read.
  Traces     : REQ-RPT-014
  Data source: ENT-RPT-001.overallStatus, ENT-RPT-002.findingOutcome, ENT-RPT-004.queryName
  Source     : CON-CHK-001; domain-profile §5 G6; ADR-RPT-007

### RULE-RPT-006 — Closed codes only
  Scope      : ENT-RPT-001, ENT-RPT-002, ENT-RPT-003
  Trigger    : on create Check run, complete, fail
  Statement  : The system shall require every Check status, Overall Status, failure reason, fetch mode, source mode, finding outcome, document read status and unreadable reason to be a code of its closed list in A6.
  Message    : Not stored: `{value}` is not a code of {lookupKey}.
  Traces     : REQ-RPT-015
  Data source: ENT-RPT-001.checkStatus, ENT-RPT-001.overallStatus, ENT-RPT-001.failureReason, ENT-RPT-001.fetchMode, ENT-RPT-002.findingOutcome, ENT-RPT-003.sourceMode, ENT-RPT-003.readStatus, ENT-RPT-003.unreadableReason
  Source     : POL-RPT-005; CON-CHK-001 … CON-CHK-003; CON-DOC-001, CON-DOC-002

### RULE-RPT-007 — A finding is complete
  Scope      : ENT-RPT-002
  Trigger    : on complete
  Statement  : The system shall require every finding to carry a condition, an outcome, an evidence and a note, each present and not blank.
  Message    : The report of Check {checkId} was not stored: finding {position} has no {field}.
  Traces     : REQ-RPT-016
  Data source: ENT-RPT-002.conditionText, ENT-RPT-002.findingOutcome, ENT-RPT-002.evidence, ENT-RPT-002.note
  Source     : POL-RPT-004; CON-CHK-002; domain-profile §5 G10

### RULE-RPT-008 — Reason exactly on UNREADABLE
  Scope      : ENT-RPT-003
  Trigger    : on complete
  Statement  : The system shall require an unreadable reason on every UNREADABLE document outcome and prevent one on a READ or MISSING outcome, and shall require a document type and source mode on every outcome.
  Message    : The report of Check {checkId} was not stored: document {position} is UNREADABLE without a reason. / The report of Check {checkId} was not stored: document {position} is {readStatus} and cannot carry a reason.
  Traces     : REQ-RPT-017
  Data source: ENT-RPT-003.readStatus, ENT-RPT-003.unreadableReason, ENT-RPT-003.documentType, ENT-RPT-003.sourceMode
  Source     : CON-DOC-001

### RULE-RPT-009 — A request's Checks need both keys
  Scope      : ENT-RPT-001
  Trigger    : on list Checks of a request
  Statement  : The system shall require a service code and a request number, both present and not blank, to list the Checks of a request.
  Message    : Both a service code and a request number are needed to list Checks.
  Traces     : REQ-RPT-029
  Data source: ENT-RPT-001.serviceCode, ENT-RPT-001.requestNumber
  Source     : ADR-RPT-005

### RULE-RPT-010 — A failure is complete
  Scope      : ENT-RPT-001
  Trigger    : on fail
  Statement  : The system shall require a failure reason, a detail not blank and an end time not earlier than the start time to store a failure.
  Message    : The failure of Check {checkId} was not stored: {field} is missing.
  Traces     : REQ-RPT-051
  Data source: ENT-RPT-001.failureReason, ENT-RPT-001.failureDetail, ENT-RPT-001.endedAt, ENT-RPT-001.startedAt
  Source     : POL-RPT-006; CON-CHK-003, CON-CHK-009

### RULE-RPT-011 — One decision per Check
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording an Employee Decision on a Check run that already holds one.
  Message    : Check {checkId} already has an Employee Decision.
  Traces     : REQ-RPT-033
  Data source: ENT-RPT-001.employeeDecision
  Source     : POL-RPT-014; ADR-RPT-003

### RULE-RPT-012 — Decision only on a completed Check
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording an Employee Decision on a Check run whose status is not COMPLETED.
  Message    : Check {checkId} is not completed; a decision can only be recorded on a completed Check.
  Traces     : REQ-RPT-034
  Data source: ENT-RPT-001.checkStatus
  Source     : POL-RPT-015; ADR-RPT-003

### RULE-RPT-013 — A decision is complete
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall require a decision code of EMPLOYEE_DECISION, a deciding employee's identity not blank and a yes / no value for execution through the Approval API to record an Employee Decision.
  Message    : The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. / The decision was not recorded: say whether it was executed through the Approval API.
  Traces     : REQ-RPT-035
  Data source: ENT-RPT-001.employeeDecision, ENT-RPT-001.decidedBy, ENT-RPT-001.approvalApiExecuted
  Source     : POL-RPT-013, POL-RPT-016; ADR-RPT-009

### RULE-RPT-014 — Approval API execution only with APPROVED
  Scope      : ENT-RPT-001
  Trigger    : on record decision
  Statement  : The system shall prevent recording a decision REJECTED as executed through the Approval API.
  Message    : The decision was not recorded: only an APPROVED decision is executed through the Approval API.
  Traces     : REQ-RPT-039
  Data source: ENT-RPT-001.employeeDecision, ENT-RPT-001.approvalApiExecuted
  Source     : [KB:raw-idea.md §11]; ADR-RPT-009

### RULE-RPT-015 — Decision agreement needs a service code
  Scope      : ENT-RPT-001
  Trigger    : on read decision agreement
  Statement  : The system shall require a service code, present and not blank, to read the decision agreement.
  Message    : A service code is needed to read the decision agreement.
  Traces     : REQ-RPT-041
  Data source: ENT-RPT-001.serviceCode
  Source     : ADR-RPT-005

## A6 — Lookups

```yaml name=lookups
lookups:
  - {key: EMPLOYEE_DECISION, seeded: [APPROVED, REJECTED], open: false, values: [APPROVED, REJECTED], fields: [employeeDecision], entity: ENT-RPT-001, control: lookup, source: "domain-profile §7.1 Employee Decision 'approve / reject'; ADR-RPT-003"}
  - {key: CHECK_STATUS, seeded: [], open: false, values: [AWAITING_DOCUMENTS, RUNNING, COMPLETED, FAILED], fields: [checkStatus], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-001; by value through the Check result port"}
  - {key: OVERALL_STATUS, seeded: [], open: false, values: [COMPLIANT, NOT_COMPLIANT, NEEDS_MANUAL_REVIEW], fields: [overallStatus], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-001; profile closed enum"}
  - {key: CHECK_FAILURE_REASON, seeded: [], open: false, values: [TIMED_OUT, MODEL_UNAVAILABLE, MODEL_OUTPUT_INVALID, MODEL_NOT_PERMITTED, UPLOAD_WINDOW_EXPIRED, INTERRUPTED, INTERNAL_ERROR], fields: [failureReason], owner: CHK, entity: ENT-RPT-001, control: lookup, source: "consumed — CHK CON-CHK-003"}
  - {key: FINDING_OUTCOME, seeded: [], open: false, values: [SATISFIED, NOT_SATISFIED, UNDETERMINED], fields: [findingOutcome], owner: CHK, entity: ENT-RPT-002, control: lookup, source: "consumed — CHK CON-CHK-002"}
  - {key: FETCH_MODE, seeded: [], open: false, values: [path, blob, manual], fields: [fetchMode, sourceMode], owner: DOC, entity: ENT-RPT-001, control: lookup, source: "consumed — DOC CON-DOC-002; profile closed enum; also ENT-RPT-003.sourceMode"}
  - {key: DOCUMENT_READ_STATUS, seeded: [], open: false, values: [READ, MISSING, UNREADABLE], fields: [readStatus], owner: DOC, entity: ENT-RPT-003, control: lookup, source: "consumed — DOC CON-DOC-001; by value through the Check result port"}
  - {key: UNREADABLE_REASON, seeded: [], open: false, values: [OUTSIDE_STORAGE_ROOT, NOT_FOUND, TOO_LARGE, UNSUPPORTED_FORMAT, READING_FAILED, OUT_OF_TIME, SOURCE_QUERY_FAILED, MODEL_NOT_PERMITTED], fields: [unreadableReason], owner: DOC, entity: ENT-RPT-003, control: lookup, source: "consumed — DOC CON-DOC-001"}
  - {key: SERVICE_CODE, seeded: [], open: true, values: [scholarship-request], fields: [serviceCode], owner: REG, entity: ENT-RPT-001, control: reference, source: "consumed — REG by value through CHK; never hardcoded, never a foreign key"}
  - {key: DOCUMENT_TYPE, seeded: [], open: true, values: [TRANSCRIPT, ID_CARD], fields: [documentType], owner: REG, entity: ENT-RPT-003, control: lookup, source: "consumed — REG by value through CHK"}
```

| Key | Labels (en) | Rationale |
|---|---|---|
| EMPLOYEE_DECISION | Approved · Rejected | Closed: the approve / reject decision of the glossary (ADR-RPT-003) |
| CHECK_STATUS, OVERALL_STATUS, CHECK_FAILURE_REASON, FINDING_OUTCOME | as CHK labels them | Consumed from CHK by value; never redefined |
| FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON | as DOC labels them | Consumed from DOC by value through CHK; never redefined |
| SERVICE_CODE, DOCUMENT_TYPE | as REG labels them | Open lists of the service registry; stored as text values |

RPT owns one lookup, EMPLOYEE_DECISION. Every consumed closed list backs an RPT field and is enforced by RULE-RPT-006 (P2 states the codes as CHECK constraints — ADR-CHK-016 hands them to RPT).

## A7 — Status lifecycle

Check status (CHECK_STATUS, CHK's list) as stored on ENT-RPT-001.checkStatus:

```
   create (fetch mode manual)    ──► AWAITING_DOCUMENTS ──mark RUNNING──► RUNNING
   create (fetch mode path|blob) ──────────────────────────────────────► RUNNING ──mark RUNNING (no change)──► RUNNING
                                                                          RUNNING ──complete──► COMPLETED  (final)
   AWAITING_DOCUMENTS ──fail──► FAILED  (final)
   RUNNING            ──fail──► FAILED  (final)
```

Constrained transitions: create → RULE-RPT-001, RULE-RPT-002; every change → RULE-RPT-003; complete → RULE-RPT-004 … RULE-RPT-008; fail → RULE-RPT-010. On COMPLETED the Employee Decision may be recorded once (RULE-RPT-011 … RULE-RPT-014); it is not a status. No approval flow: the Report Store never approves (REQ-RPT-037).

## A8 — Module dependencies
```yaml name=module-dependencies
consumes: []
```
RPT consumes no entity of another module. CHK promises no entity (its Active Check is PRIVATE — CON-CHK contract); RPT implements CHK's Check result port (CON-CHK-006 … CON-CHK-011), so the RPT → CHK edge is the platform edge (ADR-REG-002). The service code, version number, document type and DOC's codes are stored as values with no runtime read of REG or DOC (CON-DOC-001, CON-DOC-002; ADR-RPT-001).

| External service | Purpose | Integration kind |
|---|---|---|
| Check Engine (CHK, in-process) | creates, advances, completes and fails Check runs; reads one Check; lists unfinished Checks | calls RPT's implementation of the Check result port (ADR-REG-002, ADR-CHK-011) |
| Host Integration (INT, in-process) | hands over the Employee Decision | RPT's in-process decision operation (ADR-RPT-003, ADR-RPT-006) |
| Platform configuration | report retention period, purge schedule | read at start-up (ADR-RPT-010) |

# PART B — SCREEN REQUIREMENTS

Not applicable: RPT has no screen of its own (module-registry AUTO-DECISION; [KB:raw-idea.md §15 A1]). The employee reads reports and records decisions in the embedded frontend, which uses RPT's read operations and INT's decision operation.

## API expectations (module level — no screen carries them)
Base path : /api/v1/{resource}
Verbs     : GET only — RPT's HTTP surface is read-only (ADR-RPT-006); every write is in-process
Errors    : ProblemDetail (RFC 9457) → {type, title, status, detail, code}; in-process rejections are typed with the RULE messages above, which INT maps to ProblemDetail

| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| read a Check and its report | GET | /api/v1/checks/{checkId} | checkId | Check with status and, once ended, report or failure | — | REQ-RPT-023 … REQ-RPT-027 |
| list the Checks of a request | GET | /api/v1/checks | serviceCode, requestNumber | up to 100 Checks newest first + total | RULE-RPT-009 | REQ-RPT-028 … REQ-RPT-031, REQ-RPT-050 |
| read the decision agreement of a service | GET | /api/v1/decision-agreement | serviceCode | rows (versionNumber, overallStatus, employeeDecision, count) | RULE-RPT-015 | REQ-RPT-040, REQ-RPT-041 |
| record an Employee Decision (in-process, called by INT) | — | — | checkId, employeeDecision, decidedBy, approvalApiExecuted | Check with the recorded decision or rejection | RULE-RPT-011 … RULE-RPT-014 | REQ-RPT-032 … REQ-RPT-039 |
| Check result port — create a Check run (implements CON-CHK-006) | — | — | serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt | checkId | RULE-RPT-001, RULE-RPT-002, RULE-RPT-006 | REQ-RPT-001 … REQ-RPT-004 |
| Check result port — mark RUNNING (implements CON-CHK-007) | — | — | checkId, runningSince | — | RULE-RPT-003 | REQ-RPT-005, REQ-RPT-006, REQ-RPT-007 |
| Check result port — complete a Check (implements CON-CHK-008) | — | — | checkId, overallStatus, findings, documentOutcomes, unreadQueries, metadata | — | RULE-RPT-003 … RULE-RPT-008 | REQ-RPT-008 … REQ-RPT-017, REQ-RPT-020 |
| Check result port — fail a Check (implements CON-CHK-009) | — | — | checkId, failureReason, detail, endedAt | — | RULE-RPT-003, RULE-RPT-006, RULE-RPT-010 | REQ-RPT-018, REQ-RPT-051 |
| Check result port — read one Check (implements CON-CHK-010) | — | — | checkId | checkId, status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt | — | REQ-RPT-021, REQ-RPT-007 |
| Check result port — list unfinished Checks (implements CON-CHK-011) | — | — | — | list of checkId, status, startedAt | — | REQ-RPT-022 |
| purge (scheduled, no caller) | — | — | platform configuration | count deleted (log) | — | REQ-RPT-042 … REQ-RPT-046, REQ-RPT-052 |

# STANDALONE

## Traceability matrix
| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
|---|---|---|---|---|---|
| US-RPT-001 | REQ-RPT-001, REQ-RPT-002, REQ-RPT-003, REQ-RPT-004 | AC-RPT-001, AC-RPT-002, AC-RPT-003, AC-RPT-004, AC-RPT-005 | RULE-RPT-001, RULE-RPT-002 | ENT-RPT-001 | — |
| US-RPT-002 | REQ-RPT-004, REQ-RPT-005, REQ-RPT-006, REQ-RPT-007 | AC-RPT-006, AC-RPT-007, AC-RPT-008, AC-RPT-009, AC-RPT-010 | RULE-RPT-003 | ENT-RPT-001 | — |
| US-RPT-003 | REQ-RPT-008, REQ-RPT-009, REQ-RPT-010, REQ-RPT-011, REQ-RPT-012, REQ-RPT-013, REQ-RPT-014 | AC-RPT-011, AC-RPT-012, AC-RPT-013, AC-RPT-014, AC-RPT-015, AC-RPT-016, AC-RPT-017 | RULE-RPT-004, RULE-RPT-005 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-004 | REQ-RPT-014, REQ-RPT-015, REQ-RPT-016, REQ-RPT-017, REQ-RPT-018, REQ-RPT-051 | AC-RPT-017, AC-RPT-018, AC-RPT-019, AC-RPT-020, AC-RPT-021, AC-RPT-059 | RULE-RPT-005, RULE-RPT-006, RULE-RPT-007, RULE-RPT-008, RULE-RPT-010 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003 | — |
| US-RPT-005 | REQ-RPT-019, REQ-RPT-047, REQ-RPT-048, REQ-RPT-049 | AC-RPT-022, AC-RPT-055, AC-RPT-056, AC-RPT-057 | — | ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-006 | REQ-RPT-006, REQ-RPT-020 | AC-RPT-008, AC-RPT-009, AC-RPT-023 | RULE-RPT-003 | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-007 | REQ-RPT-007, REQ-RPT-021, REQ-RPT-022 | AC-RPT-010, AC-RPT-024, AC-RPT-025, AC-RPT-026 | — | ENT-RPT-001 | — |
| US-RPT-008 | REQ-RPT-023, REQ-RPT-024, REQ-RPT-025, REQ-RPT-026, REQ-RPT-027 | AC-RPT-027, AC-RPT-028, AC-RPT-029, AC-RPT-030, AC-RPT-031, AC-RPT-032 | — | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |
| US-RPT-009 | REQ-RPT-028, REQ-RPT-029, REQ-RPT-030, REQ-RPT-031, REQ-RPT-050 | AC-RPT-033, AC-RPT-034, AC-RPT-035, AC-RPT-036, AC-RPT-037, AC-RPT-058 | RULE-RPT-009 | ENT-RPT-001, ENT-RPT-002 | — |
| US-RPT-010 | REQ-RPT-032, REQ-RPT-033, REQ-RPT-034, REQ-RPT-035, REQ-RPT-036, REQ-RPT-037, REQ-RPT-038, REQ-RPT-039 | AC-RPT-038, AC-RPT-039, AC-RPT-040, AC-RPT-041, AC-RPT-042, AC-RPT-043, AC-RPT-044, AC-RPT-045, AC-RPT-046 | RULE-RPT-011, RULE-RPT-012, RULE-RPT-013, RULE-RPT-014 | ENT-RPT-001 | — |
| US-RPT-011 | REQ-RPT-040, REQ-RPT-041 | AC-RPT-047, AC-RPT-048, AC-RPT-049 | RULE-RPT-015 | ENT-RPT-001 | — |
| US-RPT-012 | REQ-RPT-042, REQ-RPT-043, REQ-RPT-044, REQ-RPT-045, REQ-RPT-046, REQ-RPT-052 | AC-RPT-050, AC-RPT-051, AC-RPT-052, AC-RPT-053, AC-RPT-054, AC-RPT-060 | — | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003, ENT-RPT-004 | — |

Raw-idea §12 guardrails at RPT's surface (AIAS-1, ADR-RPT-008): (1) LLM analyses only → REQ-RPT-049 · (2) approval only on the employee's action → REQ-RPT-037, REQ-RPT-039 · (3) read-only host access → REQ-RPT-047 · (4) bound parameters → REQ-RPT-050 · (5) storage root → REQ-RPT-048 · (6) nothing skipped silently → REQ-RPT-011, REQ-RPT-012, REQ-RPT-014 · (7) content is data → REQ-RPT-019, REQ-RPT-027 · (8) limits → REQ-RPT-031 · (9) nothing carried between Checks → REQ-RPT-030.

## Decisions applied
| DEFAULT / ADR | What | Source | Override / status |
|---|---|---|---|
| ADR-REG-001 | RPT owns the Check run, findings and Check Document rows | P0 (REG) | ACCEPTED by owner |
| ADR-REG-002 | CHK declares the result port; RPT implements it | P0 (REG) | ACCEPTED by owner |
| ADR-REG-006 | Limits are platform configuration (pattern for the retention period) | P0 (REG) | ACCEPTED by owner |
| ADR-CHK-001 | Closed lists carried by value; RPT stores the codes | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-002 | Overall Status derivation (basis of RULE-RPT-005) | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-005 | FAILED with a reason and no Overall Status; start-up closes unfinished Checks | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-007 | Several independent Checks per request | P0 (CHK) | Confirmed at CHK prd-approval |
| ADR-CHK-011 | The six result port operations | P1 (CHK) | ACCEPTED |
| ADR-CHK-014 | Unread queries as query name + detail | P1 (CHK) | ACCEPTED |
| ADR-CHK-015 | Every Check ends exactly once on CHK's side | P1 (CHK) | ACCEPTED |
| ADR-CHK-017 | Pattern: a module's own HTTP surface is read-only | P3.1 (CHK) | ACCEPTED |
| ADR-DOC-002, ADR-DOC-007 | Read status and unreadable reason lists | DOC | ACCEPTED by owner |
| ADR-DOC-008 | Fetched content lives only for the fetching call | DOC | ACCEPTED by owner |
| ADR-DOC-011 | Pattern: read-only HTTP surface, writes in-process | DOC | ACCEPTED |
| ADR-RPT-001 | Result port implemented; four records; stored whole; codes by value | P0 | Confirmed at prd-approval |
| ADR-RPT-002 | Forward-only status; ended report final | P0 | Confirmed at prd-approval |
| ADR-RPT-003 | Employee Decision on the Check Run, once, COMPLETED only, Approval API flag | P0 | Confirmed at prd-approval |
| ADR-RPT-004 | Retention period and hard-delete purge | P0 | Confirmed at prd-approval |
| ADR-RPT-005 | Three reads; no viewer restriction | P0 | Confirmed at prd-approval |
| ADR-RPT-006 | RPT serves its three reads over HTTP itself; all writes in-process | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-007 | Store-side shape guards: initial status vs fetch mode, metadata agreement, COMPLIANT consistency | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-008 | Every §12 guardrail stated at RPT's surface; listing capped at 100 with a total | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-009 | Decision inputs; recording time is RPT's clock; Approval API flag only with APPROVED | P1 (this stage) | ACCEPTED — non-breaking |
| ADR-RPT-010 | Retention and purge configuration: whole days, schedule default daily 02:00, one transaction per Check run, logged | P1 (this stage) | ACCEPTED — non-breaking |
| DEFAULT — purge schedule daily at 02:00 server time | The purge runs once a day at 02:00 | ADR-RPT-010; domain best practice | Override: set the purge schedule in the platform configuration |
| DEFAULT — age measured from the end time | A Check run's age for the purge counts from endedAt | ADR-RPT-004 | Override: count from startedAt |
| DEFAULT — listing cap 100 | The Checks of a request are returned 100 at most, newest first, with the total | ADR-RPT-008 | Override: add paging in a later version |
| DEFAULT — decidedAt is the recording time | The decision time is RPT's clock when INT hands the decision over | ADR-RPT-009 | Override: INT passes the employee's confirmation time |

## Access summary
| Role | Screens | Operations |
|---|---|---|
| Employee | none in RPT (embedded frontend) | reads a Check and its report, lists the Checks of a request (RPT HTTP); records the decision (INT → RPT in-process) |
| Service Administrator | none | reads the decision agreement (RPT HTTP); sets the report retention period and purge schedule (platform configuration) |
| CHK, INT (in-process) | — | CHK: the six result port operations · INT: record an Employee Decision |
Caller authentication and who may view stored reports are deferred (raw-idea A2, domain-profile D4); no role check is specified in this version.
══════════════════════════════════════════════════════════════════

<<<END INPUT>>>

<<<INPUT: registry-srs>>>
## REGISTRY — P1 — RPT v1

### Entities
| ENT | Name | Kind | Ownership | Status |
|---|---|---|---|---|
| ENT-RPT-001 | Check Run | transactional | SHARED (owner) | REGISTERED |
| ENT-RPT-002 | Finding | transactional | PRIVATE | REGISTERED |
| ENT-RPT-003 | Check Document | transactional | PRIVATE | REGISTERED |
| ENT-RPT-004 | Unread Query | transactional | PRIVATE | REGISTERED |

### Consumed
None — `consumes: []` (RPT implements CHK's Check result port; codes stored by value; ADR-RPT-001).

### Lookups owned
| Key | ENT | Values |
|---|---|---|
| EMPLOYEE_DECISION | ENT-RPT-001 | 2 |

### Lookups consumed
| Key | Owner |
|---|---|
| CHECK_STATUS | CHK |
| OVERALL_STATUS | CHK |
| CHECK_FAILURE_REASON | CHK |
| FINDING_OUTCOME | CHK |
| FETCH_MODE | DOC |
| DOCUMENT_READ_STATUS | DOC |
| UNREADABLE_REASON | DOC |
| SERVICE_CODE | REG |
| DOCUMENT_TYPE | REG |

### Screens
None — no SCR-REQ in this version (RPT has no screen).

### Requirements
REQ count 52 · AC count 60 · RULE count 15 · last sequence per atom (REQ: 52, AC: 60, ENT: 4, RULE: 15, SCR-REQ: 0)

| REQ | AC |
|---|---|
| REQ-RPT-001 | AC-RPT-001 |
| REQ-RPT-002 | AC-RPT-002 |
| REQ-RPT-003 | AC-RPT-003 |
| REQ-RPT-004 | AC-RPT-004, AC-RPT-005 |
| REQ-RPT-005 | AC-RPT-006, AC-RPT-007 |
| REQ-RPT-006 | AC-RPT-008, AC-RPT-009 |
| REQ-RPT-007 | AC-RPT-010 |
| REQ-RPT-008 | AC-RPT-011 |
| REQ-RPT-009 | AC-RPT-012 |
| REQ-RPT-010 | AC-RPT-013 |
| REQ-RPT-011 | AC-RPT-014 |
| REQ-RPT-012 | AC-RPT-015 |
| REQ-RPT-013 | AC-RPT-016 |
| REQ-RPT-014 | AC-RPT-017 |
| REQ-RPT-015 | AC-RPT-018 |
| REQ-RPT-016 | AC-RPT-019 |
| REQ-RPT-017 | AC-RPT-020 |
| REQ-RPT-018 | AC-RPT-021 |
| REQ-RPT-019 | AC-RPT-022 |
| REQ-RPT-020 | AC-RPT-023 |
| REQ-RPT-021 | AC-RPT-024 |
| REQ-RPT-022 | AC-RPT-025, AC-RPT-026 |
| REQ-RPT-023 | AC-RPT-027, AC-RPT-028 |
| REQ-RPT-024 | AC-RPT-029 |
| REQ-RPT-025 | AC-RPT-030 |
| REQ-RPT-026 | AC-RPT-031 |
| REQ-RPT-027 | AC-RPT-032 |
| REQ-RPT-028 | AC-RPT-033, AC-RPT-034 |
| REQ-RPT-029 | AC-RPT-035 |
| REQ-RPT-030 | AC-RPT-036 |
| REQ-RPT-031 | AC-RPT-037 |
| REQ-RPT-032 | AC-RPT-038 |
| REQ-RPT-033 | AC-RPT-039 |
| REQ-RPT-034 | AC-RPT-040 |
| REQ-RPT-035 | AC-RPT-041, AC-RPT-042 |
| REQ-RPT-036 | AC-RPT-043 |
| REQ-RPT-037 | AC-RPT-044 |
| REQ-RPT-038 | AC-RPT-045 |
| REQ-RPT-039 | AC-RPT-046 |
| REQ-RPT-040 | AC-RPT-047, AC-RPT-048 |
| REQ-RPT-041 | AC-RPT-049 |
| REQ-RPT-042 | AC-RPT-050 |
| REQ-RPT-043 | AC-RPT-051 |
| REQ-RPT-044 | AC-RPT-052 |
| REQ-RPT-045 | AC-RPT-053 |
| REQ-RPT-046 | AC-RPT-054 |
| REQ-RPT-047 | AC-RPT-055 |
| REQ-RPT-048 | AC-RPT-056 |
| REQ-RPT-049 | AC-RPT-057 |
| REQ-RPT-050 | AC-RPT-058 |
| REQ-RPT-051 | AC-RPT-059 |
| REQ-RPT-052 | AC-RPT-060 |

| RULE | Traces |
|---|---|
| RULE-RPT-001 | REQ-RPT-003 |
| RULE-RPT-002 | REQ-RPT-004 |
| RULE-RPT-003 | REQ-RPT-005, REQ-RPT-006, REQ-RPT-020 |
| RULE-RPT-004 | REQ-RPT-013 |
| RULE-RPT-005 | REQ-RPT-014 |
| RULE-RPT-006 | REQ-RPT-015 |
| RULE-RPT-007 | REQ-RPT-016 |
| RULE-RPT-008 | REQ-RPT-017 |
| RULE-RPT-009 | REQ-RPT-029 |
| RULE-RPT-010 | REQ-RPT-051 |
| RULE-RPT-011 | REQ-RPT-033 |
| RULE-RPT-012 | REQ-RPT-034 |
| RULE-RPT-013 | REQ-RPT-035 |
| RULE-RPT-014 | REQ-RPT-039 |
| RULE-RPT-015 | REQ-RPT-041 |

### Decisions
ADR-RPT-006, ADR-RPT-007, ADR-RPT-008, ADR-RPT-009, ADR-RPT-010 (new, ACCEPTED); applied ADR-RPT-001 … ADR-RPT-005, ADR-REG-001, ADR-REG-002, ADR-REG-006, ADR-CHK-001, ADR-CHK-002, ADR-CHK-005, ADR-CHK-007, ADR-CHK-011, ADR-CHK-014, ADR-CHK-015, ADR-CHK-017, ADR-DOC-002, ADR-DOC-007, ADR-DOC-008, ADR-DOC-011. BLOCKED: none.

### Event
"P1 completed: RPT v1 — REQ 52 · AC 60 · ENT 4 · RULE 15 · SCR-REQ 0 · ADR 5"

<<<END INPUT>>>

<<<INPUT: registry-db>>>
## REGISTRY — P2 — RPT v1

### Tables
| Table | ENT | Kind | DBF range |
|---|---|---|---|
| RPT_CHECK_RUN | ENT-RPT-001 | transactional | DBF-RPT-001, DBF-RPT-002, DBF-RPT-003, DBF-RPT-004, DBF-RPT-005, DBF-RPT-006, DBF-RPT-007, DBF-RPT-008, DBF-RPT-009, DBF-RPT-010, DBF-RPT-011, DBF-RPT-012, DBF-RPT-013, DBF-RPT-014, DBF-RPT-015, DBF-RPT-016, DBF-RPT-017, DBF-RPT-018, DBF-RPT-019, DBF-RPT-020 |
| RPT_FINDING | ENT-RPT-002 | transactional | DBF-RPT-021, DBF-RPT-022, DBF-RPT-023, DBF-RPT-024, DBF-RPT-025, DBF-RPT-026, DBF-RPT-027, DBF-RPT-028, DBF-RPT-029 |
| RPT_CHECK_DOCUMENT | ENT-RPT-003 | transactional | DBF-RPT-030, DBF-RPT-031, DBF-RPT-032, DBF-RPT-033, DBF-RPT-034, DBF-RPT-035, DBF-RPT-036, DBF-RPT-037, DBF-RPT-038, DBF-RPT-039 |
| RPT_UNREAD_QUERY | ENT-RPT-004 | transactional | DBF-RPT-040, DBF-RPT-041, DBF-RPT-042, DBF-RPT-043, DBF-RPT-044, DBF-RPT-045, DBF-RPT-046 |

### XM index
None — `records: []` (SRS A8 `consumes: []`; the RPT → CHK edge is the platform edge).

### Lookups
| Key | Seeded values | Owner |
|---|---|---|
| EMPLOYEE_DECISION | 2 (service code enum; CHECK on RPT_CHECK_RUN.EMPLOYEE_DECISION) | RPT |
| CHECK_STATUS, OVERALL_STATUS, FINDING_OUTCOME, CHECK_FAILURE_REASON | 0 here (CHECK constraints on RPT columns) | CHK |
| FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON | 0 here (CHECK constraints on RPT columns) | DOC |
| SERVICE_CODE, DOCUMENT_TYPE | 0 here (open lists stored as values) | REG |

### Sequences
last DBF: DBF-RPT-046 · last XM: none

### Decisions
ADR-RPT-011 (ACCEPTED). BLOCKED: none.

### Event
"P2 completed: RPT v1 — 4 tables, 46 DBF, 0 XM"

### Cascade
none by hand — `gov.py graph` derives the edges targeting RPT and raises their resolution events.

<<<END INPUT>>>

<<<INPUT: backend-execution-plan>>>
# BACKEND EXECUTION PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Dialect : oracle19c   Framework : spring-boot-4-java-21 (+ Spring AI 2.0, unused by RPT — REQ-RPT-049)
Inputs : srs-rpt.md · db-script-rpt.md · registry-srs-rpt.md · registry-db-rpt.md · contract-rpt.md · contract-chk.md (the Check result port RPT implements and the closed lists it stores) · contract-doc.md (the closed lists it stores by value)
Governance : FULL (db-script present)   Open ADRs : 0 BLOCKED — decisions applied: ADR-RPT-001 … ADR-RPT-013 (analysis/decisions/RPT/)
══════════════════════════════════════════════════════════════════

## EXECUTION PLAN INDEX — RPT v1

### Entity registry
| ENT | Name | Table | Business code | Operations |
|---|---|---|---|---|
| ENT-RPT-001 | Check Run | RPT_CHECK_RUN | — | in-process create (result port createCheckRun); in-process update (markRunning, completeCheck, failCheck; recordDecision); read (API-RPT-001, API-RPT-002, API-RPT-003; result port getCheck, listUnfinishedChecks); delete (scheduled purge) |
| ENT-RPT-002 | Finding | RPT_FINDING | — | in-process create (completeCheck); read (API-RPT-001); delete (purge, cascade) |
| ENT-RPT-003 | Check Document | RPT_CHECK_DOCUMENT | — | in-process create (completeCheck); read (API-RPT-001); delete (purge, cascade) |
| ENT-RPT-004 | Unread Query | RPT_UNREAD_QUERY | — | in-process create (completeCheck); read (API-RPT-001); delete (purge, cascade) |

### API registry
| API | Operation | Verb | Path | Traces |
|---|---|---|---|---|
| API-RPT-001 | Read a Check and its report | GET | /api/v1/checks/{checkId} | REQ-RPT-023, REQ-RPT-024, REQ-RPT-025, REQ-RPT-026, REQ-RPT-027 |
| API-RPT-002 | List the Checks of a request | GET | /api/v1/checks | REQ-RPT-028, REQ-RPT-029, REQ-RPT-031, REQ-RPT-050 |
| API-RPT-003 | Read the decision agreement of a service | GET | /api/v1/decision-agreement | REQ-RPT-040, REQ-RPT-041 |

### Rule registry
| RULE | Name | Scope | Enforced where | Message en / ar |
|---|---|---|---|---|
| RULE-RPT-001 | A Check run is complete | ENT-RPT-001 | result port createCheckRun | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-002 | Initial status agrees with the fetch mode | ENT-RPT-001 | createCheckRun + CHK_RPT_CHECK_RUN_AWAITING | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-003 | Status moves forward only | ENT-RPT-001 | markRunning, completeCheck, failCheck (conditional UPDATE) | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-004 | Report metadata agrees with its Check run | ENT-RPT-001 | completeCheck | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-005 | COMPLIANT only when fully verified | ENT-RPT-001 | completeCheck | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-006 | Closed codes only | ENT-RPT-001, ENT-RPT-002, ENT-RPT-003 | Java enums at the port + CHECK constraints | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-007 | A finding is complete | ENT-RPT-002 | completeCheck | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-008 | Reason exactly on UNREADABLE | ENT-RPT-003 | completeCheck + CHK_RPT_CHECK_DOCUMENT_REASON | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-009 | A request's Checks need both keys | ENT-RPT-001 | API-RPT-002 (HTTP 400) | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-010 | A failure is complete | ENT-RPT-001 | failCheck | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-011 | One decision per Check | ENT-RPT-001 | recordDecision (conditional UPDATE) | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-012 | Decision only on a completed Check | ENT-RPT-001 | recordDecision + CHK_RPT_CHECK_RUN_DECISION | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-013 | A decision is complete | ENT-RPT-001 | recordDecision | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-014 | Approval API execution only with APPROVED | ENT-RPT-001 | recordDecision + CHK_RPT_CHECK_RUN_DECISION | ✓ / PENDING ADR-RPT-013 |
| RULE-RPT-015 | Decision agreement needs a service code | ENT-RPT-001 | API-RPT-003 (HTTP 400) | ✓ / PENDING ADR-RPT-013 |

### Screen registry
None — RPT has no screen (SRS PART B not applicable); the embedded frontend reads API-RPT-001 and API-RPT-002.

### QRC summary
| QR | Operation | Phase | ENT |
|---|---|---|---|
| QR-RPT-001 | Check Run by identifier | SVC-API | ENT-RPT-001 |
| QR-RPT-002 | Findings of a Check Run | SVC-API | ENT-RPT-002 |
| QR-RPT-003 | Check Documents of a Check Run | SVC-API | ENT-RPT-003 |
| QR-RPT-004 | Unread Queries of a Check Run | SVC-API | ENT-RPT-004 |
| QR-RPT-005 | Newest 100 Checks of a request | SVC-API | ENT-RPT-001 |
| QR-RPT-006 | Number of Checks of a request | SVC-API | ENT-RPT-001 |
| QR-RPT-007 | Decision agreement of a service | SVC-API | ENT-RPT-001 |

DB ALIGNMENT: see manifest — ALIGNED ✓ / issues: 0 · INTEGRATION: 0 edges (RPT consumes no entity; it implements the Check Engine's result port — see CROSS-MOD; ADR-RPT-001) · SECURITY: 0 screens × 0 roles (no permission model, caller authentication deferred — raw-idea A2)

```yaml name=totals
DBF: 46
XM: 0
API: 3
QR: 7
```

## DB ALIGNMENT MANIFEST — RPT v1

Columns, types and SRS references are read from db-script-rpt.md (dbf-matrix) by DBF id. Writer: the reason a required column is written by no HTTP endpoint (every RPT write is in-process — ADR-RPT-006).

| DBF | ENT | Plan property | Plan type | XM | Status | Writer |
|---|---|---|---|---|---|---|
| DBF-RPT-001 | ENT-RPT-001 | checkRunId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-002 | ENT-RPT-001 | serviceCode | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-003 | ENT-RPT-001 | versionNumber | Integer | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-004 | ENT-RPT-001 | fetchMode | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-005 | ENT-RPT-001 | requestNumber | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-006 | ENT-RPT-001 | employeeId | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-007 | ENT-RPT-001 | checkStatus | String | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-008 | ENT-RPT-001 | startedAt | OffsetDateTime | — | ✓ | system-generated — written by the in-process result port `createCheckRun` (status later by `markRunning` / `completeCheck` / `failCheck`) |
| DBF-RPT-009 | ENT-RPT-001 | runningSince | OffsetDateTime | — | ✓ | system-generated — written by the in-process result port `markRunning` |
| DBF-RPT-010 | ENT-RPT-001 | endedAt | OffsetDateTime | — | ✓ | system-generated — written by the in-process result port `completeCheck` (endedAt also by `failCheck`) |
| DBF-RPT-011 | ENT-RPT-001 | overallStatus | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` (endedAt also by `failCheck`) |
| DBF-RPT-012 | ENT-RPT-001 | comparisonModel | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` (endedAt also by `failCheck`) |
| DBF-RPT-013 | ENT-RPT-001 | failureReason | String | — | ✓ | system-generated — written by the in-process result port `failCheck` |
| DBF-RPT-014 | ENT-RPT-001 | failureDetail | String (lob) | — | ✓ | system-generated — written by the in-process result port `failCheck` |
| DBF-RPT-015 | ENT-RPT-001 | employeeDecision | String | — | ✓ | system-generated — written by the in-process `recordDecision` (INT) |
| DBF-RPT-016 | ENT-RPT-001 | decidedBy | String | — | ✓ | system-generated — written by the in-process `recordDecision` (INT) |
| DBF-RPT-017 | ENT-RPT-001 | decidedAt | OffsetDateTime | — | ✓ | system-generated — RPT clock in the in-process `recordDecision` (ADR-RPT-009) |
| DBF-RPT-018 | ENT-RPT-001 | approvalApiExecuted | Boolean | — | ✓ | system-generated — written by the in-process `recordDecision` (INT) |
| DBF-RPT-019 | ENT-RPT-001 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-020 | ENT-RPT-001 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-021 | ENT-RPT-002 | findingId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-022 | ENT-RPT-002 | position | Integer | — | ✓ | system-generated — index of the item in the list `completeCheck` receives, from 1 |
| DBF-RPT-023 | ENT-RPT-002 | conditionText | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-024 | ENT-RPT-002 | findingOutcome | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-025 | ENT-RPT-002 | evidence | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-026 | ENT-RPT-002 | note | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-027 | ENT-RPT-002 | checkRunId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-028 | ENT-RPT-002 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-029 | ENT-RPT-002 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-030 | ENT-RPT-003 | checkDocumentId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-031 | ENT-RPT-003 | position | Integer | — | ✓ | system-generated — index of the item in the list `completeCheck` receives, from 1 |
| DBF-RPT-032 | ENT-RPT-003 | documentType | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-033 | ENT-RPT-003 | sourceMode | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-034 | ENT-RPT-003 | readStatus | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-035 | ENT-RPT-003 | unreadableReason | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-036 | ENT-RPT-003 | detail | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-037 | ENT-RPT-003 | checkRunId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-038 | ENT-RPT-003 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-039 | ENT-RPT-003 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-040 | ENT-RPT-004 | unreadQueryId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-041 | ENT-RPT-004 | position | Integer | — | ✓ | system-generated — index of the item in the list `completeCheck` receives, from 1 |
| DBF-RPT-042 | ENT-RPT-004 | queryName | String | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-043 | ENT-RPT-004 | detail | String (lob) | — | ✓ | system-generated — written by the in-process result port `completeCheck` |
| DBF-RPT-044 | ENT-RPT-004 | checkRunId | Long | — | ✓ | system-generated — identity clause GENERATED BY DEFAULT AS IDENTITY |
| DBF-RPT-045 | ENT-RPT-004 | createdAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |
| DBF-RPT-046 | ENT-RPT-004 | updatedAt | OffsetDateTime | — | ✓ | system-generated — platform-filled standard field |

Legend ✓ aligned. No derived property. No cross-module column: SERVICE_CODE, VERSION_NUMBER, DOCUMENT_TYPE and QUERY_NAME are values carried by the result port, never foreign keys (ADR-RPT-011).

<!-- PHASE:CORE:START traces=REQ-RPT-002,REQ-RPT-015,REQ-RPT-042,REQ-RPT-044,REQ-RPT-047,REQ-RPT-048,REQ-RPT-049 -->
## PHASE CORE — CORE

### R1 — Core configuration
- Type mapping (oracle19c → Java), column types only:

| oracle19c | Java |
|---|---|
| NUMBER(19) | Long |
| NUMBER(10) | Integer |
| VARCHAR2(n CHAR) | String |
| NUMBER(1) | Boolean (0/1) |
| TIMESTAMP WITH TIME ZONE | OffsetDateTime |
| CLOB | String (@Lob) |

- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` — every error-catalog row below is an instance of it, e.g. `RPT-404-CHECK-NOT-FOUND`; the in-process rejection codes of the SVC-API phase follow the same format (ADR-RPT-013).
- Error envelope: ProblemDetail (RFC 9457) → {type, title, status, detail, code}.
- Lookup values: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded. RPT stores codes only. It defines one Java enum of its own, `EmployeeDecision {APPROVED, REJECTED}` (EMPLOYEE_DECISION, CON-RPT-002), and uses by value the enums the Check result port carries: `CheckStatus`, `OverallStatus`, `FindingOutcome`, `CheckFailureReason` (Check Engine) and `FetchMode`, `DocumentReadStatus`, `UnreadableReason` (Document Access) — the published lists are named in CROSS-MOD. Each enum matches the CHECK constraint of the column that stores it (db-script-rpt.md BLOCK 5c); JPA maps them `EnumType.STRING`. SERVICE_CODE and DOCUMENT_TYPE are plain strings, never hardcoded (RULE-RPT-006).
- Workflow engine: **forbidden** — the status lifecycle is guarded by conditional updates in the domain (RULE-RPT-003).
- Search contract: no SRS screen, so no filter list beyond the SRS operations: API-RPT-002 filters on serviceCode + requestNumber (both EXACT, both required) and sorts STARTED_AT DESC only; API-RPT-003 filters on serviceCode. No client-chosen sort, no paging parameters (the profile declares none); an empty result is 200 with an empty list.
- Languages: messages en (SRS); ar PENDING ADR-RPT-013.
- Configuration properties (environment settings, never in the deployable; bound with `@ConfigurationProperties` and validated at start-up):
  - `aias.reports.retention-days` (Integer, optional) — the report retention period in whole days; absent, zero or negative → the purge deletes nothing and logs "Report purge skipped: no valid report retention period is configured." (REQ-RPT-042, REQ-RPT-044; ADR-RPT-004, ADR-RPT-010).
  - `aias.reports.purge-schedule` (cron, default `0 0 2 * * *`) — when the purge runs (ADR-RPT-010).
  - `aias.reports.list-limit` is NOT a property: the listing cap is the constant 100 of REQ-RPT-031 (ADR-RPT-008).
- No host access, no file, no model (REQ-RPT-047, REQ-RPT-048, REQ-RPT-049; ADR-RPT-008): the RPT package declares no dependency on the query channel, on any `DataSource` other than the service's own schema, on file-system APIs or on Spring AI; an architecture test (ArchUnit) fails the build if an RPT class imports `org.springframework.ai`, `java.nio.file` or the query port types.
- Identifiers kept exactly as received (REQ-RPT-002): no trimming, case folding or normalisation of requestNumber, employeeId or decidedBy anywhere in the RPT code path; a blank value is refused, never repaired.
<!-- PHASE:CORE:END -->

<!-- PHASE:DATA-DOM:START traces=ENT-RPT-001,ENT-RPT-002,ENT-RPT-003,ENT-RPT-004,DBF-RPT-001,DBF-RPT-002,DBF-RPT-003,DBF-RPT-004,DBF-RPT-005,DBF-RPT-006,DBF-RPT-007,DBF-RPT-008,DBF-RPT-009,DBF-RPT-010,DBF-RPT-011,DBF-RPT-012,DBF-RPT-013,DBF-RPT-014,DBF-RPT-015,DBF-RPT-016,DBF-RPT-017,DBF-RPT-018,DBF-RPT-019,DBF-RPT-020,DBF-RPT-021,DBF-RPT-022,DBF-RPT-023,DBF-RPT-024,DBF-RPT-025,DBF-RPT-026,DBF-RPT-027,DBF-RPT-028,DBF-RPT-029,DBF-RPT-030,DBF-RPT-031,DBF-RPT-032,DBF-RPT-033,DBF-RPT-034,DBF-RPT-035,DBF-RPT-036,DBF-RPT-037,DBF-RPT-038,DBF-RPT-039,DBF-RPT-040,DBF-RPT-041,DBF-RPT-042,DBF-RPT-043,DBF-RPT-044,DBF-RPT-045,DBF-RPT-046,REQ-RPT-003,REQ-RPT-006,REQ-RPT-020 -->
## PHASE DATA-DOM — DATA+DOM

### ENT-RPT-001 — Check Run      kind: transactional
BINDINGS   table RPT_CHECK_RUN · PK CHECK_RUN_ID (DBF-RPT-001) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · db-script-rpt.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-RPT-001 | checkRunId | CHECK_RUN_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | checkRunId / PENDING ADR-RPT-013 |
| DBF-RPT-002 | serviceCode | SERVICE_CODE | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | serviceCode / PENDING ADR-RPT-013 |
| DBF-RPT-003 | versionNumber | VERSION_NUMBER | NUMBER(10) | NOT NULL | yes (no HTTP write) | — | versionNumber / PENDING ADR-RPT-013 |
| DBF-RPT-004 | fetchMode | FETCH_MODE | VARCHAR2(10 CHAR) | NOT NULL | yes (no HTTP write) | — | fetchMode / PENDING ADR-RPT-013 |
| DBF-RPT-005 | requestNumber | REQUEST_NUMBER | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | requestNumber / PENDING ADR-RPT-013 |
| DBF-RPT-006 | employeeId | EMPLOYEE_ID | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | employeeId / PENDING ADR-RPT-013 |
| DBF-RPT-007 | checkStatus | CHECK_STATUS | VARCHAR2(30 CHAR) | NOT NULL | yes (no HTTP write) | — | checkStatus / PENDING ADR-RPT-013 |
| DBF-RPT-008 | startedAt | STARTED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | startedAt / PENDING ADR-RPT-013 |
| DBF-RPT-009 | runningSince | RUNNING_SINCE | TIMESTAMP WITH TIME ZONE | NULL | yes (no HTTP write) | — | runningSince / PENDING ADR-RPT-013 |
| DBF-RPT-010 | endedAt | ENDED_AT | TIMESTAMP WITH TIME ZONE | NULL | yes (no HTTP write) | — | endedAt / PENDING ADR-RPT-013 |
| DBF-RPT-011 | overallStatus | OVERALL_STATUS | VARCHAR2(30 CHAR) | NULL | yes (no HTTP write) | — | overallStatus / PENDING ADR-RPT-013 |
| DBF-RPT-012 | comparisonModel | COMPARISON_MODEL | VARCHAR2(200 CHAR) | NULL | yes (no HTTP write) | — | comparisonModel / PENDING ADR-RPT-013 |
| DBF-RPT-013 | failureReason | FAILURE_REASON | VARCHAR2(30 CHAR) | NULL | yes (no HTTP write) | — | failureReason / PENDING ADR-RPT-013 |
| DBF-RPT-014 | failureDetail | FAILURE_DETAIL | CLOB | NULL | yes (no HTTP write) | — | failureDetail / PENDING ADR-RPT-013 |
| DBF-RPT-015 | employeeDecision | EMPLOYEE_DECISION | VARCHAR2(30 CHAR) | NULL | yes (no HTTP write) | — | employeeDecision / PENDING ADR-RPT-013 |
| DBF-RPT-016 | decidedBy | DECIDED_BY | VARCHAR2(100 CHAR) | NULL | yes (no HTTP write) | — | decidedBy / PENDING ADR-RPT-013 |
| DBF-RPT-017 | decidedAt | DECIDED_AT | TIMESTAMP WITH TIME ZONE | NULL | yes (no HTTP write) | — | decidedAt / PENDING ADR-RPT-013 |
| DBF-RPT-018 | approvalApiExecuted | APPROVAL_API_EXECUTED | NUMBER(1) | NULL | yes (no HTTP write) | — | approvalApiExecuted / PENDING ADR-RPT-013 |
| DBF-RPT-019 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-RPT-013 |
| DBF-RPT-020 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-RPT-013 |

DTO MEMBERSHIP  create-request: none over HTTP (in-process `createCheckRun` takes serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt) · update-request: none over HTTP · response: CheckReport (API-RPT-001), CheckSummary (API-RPT-002), AgreementRow (API-RPT-003); checkRunId is exposed as `checkId`; createdAt / updatedAt are never exposed
LOOKUP FIELDS  fetchMode → FETCH_MODE · checkStatus → CHECK_STATUS · overallStatus → OVERALL_STATUS · failureReason → CHECK_FAILURE_REASON · employeeDecision → EMPLOYEE_DECISION — each stores the code; no lookup endpoint (closed enums, CHECK constraints — ADR-RPT-011)
DOMAIN RULES
- RULE-RPT-001 — A Check run is complete · trigger: on create Check run · scope: CREATE
  - statement: The system shall require a service code, a version number, a fetch mode, a request number, an employee identity, an initial status and a start time, each present and not blank, to create a Check run.
  - message (en): The Check run was not stored: {field} is missing. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: NOT NULL on DBF-RPT-002 … DBF-RPT-008 (+ app-level blank check) · owner layer: domain (`CheckRun.create`)
- RULE-RPT-002 — Initial status agrees with the fetch mode · trigger: on create Check run · scope: CREATE
  - statement: The system shall require the initial status AWAITING_DOCUMENTS when the fetch mode is `manual` and RUNNING when it is `path` or `blob`.
  - message (en): The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual. / The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_AWAITING (AWAITING_DOCUMENTS ⇒ manual) + app-level (manual ⇒ AWAITING_DOCUMENTS at creation) · owner layer: domain
- RULE-RPT-003 — Status moves forward only · trigger: on mark RUNNING, complete, fail · scope: UPDATE
  - statement: The system shall allow only the changes AWAITING_DOCUMENTS → RUNNING, RUNNING → RUNNING, RUNNING → COMPLETED, AWAITING_DOCUMENTS → FAILED and RUNNING → FAILED, and shall prevent every change from COMPLETED or FAILED.
  - message (en): Check {checkId} has already ended; its status cannot change. / Check {checkId} is not running; it cannot be completed. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_RESULT (shape per status) + conditional UPDATE `WHERE CHECK_STATUS IN (…)` (app-level) · owner layer: domain (`CheckRun.markRunning / complete / fail`) + repository guard
- RULE-RPT-004 — Report metadata agrees with its Check run · trigger: on complete · scope: UPDATE
  - statement: The system shall require the report metadata to carry a comparison model and an end time not earlier than the start time, and its service code, version number, fetch mode, employee identity and start time to equal those stored on the Check run.
  - message (en): The report of Check {checkId} was not stored: its metadata {field} {value} differs from the Check run ({stored}). / The report of Check {checkId} was not stored: {field} is missing. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_ENDED_AT, CHK_RPT_CHECK_RUN_RESULT (model present when COMPLETED) + app-level comparison · owner layer: domain
- RULE-RPT-005 — COMPLIANT only when fully verified · trigger: on complete · scope: UPDATE
  - statement: The system shall prevent storing the Overall Status COMPLIANT when any finding of the report is not SATISFIED or the report has any unread service query.
  - message (en): The report of Check {checkId} was not stored: COMPLIANT needs every finding SATISFIED and every service query read. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: app-level (spans three tables) · owner layer: domain (`CheckReport` value object)
- RULE-RPT-006 — Closed codes only · trigger: on create Check run, complete, fail · scope: CREATE / UPDATE
  - statement: The system shall require every Check status, Overall Status, failure reason, fetch mode, source mode, finding outcome, document read status and unreadable reason to be a code of its closed list in A6.
  - message (en): Not stored: `{value}` is not a code of {lookupKey}. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_CHECK_STATUS, CHK_RPT_CHECK_RUN_FETCH_MODE, CHK_RPT_CHECK_RUN_OVERALL_STATUS, CHK_RPT_CHECK_RUN_FAILURE_REASON, CHK_RPT_FINDING_FINDING_OUTCOME, CHK_RPT_CHECK_DOCUMENT_SOURCE_MODE, CHK_RPT_CHECK_DOCUMENT_READ_STATUS, CHK_RPT_CHECK_DOCUMENT_UNREADABLE_REASON (+ app-level: a null enum on a required field) · owner layer: domain
- RULE-RPT-010 — A failure is complete · trigger: on fail · scope: UPDATE
  - statement: The system shall require a failure reason, a detail not blank and an end time not earlier than the start time to store a failure.
  - message (en): The failure of Check {checkId} was not stored: {field} is missing. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_RESULT, CHK_RPT_CHECK_RUN_ENDED_AT + app-level blank check of FAILURE_DETAIL (CLOB) · owner layer: domain
- RULE-RPT-011 — One decision per Check · trigger: on record decision · scope: UPDATE
  - statement: The system shall prevent recording an Employee Decision on a Check run that already holds one.
  - message (en): Check {checkId} already has an Employee Decision. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: conditional UPDATE `WHERE EMPLOYEE_DECISION IS NULL` · owner layer: domain + repository guard
- RULE-RPT-012 — Decision only on a completed Check · trigger: on record decision · scope: UPDATE
  - statement: The system shall prevent recording an Employee Decision on a Check run whose status is not COMPLETED.
  - message (en): Check {checkId} is not completed; a decision can only be recorded on a completed Check. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_DECISION + conditional UPDATE `AND CHECK_STATUS = 'COMPLETED'` · owner layer: domain
- RULE-RPT-013 — A decision is complete · trigger: on record decision · scope: UPDATE
  - statement: The system shall require a decision code of EMPLOYEE_DECISION, a deciding employee's identity not blank and a yes / no value for execution through the Approval API to record an Employee Decision.
  - message (en): The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. / The decision was not recorded: say whether it was executed through the Approval API. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_EMPLOYEE_DECISION, CHK_RPT_CHECK_RUN_APPROVAL_API_EXECUTED, CHK_RPT_CHECK_RUN_DECISION (all-or-nothing) + app-level · owner layer: domain
- RULE-RPT-014 — Approval API execution only with APPROVED · trigger: on record decision · scope: UPDATE
  - statement: The system shall prevent recording a decision REJECTED as executed through the Approval API.
  - message (en): The decision was not recorded: only an APPROVED decision is executed through the Approval API. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_RUN_DECISION + app-level · owner layer: domain
- RULE-RPT-009, RULE-RPT-015 — request validation of API-RPT-002 and API-RPT-003, stated in full in those blocks.
STATE MACHINE  CHECK_STATUS (DBF-RPT-007) · values AWAITING_DOCUMENTS, RUNNING, COMPLETED, FAILED · initial AWAITING_DOCUMENTS (`manual`) or RUNNING (`path` / `blob`) · transitions: AWAITING_DOCUMENTS → RUNNING (markRunning), RUNNING → RUNNING (markRunning, keeps the first runningSince), RUNNING → COMPLETED (completeCheck), AWAITING_DOCUMENTS | RUNNING → FAILED (failCheck) — actor: the Check Engine through the result port only · terminal COMPLETED, FAILED · invalid transition → RULE-RPT-003 · the Employee Decision is not a status (RULE-RPT-011 … RULE-RPT-014)
CROSS-MODULE   none — 0 XM
REPOSITORY OPS QR-RPT-001, QR-RPT-005, QR-RPT-006, QR-RPT-007 (+ the in-process writes of SVC-API)

### ENT-RPT-002 — Finding      kind: transactional
BINDINGS   table RPT_FINDING · PK FINDING_ID (DBF-RPT-021) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · FK FK_RPT_FINDING_RPT_CHECK_RUN (ON DELETE CASCADE) · db-script-rpt.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-RPT-021 | findingId | FINDING_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | findingId / PENDING ADR-RPT-013 |
| DBF-RPT-022 | position | POSITION | NUMBER(10) | NOT NULL | yes (no HTTP write) | — | position / PENDING ADR-RPT-013 |
| DBF-RPT-023 | conditionText | CONDITION_TEXT | CLOB | NOT NULL | yes (no HTTP write) | — | conditionText / PENDING ADR-RPT-013 |
| DBF-RPT-024 | findingOutcome | FINDING_OUTCOME | VARCHAR2(30 CHAR) | NOT NULL | yes (no HTTP write) | — | findingOutcome / PENDING ADR-RPT-013 |
| DBF-RPT-025 | evidence | EVIDENCE | CLOB | NOT NULL | yes (no HTTP write) | — | evidence / PENDING ADR-RPT-013 |
| DBF-RPT-026 | note | NOTE | CLOB | NOT NULL | yes (no HTTP write) | — | note / PENDING ADR-RPT-013 |
| DBF-RPT-027 | checkRunId | CHECK_RUN_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | FK → RPT_CHECK_RUN (ON DELETE CASCADE) | checkRunId / PENDING ADR-RPT-013 |
| DBF-RPT-028 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-RPT-013 |
| DBF-RPT-029 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-RPT-013 |

DTO MEMBERSHIP  create-request: none over HTTP (in-process `completeCheck` findings [condition, outcome, evidence, note]) · update-request: none — never updated (REQ-RPT-020) · response: FindingView {position, condition, outcome, evidence, note} inside CheckReport
LOOKUP FIELDS  findingOutcome → FINDING_OUTCOME — stores the code
DOMAIN RULES
- RULE-RPT-007 — A finding is complete · trigger: on complete · scope: CREATE
  - statement: The system shall require every finding to carry a condition, an outcome, an evidence and a note, each present and not blank.
  - message (en): The report of Check {checkId} was not stored: finding {position} has no {field}. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: NOT NULL on DBF-RPT-023 … DBF-RPT-026 + app-level blank check (CLOB) · owner layer: domain
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-RPT-002 (+ insert in completeCheck)

### ENT-RPT-003 — Check Document      kind: transactional
BINDINGS   table RPT_CHECK_DOCUMENT · PK CHECK_DOCUMENT_ID (DBF-RPT-030) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · FK FK_RPT_CHECK_DOCUMENT_RPT_CHECK_RUN (ON DELETE CASCADE) · db-script-rpt.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-RPT-030 | checkDocumentId | CHECK_DOCUMENT_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | checkDocumentId / PENDING ADR-RPT-013 |
| DBF-RPT-031 | position | POSITION | NUMBER(10) | NOT NULL | yes (no HTTP write) | — | position / PENDING ADR-RPT-013 |
| DBF-RPT-032 | documentType | DOCUMENT_TYPE | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | documentType / PENDING ADR-RPT-013 |
| DBF-RPT-033 | sourceMode | SOURCE_MODE | VARCHAR2(10 CHAR) | NOT NULL | yes (no HTTP write) | — | sourceMode / PENDING ADR-RPT-013 |
| DBF-RPT-034 | readStatus | READ_STATUS | VARCHAR2(30 CHAR) | NOT NULL | yes (no HTTP write) | — | readStatus / PENDING ADR-RPT-013 |
| DBF-RPT-035 | unreadableReason | UNREADABLE_REASON | VARCHAR2(30 CHAR) | NULL | yes (no HTTP write) | — | unreadableReason / PENDING ADR-RPT-013 |
| DBF-RPT-036 | detail | DETAIL | CLOB | NULL | yes (no HTTP write) | — | detail / PENDING ADR-RPT-013 |
| DBF-RPT-037 | checkRunId | CHECK_RUN_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | FK → RPT_CHECK_RUN (ON DELETE CASCADE) | checkRunId / PENDING ADR-RPT-013 |
| DBF-RPT-038 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-RPT-013 |
| DBF-RPT-039 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-RPT-013 |

DTO MEMBERSHIP  create-request: none over HTTP (in-process `completeCheck` documentOutcomes [documentType, sourceMode, readStatus, reason, detail] — no content field exists, REQ-RPT-019) · update-request: none — never updated · response: DocumentView {position, documentType, sourceMode, readStatus, unreadableReason, detail} inside CheckReport
LOOKUP FIELDS  sourceMode → FETCH_MODE · readStatus → DOCUMENT_READ_STATUS · unreadableReason → UNREADABLE_REASON — each stores the code
DOMAIN RULES
- RULE-RPT-008 — Reason exactly on UNREADABLE · trigger: on complete · scope: CREATE
  - statement: The system shall require an unreadable reason on every UNREADABLE document outcome and prevent one on a READ or MISSING outcome, and shall require a document type and source mode on every outcome.
  - message (en): The report of Check {checkId} was not stored: document {position} is UNREADABLE without a reason. / The report of Check {checkId} was not stored: document {position} is {readStatus} and cannot carry a reason. · message (ar): PENDING ADR-RPT-013
  - DB enforcement: CHK_RPT_CHECK_DOCUMENT_REASON (+ app-level) · owner layer: domain
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-RPT-003 (+ insert in completeCheck)

### ENT-RPT-004 — Unread Query      kind: transactional
BINDINGS   table RPT_UNREAD_QUERY · PK UNREAD_QUERY_ID (DBF-RPT-040) · PK generation `identity` → `NUMBER(19) GENERATED BY DEFAULT AS IDENTITY` · FK FK_RPT_UNREAD_QUERY_RPT_CHECK_RUN (ON DELETE CASCADE) · db-script-rpt.md v1
DEFAULT FIELDS per kind: transactional → createdAt, updatedAt

| DBF | Property | Column | Type | Null | Read-only | Constraint | Label en / ar |
|---|---|---|---|---|---|---|---|
| DBF-RPT-040 | unreadQueryId | UNREAD_QUERY_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | PK | unreadQueryId / PENDING ADR-RPT-013 |
| DBF-RPT-041 | position | POSITION | NUMBER(10) | NOT NULL | yes (no HTTP write) | — | position / PENDING ADR-RPT-013 |
| DBF-RPT-042 | queryName | QUERY_NAME | VARCHAR2(100 CHAR) | NOT NULL | yes (no HTTP write) | — | queryName / PENDING ADR-RPT-013 |
| DBF-RPT-043 | detail | DETAIL | CLOB | NOT NULL | yes (no HTTP write) | — | detail / PENDING ADR-RPT-013 |
| DBF-RPT-044 | checkRunId | CHECK_RUN_ID | NUMBER(19) | NOT NULL | yes (no HTTP write) | FK → RPT_CHECK_RUN (ON DELETE CASCADE) | checkRunId / PENDING ADR-RPT-013 |
| DBF-RPT-045 | createdAt | CREATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | createdAt / PENDING ADR-RPT-013 |
| DBF-RPT-046 | updatedAt | UPDATED_AT | TIMESTAMP WITH TIME ZONE | NOT NULL | yes (no HTTP write) | — | updatedAt / PENDING ADR-RPT-013 |

DTO MEMBERSHIP  create-request: none over HTTP (in-process `completeCheck` unreadQueries [queryName, detail]) · update-request: none — never updated · response: UnreadQueryView {position, queryName, detail} inside CheckReport
LOOKUP FIELDS  none
DOMAIN RULES
- none specific to this entity (presence of queryName and detail is checked with RULE-RPT-007's not-blank guard; the COMPLIANT guard RULE-RPT-005 reads this list)
STATE MACHINE  none
CROSS-MODULE   none
REPOSITORY OPS QR-RPT-004 (+ insert in completeCheck)
<!-- PHASE:DATA-DOM:END -->

<!-- PHASE:PORTS:START traces=REQ-RPT-001,REQ-RPT-005,REQ-RPT-008,REQ-RPT-018,REQ-RPT-021,REQ-RPT-022,REQ-RPT-047,REQ-RPT-048,REQ-RPT-049 -->
## PHASE PORTS — PORTS+ADAPTERS

RPT runs no host query, fetches no document and calls no model: the QUERY, DOCUMENT and MODEL ports of the profile belong to the modules that run Checks and RPT has none of them (REQ-RPT-047, REQ-RPT-048, REQ-RPT-049). RPT has one inbound adapter:
- `CheckResultStore` — the RPT bean that **implements the Check result port interface `CheckResultPort` declared by the Check Engine** (ADR-RPT-001). It is the only class of RPT that imports the port and the carried enums; each method delegates to `CheckRunCommandService` / `CheckRunQueryService` (SVC-API). Implements, one method each, exactly as the Check Engine's published contract signs them: `createCheckRun`, `markRunning`, `completeCheck`, `failCheck`, `getCheck`, `listUnfinishedChecks` (the contract items are listed in CROSS-MOD). The Check Engine never reads RPT tables and RPT never calls the Check Engine.
- Exceptions and transactions at the port (ADR-RPT-012): every method joins the caller's transaction (`Propagation.REQUIRED`). Every RULE is checked on the values received **before the first write**; a refusal throws an `RptRefusalException` subclass (a `RuntimeException`, code and message from the SVC-API table) declared `noRollbackFor`, so the caller's transaction stays usable and the Check Engine can still fail the Check in the same transaction. A database failure during the writes propagates and rolls the caller's transaction back whole — nothing of the report is left (REQ-RPT-009).
<!-- PHASE:PORTS:END -->

<!-- PHASE:SVC-API:START traces=DBF-RPT-001,DBF-RPT-002,DBF-RPT-003,DBF-RPT-004,DBF-RPT-005,DBF-RPT-006,DBF-RPT-007,DBF-RPT-008,DBF-RPT-009,DBF-RPT-010,DBF-RPT-011,DBF-RPT-012,DBF-RPT-013,DBF-RPT-014,DBF-RPT-015,DBF-RPT-016,DBF-RPT-017,DBF-RPT-018,DBF-RPT-022,DBF-RPT-023,DBF-RPT-024,DBF-RPT-025,DBF-RPT-026,DBF-RPT-031,DBF-RPT-032,DBF-RPT-033,DBF-RPT-034,DBF-RPT-035,DBF-RPT-036,DBF-RPT-041,DBF-RPT-042,DBF-RPT-043,REQ-RPT-001,REQ-RPT-005,REQ-RPT-008,REQ-RPT-018,REQ-RPT-021,REQ-RPT-022,REQ-RPT-023,REQ-RPT-028,REQ-RPT-032,REQ-RPT-040,REQ-RPT-043 -->
## PHASE SVC-API — SVC+API

### Service layer — the Check result port implementation (`CheckRunCommandService`, `CheckRunQueryService`)
All writes are READ_WRITE in the caller's transaction (ADR-RPT-012); timestamps are those the Check Engine hands over, except createdAt / updatedAt (DEFAULT SYSTIMESTAMP; updatedAt set to SYSTIMESTAMP on every UPDATE).

**`createCheckRun(serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt)` → checkId** (port operation; REQ-RPT-001 … REQ-RPT-004, REQ-RPT-015, REQ-RPT-030)
  1. RULE-RPT-001 — any value absent or blank → `CheckRunIncompleteException` (RPT-400-CHECK-RUN-INCOMPLETE) "The Check run was not stored: {field} is missing." (REQ-RPT-003).
  2. RULE-RPT-006 — a code outside its enum (status not RUNNING / AWAITING_DOCUMENTS at creation counts as outside) → `UnknownCodeException` (RPT-422-UNKNOWN-CODE) "Not stored: `{value}` is not a code of {lookupKey}." (REQ-RPT-015).
  3. RULE-RPT-002 — status vs fetch mode → `InitialStatusMismatchException` (RPT-422-INITIAL-STATUS-MISMATCH) with the RULE message (REQ-RPT-004).
  4. Persist: INSERT INTO RPT_CHECK_RUN (SERVICE_CODE DBF-RPT-002, VERSION_NUMBER DBF-RPT-003, FETCH_MODE DBF-RPT-004, REQUEST_NUMBER DBF-RPT-005, EMPLOYEE_ID DBF-RPT-006 — both exactly as received, REQ-RPT-002; CHECK_STATUS DBF-RPT-007, STARTED_AT DBF-RPT-008); CHECK_RUN_ID DBF-RPT-001 by the identity clause; every result and decision column NULL; return CHECK_RUN_ID as checkId. A second Check of the same request is simply another row — nothing is looked up or copied (REQ-RPT-030).
  - Concurrency: NONE — the identifier is allocated by the identity clause; no read precedes the insert.

**`markRunning(checkId, runningSince)`** (port operation; REQ-RPT-005 … REQ-RPT-007)
  1. UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'RUNNING' (DBF-RPT-007), RUNNING_SINCE = COALESCE(RUNNING_SINCE, :runningSince) (DBF-RPT-009), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING').
  2. 0 rows → read QR-RPT-001: no row → `CheckNotFoundException` (RPT-404-CHECK-NOT-FOUND) "Check {checkId} was not found." (REQ-RPT-007); row ended → `CheckEndedException` (RPT-409-CHECK-ENDED) "Check {checkId} has already ended; its status cannot change." (RULE-RPT-003, REQ-RPT-006).
  - Concurrency: the conditional UPDATE is the guard — a concurrent end makes it update 0 rows.

**`completeCheck(checkId, overallStatus, findings, documentOutcomes, unreadQueries, metadata)`** (port operation; REQ-RPT-008 … REQ-RPT-017, REQ-RPT-019, REQ-RPT-020)
  1. Load the Check Run (QR-RPT-001) with a locking read `FOR UPDATE`; none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-007); COMPLETED / FAILED → RPT-409-CHECK-ENDED (REQ-RPT-020); AWAITING_DOCUMENTS → `CheckNotRunningException` (RPT-409-CHECK-NOT-RUNNING) "Check {checkId} is not running; it cannot be completed." (RULE-RPT-003, REQ-RPT-006).
  2. Validate the whole report in memory, in this order, before any write (ADR-RPT-012): RULE-RPT-006 codes (REQ-RPT-015) → RULE-RPT-004 metadata vs the loaded row (`MetadataMismatchException`, RPT-422-METADATA-MISMATCH; REQ-RPT-013) → RULE-RPT-007 every finding complete (`FindingIncompleteException`, RPT-422-FINDING-INCOMPLETE; REQ-RPT-016) and every unread query with name and detail → RULE-RPT-008 reasons (`DocumentReasonMismatchException`, RPT-422-DOCUMENT-REASON-MISMATCH; REQ-RPT-017) → RULE-RPT-005 COMPLIANT guard (`CompliantNotVerifiedException`, RPT-422-COMPLIANT-NOT-VERIFIED; REQ-RPT-014). Messages exactly as the DATA-DOM rules state them.
  3. Persist in this order: UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'COMPLETED' (DBF-RPT-007), OVERALL_STATUS (DBF-RPT-011), COMPARISON_MODEL (DBF-RPT-012), ENDED_AT (DBF-RPT-010), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS = 'RUNNING'; INSERT one RPT_FINDING per finding (POSITION DBF-RPT-022 = index from 1, CONDITION_TEXT DBF-RPT-023, FINDING_OUTCOME DBF-RPT-024, EVIDENCE DBF-RPT-025, NOTE DBF-RPT-026, CHECK_RUN_ID DBF-RPT-027); one RPT_CHECK_DOCUMENT per document outcome (POSITION DBF-RPT-031, DOCUMENT_TYPE DBF-RPT-032, SOURCE_MODE DBF-RPT-033, READ_STATUS DBF-RPT-034, UNREADABLE_REASON DBF-RPT-035, DETAIL DBF-RPT-036, CHECK_RUN_ID DBF-RPT-037); one RPT_UNREAD_QUERY per unread query (POSITION DBF-RPT-041, QUERY_NAME DBF-RPT-042, DETAIL DBF-RPT-043, CHECK_RUN_ID DBF-RPT-044) — order kept as received (REQ-RPT-010, REQ-RPT-011, REQ-RPT-012). No document content and no query rows exist in the carried values or in any column (REQ-RPT-019).
  4. A database failure during step 3 → the exception propagates (`ReportNotStoredException`, RPT-500-REPORT-NOT-STORED) and the caller's transaction rolls back whole: no part of the report remains and the Check stays RUNNING (REQ-RPT-009).
  - Concurrency: the `FOR UPDATE` read and the conditional UPDATE serialise a completion against a concurrent fail — the second finds the row ended and is refused with RPT-409-CHECK-ENDED.

**`failCheck(checkId, failureReason, detail, endedAt)`** (port operation; REQ-RPT-018, REQ-RPT-051)
  1. RULE-RPT-010 — reason, detail (not blank) and endedAt present, endedAt ≥ STARTED_AT → else `FailureIncompleteException` (RPT-400-FAILURE-INCOMPLETE) "The failure of Check {checkId} was not stored: {field} is missing." (REQ-RPT-051); RULE-RPT-006 on the reason (REQ-RPT-015).
  2. UPDATE RPT_CHECK_RUN SET CHECK_STATUS = 'FAILED' (DBF-RPT-007), FAILURE_REASON (DBF-RPT-013), FAILURE_DETAIL (DBF-RPT-014), ENDED_AT (DBF-RPT-010), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING'); OVERALL_STATUS and COMPARISON_MODEL stay NULL and no Finding is written (REQ-RPT-018).
  3. 0 rows → as markRunning step 2 (RPT-404-CHECK-NOT-FOUND / RPT-409-CHECK-ENDED).
  - Concurrency: the conditional UPDATE is the guard.

**`getCheck(checkId)` → {checkId, status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt}** (port operation; REQ-RPT-021) — READ_ONLY; QR-RPT-001 projected; none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-007).

**`listUnfinishedChecks()` → list of {checkId, status, startedAt}** (port operation; REQ-RPT-022) — READ_ONLY; SELECT CHECK_RUN_ID, CHECK_STATUS, STARTED_AT FROM RPT_CHECK_RUN WHERE CHECK_STATUS IN ('AWAITING_DOCUMENTS', 'RUNNING') ORDER BY STARTED_AT (IDX_RPT_CHECK_RUN_CHECK_STATUS); none → empty list.

### Service layer — the in-process interface `ReportStore` offered to Host Integration (contract-rpt.md)
Injected into INT (profile `module_interface: in_process`). Value objects are Java records with unmodifiable lists. RPT never calls an Approval API and never sets a decision on its own (REQ-RPT-037; AIAS-4) — there is no outbound HTTP client in RPT.

**`readCheck(checkId)` → CheckReport** — the same service method that serves API-RPT-001 (`CheckReportQueryService.read`).
  - Honours: CON-RPT-003

**`listChecksOfRequest(serviceCode, requestNumber)` → ChecksOfRequest** — the same service method that serves API-RPT-002.
  - Honours: CON-RPT-004

**`readDecisionAgreement(serviceCode)` → list of AgreementRow** — the same service method that serves API-RPT-003.
  - Honours: CON-RPT-005

**`recordDecision(checkId, employeeDecision, decidedBy, approvalApiExecuted)` → RecordedDecision {checkId, employeeDecision, decidedBy, decidedAt, approvalApiExecuted}** — READ_WRITE, its own transaction (REQUIRED from INT). (REQ-RPT-032 … REQ-RPT-039)
  - Honours: CON-RPT-006
  1. RULE-RPT-013 — decision not APPROVED / REJECTED, decidedBy blank, or approvalApiExecuted absent → `DecisionIncompleteException` (RPT-400-DECISION-INCOMPLETE) with the RULE message (REQ-RPT-035).
  2. RULE-RPT-014 — REJECTED with approvalApiExecuted = true → `ApprovalFlagOnRejectionException` (RPT-422-APPROVAL-FLAG-ON-REJECTION) "The decision was not recorded: only an APPROVED decision is executed through the Approval API." (REQ-RPT-039).
  3. Persist: UPDATE RPT_CHECK_RUN SET EMPLOYEE_DECISION (DBF-RPT-015), DECIDED_BY (DBF-RPT-016 — exactly as received, REQ-RPT-002), DECIDED_AT = SYSTIMESTAMP (DBF-RPT-017, ADR-RPT-009), APPROVAL_API_EXECUTED (DBF-RPT-018, REQ-RPT-036), UPDATED_AT = SYSTIMESTAMP WHERE CHECK_RUN_ID = :checkId AND CHECK_STATUS = 'COMPLETED' AND EMPLOYEE_DECISION IS NULL. Nothing else of the row or of its report changes (REQ-RPT-020).
  4. 0 rows → read QR-RPT-001: none → RPT-404-CHECK-NOT-FOUND (REQ-RPT-038); a decision present → `DecisionAlreadyRecordedException` (RPT-409-DECISION-ALREADY-RECORDED) "Check {checkId} already has an Employee Decision." (RULE-RPT-011, REQ-RPT-033); status not COMPLETED → `CheckNotCompletedException` (RPT-409-CHECK-NOT-COMPLETED) "Check {checkId} is not completed; a decision can only be recorded on a completed Check." (RULE-RPT-012, REQ-RPT-034).
  - Concurrency: the conditional UPDATE is the guard — two simultaneous decisions both pass validation, the database lets exactly one update the row; the other updates 0 rows and is refused with RPT-409-DECISION-ALREADY-RECORDED.

### Service layer — the scheduled purge (`ReportPurgeService`, ADR-RPT-004, ADR-RPT-010)
`@Scheduled(cron = aias.reports.purge-schedule)`; no HTTP API, no caller (REQ-RPT-042 … REQ-RPT-046, REQ-RPT-052).
  1. retention-days absent or < 1 → log "Report purge skipped: no valid report retention period is configured." and return; nothing deleted (REQ-RPT-044).
  2. cutOff = now − retention-days; SELECT CHECK_RUN_ID FROM RPT_CHECK_RUN WHERE CHECK_STATUS IN ('COMPLETED', 'FAILED') AND ENDED_AT < :cutOff (IDX_RPT_CHECK_RUN_ENDED_AT) — AWAITING_DOCUMENTS and RUNNING rows are never selected, whatever their age (REQ-RPT-045); rows ended within the period are never selected (REQ-RPT-042).
  3. For each id, in its own transaction (`REQUIRES_NEW`): DELETE FROM RPT_CHECK_RUN WHERE CHECK_RUN_ID = :id AND CHECK_STATUS IN ('COMPLETED', 'FAILED') AND ENDED_AT < :cutOff — FK_RPT_FINDING_RPT_CHECK_RUN, FK_RPT_CHECK_DOCUMENT_RPT_CHECK_RUN and FK_RPT_UNREAD_QUERY_RPT_CHECK_RUN cascade the Findings, Check Documents and Unread Queries; the Employee Decision is a column of the row (REQ-RPT-043). A failure rolls back that Check Run only, which stays whole, is logged, and the purge continues (REQ-RPT-052).
  4. Log "Report purge deleted {n} Check runs ended before {cutOff}." (REQ-RPT-046).
  - Concurrency: two instances purging at once delete the same row at most once; the second DELETE finds 0 rows and counts nothing.

### In-process rejection codes (typed exceptions of the result port and of `ReportStore` — ADR-RPT-012, ADR-RPT-013)
| Code | Rule / REQ | Exception | Message en | Message ar |
|---|---|---|---|---|
| RPT-400-CHECK-RUN-INCOMPLETE | RULE-RPT-001 | CheckRunIncompleteException | The Check run was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-422-INITIAL-STATUS-MISMATCH | RULE-RPT-002 | InitialStatusMismatchException | The Check run was not stored: status AWAITING_DOCUMENTS needs fetch mode manual. / The Check run was not stored: a manual Check starts AWAITING_DOCUMENTS. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-ENDED | RULE-RPT-003 | CheckEndedException | Check {checkId} has already ended; its status cannot change. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-NOT-RUNNING | RULE-RPT-003 | CheckNotRunningException | Check {checkId} is not running; it cannot be completed. | PENDING ADR-RPT-013 |
| RPT-404-CHECK-NOT-FOUND | REQ-RPT-007, REQ-RPT-038 | CheckNotFoundException | Check {checkId} was not found. | PENDING ADR-RPT-013 |
| RPT-422-METADATA-MISMATCH | RULE-RPT-004 | MetadataMismatchException | The report of Check {checkId} was not stored: its metadata {field} {value} differs from the Check run ({stored}). / The report of Check {checkId} was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-422-COMPLIANT-NOT-VERIFIED | RULE-RPT-005 | CompliantNotVerifiedException | The report of Check {checkId} was not stored: COMPLIANT needs every finding SATISFIED and every service query read. | PENDING ADR-RPT-013 |
| RPT-422-UNKNOWN-CODE | RULE-RPT-006 | UnknownCodeException | Not stored: `{value}` is not a code of {lookupKey}. | PENDING ADR-RPT-013 |
| RPT-422-FINDING-INCOMPLETE | RULE-RPT-007 | FindingIncompleteException | The report of Check {checkId} was not stored: finding {position} has no {field}. | PENDING ADR-RPT-013 |
| RPT-422-DOCUMENT-REASON-MISMATCH | RULE-RPT-008 | DocumentReasonMismatchException | The report of Check {checkId} was not stored: document {position} is UNREADABLE without a reason. / The report of Check {checkId} was not stored: document {position} is {readStatus} and cannot carry a reason. | PENDING ADR-RPT-013 |
| RPT-400-FAILURE-INCOMPLETE | RULE-RPT-010 | FailureIncompleteException | The failure of Check {checkId} was not stored: {field} is missing. | PENDING ADR-RPT-013 |
| RPT-409-DECISION-ALREADY-RECORDED | RULE-RPT-011 | DecisionAlreadyRecordedException | Check {checkId} already has an Employee Decision. | PENDING ADR-RPT-013 |
| RPT-409-CHECK-NOT-COMPLETED | RULE-RPT-012 | CheckNotCompletedException | Check {checkId} is not completed; a decision can only be recorded on a completed Check. | PENDING ADR-RPT-013 |
| RPT-400-DECISION-INCOMPLETE | RULE-RPT-013 | DecisionIncompleteException | The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. / The decision was not recorded: say whether it was executed through the Approval API. | PENDING ADR-RPT-013 |
| RPT-422-APPROVAL-FLAG-ON-REJECTION | RULE-RPT-014 | ApprovalFlagOnRejectionException | The decision was not recorded: only an APPROVED decision is executed through the Approval API. | PENDING ADR-RPT-013 |
| RPT-500-REPORT-NOT-STORED | REQ-RPT-009 | ReportNotStoredException | The report of Check {checkId} was not stored. | PENDING ADR-RPT-013 |

### HTTP endpoints
Controller `CheckReportController` → services `CheckReportQueryService`, `DecisionAgreementQueryService`. RPT exposes no POST, PUT or DELETE (ADR-RPT-006); every value is returned exactly as stored, as JSON string data — no HTML, no template rendering (REQ-RPT-027).

<!-- API:API-RPT-001:START traces=REQ-RPT-023,REQ-RPT-024,REQ-RPT-025,REQ-RPT-026,REQ-RPT-027,DBF-RPT-001,DBF-RPT-002,DBF-RPT-003,DBF-RPT-004,DBF-RPT-005,DBF-RPT-006,DBF-RPT-007,DBF-RPT-008,DBF-RPT-009,DBF-RPT-010,DBF-RPT-011,DBF-RPT-012,DBF-RPT-013,DBF-RPT-014,DBF-RPT-015,DBF-RPT-016,DBF-RPT-017,DBF-RPT-018,DBF-RPT-022,DBF-RPT-023,DBF-RPT-024,DBF-RPT-025,DBF-RPT-026,DBF-RPT-031,DBF-RPT-032,DBF-RPT-033,DBF-RPT-034,DBF-RPT-035,DBF-RPT-036,DBF-RPT-041,DBF-RPT-042,DBF-RPT-043 -->
### API-RPT-001 — Read a Check and its report
Entity       : ENT-RPT-001
Endpoint     : /api/v1/checks/{checkId}   verb: GET
Layers       : controller CheckReportController → service CheckReportQueryService
Request      : path checkId (DBF-RPT-001, integer int64, required); no query parameter; no body
Response     : 200 · CheckReport {checkId (DBF-RPT-001), status (DBF-RPT-007), serviceCode (DBF-RPT-002), versionNumber (DBF-RPT-003), fetchMode (DBF-RPT-004), requestNumber (DBF-RPT-005), employeeId (DBF-RPT-006), startedAt (DBF-RPT-008), runningSince (DBF-RPT-009), endedAt (DBF-RPT-010), overallStatus (DBF-RPT-011, COMPLETED only), comparisonModel (DBF-RPT-012, COMPLETED only), failureReason (DBF-RPT-013, FAILED only), failureDetail (DBF-RPT-014, FAILED only), findings [FindingView {position (DBF-RPT-022), condition (DBF-RPT-023), outcome (DBF-RPT-024), evidence (DBF-RPT-025), note (DBF-RPT-026)}], documents [DocumentView {position (DBF-RPT-031), documentType (DBF-RPT-032), sourceMode (DBF-RPT-033), readStatus (DBF-RPT-034), unreadableReason (DBF-RPT-035), detail (DBF-RPT-036)}], unreadQueries [UnreadQueryView {position (DBF-RPT-041), queryName (DBF-RPT-042), detail (DBF-RPT-043)}], decision {employeeDecision (DBF-RPT-015), decidedBy (DBF-RPT-016), decidedAt (DBF-RPT-017), approvalApiExecuted (DBF-RPT-018)} or null} · lists empty until COMPLETED · not paginated · no envelope
Validations  : checkId numeric (PLATFORM-STD, ADR-RPT-013)
Errors       : RPT-400-CHECK-ID-INVALID (400, PLATFORM-STD) · RPT-404-CHECK-NOT-FOUND (404, PLATFORM-STD — REQ-RPT-025; also a purged Check) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate checkId → load the Check Run (QR-RPT-001) → none → RPT-404-CHECK-NOT-FOUND → COMPLETED: load Findings (QR-RPT-002), Check Documents (QR-RPT-003), Unread Queries (QR-RPT-004), each ordered by POSITION → map to CheckReport, each finding one entry with its evidence (REQ-RPT-024); FAILED: reason and detail, no Overall Status (REQ-RPT-026); writes nothing
Repository   : QR-RPT-001, QR-RPT-002, QR-RPT-003, QR-RPT-004 · FIND_ONE / FIND_BY_CRITERIA · join NONE (four single-table reads) · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes; the four reads run in one READ_ONLY transaction so a report is read as one consistent state
Security     : none — no permission model, endpoints are open per the SRS (caller authentication and report-viewing rights deferred, raw-idea A2, domain-profile D4; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-003, CON-RPT-001
Covers       : the data this endpoint returns is written only by the in-process result port and decision procedures of this phase, which implement and are listed here for the coverage check: REQ-RPT-001, REQ-RPT-002, REQ-RPT-003, REQ-RPT-004, REQ-RPT-005, REQ-RPT-006, REQ-RPT-007, REQ-RPT-008, REQ-RPT-009, REQ-RPT-010, REQ-RPT-011, REQ-RPT-012, REQ-RPT-013, REQ-RPT-014, REQ-RPT-015, REQ-RPT-016, REQ-RPT-017, REQ-RPT-018, REQ-RPT-019, REQ-RPT-020, REQ-RPT-021, REQ-RPT-022, REQ-RPT-032, REQ-RPT-033, REQ-RPT-034, REQ-RPT-035, REQ-RPT-036, REQ-RPT-037, REQ-RPT-038, REQ-RPT-039, REQ-RPT-051; the purge (REQ-RPT-042, REQ-RPT-043, REQ-RPT-044, REQ-RPT-045, REQ-RPT-046, REQ-RPT-052) is why a purged Check answers RPT-404-CHECK-NOT-FOUND; CORE carries REQ-RPT-047, REQ-RPT-048, REQ-RPT-049
<!-- API:API-RPT-001:END -->

<!-- API:API-RPT-002:START traces=REQ-RPT-028,REQ-RPT-029,REQ-RPT-030,REQ-RPT-031,REQ-RPT-050,DBF-RPT-001,DBF-RPT-002,DBF-RPT-005,DBF-RPT-007,DBF-RPT-008,DBF-RPT-010,DBF-RPT-011,DBF-RPT-015 -->
### API-RPT-002 — List the Checks of a request
Entity       : ENT-RPT-001
Endpoint     : /api/v1/checks   verb: GET
Layers       : controller CheckReportController → service CheckReportQueryService
Request      : query serviceCode (DBF-RPT-002, string ≤ 100, required) · query requestNumber (DBF-RPT-005, string ≤ 100, required) — both matched exactly as sent; no body
Response     : 200 · ChecksOfRequest {total (count of every Check of the pair), checks [CheckSummary {checkId (DBF-RPT-001), status (DBF-RPT-007), overallStatus (DBF-RPT-011), startedAt (DBF-RPT-008), endedAt (DBF-RPT-010), employeeDecision (DBF-RPT-015)}] — at most 100, newest first (STARTED_AT DESC, CHECK_RUN_ID DESC)} · not paginated (fixed cap, REQ-RPT-031) · no envelope
Validations  : RULE-RPT-009 — A request's Checks need both keys · trigger: on list Checks of a request · statement: The system shall require a service code and a request number, both present and not blank, to list the Checks of a request. · message (en): Both a service code and a request number are needed to list Checks. · message (ar): PENDING ADR-RPT-013
Errors       : RPT-400-REQUEST-KEYS-MISSING (400, RULE-RPT-009) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate RULE-RPT-009 → newest 100 (QR-RPT-005) and total (QR-RPT-006), both with serviceCode and requestNumber as bound parameters (REQ-RPT-050) → ChecksOfRequest; each Check is its own row, nothing merged across Checks (REQ-RPT-030); writes nothing
Repository   : QR-RPT-005, QR-RPT-006 · FIND_BY_CRITERIA / COUNT · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-004
<!-- API:API-RPT-002:END -->

<!-- API:API-RPT-003:START traces=REQ-RPT-040,REQ-RPT-041,DBF-RPT-002,DBF-RPT-003,DBF-RPT-011,DBF-RPT-015 -->
### API-RPT-003 — Read the decision agreement of a service
Entity       : ENT-RPT-001
Endpoint     : /api/v1/decision-agreement   verb: GET
Layers       : controller CheckReportController → service DecisionAgreementQueryService
Request      : query serviceCode (DBF-RPT-002, string ≤ 100, required); no body
Response     : 200 · array of AgreementRow {versionNumber (DBF-RPT-003), overallStatus (DBF-RPT-011), employeeDecision (DBF-RPT-015), count} ordered by versionNumber DESC, overallStatus, employeeDecision · only Checks with a recorded decision · empty array when none · not paginated · no envelope
Validations  : RULE-RPT-015 — Decision agreement needs a service code · trigger: on read decision agreement · statement: The system shall require a service code, present and not blank, to read the decision agreement. · message (en): A service code is needed to read the decision agreement. · message (ar): PENDING ADR-RPT-013
Errors       : RPT-400-SERVICE-CODE-MISSING (400, RULE-RPT-015) · RPT-500 (500, PLATFORM-STD)
Orchestration : validate RULE-RPT-015 → aggregate (QR-RPT-007, serviceCode bound) → AgreementRow list; writes nothing
Repository   : QR-RPT-007 · AGGREGATE · join NONE · READ_ONLY
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
Security     : none — no permission model, endpoints are open per the SRS (caller authentication deferred, raw-idea A2; no REQ of the SRS names a role check)
Localization : messages en per SRS; ar PENDING ADR-RPT-013
Honours      : CON-RPT-005, CON-RPT-002
<!-- API:API-RPT-003:END -->

<!-- PHASE:SVC-API:END -->

<!-- PHASE:ALIGN-BE:START traces=REQ-RPT-023,ENT-RPT-001 -->
## PHASE ALIGN-BE — ALIGN-BE

```
ALIGN — RPT v1
row               backing check        assertion
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF
COVERED           orphans              every REQ is covered by ≥1 API or DBF
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares for it
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not
QRC (§5)          orphans              every catalogued query is reached by ≥1 API
API (R3)          code-format          every catalog code is an instance of the declared format and carries a status the platform can emit
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-rpt.yaml and every operation one block, agreeing on method and path
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation of api-spec-rpt.yaml with its status and code
DOCUMENT VALID    api-spec-valid       api-spec-rpt.yaml validates against OPENAPI 3.1.0 and reaches every required item
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from, or is deferred
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered — examined nothing (0 XM)
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase, and nothing else names its target — examined nothing (0 XM)
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's own registry
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why it is not — examined nothing (0 SCR-REQ)
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/RPT/
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves to something that exists
COVERAGE          (the report)         as stamped by the orchestrator from the analyze report
```
```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C7.19
- C7.20
- C7.23
- C7.24
- C7.26
- C7.27
- C7.28
- C7.5
- C7.5b
```
<!-- PHASE:ALIGN-BE:END -->

<!-- PHASE:CROSS-MOD:START traces=REQ-RPT-001 -->
## PHASE CROSS-MOD — CROSS-MODULE

No edge: RPT consumes no entity of another module (SRS A8 `consumes: []`, db-script `records: []`). The Check result port RPT implements is the Check Engine's declared port (ADR-RPT-001) — an inbound call, not a dependency on Check Engine data. RPT's `CheckResultStore` implements its six operations as contract-chk.md promises them: `createCheckRun` (CON-CHK-006), `markRunning` (CON-CHK-007), `completeCheck` (CON-CHK-008), `failCheck` (CON-CHK-009), `getCheck` (CON-CHK-010), `listUnfinishedChecks` (CON-CHK-011); the codes it stores are those of CON-CHK-001 … CON-CHK-003 and of Document Access CON-DOC-001, CON-DOC-002, enforced by the CHECK constraints of db-script-rpt.md; inbound edges from Host Integration reach RPT through contract-rpt.md.
<!-- PHASE:CROSS-MOD:END -->

## QUERY REFERENCE CATALOG — RPT v1

### QR-RPT-001 — Check Run by identifier
Phase        : SVC-API
API          : API-RPT-001
Entity       : ENT-RPT-001
Operation    : FIND_ONE
Intent       : one Check with its status, metadata, result or failure and decision
Logical spec : SELECT CHECK_RUN_ID, SERVICE_CODE, VERSION_NUMBER, FETCH_MODE, REQUEST_NUMBER, EMPLOYEE_ID, CHECK_STATUS, STARTED_AT, RUNNING_SINCE, ENDED_AT, OVERALL_STATUS, COMPARISON_MODEL, FAILURE_REASON, FAILURE_DETAIL, EMPLOYEE_DECISION, DECIDED_BY, DECIDED_AT, APPROVAL_API_EXECUTED FROM RPT_CHECK_RUN WHERE CHECK_RUN_ID = :checkId
Join         : NONE
Transaction  : READ_ONLY (also used, with FOR UPDATE, inside completeCheck — READ_WRITE)
Locking      : NONE for the HTTP read; `FOR UPDATE` when completeCheck decides on the row it then writes
Pagination   : NO
Filters      : CHECK_RUN_ID: EXACT (PK_RPT_CHECK_RUN)
Result shape : full entity minus audit columns
Null handling: RUNNING_SINCE, ENDED_AT, OVERALL_STATUS, COMPARISON_MODEL, FAILURE_REASON, FAILURE_DETAIL and the four decision columns are null when not applicable; no row → RPT-404-CHECK-NOT-FOUND

### QR-RPT-002 — Findings of a Check Run
Phase        : SVC-API
API          : API-RPT-001
Entity       : ENT-RPT-002
Operation    : FIND_BY_CRITERIA
Intent       : the findings of one completed report in report order
Logical spec : SELECT POSITION, CONDITION_TEXT, FINDING_OUTCOME, EVIDENCE, NOTE FROM RPT_FINDING WHERE CHECK_RUN_ID = :checkId ORDER BY POSITION
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : CHECK_RUN_ID: EXACT (UQ_RPT_FINDING_RUN_POS)
Result shape : projection POSITION, CONDITION_TEXT, FINDING_OUTCOME, EVIDENCE, NOTE
Null handling: none optional; empty list for a Check not COMPLETED

### QR-RPT-003 — Check Documents of a Check Run
Phase        : SVC-API
API          : API-RPT-001
Entity       : ENT-RPT-003
Operation    : FIND_BY_CRITERIA
Intent       : the document outcomes of one completed report in report order
Logical spec : SELECT POSITION, DOCUMENT_TYPE, SOURCE_MODE, READ_STATUS, UNREADABLE_REASON, DETAIL FROM RPT_CHECK_DOCUMENT WHERE CHECK_RUN_ID = :checkId ORDER BY POSITION
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : CHECK_RUN_ID: EXACT (UQ_RPT_CHECK_DOCUMENT_RUN_POS)
Result shape : projection POSITION, DOCUMENT_TYPE, SOURCE_MODE, READ_STATUS, UNREADABLE_REASON, DETAIL
Null handling: UNREADABLE_REASON null unless UNREADABLE; DETAIL nullable; empty list for a Check not COMPLETED

### QR-RPT-004 — Unread Queries of a Check Run
Phase        : SVC-API
API          : API-RPT-001
Entity       : ENT-RPT-004
Operation    : FIND_BY_CRITERIA
Intent       : the service queries of one completed report whose data could not be read
Logical spec : SELECT POSITION, QUERY_NAME, DETAIL FROM RPT_UNREAD_QUERY WHERE CHECK_RUN_ID = :checkId ORDER BY POSITION
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : CHECK_RUN_ID: EXACT (UQ_RPT_UNREAD_QUERY_RUN_POS)
Result shape : projection POSITION, QUERY_NAME, DETAIL
Null handling: none optional; empty list when every query was read

### QR-RPT-005 — Newest 100 Checks of a request
Phase        : SVC-API
API          : API-RPT-002
Entity       : ENT-RPT-001
Operation    : FIND_BY_CRITERIA
Intent       : the Checks of one service code and request number, newest first, capped at 100 (REQ-RPT-028, REQ-RPT-031)
Logical spec : SELECT CHECK_RUN_ID, CHECK_STATUS, OVERALL_STATUS, STARTED_AT, ENDED_AT, EMPLOYEE_DECISION FROM RPT_CHECK_RUN WHERE SERVICE_CODE = :serviceCode AND REQUEST_NUMBER = :requestNumber ORDER BY STARTED_AT DESC, CHECK_RUN_ID DESC FETCH FIRST 100 ROWS ONLY
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO (fixed cap of 100)
Filters      : SERVICE_CODE: EXACT · REQUEST_NUMBER: EXACT — bound parameters (IDX_RPT_CHECK_RUN_SVC_REQ)
Result shape : projection CHECK_RUN_ID, CHECK_STATUS, OVERALL_STATUS, STARTED_AT, ENDED_AT, EMPLOYEE_DECISION
Null handling: OVERALL_STATUS, ENDED_AT, EMPLOYEE_DECISION null when not applicable

### QR-RPT-006 — Number of Checks of a request
Phase        : SVC-API
API          : API-RPT-002
Entity       : ENT-RPT-001
Operation    : COUNT
Intent       : the total that keeps the 100-row cap visible (REQ-RPT-031)
Logical spec : SELECT COUNT(*) FROM RPT_CHECK_RUN WHERE SERVICE_CODE = :serviceCode AND REQUEST_NUMBER = :requestNumber
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : SERVICE_CODE: EXACT · REQUEST_NUMBER: EXACT — bound parameters
Result shape : count
Null handling: 0 when no Check exists

### QR-RPT-007 — Decision agreement of a service
Phase        : SVC-API
API          : API-RPT-003
Entity       : ENT-RPT-001
Operation    : AGGREGATE
Intent       : per service package version, how many decided Checks of each Overall Status were approved or rejected (REQ-RPT-040)
Logical spec : SELECT VERSION_NUMBER, OVERALL_STATUS, EMPLOYEE_DECISION, COUNT(*) FROM RPT_CHECK_RUN WHERE SERVICE_CODE = :serviceCode AND EMPLOYEE_DECISION IS NOT NULL GROUP BY VERSION_NUMBER, OVERALL_STATUS, EMPLOYEE_DECISION ORDER BY VERSION_NUMBER DESC, OVERALL_STATUS, EMPLOYEE_DECISION
Join         : NONE
Transaction  : READ_ONLY
Locking      : NONE — read only, nothing is written back
Pagination   : NO
Filters      : SERVICE_CODE: EXACT (bound) · EMPLOYEE_DECISION: NOT NULL
Result shape : projection VERSION_NUMBER, OVERALL_STATUS, EMPLOYEE_DECISION, count
Null handling: OVERALL_STATUS is never null on a decided Check (CHK_RPT_CHECK_RUN_DECISION ⇒ COMPLETED); empty list when nothing is decided

## ERROR CATALOG — RPT v1

```yaml name=error-catalog
rows:
  - {code: "RPT-400-CHECK-ID-INVALID", rule: PLATFORM-STD, api: [API-RPT-001], http: 400, trigger: "the checkId path parameter is not a number", messages: {en: "A numeric Check identifier is required.", ar: "PENDING ADR-RPT-013"}, adr: "ADR-RPT-013"}
  - {code: "RPT-404-CHECK-NOT-FOUND", rule: PLATFORM-STD, api: [API-RPT-001], http: 404, trigger: "no Check Run exists for the checkId — never created or purged (REQ-RPT-025)", messages: {en: "Check {checkId} was not found.", ar: "PENDING ADR-RPT-013"}, adr: "ADR-RPT-013"}
  - {code: "RPT-400-REQUEST-KEYS-MISSING", rule: RULE-RPT-009, api: [API-RPT-002], http: 400, trigger: "serviceCode or requestNumber absent or blank", messages: {en: "Both a service code and a request number are needed to list Checks.", ar: "PENDING ADR-RPT-013"}}
  - {code: "RPT-400-SERVICE-CODE-MISSING", rule: RULE-RPT-015, api: [API-RPT-003], http: 400, trigger: "serviceCode absent or blank", messages: {en: "A service code is needed to read the decision agreement.", ar: "PENDING ADR-RPT-013"}}
  - {code: "RPT-500", rule: PLATFORM-STD, api: [API-RPT-001, API-RPT-002, API-RPT-003], http: 500, trigger: "an unexpected server failure", messages: {en: "The Report Store could not complete the request.", ar: "PENDING ADR-RPT-013"}, adr: "ADR-RPT-013"}
```

## Coverage
| RULE | Where enforced | Catalog / in-process code |
|---|---|---|
| RULE-RPT-001 | createCheckRun step 1 | RPT-400-CHECK-RUN-INCOMPLETE (in-process) |
| RULE-RPT-002 | createCheckRun step 3 + CHK_RPT_CHECK_RUN_AWAITING | RPT-422-INITIAL-STATUS-MISMATCH (in-process) |
| RULE-RPT-003 | markRunning / completeCheck / failCheck conditional UPDATE | RPT-409-CHECK-ENDED, RPT-409-CHECK-NOT-RUNNING (in-process) |
| RULE-RPT-004 | completeCheck step 2 | RPT-422-METADATA-MISMATCH (in-process) |
| RULE-RPT-005 | completeCheck step 2 | RPT-422-COMPLIANT-NOT-VERIFIED (in-process) |
| RULE-RPT-006 | port enums + BLOCK 5c CHECK constraints | RPT-422-UNKNOWN-CODE (in-process) |
| RULE-RPT-007 | completeCheck step 2 | RPT-422-FINDING-INCOMPLETE (in-process) |
| RULE-RPT-008 | completeCheck step 2 + CHK_RPT_CHECK_DOCUMENT_REASON | RPT-422-DOCUMENT-REASON-MISMATCH (in-process) |
| RULE-RPT-009 | API-RPT-002 | RPT-400-REQUEST-KEYS-MISSING |
| RULE-RPT-010 | failCheck step 1 | RPT-400-FAILURE-INCOMPLETE (in-process) |
| RULE-RPT-011 | recordDecision conditional UPDATE | RPT-409-DECISION-ALREADY-RECORDED (in-process) |
| RULE-RPT-012 | recordDecision conditional UPDATE + CHK_RPT_CHECK_RUN_DECISION | RPT-409-CHECK-NOT-COMPLETED (in-process) |
| RULE-RPT-013 | recordDecision step 1 | RPT-400-DECISION-INCOMPLETE (in-process) |
| RULE-RPT-014 | recordDecision step 2 + CHK_RPT_CHECK_RUN_DECISION | RPT-422-APPROVAL-FLAG-ON-REJECTION (in-process) |
| RULE-RPT-015 | API-RPT-003 | RPT-400-SERVICE-CODE-MISSING |

| ENT / DBF | Phases | QR | XM |
|---|---|---|---|
| ENT-RPT-001 / DBF-RPT-001 … DBF-RPT-020 | DATA-DOM, PORTS, SVC-API | QR-RPT-001, QR-RPT-005, QR-RPT-006, QR-RPT-007 | — |
| ENT-RPT-002 / DBF-RPT-021 … DBF-RPT-029 | DATA-DOM, SVC-API | QR-RPT-002 | — |
| ENT-RPT-003 / DBF-RPT-030 … DBF-RPT-039 | DATA-DOM, SVC-API | QR-RPT-003 | — |
| ENT-RPT-004 / DBF-RPT-040 … DBF-RPT-046 | DATA-DOM, SVC-API | QR-RPT-004 | — |

Integration: none (0 XM). ADRs cited: ADR-RPT-001, ADR-RPT-002, ADR-RPT-003, ADR-RPT-004, ADR-RPT-006, ADR-RPT-008, ADR-RPT-009, ADR-RPT-010, ADR-RPT-011, ADR-RPT-012, ADR-RPT-013.

<<<END INPUT>>>

<<<INPUT: frontend-execution-plan>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: api-spec>>>
openapi: 3.1.0
info:
  title: Report Store (RPT) API
  version: 1.0.0
  description: 'Derived from backend-execution-plan-rpt.md (API-RPT-001 … API-RPT-003). Read-only; the Check result port and
    the Employee Decision are in-process (ADR-RPT-006). No security scheme: caller authentication is deferred (raw-idea A2).'
paths:
  /api/v1/checks/{checkId}:
    get:
      operationId: readCheck
      summary: Read a Check and its report
      x-api-id: API-RPT-001
      x-traces:
      - REQ-RPT-023
      - REQ-RPT-024
      - REQ-RPT-025
      - REQ-RPT-026
      - REQ-RPT-027
      - DBF-RPT-001
      - DBF-RPT-002
      - DBF-RPT-003
      - DBF-RPT-004
      - DBF-RPT-005
      - DBF-RPT-006
      - DBF-RPT-007
      - DBF-RPT-008
      - DBF-RPT-009
      - DBF-RPT-010
      - DBF-RPT-011
      - DBF-RPT-012
      - DBF-RPT-013
      - DBF-RPT-014
      - DBF-RPT-015
      - DBF-RPT-016
      - DBF-RPT-017
      - DBF-RPT-018
      - DBF-RPT-022
      - DBF-RPT-023
      - DBF-RPT-024
      - DBF-RPT-025
      - DBF-RPT-026
      - DBF-RPT-031
      - DBF-RPT-032
      - DBF-RPT-033
      - DBF-RPT-034
      - DBF-RPT-035
      - DBF-RPT-036
      - DBF-RPT-041
      - DBF-RPT-042
      - DBF-RPT-043
      parameters:
      - name: checkId
        in: path
        required: true
        description: The Check identifier (DBF-RPT-001)
        schema:
          type: integer
          format: int64
      responses:
        '200':
          description: The Check with, once COMPLETED, its report
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/CheckReport'
        '400':
          description: The checkId path parameter is not a number
          content:
            application/problem+json:
              schema: &id001
                $ref: '#/components/schemas/ProblemDetail'
          x-error-codes:
          - RPT-400-CHECK-ID-INVALID
        '404':
          description: No Check Run exists for the checkId — never created or purged
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-404-CHECK-NOT-FOUND
        '500':
          description: Unexpected server failure
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-500
  /api/v1/checks:
    get:
      operationId: listChecksOfRequest
      summary: List the Checks of a request
      x-api-id: API-RPT-002
      x-traces:
      - REQ-RPT-028
      - REQ-RPT-029
      - REQ-RPT-030
      - REQ-RPT-031
      - REQ-RPT-050
      - DBF-RPT-001
      - DBF-RPT-002
      - DBF-RPT-005
      - DBF-RPT-007
      - DBF-RPT-008
      - DBF-RPT-010
      - DBF-RPT-011
      - DBF-RPT-015
      parameters:
      - name: serviceCode
        in: query
        required: true
        description: DBF-RPT-002, matched exactly
        schema: &id002
          type: string
          maxLength: 100
      - name: requestNumber
        in: query
        required: true
        description: DBF-RPT-005, matched exactly as sent
        schema: *id002
      responses:
        '200':
          description: At most 100 Checks, newest first, with the total
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ChecksOfRequest'
        '400':
          description: serviceCode or requestNumber absent or blank
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-400-REQUEST-KEYS-MISSING
        '500':
          description: Unexpected server failure
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-500
  /api/v1/decision-agreement:
    get:
      operationId: readDecisionAgreement
      summary: Read the decision agreement of a service
      x-api-id: API-RPT-003
      x-traces:
      - REQ-RPT-040
      - REQ-RPT-041
      - DBF-RPT-002
      - DBF-RPT-003
      - DBF-RPT-011
      - DBF-RPT-015
      parameters:
      - name: serviceCode
        in: query
        required: true
        description: DBF-RPT-002
        schema: *id002
      responses:
        '200':
          description: Counts of decided Checks per version, Overall Status and Employee Decision
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/AgreementRow'
        '400':
          description: serviceCode absent or blank
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-400-SERVICE-CODE-MISSING
        '500':
          description: Unexpected server failure
          content:
            application/problem+json:
              schema: *id001
          x-error-codes:
          - RPT-500
components:
  schemas:
    FindingView:
      type: object
      required:
      - position
      - condition
      - outcome
      - evidence
      - note
      properties:
        position:
          type: integer
          description: DBF-RPT-022
        condition:
          type: string
          description: DBF-RPT-023
        outcome:
          type: string
          enum:
          - SATISFIED
          - NOT_SATISFIED
          - UNDETERMINED
          maxLength: 30
          description: DBF-RPT-024 — FINDING_OUTCOME
        evidence:
          type: string
          description: DBF-RPT-025 — returned exactly as stored, as data
        note:
          type: string
          description: DBF-RPT-026
    DocumentView:
      type: object
      required:
      - position
      - documentType
      - sourceMode
      - readStatus
      properties:
        position:
          type: integer
          description: DBF-RPT-031
        documentType:
          type: string
          maxLength: 100
          description: DBF-RPT-032
        sourceMode:
          type: string
          enum: &id003
          - path
          - blob
          - manual
          maxLength: 10
          description: DBF-RPT-033 — FETCH_MODE
        readStatus:
          type: string
          enum:
          - READ
          - MISSING
          - UNREADABLE
          maxLength: 30
          description: DBF-RPT-034 — DOCUMENT_READ_STATUS
        unreadableReason:
          type:
          - string
          - 'null'
          enum:
          - OUTSIDE_STORAGE_ROOT
          - NOT_FOUND
          - TOO_LARGE
          - UNSUPPORTED_FORMAT
          - READING_FAILED
          - OUT_OF_TIME
          - SOURCE_QUERY_FAILED
          - MODEL_NOT_PERMITTED
          - null
          maxLength: 30
          description: DBF-RPT-035 — UNREADABLE_REASON, UNREADABLE only
        detail:
          type:
          - string
          - 'null'
          description: DBF-RPT-036
    UnreadQueryView:
      type: object
      required:
      - position
      - queryName
      - detail
      properties:
        position:
          type: integer
          description: DBF-RPT-041
        queryName:
          type: string
          maxLength: 100
          description: DBF-RPT-042
        detail:
          type: string
          description: DBF-RPT-043
    DecisionView:
      type: object
      required:
      - employeeDecision
      - decidedBy
      - decidedAt
      - approvalApiExecuted
      properties:
        employeeDecision:
          type: string
          enum: &id005
          - APPROVED
          - REJECTED
          maxLength: 30
          description: DBF-RPT-015 — EMPLOYEE_DECISION
        decidedBy:
          type: string
          maxLength: 100
          description: DBF-RPT-016 — exactly as sent
        decidedAt:
          type: string
          format: date-time
          description: DBF-RPT-017
        approvalApiExecuted:
          type: boolean
          description: DBF-RPT-018
    CheckReport:
      type: object
      required:
      - checkId
      - status
      - serviceCode
      - versionNumber
      - fetchMode
      - requestNumber
      - employeeId
      - startedAt
      - findings
      - documents
      - unreadQueries
      properties:
        checkId:
          type: integer
          format: int64
          description: DBF-RPT-001
        status:
          type: string
          enum: &id004
          - AWAITING_DOCUMENTS
          - RUNNING
          - COMPLETED
          - FAILED
          maxLength: 30
          description: DBF-RPT-007 — CHECK_STATUS
        serviceCode:
          type: string
          maxLength: 100
          description: DBF-RPT-002
        versionNumber:
          type: integer
          description: DBF-RPT-003
        fetchMode:
          type: string
          enum: *id003
          maxLength: 10
          description: DBF-RPT-004 — FETCH_MODE
        requestNumber:
          type: string
          maxLength: 100
          description: DBF-RPT-005 — exactly as sent
        employeeId:
          type: string
          maxLength: 100
          description: DBF-RPT-006 — exactly as sent
        startedAt:
          type: string
          format: date-time
          description: DBF-RPT-008
        runningSince:
          type:
          - string
          - 'null'
          format: date-time
          description: DBF-RPT-009
        endedAt:
          type:
          - string
          - 'null'
          format: date-time
          description: DBF-RPT-010
        overallStatus:
          type:
          - string
          - 'null'
          enum:
          - COMPLIANT
          - NOT_COMPLIANT
          - NEEDS_MANUAL_REVIEW
          - null
          maxLength: 30
          description: DBF-RPT-011 — OVERALL_STATUS, COMPLETED only
        comparisonModel:
          type:
          - string
          - 'null'
          maxLength: 200
          description: DBF-RPT-012 — COMPLETED only
        failureReason:
          type:
          - string
          - 'null'
          enum:
          - TIMED_OUT
          - MODEL_UNAVAILABLE
          - MODEL_OUTPUT_INVALID
          - MODEL_NOT_PERMITTED
          - UPLOAD_WINDOW_EXPIRED
          - INTERRUPTED
          - INTERNAL_ERROR
          - null
          maxLength: 30
          description: DBF-RPT-013 — CHECK_FAILURE_REASON, FAILED only
        failureDetail:
          type:
          - string
          - 'null'
          description: DBF-RPT-014 — FAILED only
        findings:
          type: array
          items:
            $ref: '#/components/schemas/FindingView'
        documents:
          type: array
          items:
            $ref: '#/components/schemas/DocumentView'
        unreadQueries:
          type: array
          items:
            $ref: '#/components/schemas/UnreadQueryView'
        decision:
          oneOf:
          - $ref: '#/components/schemas/DecisionView'
          - type: 'null'
    CheckSummary:
      type: object
      required:
      - checkId
      - status
      - startedAt
      properties:
        checkId:
          type: integer
          format: int64
          description: DBF-RPT-001
        status:
          type: string
          enum: *id004
          maxLength: 30
          description: DBF-RPT-007
        overallStatus:
          type:
          - string
          - 'null'
          enum:
          - COMPLIANT
          - NOT_COMPLIANT
          - NEEDS_MANUAL_REVIEW
          - null
          maxLength: 30
          description: DBF-RPT-011
        startedAt:
          type: string
          format: date-time
          description: DBF-RPT-008
        endedAt:
          type:
          - string
          - 'null'
          format: date-time
          description: DBF-RPT-010
        employeeDecision:
          type:
          - string
          - 'null'
          enum:
          - APPROVED
          - REJECTED
          - null
          maxLength: 30
          description: DBF-RPT-015
    ChecksOfRequest:
      type: object
      required:
      - total
      - checks
      properties:
        total:
          type: integer
          description: every Check of the service code and request number
        checks:
          type: array
          maxItems: 100
          items:
            $ref: '#/components/schemas/CheckSummary'
    AgreementRow:
      type: object
      required:
      - versionNumber
      - overallStatus
      - employeeDecision
      - count
      properties:
        versionNumber:
          type: integer
          description: DBF-RPT-003
        overallStatus:
          type: string
          enum:
          - COMPLIANT
          - NOT_COMPLIANT
          - NEEDS_MANUAL_REVIEW
          maxLength: 30
          description: DBF-RPT-011
        employeeDecision:
          type: string
          enum: *id005
          maxLength: 30
          description: DBF-RPT-015
        count:
          type: integer
          minimum: 1
    ProblemDetail:
      type: object
      required:
      - type
      - title
      - status
      - code
      properties:
        type:
          type: string
        title:
          type: string
        status:
          type: integer
        detail:
          type: string
        code:
          type: string

<<<END INPUT>>>

<<<INPUT: dependency-graph>>>
<<<dependency-graph.json>>>
{
  "schema": 1,
  "generated_from": {
    "platform-summary": {
      "CHK": "v1",
      "DOC": "v1",
      "INT": "v1",
      "REG": "v1",
      "RPT": "v1"
    },
    "srs": {
      "CHK": "v1",
      "DOC": "v1",
      "INT": "v1",
      "REG": "v1",
      "RPT": "v1"
    }
  },
  "modules": {
    "CHK": {
      "tier": 2,
      "tier_source": "declared"
    },
    "DOC": {
      "tier": 1,
      "tier_source": "declared"
    },
    "INT": {
      "tier": 4,
      "tier_source": "declared"
    },
    "REG": {
      "tier": 0,
      "tier_source": "declared"
    },
    "RPT": {
      "tier": 3,
      "tier_source": "declared"
    }
  },
  "edges": [
    {
      "from": "CHK",
      "to": "DOC",
      "state": "READY",
      "source": "platform"
    },
    {
      "id": "XM-CHK-001",
      "from": "CHK",
      "to": "REG",
      "entity": "ENT-REG-001",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-001",
      "traces": [
        "REQ-CHK-005",
        "REQ-CHK-007"
      ],
      "source": "xm"
    },
    {
      "id": "XM-CHK-002",
      "from": "CHK",
      "to": "REG",
      "entity": "ENT-REG-002",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-002",
      "traces": [
        "REQ-CHK-007",
        "REQ-CHK-008",
        "REQ-CHK-015",
        "REQ-CHK-027",
        "REQ-CHK-034",
        "REQ-CHK-056",
        "REQ-CHK-057"
      ],
      "source": "xm"
    },
    {
      "id": "XM-CHK-003",
      "from": "CHK",
      "to": "REG",
      "entity": "ENT-REG-003",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-003",
      "traces": [
        "REQ-CHK-011",
        "REQ-CHK-012",
        "REQ-CHK-015"
      ],
      "source": "xm"
    },
    {
      "id": "XM-CHK-004",
      "from": "CHK",
      "to": "REG",
      "entity": "ENT-REG-004",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-004",
      "traces": [
        "REQ-CHK-019",
        "REQ-CHK-020",
        "REQ-CHK-021",
        "REQ-CHK-022"
      ],
      "source": "xm"
    },
    {
      "id": "XM-CHK-005",
      "from": "CHK",
      "to": "REG",
      "entity": "ENT-REG-005",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-005",
      "traces": [
        "REQ-CHK-006",
        "REQ-CHK-013",
        "REQ-CHK-014"
      ],
      "source": "xm"
    },
    {
      "id": "XM-DOC-001",
      "from": "DOC",
      "to": "REG",
      "entity": "ENT-REG-002",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-002",
      "traces": [
        "REQ-DOC-002",
        "REQ-DOC-003",
        "REQ-DOC-020"
      ],
      "source": "xm"
    },
    {
      "id": "XM-DOC-002",
      "from": "DOC",
      "to": "REG",
      "entity": "ENT-REG-003",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-003",
      "traces": [
        "REQ-DOC-004",
        "REQ-DOC-012",
        "REQ-DOC-051"
      ],
      "source": "xm"
    },
    {
      "id": "XM-DOC-003",
      "from": "DOC",
      "to": "REG",
      "entity": "ENT-REG-004",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-004",
      "traces": [
        "REQ-DOC-021",
        "REQ-DOC-035"
      ],
      "source": "xm"
    },
    {
      "id": "XM-DOC-004",
      "from": "DOC",
      "to": "REG",
      "entity": "ENT-REG-005",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-005",
      "traces": [
        "REQ-DOC-012",
        "REQ-DOC-014",
        "REQ-DOC-052"
      ],
      "source": "xm"
    },
    {
      "from": "INT",
      "to": "CHK",
      "state": "READY",
      "source": "platform"
    },
    {
      "from": "INT",
      "to": "DOC",
      "state": "READY",
      "source": "platform"
    },
    {
      "id": "XM-INT-001",
      "from": "INT",
      "to": "REG",
      "entity": "ENT-REG-002",
      "type": "SOFT-READ",
      "state": "READY",
      "declared": "CONTRACTED",
      "contract_ref": "CON-REG-002",
      "traces": [
        "REQ-INT-025",
        "REQ-INT-028",
        "REQ-INT-030"
      ],
      "source": "xm"
    },
    {
      "from": "INT",
      "to": "RPT",
      "state": "READY",
      "source": "platform"
    },
    {
      "from": "RPT",
      "to": "CHK",
      "state": "READY",
      "source": "platform"
    }
  ]
}

<<<END INPUT>>>

---
# KNOWLEDGE (profile primary sources — cite as [KB:<file> §n])

<<<KB: profiles/aias/knowledge/raw-idea.md>>>
# Request Verification Service — Raw Idea (project `aias`)

As of 2026-10-01. Author: Hesham Ezzat. Amended 2026-10-01 — see section 15.
Save as: `governance-shared/profiles/aias/knowledge/raw-idea.md` and list it under `knowledge.files` in `profiles/aias.yaml`.

## 0. How the factory should read this file

- Section 13 "Decided" is locked input. `domain-profile`, P0 and the PRD must not reopen those points.
- Section 13 "Open" is the complete set of questions to resolve, by research and a recommended answer.
- Section 12 "Guardrails" are non-negotiable and should become formal requirements in P1.
- YAML and endpoint examples illustrate intent. Final names, schemas and contracts are for the analysis stages to define.

## 1. Purpose

A standalone service that verifies a government service request before an employee approves it. It collects the request's data and attached documents, compares them with the conditions of the service, and returns a short report: compliant or not, and where the problems are.

The employee stays the decision maker. The service informs the decision and never makes it.

The service is generic. It covers many services, each with its own conditions, data queries and documents, all defined in configuration. It supersedes the earlier "Reusable Agentic AI Library" idea, which was broader than the first real need.

## 2. Scope

In scope:

- A standalone Spring Boot service, called by host systems over REST.
- Per-service configuration: a knowledge file plus a structured definition, maintained by the service administrator. Employees never supply knowledge files.
- Read-only access to request data through an MCP server (Oracle expected).
- Document retrieval by file path, by database BLOB, or by manual upload.
- Reading PDF, XLS and image documents (other extensions may appear).
- A structured verification report, stored in a database and shown to the employee inside the host system.
- A web frontend for the employee, embedded in the host screen (amendment A1, section 15).
- Optional execution of an approval API, per service, triggered by the employee.

Out of scope for now:

- Multi-tenancy.
- Conversation memory.
- RAG and a vector store.
- Multi-agent orchestration.
- A full administration UI and an internal permission system.
- Caller authentication and the security phases, deferred to a later version (amendment A2, section 15).

Memory is excluded because a check is one independent run, and carrying state between requests risks leaking one request's data into another. RAG is excluded because each service's knowledge fits whole in the prompt, which is more reliable for compliance than retrieving fragments. RAG becomes relevant only if one service's knowledge grows to hundreds of pages.

## 3. Architecture

Stack: Java 21, Spring Boot 4, Spring AI 2.0.

```text
Host system (Oracle ADF or other)
        |  REST
        v
Request Verification Service
  - Service Registry   loads each service package
  - Check Engine       fixed pipeline (the only fixed logic)
  - Report Store       saves the report and the employee decision
        |
        +--> MCP server        read-only queries on Oracle
        +--> Documents         storage path, BLOB or manual upload
        +--> LLM provider      through Spring AI, replaceable
        +--> Service database  runs, findings, documents
```

Each dependency sits behind an interface so it can be replaced without touching the engine.

## 4. Service package (the variable part)

Everything that differs between services lives in one package per service. Adding a service needs no code as long as it uses existing check types.

```text
services/<service-code>/
  knowledge.md     conditions and rules in natural language (read by the LLM)
  service.yaml     queries, required documents, fetch mode, optional approval API
```

The two files are separate on purpose. SQL and file locations are executed literally by the engine; only the conditions are interpreted by the LLM. Each package carries a version, and every report records the version it was built on.

```yaml
service: scholarship-request
version: 3
input: requestId

queries:
  request_details:
    connection: main-db
    sql: >
      SELECT r.status, r.gpa, s.national_id
      FROM requests r JOIN students s ON s.id = r.student_id
      WHERE r.id = :requestId
  attachments:
    connection: main-db
    sql: >
      SELECT a.doc_type, a.file_path
      FROM request_attachments a
      WHERE a.request_id = :requestId

documents:
  source: attachments
  type_column: doc_type
  fetch: path            # path | blob | manual
  path_column: file_path
  required: [TRANSCRIPT, ID_CARD]

approval:
  enabled: false
  api: POST /requests/{requestId}/approve
```

Connections are defined once, outside the service packages, and set at activation time for each environment.

```yaml
connections:
  main-db:
    type: mcp
    endpoint: ...
    query_tool: run_query
    dialect: oracle
```

## 5. Check flow (the fixed part)

1. The host system sends the service code, the request number and the employee's identity.
2. The engine loads the service package and runs its queries through the MCP connection.
3. It fetches the documents using the service's fetch mode.
4. It reads each document by type: text extraction for PDF, table extraction for XLS, OCR or a vision model for scans and images.
5. It runs the deterministic checks in code: required documents present, explicit values and dates.
6. The LLM compares the data and document content with the service knowledge.
7. The engine stores the structured report and makes it available to the host system.
8. The employee takes the action in the host system, or through the approval API where it is enabled.

A check takes time, so it runs asynchronously: the host starts it and then polls for the result.

## 6. Data and document access

Queries and documents use separate channels, each behind its own interface.

Queries go through `QueryExecutor`. The first implementation, `McpQueryExecutor`, calls the MCP server's query tool using the Spring AI MCP client. The engine calls MCP to run the queries written in `service.yaml`. The LLM is never given a tool that runs SQL.

Documents go through `DocumentFetcher`. All three modes are available, chosen per service.

| Mode | Used when | How |
| --- | --- | --- |
| `path` | The file is in storage and its path is in the database | Read from storage by the path the query returns |
| `blob` | The file is stored inside the database | Read the column directly over JDBC with a read-only user |
| `manual` | The service is not allowed direct access | The employee uploads the files; the report is marked accordingly |

BLOBs are not moved through MCP, because binary content would travel as Base64 text inside a message, which is slow and size-limited.

Requirements for the MCP server chosen at activation: bind-variable support or strict parameter type validation in the engine, structured (JSON) results, a row limit and a timeout.

## 7. Report model

The report has a fixed structure for every service, produced as structured output and stored as data.

| Part | Content |
| --- | --- |
| Overall status | `COMPLIANT`, `NOT_COMPLIANT` or `NEEDS_MANUAL_REVIEW` |
| Findings | One per condition: satisfied or not, the evidence (actual value found), and a note for the employee |
| Documents | What was read, what is missing, what could not be read |
| Metadata | Service version, document source mode, model used, time, employee |

Every finding carries its evidence so the employee can verify it. A required document that is missing or unreadable prevents a `COMPLIANT` status.

## 8. API

| Endpoint | Purpose |
| --- | --- |
| `POST /checks` | Start a check for a service code and request number |
| `GET /checks/{id}` | Return the status and the report as JSON |
| `GET /checks/{id}/view` | Return a ready report page to embed in the host screen |
| `POST /checks/{id}/documents` | Upload documents in manual mode |
| `POST /checks/{id}/decision` | Record the employee's decision; call the approval API if the service enables it |

The calling system authenticates itself (API key or mTLS) and passes the employee's identity, which is recorded with the check.

## 9. Persistence

Reports are stored in database tables in a schema owned by the service, separate from the read-only user that reaches host data.

| Table | Holds |
| --- | --- |
| `CHECK_RUN` | One row per check: service, request number, employee, status, result, service version, model, timestamps, employee decision |
| `CHECK_FINDING` | One row per condition: satisfied flag, evidence, note |
| `CHECK_DOCUMENT` | One row per document: type, source mode, read status |

Storing the employee's decision beside the report result gives a direct measure of accuracy: where the two disagree, the service knowledge or a check needs attention.

## 10. LLM strategy

The provider is cloud-based for now and must be replaceable through configuration alone.

- Testing: a free cloud tier. Gemini Flash-Lite through Google AI Studio is the starting candidate. Free-tier limits change often.
- Test data only: free tiers may use submitted data for model training. Only synthetic or anonymised requests and documents are sent while a free provider is in use.
- Real data: the provider for real requests, cloud or inside the network, is decided before go-live.

Rules that keep the provider replaceable:

- The engine depends on Spring AI's `ChatModel` only, with no provider-specific features.
- Document reading (OCR or vision) is a separate step with its own configurable model.
- A fixed set of test requests with known expected results is run on every model change.

## 11. Host integration

The service is reached from Oracle ADF applications, and from other government systems later, over HTTP only. It is not embedded in ADF.

Display. The first version embeds the ready report page (`/checks/{id}/view`) inside the ADF screen. The same report is available as JSON, so it can later be rendered with ADF components or shown in the request log without changing the service.

Approval. Two options, chosen per service:

1. Default: the employee approves in the host system as today. The host notifies the service of the decision for the record. The service needs no write access to any system.
2. Optional: where the host exposes an approval API, the service calls it after the employee confirms, and records the report the approval was based on.

## 12. Guardrails

- The LLM analyses and summarises. It does not write SQL and does not trigger approval.
- Approval is executed only as a result of the employee's action.
- All access to host data uses a read-only database user, preferably limited to specific views.
- Query parameters are bound or strictly type-validated; SQL is never built from free text.
- File paths are validated to be inside the allowed storage root before opening.
- Anything that could not be read appears in the report. It is never skipped silently.
- Document content is treated as data, never as instructions to the model.
- Each check has limits: timeout, maximum rows, maximum file size.
- No data is carried from one check to another.

## 13. Decisions and open items

Decided:

| Topic | Decision |
| --- | --- |
| Form | A standalone service, not a library |
| Stack | Java 21, Spring Boot 4, Spring AI 2.0 |
| Tenancy | Single tenant |
| Configuration | One package per service, maintained by the administrator |
| Database access | Read-only through an MCP server, specified at activation |
| Documents | `path`, `blob` and `manual` modes all available |
| Decision maker | The employee; approval API is an optional second path |
| Display | The frontend (A1) embedded in the host screen; JSON available for native display or the request log (amended — A1) |
| Storage | Reports kept in the service's own database tables |
| LLM | Cloud, free tier for testing, replaceable by configuration |
| Memory, RAG, vector store | Not included |
| Frontend | A web frontend for the employee, embedded in the host screen (amended — A1) |
| Security | Caller authentication and security phases deferred to a later version; the section 12 guardrails stay (amended — A2) |

Open:

- Which MCP server to use for Oracle, confirmed against the requirements in section 6.
- Which LLM provider is permitted for real request data.
- How the host system authenticates to the service: API key or mTLS. Deferred with A2 — not to be resolved in this version.
- Report retention period and who may view stored reports.
- The first service to implement as the pilot.

## 14. Proposed module split (for `domain-profile` to confirm)

| Code | Module | Scope |
| --- | --- | --- |
| `REG` | Service Registry | Service packages, versions, connections |
| `CHK` | Check Engine | The fixed pipeline, deterministic checks, LLM comparison |
| `DOC` | Document Access | `path`, `blob` and `manual` fetching; reading PDF, XLS and images |
| `RPT` | Report Store | Runs, findings, documents, employee decision |
| `INT` | Host Integration | REST API, optional approval API |

Tracks: backend and frontend (amended — A1). The platform track covers the MCP connection, the LLM provider configuration and the service database.

## 15. Amendments

| # | Date | Change | Supersedes |
| --- | --- | --- | --- |
| A1 | 2026-10-01 | A frontend track is added. A web frontend (React + TypeScript), embedded in the host screen, gives the employee: the checks of a request, the report (overall status, findings with evidence, documents read / missing / unreadable), manual document upload, and recording the decision. It consumes the same REST API as any host. It replaces the server-rendered report page (`GET /checks/{id}/view`) as the display path. A full administration UI stays out of scope. | Section 13 "Frontend: None" and "Display"; the section 14 tracks paragraph; the server-rendered page in sections 8 and 11 |
| A2 | 2026-10-01 | Caller authentication (API key or mTLS) and the security phases are deferred to a later version; the owner already has the solution and adds it then. The section 12 guardrails are NOT deferred: they are part of what the service does. | The auth item under section 13 "Open" |

<<<END KB>>>

