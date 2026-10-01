# PASS 1 — module CHK v1 — bundled session (6 stages, one commit per stage)

- `P0` Platform Inception — questions allowed
- `P0.5` PRD — questions allowed
- `P1` SRS — questions forbidden — **blocked until human approval of `prd-approval`**
- `P1.5` Contract (outbound promise) — questions forbidden
- `P2` Database — questions forbidden
- `P3.1` Backend Execution Plan — questions forbidden

==============================================================================
# BRIEF — stage `P0` (Platform Inception) · module CHK · v1 · profile `aias`

Lane `analysis-dialogue` · implementer ['claude:opus', 'claude:sonnet'] · effort high · round 1

## Rules that bind this run
- Questions: **allowed**. Close every open point inside this dialogue with a researched, recommended answer; never write an external open-questions file.
- Owns IDs: POL — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-CHK-001`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared/analysis/modules/CHK/P0/platform-summary.md`
- `governance-shared/analysis/modules/CHK/P0/module-registry-chk.md`
- `governance-shared/analysis/modules/CHK/P0/business-policies-chk.md`
- Write the files directly into the project checkout (you are the operator); the orchestrator reads them on `--complete`.

## Dialogue protocol (converging, in-brief)
Implementers claude:opus, claude:sonnet alternate for at most 4 rounds; converge on **mutually-acceptable**.
Round 1 drafts the artifacts and, for every open point, a `PROPOSAL:` block (options, researched recommendation, sources).
Each later round answers every open PROPOSAL with a `DECISION: <title>` line followed by the reasoning (accept / amend, with the source), refines the artifacts, and appends `<!-- CONVERGED -->` at the end of the response when nothing material remains open. The last response is final. Every `DECISION:` block is persisted by the orchestrator as an ADR (`analysis/decisions/CHK/ADR-{MOD}-{seq:03d}.md`, status RESOLVED-IN-DIALOGUE) — write the title as the decision, and cite the ids it binds on a `traces` line.

## Contracts checked by `gov.py analyze` after this stage
- **C2** project registry → inception: C2.1 exists {'artifact': 'project-registry'} [CRITICAL]; C2.2 registry-agree {'registry': 'project-registry', 'categories': 'all'} [MAJOR]; C2.3 ids-owned {'artifact': 'project-registry', 'defines': []} [MAJOR]
- **C3** inception → PRD: C3.1 exists {'artifact': 'platform-summary'} [CRITICAL]; C3.2 exists {'artifact': 'module-registry'} [CRITICAL]; C3.3 exists {'artifact': 'business-policies'} [CRITICAL]; C3.4 ids-owned {'stage': 'P0'} [CRITICAL]; C3.5 no-questions {'stage': 'P0'} [CRITICAL]; C3.6 languages {'stage': 'P0'} [MAJOR]; C3.7 registry-agree {'artifact': 'business-policies', 'registry': 'module-registry', 'kinds': ['POL']} [MAJOR]; C3.8 ears {'kind': 'POL', 'patterns': 'factory.ids.ears.patterns'} [MAJOR]; C3.9 ids-continue {'stage': 'P0'} [CRITICAL]

## Blocks this stage emits — each a ```yaml name={name} fence, held to its schema by C0.1 (`gov.py analyze`)
- `platform-dependencies` in `platform-summary` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"platform-dependencies","description":"P0 platform-summary: every module of the platform, its tier (derived from the graph's levels when absent) and the modules it reads from (factory.yaml → xm.graph.platform_block)","type":"object","required":["modules"],"additionalProperties":false,"properties":{"modules":{"type":"object","propertyNames":{"pattern":"^[A-Z][A-Z0-9]*$"},"additionalProperties":{"type":"object","required":[],"additionalProperties":false,"properties":{"tier":{"type":"integer","minimum":0},"depends_on":{"type":"array","items":{"type":"string","pattern":"^[A-Z][A-Z0-9]*$"}}}}}}}
  ```

---
# ENGINE
# Platform Inception — ENGINE

```
Engine        : Platform Inception
Stage id      : P0
Pass          : 1
Questions     : allowed — resolved in-dialogue with recommended answers; user confirms
Dialogue      : yes — lane analysis-dialogue (claude:opus + claude:sonnet; ≤ 4 rounds; converge on "mutually-acceptable"; output: resolved-decisions)
Inputs        : domain-profile, project-registry
Produces      : platform-summary.md · module-registry-{mod}.md · business-policies-{mod}.md
Owns IDs      : POL   → `{prefix}-{MOD}-{seq}` (seq width 3)
Next          : P0.5
Module        : CHK   Version: 1
Profile       : aias — Request Verification Service
```

This engine turns free-form vision text into a closed architectural context: first a
**platform summary** (tiered module table, dependency map), then — per module — a
**module registry** and **business policies** written as EARS statements with
`POL` IDs. Its outputs are CONTEXT for `P0.5`, never requirements. [C:C3.4]

Completion (write → registry → analyze → commit) is owned by the orchestrator — see
`shared/GOVERNANCE-CORE.md`. In a delta version (version > 1): read `_state/current-{artifact}` of the previous
version for every input and for this stage's own artifacts, and emit only ADDED / [C:C12.2]
MODIFIED / REMOVED elements plus the `change-manifest.md` per `shared/VERSIONING.md`.

```
╔══════════════════════════════════════════════════════════════════════╗
║ ABSOLUTE BOUNDARY                                                    ║
║ This stage does not write requirements, screens, field lists,        ║
║ validation rules, entity IDs or any content owned by P0.5 or later.  ║
║ A request for such content → produce this stage's artifacts, then    ║
║ redirect once (§7). No partial draft. No exception.                  ║
╚══════════════════════════════════════════════════════════════════════╝
```

Language policy: narrative in `en`.

---

## 1 — Reading protocol (before any analysis)

```
STEP A — domain-profile.md (steering — read first)
  §7 STEERING → vocabulary (use verbatim), bounded contexts, module codes,
                identifier rules, knowledge sources
  §1–§6       → scope, purpose, responsibilities, components, rules, relations
  §8          → resolved decisions — do not re-open one [G]

STEP B — project-registry.md (categories per shared/REGISTRY-SCHEMA.md)
  module index            → known modules — do NOT re-discover
  entity ownership        → known owners — apply directly
  shared declarations     → known shared entities — apply directly
  dependency graph        → platform/dependency-graph.json (gov.py graph) — extend, do not contradict [G]
  open-question index     → OPEN rows this stage may resolve in dialogue (§5)
  pipeline status         → modules already past this stage → EXISTING / EXCEPTION

STEP C — prior module artifacts (this stage's own outputs for other modules,
         from _state/ when they exist)
  entities owned / lookups owned / lookups consumed / dependencies → ground truth;
  a conflict between vision text and a module registry → the registry wins and the
  conflict is listed under OPEN ITEMS of the platform summary.

STEP D — knowledge sources (cite when applying a default)
  - profiles/aias/knowledge/raw-idea.md
```

Every structural decision comes from A → B → C → D in that order, then from domain
best practice; the user is asked only what none of these settle (§5). [G]

---

## 2 — Phase 1: vision → `platform-summary.md`

### 2.1 Transformation (three steps)

```
STEP 1 — EXTRACT from the vision text
  Modules (explicit or implied) — detection table from the profile:
    REG    Service Registry  ← service package / service definition / service.yaml / knowledge.md / connection / version / registry
    CHK    Check Engine  ← check / pipeline / deterministic check / LLM comparison / compliance / condition / finding
    DOC    Document Access  ← document / attachment / path / blob / manual upload / PDF / XLS / image / OCR / vision
    RPT    Report Store  ← report / run / finding / evidence / employee decision / retention / CHECK_RUN
    INT    Host Integration  ← REST / host system / ADF / report page / view / approval API / decision / polling
    (unlisted) → domain-profile §4 components + layer heuristics; still one of the
               profile's codes (REG, CHK, DOC, RPT, INT) or RESERVED per the registry.
  Explicit statements:
    scope exclusions ("without X", "not now")
    specific policies (limits, thresholds, exceptions)   → §3.3 candidates
    custom values (named lookup values)                   → §3.3 candidates

STEP 2 — ENRICH from knowledge sources + domain-profile
  For each module: layer, type, tier, dependencies from the knowledge files and
  the domain-profile relations; remove what the user excluded; add what the user
  mentioned beyond the pattern. Cite the source of every enrichment.

STEP 3 — RESOLVE from the registry
  module past this stage in pipeline status   → EXCEPTION (read as-is; skip in Phase 2)
  module with a prior module-registry artifact → EXISTING (Phase 2 extends it)
  otherwise                                    → NEW
```

### 2.2 Platform summary — template

```markdown
# PLATFORM SUMMARY — [Platform name — from the vision text or domain-profile]
══════════════════════════════════════════════════════════════════
Profile : aias   Domain profile : v[N]   Registry : v[semver]
══════════════════════════════════════════════════════════════════

## OVERVIEW
[One paragraph — what the platform does; enriched with domain context, not a restatement.]

## MODULES
| #   | Code | Module | Bounded context | Layer | Type | Status |
|-----|------|--------|-----------------|-------|------|--------|
| 1.1 | [code] | [display] | [context] | L1 | [master data / engine / reference / transactional / reporting] | NEW |
| 2.1 | [code] | [display] | [context] | L3 | [type] | EXISTING |
Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is)
Numbering: [tier].[sequence within tier] — the user requests Phase 2 by this number.

## DEPENDENCIES — data, not prose (`gov.py graph` reads this named block; the build order is DERIVED from it; C0.1 holds it to its schema)
```yaml name=platform-dependencies
modules:
  [CODE-A]: {tier: 1, depends_on: []}
  [CODE-B]: {tier: 2, depends_on: [CODE-A]}
```
One entry per module of the platform, every one with its tier; `depends_on` lists the
modules it reads from (a lower tier never depends on a higher one — `xm.tier_rule`). The build [C:C6.11]
order, the waves and the critical path are computed from this block (`gov.py plan-order`);
never write them by hand. [C:C9.23]

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
| Workflow engine | profile: `forbidden` |
| [user-excluded item] | user stated "not now" |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |

## OPEN ITEMS
[Only a registry ↔ vision conflict or a genuinely ambiguous scope boundary that the
 dialogue could not close. Otherwise: "None — platform scope fully determined."]

## NEXT STEP
Reply with a plain instruction to adjust, or with a module number to start Phase 2.
```

### 2.3 Confirmation and number stability

```
After producing the summary: "[N] modules, [N] exceptions. Confirm, or state one change."
Adjustments apply immediately and only the MODULES table is re-shown: [G]
  add a module      → next number at the end of its tier
  defer / remove    → status changes, number kept
  change a status   → status changes, number kept
NUMBER STABILITY: a number is fixed at first assignment — "1.1" goes on meaning [G]
the same module. Phase 2 begins on the user's first number request.
```

---

## 3 — Phase 2: module convergence (per requested module)

### 3.1 Request protocol

```
Status EXCEPTION → no files; confirm "[n] [module] is EXCEPTION — read as-is" and offer
                   the next module.
Status EXISTING  → read the prior module registry; EXTEND it (fill gaps, do not replace); [G]
                   note "[N] gaps filled from [source]".
Status NEW       → full pattern from knowledge sources + domain-profile.
Always append the readiness block (§3.5) after the two files.
```

### 3.2 Auto-completion protocol (for every gap in the module structure)

```
STEP 1 → prior module registry (EXISTING)         → use it
STEP 2 → project-registry ownership / dependency  → use it
STEP 3 → knowledge sources (§1 STEP D)            → apply, cite
STEP 4 → domain-profile rules + domain best practice → apply, cite
Document every auto-decision:   AUTO: [decision]  FROM: [step / source]  IF WRONG: [override]
STEPS 1–4 all fail → the point is a QUESTION (§5) — resolved in dialogue with a
recommended answer; the user confirms. Never an assumption written as fact.
```

### 3.3 Module registry — template (`module-registry-{mod}.md`)

```markdown
## MODULE REGISTRY — [Module display] ([CODE])
══════════════════════════════════════════════════════════════════
Module Code    : [CODE]   (profile.vocabulary.module_prefixes)
Bounded context: [context id]
Layer / Type   : [L1–L4] / [type]     Execution tier : [n.m]
Source         : NEW / EXTENDED from prior registry
Knowledge      : [knowledge file(s) / domain-profile §]
Readiness      : READY / PARTIALLY_READY
══════════════════════════════════════════════════════════════════

ENTITIES OWNED   (names only — entity IDs are assigned by P1) [C:C3.4]
| Entity | Kind (config / transactional) | PRIVATE / SHARED | Source |

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source [G] |
Rule (profile): Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
ROOT: YES / NO

AUTO-DECISIONS
AUTO: [decision]  FROM: [source]  IF WRONG: [override]

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
══════════════════════════════════════════════════════════════════
```

### 3.4 Business policies — template (`business-policies-{mod}.md`)

This file carries what the domain's standards cannot know: the client's own policies,
custom values and scope exceptions. Standard domain behaviour is applied by
`P0.5` and later stages from the knowledge sources — it is not repeated here.
If the user stated nothing specific, the file is minimal by design.

Every policy is one `POL` record written in **EARS** form (one pattern per
statement; `factory.ids.ears.patterns`):

```
  ubiquitous  The system shall …
  state       While <condition>, the system shall …
  event       When <condition>, the system shall …
  optional    Where <condition>, the system shall …
  unwanted    If <condition>, then the system shall …
```

```markdown
## BUSINESS POLICIES — [Module display] ([CODE])
══════════════════════════════════════════════════════════════════
Module   : [CODE]     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers) [G]
POL-[CODE]-001 — [short name]
  Statement : [EARS — exactly one pattern; the subject is "the system"] [C:C3.8]
  Pattern   : [ubiquitous | state | event | optional | unwanted]
  Trigger   : [Create / Update / Submit / Approve / …]
  Rationale : [why the client wants it — one line]
  Source    : [vision text quote / dialogue resolution #]
  Status    : CONFIRMED
(If none: "None — standard domain rules apply.")

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
| Lookup key | Added values | Source |
(If none: "None — standard values apply.")

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
(If none: "None — standard scope applies.")

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
══════════════════════════════════════════════════════════════════
```

Policy rules: a policy is a NEED at platform level, not a validation rule — no field
names, no error messages, no API shapes. Sequence numbers are continuous per module
and never reused. A policy the user did not state and did not confirm is not written. [C:C3.9]

### 3.5 Readiness block (after every module)

```
✓ [Module] — P0 complete
  Next : P0.5 reads platform-summary.md · module-registry-{mod}.md · business-policies-{mod}.md
  Precondition for P0.5: every module in `depends_on` has a published contract or a passed gate
  Another module? [the next module of `gov.py plan-order`]
```

---

## 4 — Registry step content

`module-registry-{mod}.md` IS this stage's registry output. In addition the
orchestrator merges into `project-registry.md`:

```
module index          : status of the module (NEW → IN PROGRESS), tier, layer, type
entity ownership      : ENTITIES OWNED rows (CANDIDATE → REGISTERED, still no ID)
shared declarations   : SHARED rows
dependency graph      : derived by gov.py graph from the `platform-dependencies` block (never merged by hand) [C:C9.23]
open-question index   : rows RESOLVED by this stage's dialogue (+ resolution)
pipeline status       : P0 = DONE for the module
event history         : "P0 completed: [module list]"
```

---

## 5 — Questions (allowed here) — how they are asked and closed

```
A QUESTION exists only when §1 A–D and §3.2 STEPS 1–4 leave a point unresolved. [G]

QUESTION — [point]
  Affects       : [module / entity / dependency / scope]
  Options       : A) … (trade-off)   B) … (trade-off)
  Researched    : [what the knowledge sources / domain-profile say — cited]
  Recommended   : [option] — because [rationale]
```

Lane `analysis-dialogue`: the implementers (claude:opus, claude:sonnet) converge on each QUESTION —
challenge, answer, ≤ 4 rounds, until **mutually-acceptable**. The converged
block is presented to the user as the recommended answer; the user confirms or adjusts.

```
Resolution is recorded in the RESOLVED DECISIONS table of the artifact it affects.
No external open-questions file. A point the user leaves undecided stays under
OPEN ITEMS of the platform summary and is carried to P0.5 (the last stage that may ask).
Never ask about: anything in the domain-profile, the registry, a prior module registry,
or the knowledge sources.
```

---

## 6 — Continuation

```
Resume with: platform summary + the module artifacts of completed modules (from
_state/ or the version folder) + registry pipeline status.
Announce: "Resuming P0. Completed: [list]. Pending: [NEW/EXISTING from the summary]."
No re-analysis of completed modules. Always re-emit the platform summary before
ending if in-session adjustments were made.
```

---

## 7 — Boundaries and enforcement

```
OWNS      : platform-summary.md · module-registry-{mod}.md · business-policies-{mod}.md · POL IDs · tier and build-order
            assignment · entity / lookup candidate discovery and ownership · dependency map
DOES NOT  : any ID of US (P0.5), REQ (P1), AC (P1), ENT (P1), RULE (P1), DBF (P2), XM (P2), CON (P1.5), API (P3.1), QR (P3.1), UXD (P3.2), SCR (P3.2), SCR-REQ (P1), TC (P4), FEAT (feature) ·
            requirements · screens · field lists · validation rules · DDL · execution phases

VIOLATION (this stage's output contains any of these):
  screens with field lists · validation logic · requirement statements other than
  POL policies · any ID owned by another stage · permission tables · test scenarios

RUNTIME REDIRECT — when the user asks for requirements / screens / rules / fields:
  1. Complete this stage's artifacts for the module (they ARE the correct answer).
  2. Redirect once: "Requirements begin in P0.5 and later stages; these files are
     their input."
  3. Offer the next valid action (another module number, or proceed).
```

---

## 8 — Self-check before emitting

- [ ] Every module in the summary has a code from the profile (or RESERVED in the registry), a tier, a status.
- [ ] Every entity owned has a kind from `config, transactional` and a source; no entity ID.
- [ ] Every policy is exactly one EARS pattern, has a Source, a Trigger, a continuous sequence number. [C:C3.8]
- [ ] Every auto-decision carries AUTO / FROM / IF WRONG; every default cites a knowledge source.
- [ ] Every QUESTION raised appears in a RESOLVED DECISIONS table (or under OPEN ITEMS with the user's explicit deferral).
- [ ] No requirement, screen, field, rule, permission or later-stage ID anywhere.
- [ ] Vocabulary matches the domain-profile STEERING block verbatim.


---
# INPUTS (generated current state)

<<<INPUT: domain-profile>>>
# DOMAIN PROFILE — Request Verification Service
══════════════════════════════════════════════════════════════════
Profile         : aias (Request Verification Service)
Version         : 1
Last Updated    : 2026-10-01
Status          : FRESH
Research        : 0 web sources cited — the research capability was unavailable this run (block 9); every claim cites the owner's raw idea [KB:raw-idea.md] or an owner confirmation
══════════════════════════════════════════════════════════════════

## 1. SCOPE

In bounds [KB:raw-idea.md §2, §15]:
- A standalone Spring Boot service that host systems call over REST to verify one government service request before an employee approves it.
- Per-service configuration (a service package: service knowledge + service definition), maintained by the service administrator. Employees never supply service knowledge.
- Read-only access to host request data through an MCP server (Oracle).
- Document retrieval in three fetch modes — `path`, `blob`, `manual` — and reading PDF, XLS and image documents (other extensions may appear).
- A structured, evidence-backed verification report, stored in the service's own database.
- A web frontend for the employee, embedded in the host screen: the checks of a request, the report, manual document upload, recording the decision (amendment A1).
- Optional execution of a host approval API, per service, triggered only by the employee.

Out of bounds for this version [KB:raw-idea.md §2, §15]:
- Multi-tenancy; conversation memory; RAG and vector stores; multi-agent orchestration.
- A full administration UI and an internal permission system.
- Caller authentication (API key or mTLS) and the security phases — deferred to a later version; the owner already has the solution (amendment A2). The §12 guardrails are NOT deferred.

## 2. PURPOSE

A government employee approving a service request today checks the request's data and attached documents against the service's conditions by hand. This service does that check first and returns a short report — compliant or not, and where the problems are, with the evidence for each — so the employee decides faster and on verified facts. The employee stays the decision maker; the service informs the decision and never makes it [KB:raw-idea.md §1].

The service is generic: every service it verifies is configuration, not code. It supersedes the earlier "Reusable Agentic AI Library" idea, which was broader than the first real need [KB:raw-idea.md §1].

## 3. RESPONSIBILITIES

- Hold the registry of service packages and their versions, and the connections set per environment at activation.
- Run one check per request as a fixed, asynchronous pipeline: load the package → run its queries → fetch and read its documents → run the deterministic checks in code → let the LLM compare data and document content with the service knowledge → store the report [KB:raw-idea.md §5].
- Fetch documents by `path`, `blob` or `manual` upload and read them by type (text extraction, table extraction, OCR or a vision model).
- Store every report — overall status, one finding per condition with its evidence, the documents read / missing / unreadable, and the metadata (service version, fetch mode, model, time, employee) — and the employee's decision beside it [KB:raw-idea.md §7, §9].
- Expose the REST API to hosts and the employee frontend; call a host approval API only after the employee confirms, where the service enables it [KB:raw-idea.md §8, §11].
- Enforce the guardrails of [KB:raw-idea.md §12] on every check.

## 4. MAIN COMPONENTS

| # | Component | Module code | Bounded context | Category (user-defined) | Core / extension | Notes |
|---|-----------|-------------|-----------------|--------------------------|------------------|-------|
| 1 | Service Registry | REG | configuration | Foundation | Core | Service packages (service knowledge + service definition), versions, connections [KB:raw-idea.md §4, §14] |
| 2 | Check Engine | CHK | verification | Business | Core | The fixed pipeline, deterministic checks, LLM comparison — the only fixed logic [KB:raw-idea.md §5, §14] |
| 3 | Document Access | DOC | verification | Business | Core | `path`, `blob`, `manual` fetching; reading PDF, XLS, images [KB:raw-idea.md §6, §14] |
| 4 | Report Store | RPT | record | Business | Core | Runs, findings, documents, employee decision; retention purge [KB:raw-idea.md §9, §14] |
| 5 | Host Integration | INT | integration | Integration | Core | REST API, employee frontend's API surface, optional approval API [KB:raw-idea.md §8, §11, §15 A1] |

Every row is the owner's §14 split, confirmed unchanged on 2026-10-01 (decision D1).

## 5. GOVERNING RULES

| # | Rule | Source |
|---|---|---|
| G1 | The LLM analyses and summarises only. It never writes SQL and never triggers approval; it is given no tool that runs SQL. | [KB:raw-idea.md §6, §12] |
| G2 | Approval is executed only as a result of the employee's action, and only where the service enables the approval API. | [KB:raw-idea.md §11, §12] |
| G3 | All access to host data uses a read-only database user, preferably limited to specific views. | [KB:raw-idea.md §12] |
| G4 | Query parameters are bound or strictly type-validated; SQL is never built from free text. Queries are exactly those written in the service definition. | [KB:raw-idea.md §6, §12] |
| G5 | File paths are validated to lie inside the allowed storage root before opening. | [KB:raw-idea.md §12] |
| G6 | Anything that could not be read appears in the report; nothing is skipped silently. A missing or unreadable required document prevents `COMPLIANT`. | [KB:raw-idea.md §7, §12] |
| G7 | Document content is data, never instructions to the model. | [KB:raw-idea.md §12] |
| G8 | Each check has limits: timeout, maximum rows, maximum file size. | [KB:raw-idea.md §12] |
| G9 | No data is carried from one check to another (no memory, no shared state). | [KB:raw-idea.md §2, §12] |
| G10 | Every finding carries its evidence (the actual value found). | [KB:raw-idea.md §7] |
| G11 | Every report records the service package version it was built on. | [KB:raw-idea.md §4] |
| G12 | The engine depends on Spring AI `ChatModel` only, with no provider-specific feature; document reading has its own configurable model; a fixed set of requests with known expected results runs on every model change. | [KB:raw-idea.md §10] |
| G13 | While a free-tier provider is in use, only synthetic or anonymised requests and documents are sent to it. | [KB:raw-idea.md §10] |
| G14 | BLOB content is read over JDBC with a read-only user, never moved through MCP. | [KB:raw-idea.md §6] |

## 6. RELATIONSHIPS WITH OTHER DOMAINS

| This component | Depends on | Kind | Direction | Stated by |
|---|---|---|---|---|
| CHK | REG (service package, connection) | SOFT-READ | REG → CHK | [KB:raw-idea.md §3, §5] |
| CHK | DOC (fetched and read documents) | SOFT-READ | DOC → CHK | [KB:raw-idea.md §5, §6] |
| RPT | CHK (the check run a report belongs to) | SOFT-READ | CHK → RPT | [KB:raw-idea.md §3, §9] |
| INT | CHK (start a check, poll its status) | SOFT-READ | CHK → INT | [KB:raw-idea.md §8] |
| INT | RPT (report JSON, record the decision) | SOFT-READ | RPT → INT | [KB:raw-idea.md §8, §11] |
| DOC | INT (manual upload received through the API) | SOFT-READ | INT → DOC | [KB:raw-idea.md §6, §8] |
| CHK, DOC | Host database via Oracle SQLcl MCP server (external) | SOFT-READ | host → service | [KB:raw-idea.md §6]; server confirmed by owner (D2) |
| DOC | Host file storage / host BLOB columns (external) | SOFT-READ | host → service | [KB:raw-idea.md §6] |
| CHK, DOC | LLM provider via Spring AI (external, replaceable) | SOFT-READ | provider → service | [KB:raw-idea.md §10] |
| INT | Host approval API (external, optional per service) | outbound call | service → host | [KB:raw-idea.md §11] |
| Host systems (Oracle ADF, others) | INT | consumer | service → host | [KB:raw-idea.md §11] |

All modules run in one deployable and reach each other through injected interfaces (profile `conventions.module_interface: in_process`). The host data lives outside the service's schema, so no host identifier is ever a foreign key.

## 7. STEERING  (read verbatim by every later stage)

### 7.1 Ubiquitous language

| Term | Definition | Do not say | Module code |
|---|---|---|---|
| Check | One independent verification run of one request against one service package; nothing is carried from one check to another. | job, audit, scan | CHK |
| Service Package | The per-service configuration — service knowledge plus service definition — maintained by the service administrator and versioned; adding a service needs no code. | plugin, template, tenant | REG |
| Service Knowledge | The natural-language conditions and rules of a service, read whole by the LLM (no retrieval). | prompt, rules file | REG |
| Service Definition | The structured part of a service package — queries, required documents, fetch mode, optional approval API — executed literally by the engine, never interpreted by the LLM. | — | REG |
| Connection | A named data source defined once outside the service packages and set at activation time per environment. | — | REG |
| Fetch Mode | How a service's documents are obtained: `path`, `blob` or `manual`. | — | DOC |
| Finding | The outcome of one condition in a report — satisfied or not, its evidence and a note for the employee. | issue, violation | RPT |
| Evidence | The actual value found that a finding is based on, shown so the employee can verify it. | — | RPT |
| Overall Status | The report's verdict: `COMPLIANT`, `NOT_COMPLIANT` or `NEEDS_MANUAL_REVIEW`. | — | RPT |
| Host System | The calling application (Oracle ADF or another government system) that starts checks and embeds the employee frontend. | client app | INT |
| Employee Decision | The approve / reject decision the employee takes, recorded beside the report result. | verdict | RPT |
| Approval API | An optional, per-service host endpoint the service calls only after the employee confirms a decision. | — | INT |
| Service Administrator | The person who maintains service packages and connections. | — | REG |
| Employee | The host-system user who reviews the report and takes the decision; identified by the host, recorded with the check. | — | INT |

### 7.2 Bounded contexts

| Context | Owns module codes | Boundary statement |
|---|---|---|
| configuration | REG | What a service IS: its packages, versions and connections. Read by every check, written only by the administrator. |
| verification | CHK, DOC | Running one check: the pipeline and the documents it fetches and reads. Holds no state between checks. |
| record | RPT | What a check FOUND and what the employee DECIDED, kept for the retention period. |
| integration | INT | Everything a host or the employee frontend touches: the REST API and the outbound approval call. |

### 7.3 Module prefixes proposal

| Code | Display | Status |
|---|---|---|
| REG | Service Registry | IN PROFILE |
| CHK | Check Engine | IN PROFILE |
| DOC | Document Access | IN PROFILE |
| RPT | Report Store | IN PROFILE |
| INT | Host Integration | IN PROFILE |

### 7.4 Identifier rules

Later stages build IDs as `{prefix}-{MOD}-{seq}` (seq width 3) with the module codes above. Entity kinds: `config, transactional`. The profile adds no domain-specific atom.

### 7.5 Knowledge sources to cite

- `profiles/aias/knowledge/raw-idea.md` (as amended in its §15, A1–A2)

## 8. RESOLVED DECISIONS

| # | Point | Decision | Recommended by dialogue? | Confirmed by user | Sources |
|---|---|---|---|---|---|
| D1 | Module split (§14 "for domain-profile to confirm") | Keep all five modules unchanged: REG, CHK, DOC, RPT, INT | yes | yes — 2026-10-01 | [KB:raw-idea.md §14] |
| D2 | MCP server for Oracle (§13 Open) | Oracle SQLcl MCP server. The read-only user and views are enforced at its saved connection; bind variables or strict type validation, row limit and timeout are enforced by the engine before every call, so the §6 requirements hold whatever the server does | yes | yes — 2026-10-01 | [KB:raw-idea.md §6]; NO-RESEARCH |
| D3 | LLM provider for real request data (§13 Open) | Decided before go-live, as a go-live gate. Until then the free tier with synthetic / anonymised data only (G13). The gate's acceptance: no training on submitted data, a data-processing agreement, in-region processing, swappable through Spring AI. No design element waits on it | yes | yes — 2026-10-01 | [KB:raw-idea.md §10]; NO-RESEARCH |
| D4 | Report retention (§13 Open) | A report is kept as long as the host keeps the request it verified. The period is configuration; a purge removes older runs with their findings and documents (hard delete). Who may view stored reports is deferred with caller authentication (A2) | yes | yes — 2026-10-01 | [KB:raw-idea.md §9, §15 A2]; NO-RESEARCH |
| D5 | Pilot service (§13 Open) | Scholarship request — the raw idea's own example: explicit numeric conditions, two required documents (TRANSCRIPT, ID_CARD), `path` fetch mode | yes | yes — 2026-10-01 | [KB:raw-idea.md §4] |
| D6 | Frontend (amendment A1) | A web frontend (React + TypeScript) embedded in the host screen replaces the server-rendered report page as the display path; it uses the same REST API as any host | — (owner decision) | yes — 2026-10-01 | [KB:raw-idea.md §15 A1] |
| D7 | Security (amendment A2) | Caller authentication and the security phases are deferred to a later version; the §12 guardrails stay in this version | — (owner decision) | yes — 2026-10-01 | [KB:raw-idea.md §15 A2] |

## 9. RESEARCH LOG

The `research-log` block — `gov.py analyze` reads it (C1.4 sourced; C0.1 holds it to its schema). Web research was unavailable this run; the rows below state that rather than carry unsourced claims.

```yaml name=research-log
rows:
  - {point: "MCP server for Oracle", finding: "Recommendation made from the raw idea's §6 requirements and operator knowledge; not web-researched this run", source: NO-RESEARCH, used_in: "8 D2"}
  - {point: "LLM provider for real data", finding: "Go-live gate criteria derived from the raw idea's §10 free-tier caution; provider terms not web-researched this run", source: NO-RESEARCH, used_in: "8 D3"}
  - {point: "Report retention", finding: "Tied to the host's record retention; no regulation was researched this run", source: NO-RESEARCH, used_in: "8 D4"}
```

## 10. OPEN ITEMS

None. Caller authentication (API key or mTLS) and who may view stored reports are not open items of this version: they are deferred by the owner's amendment A2 and return with the security version.
══════════════════════════════════════════════════════════════════

<<<END INPUT>>>

<<<INPUT: project-registry>>>
# PROJECT REGISTRY — Request Verification Service
══════════════════════════════════════════════════════════════════
Profile            : aias
Registry Version   : 1.0.0
Domain Profile     : analysis/domain/domain-profile.md v1
Last Updated       : 2026-10-01 by P-1
Modules registered : 5   Entity candidates : 6   Open items : 2
══════════════════════════════════════════════════════════════════

## SCHEMA COMPLIANCE MAP

The block `gov.py analyze` reads (C2.2): every category of `factory.yaml → registry.categories`, mapped to the section of this registry that covers it.

```yaml name=compliance-map
categories:
  CAT-1: "1. Identity & versioning"
  CAT-2: "2. Conventions & steering"
  CAT-3: "3. Module / component index"
  CAT-4: "4. Entity ownership"
  CAT-5: "5. Shared entity declarations"
  CAT-6: "6. Structural / implementation registry"
  CAT-7: "7. Cross-module dependency index"
  CAT-8: "8. Open question index"
  CAT-9: "9. Pipeline / progress status"
  CAT-10: "10. Change / event history"
uncovered: []
```

---

## 1. Identity & versioning

| Field | Value | Source |
|---|---|---|
| Platform | Request Verification Service | domain-profile header |
| Profile | aias | profile |
| Form | A standalone Spring Boot service called by host systems over REST; not a library | domain-profile §1; [KB:raw-idea.md §13] |
| Stack | Java 21, Spring Boot 4, Spring AI 2.0 | [KB:raw-idea.md §3, §13] |
| Tenancy | Single tenant | domain-profile §1; [KB:raw-idea.md §13] |
| Tracks | backend, frontend (A1); platform track covers the MCP connection, LLM provider configuration and service database | [KB:raw-idea.md §14, §15 A1] |
| Module interface | in-process — all modules in one deployable, reached through injected interfaces | domain-profile §6 (profile `conventions.module_interface: in_process`) |
| Domain profile | analysis/domain/domain-profile.md v1 (Status FRESH, 2026-10-01) | domain-profile header |

### Version history

| Registry version | Date | Stage | Change | Source |
|---|---|---|---|---|
| 1.0.0 | 2026-10-01 | P-1 | Registry bootstrapped from domain-profile v1 (first creation) | domain-profile v1 |

---

## 2. Conventions & steering

### 2.1 Steering — copied verbatim from domain-profile §7

#### 7.1 Ubiquitous language

| Term | Definition | Do not say | Module code |
|---|---|---|---|
| Check | One independent verification run of one request against one service package; nothing is carried from one check to another. | job, audit, scan | CHK |
| Service Package | The per-service configuration — service knowledge plus service definition — maintained by the service administrator and versioned; adding a service needs no code. | plugin, template, tenant | REG |
| Service Knowledge | The natural-language conditions and rules of a service, read whole by the LLM (no retrieval). | prompt, rules file | REG |
| Service Definition | The structured part of a service package — queries, required documents, fetch mode, optional approval API — executed literally by the engine, never interpreted by the LLM. | — | REG |
| Connection | A named data source defined once outside the service packages and set at activation time per environment. | — | REG |
| Fetch Mode | How a service's documents are obtained: `path`, `blob` or `manual`. | — | DOC |
| Finding | The outcome of one condition in a report — satisfied or not, its evidence and a note for the employee. | issue, violation | RPT |
| Evidence | The actual value found that a finding is based on, shown so the employee can verify it. | — | RPT |
| Overall Status | The report's verdict: `COMPLIANT`, `NOT_COMPLIANT` or `NEEDS_MANUAL_REVIEW`. | — | RPT |
| Host System | The calling application (Oracle ADF or another government system) that starts checks and embeds the employee frontend. | client app | INT |
| Employee Decision | The approve / reject decision the employee takes, recorded beside the report result. | verdict | RPT |
| Approval API | An optional, per-service host endpoint the service calls only after the employee confirms a decision. | — | INT |
| Service Administrator | The person who maintains service packages and connections. | — | REG |
| Employee | The host-system user who reviews the report and takes the decision; identified by the host, recorded with the check. | — | INT |

#### 7.2 Bounded contexts

| Context | Owns module codes | Boundary statement |
|---|---|---|
| configuration | REG | What a service IS: its packages, versions and connections. Read by every check, written only by the administrator. |
| verification | CHK, DOC | Running one check: the pipeline and the documents it fetches and reads. Holds no state between checks. |
| record | RPT | What a check FOUND and what the employee DECIDED, kept for the retention period. |
| integration | INT | Everything a host or the employee frontend touches: the REST API and the outbound approval call. |

#### 7.3 Module prefixes proposal

| Code | Display | Status |
|---|---|---|
| REG | Service Registry | IN PROFILE |
| CHK | Check Engine | IN PROFILE |
| DOC | Document Access | IN PROFILE |
| RPT | Report Store | IN PROFILE |
| INT | Host Integration | IN PROFILE |

#### 7.4 Identifier rules

Later stages build IDs as `{prefix}-{MOD}-{seq}` (seq width 3) with the module codes above. Entity kinds: `config, transactional`. The profile adds no domain-specific atom.

#### 7.5 Knowledge sources to cite

- `profiles/aias/knowledge/raw-idea.md` (as amended in its §15, A1–A2)

### 2.2 Profile echo (read-only, cited as "profile")

| Fact | Value | Agrees with steering? |
|---|---|---|
| Module codes | REG, CHK, DOC, RPT, INT | yes — all five IN PROFILE, none RESERVED-outside-profile |
| Entity kinds | config, transactional | yes |
| Bounded contexts | configuration [REG]; verification [CHK, DOC]; record [RPT]; integration [INT] | yes |
| Knowledge files | profiles/aias/knowledge/raw-idea.md | yes |

### 2.3 Enforcement notes

- **E1** Every later artifact uses the terms of §2.1 (7.1) verbatim; a synonym listed under "do not say" (e.g. job / audit / scan for Check; plugin / template / tenant for Service Package; prompt / rules file for Service Knowledge; issue / violation for Finding; client app for Host System; verdict for Employee Decision) is a consistency finding at the pass gate (`gov.py analyze` checks registry ↔ artifact agreement).
- **E2** IDs follow `{prefix}-{MOD}-{seq}` (seq width 3) with the module codes of this section only (REG, CHK, DOC, RPT, INT).
- **E3** Entities are classified with the kinds `config, transactional`.
- **E4** Sources to cite when a stage resolves an ambiguity: the knowledge sources listed here (`profiles/aias/knowledge/raw-idea.md`, as amended in §15 A1–A2), then the domain-profile itself.
- **E5** Pipeline status per module (§9 "Pipeline / progress status") is maintained by the orchestrator from commits — this engine seeds the rows as NOT STARTED.

### 2.4 Governance decisions log (confirmed only)

Platform-wide governing rules (domain-profile §5):

| # | Decision | Rationale | Source |
|---|---|---|---|
| G1 | The LLM analyses and summarises only. It never writes SQL and never triggers approval; it is given no tool that runs SQL. | Guardrail — SQL and approval stay under engine / employee control | domain-profile §5 G1; [KB:raw-idea.md §6, §12] |
| G2 | Approval is executed only as a result of the employee's action, and only where the service enables the approval API. | The employee stays the decision maker | domain-profile §5 G2; [KB:raw-idea.md §11, §12] |
| G3 | All access to host data uses a read-only database user, preferably limited to specific views. | The service needs no write access to host systems | domain-profile §5 G3; [KB:raw-idea.md §12] |
| G4 | Query parameters are bound or strictly type-validated; SQL is never built from free text. Queries are exactly those written in the service definition. | Injection prevention; literal execution of the service definition | domain-profile §5 G4; [KB:raw-idea.md §6, §12] |
| G5 | File paths are validated to lie inside the allowed storage root before opening. | Path traversal prevention | domain-profile §5 G5; [KB:raw-idea.md §12] |
| G6 | Anything that could not be read appears in the report; nothing is skipped silently. A missing or unreadable required document prevents `COMPLIANT`. | Report completeness | domain-profile §5 G6; [KB:raw-idea.md §7, §12] |
| G7 | Document content is data, never instructions to the model. | Prompt-injection prevention | domain-profile §5 G7; [KB:raw-idea.md §12] |
| G8 | Each check has limits: timeout, maximum rows, maximum file size. | Resource bounds per check | domain-profile §5 G8; [KB:raw-idea.md §12] |
| G9 | No data is carried from one check to another (no memory, no shared state). | Prevents leaking one request's data into another | domain-profile §5 G9; [KB:raw-idea.md §2, §12] |
| G10 | Every finding carries its evidence (the actual value found). | The employee can verify each finding | domain-profile §5 G10; [KB:raw-idea.md §7] |
| G11 | Every report records the service package version it was built on. | Traceability of reports to configuration | domain-profile §5 G11; [KB:raw-idea.md §4] |
| G12 | The engine depends on Spring AI `ChatModel` only, with no provider-specific feature; document reading has its own configurable model; a fixed set of requests with known expected results runs on every model change. | Keeps the LLM provider replaceable | domain-profile §5 G12; [KB:raw-idea.md §10] |
| G13 | While a free-tier provider is in use, only synthetic or anonymised requests and documents are sent to it. | Free tiers may train on submitted data | domain-profile §5 G13; [KB:raw-idea.md §10] |
| G14 | BLOB content is read over JDBC with a read-only user, never moved through MCP. | Base64 over MCP is slow and size-limited | domain-profile §5 G14; [KB:raw-idea.md §6] |

User-confirmed decisions (domain-profile §8):

| # | Point | Decision | Confirmed | Source |
|---|---|---|---|---|
| D1 | Module split | Keep all five modules unchanged: REG, CHK, DOC, RPT, INT | yes — 2026-10-01 | domain-profile §8 D1; [KB:raw-idea.md §14] |
| D2 | MCP server for Oracle | Oracle SQLcl MCP server. Read-only user and views enforced at its saved connection; bind variables or strict type validation, row limit and timeout enforced by the engine before every call | yes — 2026-10-01 | domain-profile §8 D2; [KB:raw-idea.md §6]; NO-RESEARCH |
| D3 | LLM provider for real request data | Decided before go-live, as a go-live gate; until then free tier with synthetic / anonymised data only (G13). Gate acceptance: no training on submitted data, a data-processing agreement, in-region processing, swappable through Spring AI. No design element waits on it | yes — 2026-10-01 | domain-profile §8 D3; [KB:raw-idea.md §10]; NO-RESEARCH |
| D4 | Report retention | A report is kept as long as the host keeps the request it verified; the period is configuration; a purge removes older runs with their findings and documents (hard delete). Who may view stored reports is deferred with caller authentication (A2) | yes — 2026-10-01 | domain-profile §8 D4; [KB:raw-idea.md §9, §15 A2]; NO-RESEARCH |
| D5 | Pilot service | Scholarship request — explicit numeric conditions, two required documents (TRANSCRIPT, ID_CARD), `path` fetch mode | yes — 2026-10-01 | domain-profile §8 D5; [KB:raw-idea.md §4] |
| D6 | Frontend (A1) | A web frontend (React + TypeScript) embedded in the host screen replaces the server-rendered report page as the display path; it uses the same REST API as any host | yes — 2026-10-01 | domain-profile §8 D6; [KB:raw-idea.md §15 A1] |
| D7 | Security (A2) | Caller authentication and the security phases are deferred to a later version; the §12 guardrails stay in this version | yes — 2026-10-01 | domain-profile §8 D7; [KB:raw-idea.md §15 A2] |

Locked decisions from the knowledge source (raw idea §13 "Decided", not to be reopened):

| Topic | Decision | Source |
|---|---|---|
| Configuration | One service package per service, maintained by the service administrator | [KB:raw-idea.md §13] |
| Database access | Read-only through an MCP server, specified at activation | [KB:raw-idea.md §13] |
| Documents | `path`, `blob` and `manual` fetch modes all available, chosen per service | [KB:raw-idea.md §6, §13] |
| Decision maker | The employee; the approval API is an optional second path | [KB:raw-idea.md §13] |
| Storage | Reports kept in the service's own database tables, in a schema separate from the read-only host user | [KB:raw-idea.md §9, §13] |
| LLM | Cloud, free tier for testing, replaceable by configuration | [KB:raw-idea.md §10, §13] |
| Memory, RAG, vector store | Not included | [KB:raw-idea.md §2, §13] |
| Check execution | Asynchronous: the host starts a check and polls for the result | [KB:raw-idea.md §5]; domain-profile §3 |

Out of bounds for this version (domain-profile §1): multi-tenancy; conversation memory; RAG and vector stores; multi-agent orchestration; a full administration UI and an internal permission system; caller authentication (API key or mTLS) and the security phases (A2).

ADRs referenced: none — this run made no decision of its own.

---

## 3. Module / component index

| Module code | Display name | Bounded context | Category | Core / extension | Status | Scope | Source |
|---|---|---|---|---|---|---|---|
| REG | Service Registry | configuration | Foundation | Core | RESERVED | Service packages (service knowledge + service definition), versions, connections | domain-profile §4 row 1, §7.3; D1; profile |
| CHK | Check Engine | verification | Business | Core | RESERVED | The fixed pipeline, deterministic checks, LLM comparison — the only fixed logic | domain-profile §4 row 2, §7.3; D1; profile |
| DOC | Document Access | verification | Business | Core | RESERVED | `path`, `blob`, `manual` fetching; reading PDF, XLS, images | domain-profile §4 row 3, §7.3; D1; profile |
| RPT | Report Store | record | Business | Core | RESERVED | Runs, findings, documents, employee decision; retention purge | domain-profile §4 row 4, §7.3; D1, D4; profile |
| INT | Host Integration | integration | Integration | Core | RESERVED | REST API, employee frontend's API surface, optional approval API | domain-profile §4 row 5, §7.3; D1, D6; profile |

All five codes are in `profile.vocabulary.module_prefixes`; RESERVED = code reserved, module not yet started (RULE-4).

External systems (not modules — no code assigned, recorded for context only): host database via Oracle SQLcl MCP server; host file storage / host BLOB columns; LLM provider via Spring AI; host approval API; host systems (Oracle ADF, others). Source: domain-profile §6.

---

## 4. Entity ownership

Analysis-phase candidates. `CAND-*` refs are registry-local handles, not pipeline IDs; they are replaced by formal IDs when the owning stage registers the element.

| Candidate ref | Entity name | Owner module | Kind | PRIVATE/SHARED | Status | Source |
|---|---|---|---|---|---|---|
| CAND-REG-001 | Service Package (service knowledge + service definition, versioned) | REG | config | SHARED? | CANDIDATE | domain-profile §4 row 1, §7.1, §7.2, G11; [KB:raw-idea.md §4] |
| CAND-REG-002 | Connection | REG | config | SHARED? | CANDIDATE | domain-profile §6 (CHK depends on REG connection), §7.1; [KB:raw-idea.md §4] |
| CAND-CHK-001 | Check (one check run: service, request number, employee, status, result, service version, model, timestamps) | UNCLEAR (CHK or RPT) | transactional | SHARED? | OPEN | domain-profile §7.1 (term "Check" → CHK), §4 row 4 (RPT holds "Runs"); [KB:raw-idea.md §9 CHECK_RUN] — see OQ-1 |
| CAND-RPT-001 | Finding (satisfied flag, evidence, note) | RPT | transactional | PRIVATE | CANDIDATE | domain-profile §4 row 4, §7.1, G10; [KB:raw-idea.md §7, §9 CHECK_FINDING] |
| CAND-RPT-002 | Check Document (type, source mode, read status) | UNCLEAR (RPT or DOC) | transactional | SHARED? | OPEN | domain-profile §4 rows 3–4, §7.1 (Fetch Mode → DOC); [KB:raw-idea.md §9 CHECK_DOCUMENT] — see OQ-2 |
| CAND-RPT-003 | Employee Decision | RPT | transactional | PRIVATE | CANDIDATE | domain-profile §7.1, §7.2 (record context), §4 row 4; [KB:raw-idea.md §9, §11] |

Notes (inferred, not confirmed):
- Service Knowledge and Service Definition (domain-profile §7.1) are registered as parts of CAND-REG-001, not as separate candidates; P2 decides whether they become separate entities.
- Raw idea §9 stores the employee decision as columns of `CHECK_RUN`; whether CAND-RPT-003 is an entity of its own or attributes of CAND-CHK-001 is for P2.
- The verification report itself (Overall Status, findings, documents, metadata) is the composition of CAND-CHK-001, CAND-RPT-001 and CAND-RPT-002 [KB:raw-idea.md §7, §9]; it is not registered as a separate candidate.

---

## 5. Shared entity declarations

| Candidate ref | Entity name | Owner module | Referenced by | Basis | Status | Source |
|---|---|---|---|---|---|---|
| CAND-REG-001 | Service Package | REG | CHK (loads package), RPT (records package version, G11) | Read across modules through the REG interface | CANDIDATE | domain-profile §6 (REG → CHK), §5 G11 |
| CAND-REG-002 | Connection | REG | CHK, DOC (queries / BLOB reads use a connection) | Read across modules through the REG interface | CANDIDATE | domain-profile §6 (REG → CHK; CHK, DOC → host database) |
| CAND-CHK-001 | Check | UNCLEAR (CHK or RPT) | CHK (runs it), RPT (report belongs to it), INT (start / poll) | Referenced by three modules; owner open | OPEN | domain-profile §6 (CHK → RPT, CHK → INT) — see OQ-1 |
| CAND-RPT-002 | Check Document | UNCLEAR (RPT or DOC) | DOC (fetches / reads), RPT (stores), CHK (deterministic required-document check) | Referenced by three modules; owner open | OPEN | domain-profile §6 (DOC → CHK), §4 rows 3–4 — see OQ-2 |

---

## 6. Structural / implementation registry

None yet — filled by P2 / P3.1.

---

## 7. Cross-module dependency index

Derived: `platform/dependency-graph.json` (`gov.py graph`). No dependency row is written in this registry; the relations stated in domain-profile §6 reach P0, which turns them into its `platform-dependencies` block.

---

## 8. Open question index

| Ref | Question | Evidence | Affected rows | Status | Resolution |
|---|---|---|---|---|---|
| OQ-1 | Which module owns the Check (check run) record — CHK, which runs the check and owns the term, or RPT, which stores runs? | Side A: domain-profile §7.1 maps the term "Check" to CHK; §3 / §4 row 2 make CHK run the pipeline. Side B: domain-profile §4 row 4 lists "Runs" in RPT's scope; §6 "RPT depends on CHK (the check run a report belongs to)"; [KB:raw-idea.md §9] puts `CHECK_RUN` with the report tables. | CAND-CHK-001 (§4, §5) | OPEN | To be resolved by P0 dialogue |
| OQ-2 | Which module owns the Check Document record (type, source mode, read status) — RPT, which stores "documents", or DOC, which fetches and reads them? | Side A: domain-profile §4 row 4 lists "documents" in RPT's scope; [KB:raw-idea.md §9] `CHECK_DOCUMENT` is a report table; §7 report "Documents" part. Side B: domain-profile §4 row 3 gives DOC fetching and reading; §7.1 maps "Fetch Mode" to DOC. | CAND-RPT-002 (§4, §5) | OPEN | To be resolved by P0 dialogue |

domain-profile §10 lists no open items; caller authentication and report-viewing rights are deferred by A2, not open (domain-profile §10; D7).

---

## 9. Pipeline / progress status

| Module code | Pipeline status | Source |
|---|---|---|
| REG | NOT STARTED | seeded by P-1 (E5) |
| CHK | NOT STARTED | seeded by P-1 (E5) |
| DOC | NOT STARTED | seeded by P-1 (E5) |
| RPT | NOT STARTED | seeded by P-1 (E5) |
| INT | NOT STARTED | seeded by P-1 (E5) |

Maintained by the orchestrator from commits from here on (E5).

---

## 10. Change / event history

| Date | Event | ID | Ref | Summary |
|---|---|---|---|---|
| 2026-10-01 | BOOTSTRAP | — | — | Registry 1.0.0 created from domain-profile v1: 5 modules, 6 entity candidates (4 shared declarations), 14 governing rules + 7 user decisions + 8 locked knowledge decisions, 2 open items; steering copied (14 terms · 4 contexts · 5 codes, 0 RESERVED-outside-profile); 5 pipeline rows seeded NOT STARTED; 0 structural rows; 0 dependency rows (derived) |

### Extraction report — P-1 run

```
══════════════════════════════════════════════════════════════════
EXTRACTION REPORT — P-1 — 2026-10-01 — profile aias
Input : analysis/domain/domain-profile.md v1
══════════════════════════════════════════════════════════════════
MODULES IDENTIFIED     : + REG Service Registry — context configuration — RESERVED
                         + CHK Check Engine — context verification — RESERVED
                         + DOC Document Access — context verification — RESERVED
                         + RPT Report Store — context record — RESERVED
                         + INT Host Integration — context integration — RESERVED
ENTITY CANDIDATES      : + Service Package — owner REG — config — SHARED?
                         + Connection — owner REG — config — SHARED?
                         + Check — owner UNCLEAR (CHK/RPT) — transactional — SHARED?
                         + Finding — owner RPT — transactional — PRIVATE
                         + Check Document — owner UNCLEAR (RPT/DOC) — transactional — SHARED?
                         + Employee Decision — owner RPT — transactional — PRIVATE
DECISIONS CONFIRMED    : + G1–G14 governing rules — source domain-profile §5
                         + D1–D7 user-confirmed decisions — source domain-profile §8
                         + 8 locked decisions — source [KB:raw-idea.md §13 Decided]
OPEN ITEMS RECORDED    : + OQ-1 owner of Check — evidence domain-profile §4, §7.1; KB §9
                         + OQ-2 owner of Check Document — evidence domain-profile §4, §7.1; KB §9
STEERING COPIED        : 14 terms · 4 contexts · 5 codes (0 RESERVED)
SECTIONS UPDATED       : 1, 2, 3, 4, 5, 7, 8, 9, 10
NOTHING EXTRACTED FOR  : 6 Structural / implementation registry (filled by P2 / P3.1)
══════════════════════════════════════════════════════════════════
```

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
# BRIEF — stage `P0.5` (PRD) · module CHK · v1 · profile `aias`

Lane `analysis-dialogue` · implementer ['claude:opus', 'claude:sonnet'] · effort high · round 1

## Rules that bind this run
- Questions: **allowed**. Close every open point inside this dialogue with a researched, recommended answer; never write an external open-questions file.
- Owns IDs: US — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-CHK-001`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared/analysis/modules/CHK/P0_5/prd-chk.md`
- Write the files directly into the project checkout (you are the operator); the orchestrator reads them on `--complete`.

## Dialogue protocol (converging, in-brief)
Implementers claude:opus, claude:sonnet alternate for at most 4 rounds; converge on **mutually-acceptable**.
Round 1 drafts the artifacts and, for every open point, a `PROPOSAL:` block (options, researched recommendation, sources).
Each later round answers every open PROPOSAL with a `DECISION: <title>` line followed by the reasoning (accept / amend, with the source), refines the artifacts, and appends `<!-- CONVERGED -->` at the end of the response when nothing material remains open. The last response is final. Every `DECISION:` block is persisted by the orchestrator as an ADR (`analysis/decisions/CHK/ADR-{MOD}-{seq:03d}.md`, status RESOLVED-IN-DIALOGUE) — write the title as the decision, and cite the ids it binds on a `traces` line.

## Contracts checked by `gov.py analyze` after this stage
- **C3** inception → PRD: C3.1 exists {'artifact': 'platform-summary'} [CRITICAL]; C3.2 exists {'artifact': 'module-registry'} [CRITICAL]; C3.3 exists {'artifact': 'business-policies'} [CRITICAL]; C3.4 ids-owned {'stage': 'P0'} [CRITICAL]; C3.5 no-questions {'stage': 'P0'} [CRITICAL]; C3.6 languages {'stage': 'P0'} [MAJOR]; C3.7 registry-agree {'artifact': 'business-policies', 'registry': 'module-registry', 'kinds': ['POL']} [MAJOR]; C3.8 ears {'kind': 'POL', 'patterns': 'factory.ids.ears.patterns'} [MAJOR]; C3.9 ids-continue {'stage': 'P0'} [CRITICAL]
- **C4** PRD → SRS (human PRD approval in between): C4.1 exists {'artifact': 'prd'} [CRITICAL]; C4.2 gate-approved {'gate': 'prd-approval'} [CRITICAL]; C4.3 ids-owned {'stage': 'P0.5'} [CRITICAL]; C4.4 traces {'from': 'US', 'to': ['POL'], 'min': 1} [MAJOR]; C4.5 no-questions {'stage': 'P0.5'} [CRITICAL]; C4.6 languages {'stage': 'P0.5'} [MAJOR]; C4.7 glossary {'artifact': ['prd'], 'glossary': 'vocabulary.glossary', 'synonyms': 'vocabulary.glossary_synonyms'} [MINOR]; C4.8 ids-continue {'stage': 'P0.5'} [CRITICAL]

---
# ENGINE
# PRD — ENGINE

```
Engine        : PRD
Stage id      : P0.5
Pass          : 1
Questions     : allowed — the LAST stage that may ask; resolved in-dialogue, user confirms
Dialogue      : yes — lane analysis-dialogue (claude:opus + claude:sonnet; ≤ 4 rounds; converge on "mutually-acceptable"; output: resolved-decisions)
Inputs        : platform-summary, module-registry, business-policies
Produces      : prd-{mod}.md
Owns IDs      : US   → `{prefix}-{MOD}-{seq}` (seq width 3); each traces to POL
Gate after    : prd-approval (human-approval) — blocks P1
Next          : P1
Module        : CHK   Version: 1
Profile       : aias — Request Verification Service
```

This engine restates what the previous stage established about a module — its scope,
its policies, its priorities — as a set of clear, traceable **user stories**. A user
story is a NEED, never a RULE. If a story reads like an enforceable rule ("the system [C:C4.3]
shall reject any submission where X"), soften it back to intent ("the requester needs
to know the submission will not go through if X") and let `P1` decide the rule.

Completion (write → registry → analyze → commit → gate) is owned by the orchestrator —
see `shared/GOVERNANCE-CORE.md`. In a delta version (version > 1): read
`_state/current-{artifact}` of the previous version for every input and for this
stage's own artifact, and emit only ADDED / MODIFIED / REMOVED stories plus the [C:C12.2]
`change-manifest.md` per `shared/VERSIONING.md`.

Language policy: narrative in `en`.

---

## 1 — Inputs and entry check

```
  platform-summary  : ✓ present / ✗ MISSING
  module-registry   : ✓ present / ✗ MISSING
  business-policies : ✓ present / ✗ MISSING
  Module             : CHK
```
A missing input is a pipeline error (the orchestrator does not start this stage
without it) — not a question for the user. The domain-profile STEERING block and the
project-registry are read for vocabulary and ownership; the knowledge sources are read
for recommended answers (§4).

---

## 2 — User story record (`US` — mandatory format) [C:C4.4]

```
US-[MOD]-[SEQ]
  Title          : [short name]
  Story          : As a [role], I need [capability], so that [outcome]   — a NEED
  Priority       : HIGH / MEDIUM / LOW — only if stated or clearly implied; otherwise "—" [G]
  Success metric : only if stated; otherwise "—" [G]
  Traces         : POL-[MOD]-[SEQ] [, …]   — the policies this story serves
  Source         : [document § / quote]   — MANDATORY, no exceptions
  Status         : DRAFT → APPROVED (by the PRD approval gate)
```

```
MANDATORY SOURCING RULE
  A story with no Source is a contract violation. Do not write one "to be thorough":
  an untraceable story looks governed when it is not.
TRACES RULE
  Every story traces to at least one POL id — `gov.py analyze` refuses a [C:C4.4]
  story with none, and reports a dangling reference. A need that serves no policy is
  not a story: it is scope, and stays in the platform summary or module registry it
  came from; a policy the module lacks is P0's to add, not this stage's to bypass.
SEQUENCE RULE
  Continuous per module, never reused; a re-run continues from the highest existing [C:C4.8]
  sequence; an APPROVED story is not edited in place — amend via a new story. [G]
```

---

## 3 — Extraction rules

```
FROM the business policies
  every policy → at least one story that expresses the NEED behind it (Traces: that policy)
  stated priorities and success criteria → Priority / Success metric
FROM the module registry
  each entity owned → "the [role] needs to manage [entity]" is a legitimate seed
  lookups owned / consumed, dependencies → integration needs — only when explicit [G]
FROM the platform summary
  tier / dependency classification → cross-module needs — only if stated, not inferred [G]
FROM the domain-profile
  scope statements → what NOT to write; vocabulary → use verbatim

DO NOT EXTRACT
  validation rules, data constraints, API shapes, permission matrices — those are
  P1 outputs. If a policy already states a mechanism, restate it at NEED level.
  Stories for entities or capabilities the inputs do not mention. [G]
```

---

## 4 — Questions (allowed — for the last time in the pipeline)

```
A QUESTION exists only when the inputs leave a story's scope, priority or role [G]
genuinely ambiguous. It is not a request for a rule or a mechanism. [G]

QUESTION — [point]
  Affects       : US candidates [list] / scope
  Options       : A) … (trade-off)   B) … (trade-off)
  Researched    : [what the knowledge sources say — cited: profiles/aias/knowledge/raw-idea.md]
  Recommended   : [option] — because [rationale]
```

Lane `analysis-dialogue`: the implementers (claude:opus, claude:sonnet) converge on each QUESTION —
challenge, answer, ≤ 4 rounds, until **mutually-acceptable** — and the converged
recommendation is presented to the user, who confirms or adjusts.

```
Resolutions are recorded in the RESOLVED DECISIONS table of the PRD. No external
open-questions file. Because no later stage may ask, every question MUST be closed [C:C4.5]
before the approval gate: an item the user will not decide is written as a story
with Status DEFERRED and an explicit "out of scope for v1" note — never left open. [C:C4.5]
```

---

## 5 — Extraction report (emitted before the PRD)

```
══════════════════════════════════════════════════════════════════
PRD EXTRACTION REPORT — CHK — [date]
══════════════════════════════════════════════════════════════════
STORIES DRAFTED
  + US-CHK-001 — [one line] — Traces: [ids] — Source: [ref]
STORIES SKIPPED (no traceable source)
  — [what was considered and why it was not written]
QUESTIONS RAISED → RESOLVED IN DIALOGUE
  ? [point] → [resolution] (confirmed by user: yes/no)
POLICIES WITHOUT A STORY (must be empty)
  — [policy id] — [why]
══════════════════════════════════════════════════════════════════
```
The report precedes the file in the run output; its counts become the registry
event row (§7).

---

## 6 — Output template — `prd-{mod}.md`

```markdown
# PRD — [Module display] (CHK)
══════════════════════════════════════════════════════════════════
Module          : CHK     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : [N]   Policies covered : [N]/[N]   Deferred : [N]
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

## USER STORIES
US-CHK-001
  Title / Story / Priority / Success metric / Traces / Source / Status   (record §2)
(repeat per story, in sequence order)

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
(every policy of the module appears in at least one row; a policy with no story is a
 completeness finding)

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |

## DEFERRED
| US | Reason | Activation trigger |

## APPROVAL
Approved by : [user]   Date : [date]
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════
```

---

## 7 — Registry step content

```
project-registry : module row → "PRD v1: [N] stories"; open-question rows resolved
                   by this dialogue → RESOLVED (+ resolution); pipeline status P0.5 = DONE
event history    : "P0.5 completed: CHK v1 — [N] stories, [N] decisions"
```
(There is no separate registry artifact for this stage; the story index lives in the
PRD's traceability table.)

---

## 8 — Never produce

```
✗ any ID owned by another stage (POL, REQ, AC, ENT, RULE, DBF, XM, CON, API, QR, UXD, SCR, SCR-REQ, TC, FEAT)
✗ enforceable validation logic, field-level constraints, API shapes, permissions
✗ a story with no Source, or a story invented to "fill out" the PRD
✗ an open question left unresolved at the gate
✗ padding — produce the PRD as fast as the sourcing discipline allows; the gate is
  about the file's existence and approval, not its volume
```

---

## 9 — PRD approval gate

```
Gate `prd-approval` (type human-approval) follows this stage and blocks P1.
The user approves THIS FILE. After the user approves it:
  - no stage may raise a question — P1 and every later stage resolve
    ambiguity themselves: non-breaking → ADR + continue; breaking → ADR BLOCKED + stop
    (`factory.yaml → ambiguity`, shared/GOVERNANCE-CORE.md);
  - the approved stories are frozen for v1; a change is a new story in a
    new version (shared/VERSIONING.md).
```

---

## 10 — Self-check before emitting

- [ ] Every story has Title, Story (As a / I need / So that), Traces, Source, Status.
- [ ] Every policy of the module is traced by at least one story; the traceability table is complete.
- [ ] No story states a mechanism (rule, constraint, API, permission).
- [ ] Every question raised is in RESOLVED DECISIONS or DEFERRED with the user's explicit decision.
- [ ] Sequence numbers continuous; no APPROVED story edited in place.
- [ ] Vocabulary matches the domain-profile STEERING block verbatim.


---
# INPUTS (generated current state)

<<<INPUT: platform-summary>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: module-registry>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: business-policies>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
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
# BRIEF — stage `P1` (SRS) · module CHK · v1 · profile `aias`

Lane `analysis` · implementer ['claude:opus'] · effort high · round 1

## Rules that bind this run
- Questions: **forbidden**. A `[QUESTION]` block is refused. Ambiguity → ADR in `analysis/decisions/CHK/` (`ADR-{MOD}-{seq:03d}.md`): non-breaking → continue; breaking → status BLOCKED and stop.
- Owns IDs: REQ, AC, ENT, RULE, SCR-REQ — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-CHK-001`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared/analysis/modules/CHK/P1/srs-chk.md`
- `governance-shared/analysis/modules/CHK/P1/registry-srs-chk.md` (registry)
- Write the files directly into the project checkout (you are the operator); the orchestrator reads them on `--complete`.

## Contracts checked by `gov.py analyze` after this stage
- **C4** PRD → SRS (human PRD approval in between): C4.1 exists {'artifact': 'prd'} [CRITICAL]; C4.2 gate-approved {'gate': 'prd-approval'} [CRITICAL]; C4.3 ids-owned {'stage': 'P0.5'} [CRITICAL]; C4.4 traces {'from': 'US', 'to': ['POL'], 'min': 1} [MAJOR]; C4.5 no-questions {'stage': 'P0.5'} [CRITICAL]; C4.6 languages {'stage': 'P0.5'} [MAJOR]; C4.7 glossary {'artifact': ['prd'], 'glossary': 'vocabulary.glossary', 'synonyms': 'vocabulary.glossary_synonyms'} [MINOR]; C4.8 ids-continue {'stage': 'P0.5'} [CRITICAL]
- **C5** SRS → database: C5.1 exists {'artifact': 'srs'} [CRITICAL]; C5.2 ears {'kind': 'REQ', 'patterns': 'factory.ids.ears.patterns'} [CRITICAL]; C5.3 traces {'from': 'REQ', 'to': ['US'], 'min': 1} [MAJOR]; C5.4 orphans {'kind': 'REQ', 'referenced_by': ['AC'], 'min': 1} [CRITICAL]; C5.5 traces {'from': 'AC', 'to': ['REQ'], 'min': 1} [MAJOR]; C5.6 traces {'from': 'RULE', 'to': ['REQ'], 'min': 1} [MAJOR]; C5.7 ids-owned {'stage': 'P1'} [CRITICAL]; C5.8 registry-agree {'artifact': 'srs', 'registry': 'registry-srs', 'kinds': ['REQ', 'AC', 'ENT', 'RULE']} [MAJOR]; C5.9 no-questions {'stage': 'P1'} [CRITICAL]; C5.10 languages {'stage': 'P1'} [MAJOR]; C5.11 ids-continue {'stage': 'P1'} [CRITICAL]; C5.12 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED'} [CRITICAL]; C5.13 ambiguity {'artifact': 'srs', 'kinds': ['REQ', 'AC', 'RULE'], 'lines': ['Statement', 'Given', 'When', 'Then'], 'lexicon': 'analyze.maturity.ambiguity_lexicon', 'extend': 'review.ambiguity_lexicon'} [MINOR]; C5.14 ac-measurable {'artifact': 'srs', 'kind': 'AC', 'label': 'Then', 'spec': 'analyze.maturity.measurable'} [MINOR]; C5.15 crud-covered {'artifact': 'srs', 'entity': 'ENT', 'kind': 'REQ', 'statement': 'Statement', 'spec': 'analyze.maturity.crud'} [MINOR]; C5.16 feature-unwanted {'artifact': 'srs', 'group': 'US', 'kind': 'REQ', 'statement': 'Statement', 'pattern': 'unwanted'} [MINOR]; C5.17 glossary {'artifact': ['srs'], 'glossary': 'vocabulary.glossary', 'synonyms': 'vocabulary.glossary_synonyms'} [MINOR]
- **C6** SRS + database → backend execution plan: C6.1 exists {'artifact': 'db-script'} [CRITICAL]; C6.2 traces {'from': 'DBF', 'to': ['REQ', 'ENT'], 'min': 1} [MAJOR]; C6.3 traces {'from': 'XM', 'to': ['REQ'], 'min': 1} [MAJOR]; C6.4 ids-owned {'stage': 'P2'} [CRITICAL]; C6.5 registry-agree {'artifact': 'db-script', 'registry': 'registry-db', 'kinds': ['DBF', 'XM']} [MAJOR]; C6.6 orphans {'kind': 'ENT', 'referenced_by': ['DBF'], 'min': 1} [MAJOR]; C6.7 no-questions {'stage': 'P2'} [CRITICAL]; C6.8 ids-continue {'stage': 'P2'} [CRITICAL]; C6.9 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED', 'bound_in': 'db-script'} [CRITICAL]; C6.10 xm-record-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.11 xm-tier-rule {} [CRITICAL]; C6.12 xm-transition-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.13 graph-acyclic {} [CRITICAL]; C6.14 graph-target-known {} [CRITICAL]; C6.15 db-column-only {'artifact': 'db-script', 'register': ['db-script', 'registry-db']} [MAJOR]

## Blocks this stage emits — each a ```yaml name={name} fence, held to its schema by C0.1 (`gov.py analyze`)
- `module-dependencies` in `srs` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"module-dependencies","description":"P1 SRS §A8: the entities this module consumes from other modules (factory.yaml → xm.graph.module_block); `consumes: []` when it consumes nothing","type":"object","required":["consumes"],"additionalProperties":false,"properties":{"consumes":{"type":"array","items":{"type":"object","required":["module","entity","type"],"additionalProperties":false,"properties":{"module":{"type":"string","pattern":"^[A-Z][A-Z0-9]*$"},"entity":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"type":{"type":"string"}}}}}}
  ```
- `lookups` in `srs` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"lookups","description":"P1 SRS §A6: every lookup key the module owns — the values it seeds, whether the set is open (host data may add values), every value the analysis names for it, and the entity fields bound to it","type":"object","required":["lookups"],"additionalProperties":false,"properties":{"lookups":{"type":"array","items":{"type":"object","required":["key","seeded","open"],"additionalProperties":false,"properties":{"key":{"type":"string","pattern":"^[A-Z][A-Z0-9]*(?:_[A-Z0-9]+)*$"},"seeded":{"type":"array","items":{"type":"string"}},"open":{"type":"boolean"},"values":{"type":"array","items":{"type":"string"}},"fields":{"type":"array","items":{"type":"string"}},"owner":{"type":"string","pattern":"^[A-Z][A-Z0-9]*$"},"entity":{"type":"string"},"control":{"type":"string"},"source":{"type":"string"}}}}}}
  ```

---
# ENGINE
# SRS — ENGINE

```
Engine        : SRS
Stage id      : P1
Pass          : 1
Questions     : forbidden — ambiguity is self-resolved (§9)
Lane          : analysis
Inputs        : prd, domain-profile, project-registry   (PRD must be APPROVED — gate prd-approval)
Produces      : srs-{mod}.md · registry-srs-{mod}.md (registry)
Owns IDs      : REQ, AC, ENT, RULE, SCR-REQ   → `{prefix}-{MOD}-{seq}` (seq width 3)
Format        : EARS for every functional requirement (§4)
Next          : P1.5
Module        : CHK   Version: 1
Profile       : aias — Request Verification Service
```

This engine produces the module's **functional truth**: the SRS. Everything downstream
(database, execution plans, UX, tests) derives from it; when a downstream artifact
disagrees with the SRS, the SRS governs and the other artifact is corrected. The SRS
never contains DDL, execution phases, component names or any ID owned by a later stage. [C:C5.7]

Completion (write → registry → analyze → commit) is owned by the orchestrator — see
`shared/GOVERNANCE-CORE.md`. In a delta version (version > 1): read
`_state/current-{artifact}` of the previous version for every input and for this
stage's own artifacts, and emit only ADDED / MODIFIED / REMOVED elements plus the [C:C12.2]
`change-manifest.md` per `shared/VERSIONING.md`. ID sequences continue from the current state.

Language policy: narrative in `en`; identifiers, IDs and field names in
the domain-profile's identifier language.

### IDs this stage assigns

| Atom | Meaning | Traces to | Requires |
|---|---|---|---|
| `REQ` | requirement (EARS) | US | AC |
| `AC` | acceptance criterion (Given/When/Then) | REQ | — |
| `ENT` | entity | — | — |
| `RULE` | business rule | REQ | — |
| `SCR-REQ` | screen requirement | REQ | — |

Sequences are continuous per module and per atom, never reused. [C:C5.11]

---

## 1 — Inputs and reading protocol

```
STEP A — PRD (approved): every P0.5 story with its Traces → the demand this SRS must cover
STEP B — domain-profile.md §7 STEERING: vocabulary (verbatim), bounded contexts, codes,
         identifier rules, knowledge sources; §8 resolved decisions — not re-opened [G]
STEP C — project-registry.md (shared/REGISTRY-SCHEMA.md categories):
         entity ownership + shared declarations → reuse, do not re-create [G]
         dependency graph (platform/dependency-graph.json) → edges to carry into A8
         structural registry                      → names already fixed by other modules
         open-question index                      → must be empty for this module
STEP D — this stage's upstream module artifacts (module registry, business policies):
         entities owned / lookups / dependencies → names as given
         policies                                → the rules and requirements they imply
STEP E — knowledge sources (cite when applying a default)
         - profiles/aias/knowledge/raw-idea.md
```

### 1.1 Registry pre-check (before any generation)

```
Does this entity already exist (any module)?      → reuse its ID; do not re-create
Does an equivalent lookup exist?                    → reuse its key
Does the module exist in the registry?              → extend, continue sequences
Naming conflict with a registered element?          → §9: ADR (non-breaking) or ADR BLOCKED
A closed decision / resolved open item affects it?  → apply as-is, no deviation
```

### 1.2 Feature type (auto-determined, documented, not asked)

```
Entity kinds (profile.vocabulary.entity_kinds): config / transactional
For every entity: kind + reason (one line), recorded in A3. The user is not asked.
```

---

## 2 — Resolution order (zero questions)

Before writing any section, answer every needed fact from, in order:

```
1. business policies (P0)   → apply directly; cite the policy id
2. PRD stories (P0.5)            → the need; scope and priority
3. registries (project + module)         → ownership, names, dependencies
4. knowledge sources (§1 STEP E)         → domain defaults — apply and document as DEFAULT
5. domain-profile STEERING + rules       → vocabulary, constraints
6. domain best practice                  → choose, document as ADR (§9)

DEFAULT documentation (inline, where applied):
  DEFAULT : [what was decided]
  Source  : [knowledge file § / domain-profile §]
  Override: [what to change if the client wants otherwise]
```

Nothing is asked. A fact that 1–6 cannot settle is an ambiguity → §9.

---

## 3 — Entities (`ENT`)

### 3.1 Ownership classification — first step, before any ID

| Classification | Rule | Declaration |
|---|---|---|
| PRIVATE | fully owned by this module | `ENT-CHK-[SEQ]` — [name] — PRIVATE |
| SHARED (owner) | other modules consume read-only | `ENT-CHK-[SEQ]` — [name] — SHARED (owner) |
| SHARED (consumer) | mastered by another module — NO new ID | consumes `ENT-[OWNER]-[SEQ]` — HARD-FK / SOFT-READ → XM candidate for P2 |

Entities already registered by another module are consumed, not re-created. [G]

### 3.2 Defaults per entity kind (profile.conventions.entity_defaults)

Every entity of a kind below carries these fields automatically (in A3 they are
listed once under "standard fields — per profile", not re-typed per entity):

| Kind | Default fields |
|---|---|
| transactional | createdAt, updatedAt |

Naming (profile.stack.db.naming): primary key `{entity}Id`; audit fields `createdAt, updatedAt` are system-filled and not accepted from a client. Field names are [G]
taken from the module registry, the policies and the knowledge sources — not invented [G]
from generic templates. Physical types belong to P2; the SRS states the
logical type only (text / number / decimal / flag / date-time / lookup / reference). [G]

### 3.3 Structural rules

```
ARCH-1  Reuse before create — registry first.
ARCH-2  Master data has ONE owner; others consume via XM (not duplicated). [G]
ARCH-3  Every referenced entity is defined (own ID) or consumed (owner's ID); no
        undefined reference anywhere.
ARCH-4  Names match the registry exactly. [G]
ARCH-5  A SHARED (owner) entity that can be deactivated/deleted → a RULE stating the
        effect on SOFT-READ consumers (prevent / notify / cascade), traced to the REQ
        that introduces the operation. Undecidable → ADR (§9), never an open question. [T:questions-forbidden]
IDENTIFIERS Profile rule: Every table of the service's own schema keys on NUMBER(19) GENERATED BY DEFAULT AS IDENTITY. Host-system identifiers (service request number, employee identity) are stored as strings exactly as the host sent them and are never foreign keys — the host data lives outside this schema.
        Every entity's identifier field states this type in A3; no other identifier type is invented. [G]
LOOKUPS Profile rule: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.
        Lookup-backed fields reference a lookup key declared in A6; the SRS decides
        control type (fixed short list → lookup; growing/large set → reference entity
        with its own ENT). Same field → same lookup key in every screen.
WORKFLOW Profile: workflow engine `forbidden`. Status lifecycles (a status field +
        allowed transitions) are documented (A7). A module-specific approval flow [G]
        is written only when a story explicitly asks for it and is custom to the module — [G]
        not a generic engine. [G]
```

---

## 4 — Requirements (`REQ`) — EARS is mandatory [C:C5.2]

Every functional requirement is **exactly one** EARS pattern [C:C5.2]
(`factory.ids.ears.patterns`); free prose is a contract violation (`gov.py analyze`:
CRITICAL).

```
  ubiquitous  The system shall <response>
  state       While <condition / trigger / feature>, the system shall <response>
  event       When <condition / trigger / feature>, the system shall <response>
  optional    Where <condition / trigger / feature>, the system shall <response>
  unwanted    If <condition / trigger / feature>, then the system shall <response>
  complex     a legitimate composition of the above (e.g. While … , when … , the system shall …)
```

```
REQ-CHK-[SEQ] — [short name]
  Pattern    : [ubiquitous | state | event | optional | unwanted | complex]
  Statement  : [one EARS sentence — one behaviour, one subject "the system"]
  Traces     : P0.5-CHK-[SEQ] [, …]      (≥ 1, mandatory) [C:C5.3]
  Entities   : ENT-CHK-[SEQ] [, …]
  Rationale  : [one line — why]
  Source     : [PRD story / policy / knowledge file / ADR]
  Priority   : [from the story]
```

Rules: singular (one requirement per REQ — "and" between behaviours means two REQs);
verifiable (a test can pass/fail it); no design (no table, endpoint, component);
every P0.5 story is covered by ≥ 1 REQ; every REQ traces to ≥ 1 story
(a REQ with no story = invented scope → remove or raise an ADR).

### 4.1 Acceptance criteria (`AC`) — ≥ 1 per REQ

```
#### AC-CHK-<seq> — [REQ-CHK-<seq>]        ← the square brackets are LITERAL: they are the trace to the REQ
  Given  : <precondition / state>
  When   : <action / event>
  Then   : <observable outcome — with the exact message when one is shown>
```
(`<…>` is a placeholder; `[…]` is not — `gov.py analyze` C5.5 reads the REQ id inside the brackets on the heading, and an AC without them traces to nothing. Runs 8 and 9 of the golden wrote `— REQ-…` bare and lost every trace.)

Rules: each AC tests one path of one REQ (happy path first, then each unwanted/edge
path); an AC that cannot be phrased Given/When/Then means the REQ is not verifiable —
rewrite the REQ. ACs are the mechanical source of test cases for the test stage
(`P4` — `TC` traces to `AC`).

---

## 5 — Business rules (`RULE`)

```
RULE-CHK-[SEQ] — [short name]
  Scope      : ENT-CHK-[SEQ]
  Trigger    : [when evaluated — on create / update / submit / transition …]
  Statement  : The system shall [prevent / require / validate …] when [condition]
  Message    : [text]   (business language, not a literal translation)
  Traces     : REQ-CHK-[SEQ] [, …]                     (≥ 1, mandatory) [C:C5.6]
  Data source: ENT-CHK-[SEQ].[field] [, …]             (mandatory — see below) [C:C5.12]
  Source     : [policy id / story / DEFAULT / ADR]
  Test-Hint  : [optional, one line of business intent for the test stage; omit if obvious]
```

Rules: a RULE formalises a constraint that a REQ needs; it never introduces behaviour [C:C5.6]
absent from every REQ. Database errors do not reach users — every constraint that can [G]
fail has a RULE with a message. Rules are defined once (A5) and referenced by ID from
every screen block.

**`Data source` — where the data the check READS comes from (mandatory).** `Scope` says [C:C5.12]
which entities the rule *guards*; `Data source` says which declared fields the rule
*reads to decide*. Every field named here is an `ENT-CHK-[SEQ].[field]` that A3
actually declares (the next stage binds each one to a column, so a field A3 never [C:C6.9]
declares can never be read at runtime). [C:C6.9]

A rule whose statement leans on data this module does not declare — "a
{module|admin|externally}-declared X", "a configured Y", "a registered pair" — has two honest
outcomes, and no third:

```
  Data source: ENT-CHK-[SEQ].[field]        ← the declaration surface exists: name it,
                                                    and A3 carries the field / A6 the lookup
  Data source: DEFERRED — no declaration surface in this version ([what would be needed])
```

A `DEFERRED` rule is still written, still traced and still counted, but it is marked
unenforceable *here* — the next stages do not mint an error code, an enforcement query or
an enforcing endpoint for it, and the test stage does not derive a test case whose precondition
no API of this version can establish. Emitting such a rule as if it were enforceable
produces a guard that compiles, an error code that is registered, and a check that can
never fire. `gov.py analyze` resolves this mechanically (`data-source`): a rule with [C:C5.12]
neither a resolvable `Data source` nor the deferral marker is a finding, not a pass.

---

## 6 — Lookups and status lifecycle

```
A6 LOOKUPS — the `lookups` block (data, not prose — `gov.py analyze` reads it: tc-data, tc-consistency, bootstrap-complete; C0.1 holds it to its schema)
  one row per lookup key this module OWNS: key · seeded (the values the product seeds) · open
  (true when host data may add values — "added as data") · values (EVERY value the analysis
  names for the key, seeded or not) · fields (the entity fields bound to it) · entity · control · source
  Consumed lookups are listed by key + owner only (not redefined). [G]
  The labels per language and the rationale stay in the prose beside the block.

A7 STATUS LIFECYCLE — for every entity with a status field and > 2 transitions
  Diagram of states and allowed transitions ONLY — no roles, no approval steps.
  Each transition that carries a constraint → RULE id.
  Module-specific approval flow (only if a story explicitly asks and the profile allows): [G]
  documented as its own block with the story it traces to.
```

---

## 7 — Screens for `P3.2` (`SCR-REQ`)

This stage lists what screens the module needs — functional scope, not design.
`P3.2` turns each entry into `SCR` / `UXD` decisions and owns pattern,
container, layout and component choices. This stage never decides those. [C:C5.7]

```
SCR-REQ-CHK-[SEQ] — [screen name]
  Purpose      : [what the user achieves]
  Entities     : ENT-CHK-[SEQ] [, …]
  Operations   : [search / list / create / read / update / deactivate / custom …]
  Users        : [roles]
  Navigation   : [module] → [menu] → [screen]; from: [screens]; to: [screens]
  Content shape: [flat record | header + repeating lines with totals | true hierarchy
                  (parent/child) | other (journal, calendar …)] — a hint, not a design
  Traces       : REQ-CHK-[SEQ] [, …]
```

Screen rules: search filters correspond to result columns; the same field uses the same
lookup key in search and entry; every screen declares its navigation position; every
operation on a screen is backed by a REQ.



---

## 8 — API expectations (stack-neutral)

The SRS states what operations the backend must expose; `P3.1` assigns the
`API` ids and designs them. Expectations follow the profile's conventions
(profile.stack.backend.api) and are referenced from screens by REQ, not by an API id. [G]

```
Base path      : /api/v1/{resource}
Verbs          : POST = create (start a check, upload documents, record a decision) · GET = read
Errors         : ProblemDetail (RFC 9457) → {type, title, status, detail, code}

| Operation | Verb | Path (per base path) | Inputs | Outputs | RULEs | Traces (REQ) |
|---|---|---|---|---|---|---|
| create [entity] | [verb] | [base path with module/resource] | [fields] | [entity] | RULE-… | REQ-… |
| search [entity] | [verb] | … | [filters, paging] | [page of entity] | — | REQ-… |
| update [entity] | [verb] | …/{id} | [fields] | [entity] | RULE-… (immutability of number/code if any; uniqueness of names) | REQ-… |
| deactivate [entity] | [verb] | …/{id} | id | confirmation | RULE-… | REQ-… |
| read [entity] | [verb] | …/{id} | id | [entity] | — | REQ-… |
```
Lookup values are runtime-loaded (not enumerated in an API definition). Mobile or [G]
channel-specific behaviour is stated as a REQ (`Where` pattern), not as widget design.

---

## 9 — Ambiguity rule (questions are forbidden here) [T:questions-forbidden]

```
When steps 1–6 of §2 leave a genuine choice:
  NON-BREAKING (does not contradict an approved story, a policy, a registry fact or
  a closed decision) → write an ADR and CONTINUE with the chosen best practice.
      action: adr · then: continue
  BREAKING (contradicts an approved story / policy / registry fact / closed decision,
  or would change a REQ another artifact already relies on) → write the ADR with
  status BLOCKED and STOP the pass; the orchestrator surfaces it at the next
  human point.
      action: adr · status: BLOCKED · then: stop

ADR file : analysis/decisions/CHK/ADR-{MOD}-{seq:03d}.md
Content  : Context · Decision · Consequences · traces (REQ / ENT / story ids) · status
Every ADR is referenced from the SRS "Decisions applied" section (§10 STANDALONE).
Details: shared/GOVERNANCE-CORE.md.
```

---

## 10 — `srs-{mod}.md` — canonical template

Structure: **PART A** (module foundation — defined once) → **PART B** (one block per
screen requirement — references PART A by ID, does not redefine) → **STANDALONE**. [G]
No section is omitted; a section that does not apply says so in one line.

```markdown
# SRS — [Module display] (CHK)
══════════════════════════════════════════════════════════════════
Module : CHK   Version : v1   Profile : aias
Inputs : prd, domain-profile, project-registry (PRD approved [date])
Counts : REQ [N] · AC [N] · ENT [N] · RULE [N] · SCR-REQ [N] · ADR [N]
══════════════════════════════════════════════════════════════════

# PART A — MODULE FOUNDATION

## A1 — Document information
| Item | Value |   (module, feature code, version, date, status, prepared by, decisions applied count)

## A2 — Functional context
In scope · Out of scope · Module function (one paragraph) · Detailed description
(workflow narrative, roles) · Current situation (steps / party / notes) ·
Current difficulties · Proposed system and benefits · General notes (constraints,
deferred items) — delete a sub-section only if it has no content and say so. [G]

## A3 — Entities and fields
Standard fields per kind (profile) — listed once here.
### ENT-CHK-001 — [name]
| Kind | Ownership | Business number (yes/no — per §3.3 test) | Operations | Cross-module | Source |
| Field | Logical type | Required | Values / source (lookup key, ENT ref) | Notes | Label |
(repeat per entity)

## A4 — Functional requirements (EARS) and acceptance criteria
### REQ-CHK-001 — [name]        (record §4)
#### AC-CHK-001 … (record §4.1, ≥ 1 per REQ)
(repeat per requirement)

## A5 — Business rules
### RULE-CHK-001 — [name]       (record §5)
(repeat per rule — the ONLY place rule text appears)

## A6 — Lookups                      (§6 — the ONLY place lookup values appear)
```yaml name=lookups
lookups:
  - {key: [KEY], seeded: [CODE_A, CODE_B], open: false, values: [CODE_A, CODE_B], fields: [fieldName], entity: ENT-CHK-[SEQ], control: lookup, source: "[policy / default]"}
```

## A7 — Status lifecycle             (§6 — diagram only; "not applicable" if ≤ 2 states) [G]

## A8 — Module dependencies
```yaml name=module-dependencies
consumes:            # data, not prose — `gov.py graph` reads this named block; XM ids are assigned by P2
  - {module: [OWNER], entity: ENT-[OWNER]-[SEQ], type: HARD-FK | SOFT-READ}
```
(`consumes: []` when this module consumes nothing — the block is always present; C0.1 refuses an SRS without it) [C:C0.1]
| External service | Purpose | Integration kind |

# PART B — SCREEN REQUIREMENTS   (one block per SCR-REQ; references PART A by ID only) [G]

## SCR-REQ-CHK-001 — [name]
### B1 — Definition        (record §7: purpose, entities, operations, users, navigation, content shape, traces)
### B2 — Search / list     (filters = result columns; lookup keys by reference; RULEs applied by id) — "not applicable" if no search
### B3 — Input             (fields by ENT reference; buttons/actions → operation + RULE ids)
### B4 — Access            (roles per operation)
### B5 — API expectations  (table §8, scoped to this screen)
(repeat per screen requirement)

# STANDALONE

## Traceability matrix
| P0.5 | REQ | AC | RULE | ENT | SCR-REQ |
(every story → ≥ 1 REQ; every REQ → ≥ 1 AC; every RULE → REQ; every SCR-REQ → REQ.
 Orphans and dangling references are gate failures — `gov.py analyze`.)

## Decisions applied
| DEFAULT / ADR | What | Source | Override / status |

## Access summary            (roles × screens aggregate)
══════════════════════════════════════════════════════════════════
```

Single-source rule: PART A defines; PART B references by ID ("applies RULE-CHK-003").
Restating rule, lookup or entity text in PART B is a DUPLICATE finding (MAJOR).

---

## 11 — `registry-srs-{mod}.md` — registry content

```
## REGISTRY — P1 — CHK v1
Entities      : ENT id · name · kind · PRIVATE / SHARED(owner) · status REGISTERED
Consumed      : the ids of the A8 `module-dependencies` block   (→ gov.py graph)
Lookups owned : key · ENT · values count          Lookups consumed : key · owner
Screens       : SCR-REQ id · name · page code (if any)
Requirements  : REQ count · AC count · RULE count · last sequence per atom
                (REQ: [n], AC: [n], ENT: [n], RULE: [n], SCR-REQ: [n])
Decisions     : ADR ids (+ BLOCKED, if any)
Event         : "P1 completed: CHK v1 — [counts]"
```
The orchestrator merges these rows into `project-registry.md` (entity ownership,
shared declarations, structural registry, pipeline status, events); dependencies reach the graph through §A8, not through the registry. [G]

---

## 12 — Boundaries

```
OWNS      : REQ, AC, ENT, RULE, SCR-REQ · functional truth · the traceability matrix from stories down
DOES NOT  : POL (P0) · US (P0.5) · DBF (P2) · XM (P2) · CON (P1.5) · API (P3.1) · QR (P3.1) · UXD (P3.2) · SCR (P3.2) · TC (P4) · FEAT (feature)
            · DDL / physical types · execution phases · UX patterns, containers, components
            · endpoint design · permission seed data · test cases
```

---

## 13 — Self-check before emitting (ISO/IEC/IEEE 29148 attributes + structure)

Quality attributes scored at the pass gate (`factory.review.rubric`):
- [ ] Every RULE carries a `Data source` that either names `ENT-CHK-[SEQ].[field]` values A3 declares, or is the explicit `DEFERRED — no declaration surface in this version` marker (§5).
 [C:C5.13]- [ ] **unambiguous** — one reading per REQ / AC / RULE; no "etc.", "as appropriate", "fast"
- [ ] **verifiable** — every REQ has ≥ 1 Given/When/Then AC; every RULE has a message
- [ ] **complete** — every story covered; A1–A8, every B1–B5, STANDALONE present; no placeholder left
- [ ] **consistent** — vocabulary = STEERING block; names = registry; no REQ contradicts a policy or another REQ
- [ ] **singular** — one behaviour per REQ; one path per AC
- [ ] **feasible** — no requirement depends on an undefined entity, unavailable module or forbidden mechanism
- [ ] **traceable** — REQ→P0.5, AC→REQ, RULE→REQ, SCR-REQ→REQ all present; no orphan, no dangling id

Structural checks:
- [ ] Every REQ statement matches exactly one EARS pattern. [C:C5.2]
- [ ] Every entity has a kind from `config, transactional` and carries its kind's default fields.
- [ ] Every consumed entity references the owner's ENT id; none re-created.
- [ ] Rules, lookups, entities defined in PART A only; PART B references by ID. [G]
- [ ] Every DEFAULT has Source + Override; every ADR is listed under Decisions applied; no BLOCKED ADR unless the pass stopped.
- [ ] No question raised anywhere; no open-questions section exists.
- [ ] Sequences continuous per atom.
- [ ] Profile check `AIAS-1` (CRITICAL): every guardrail of raw-idea §12 is a REQ with at least one AC, and no §13 Decided point (as amended in §15) is reopened.
- [ ] Profile check `AIAS-2` (MAJOR): no requirement introduces multi-tenancy, conversation memory, RAG/vector store, multi-agent orchestration, a full administration UI, or caller authentication (deferred, raw-idea A2).


---
# INPUTS (generated current state)

<<<INPUT: prd>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: domain-profile>>>
# DOMAIN PROFILE — Request Verification Service
══════════════════════════════════════════════════════════════════
Profile         : aias (Request Verification Service)
Version         : 1
Last Updated    : 2026-10-01
Status          : FRESH
Research        : 0 web sources cited — the research capability was unavailable this run (block 9); every claim cites the owner's raw idea [KB:raw-idea.md] or an owner confirmation
══════════════════════════════════════════════════════════════════

## 1. SCOPE

In bounds [KB:raw-idea.md §2, §15]:
- A standalone Spring Boot service that host systems call over REST to verify one government service request before an employee approves it.
- Per-service configuration (a service package: service knowledge + service definition), maintained by the service administrator. Employees never supply service knowledge.
- Read-only access to host request data through an MCP server (Oracle).
- Document retrieval in three fetch modes — `path`, `blob`, `manual` — and reading PDF, XLS and image documents (other extensions may appear).
- A structured, evidence-backed verification report, stored in the service's own database.
- A web frontend for the employee, embedded in the host screen: the checks of a request, the report, manual document upload, recording the decision (amendment A1).
- Optional execution of a host approval API, per service, triggered only by the employee.

Out of bounds for this version [KB:raw-idea.md §2, §15]:
- Multi-tenancy; conversation memory; RAG and vector stores; multi-agent orchestration.
- A full administration UI and an internal permission system.
- Caller authentication (API key or mTLS) and the security phases — deferred to a later version; the owner already has the solution (amendment A2). The §12 guardrails are NOT deferred.

## 2. PURPOSE

A government employee approving a service request today checks the request's data and attached documents against the service's conditions by hand. This service does that check first and returns a short report — compliant or not, and where the problems are, with the evidence for each — so the employee decides faster and on verified facts. The employee stays the decision maker; the service informs the decision and never makes it [KB:raw-idea.md §1].

The service is generic: every service it verifies is configuration, not code. It supersedes the earlier "Reusable Agentic AI Library" idea, which was broader than the first real need [KB:raw-idea.md §1].

## 3. RESPONSIBILITIES

- Hold the registry of service packages and their versions, and the connections set per environment at activation.
- Run one check per request as a fixed, asynchronous pipeline: load the package → run its queries → fetch and read its documents → run the deterministic checks in code → let the LLM compare data and document content with the service knowledge → store the report [KB:raw-idea.md §5].
- Fetch documents by `path`, `blob` or `manual` upload and read them by type (text extraction, table extraction, OCR or a vision model).
- Store every report — overall status, one finding per condition with its evidence, the documents read / missing / unreadable, and the metadata (service version, fetch mode, model, time, employee) — and the employee's decision beside it [KB:raw-idea.md §7, §9].
- Expose the REST API to hosts and the employee frontend; call a host approval API only after the employee confirms, where the service enables it [KB:raw-idea.md §8, §11].
- Enforce the guardrails of [KB:raw-idea.md §12] on every check.

## 4. MAIN COMPONENTS

| # | Component | Module code | Bounded context | Category (user-defined) | Core / extension | Notes |
|---|-----------|-------------|-----------------|--------------------------|------------------|-------|
| 1 | Service Registry | REG | configuration | Foundation | Core | Service packages (service knowledge + service definition), versions, connections [KB:raw-idea.md §4, §14] |
| 2 | Check Engine | CHK | verification | Business | Core | The fixed pipeline, deterministic checks, LLM comparison — the only fixed logic [KB:raw-idea.md §5, §14] |
| 3 | Document Access | DOC | verification | Business | Core | `path`, `blob`, `manual` fetching; reading PDF, XLS, images [KB:raw-idea.md §6, §14] |
| 4 | Report Store | RPT | record | Business | Core | Runs, findings, documents, employee decision; retention purge [KB:raw-idea.md §9, §14] |
| 5 | Host Integration | INT | integration | Integration | Core | REST API, employee frontend's API surface, optional approval API [KB:raw-idea.md §8, §11, §15 A1] |

Every row is the owner's §14 split, confirmed unchanged on 2026-10-01 (decision D1).

## 5. GOVERNING RULES

| # | Rule | Source |
|---|---|---|
| G1 | The LLM analyses and summarises only. It never writes SQL and never triggers approval; it is given no tool that runs SQL. | [KB:raw-idea.md §6, §12] |
| G2 | Approval is executed only as a result of the employee's action, and only where the service enables the approval API. | [KB:raw-idea.md §11, §12] |
| G3 | All access to host data uses a read-only database user, preferably limited to specific views. | [KB:raw-idea.md §12] |
| G4 | Query parameters are bound or strictly type-validated; SQL is never built from free text. Queries are exactly those written in the service definition. | [KB:raw-idea.md §6, §12] |
| G5 | File paths are validated to lie inside the allowed storage root before opening. | [KB:raw-idea.md §12] |
| G6 | Anything that could not be read appears in the report; nothing is skipped silently. A missing or unreadable required document prevents `COMPLIANT`. | [KB:raw-idea.md §7, §12] |
| G7 | Document content is data, never instructions to the model. | [KB:raw-idea.md §12] |
| G8 | Each check has limits: timeout, maximum rows, maximum file size. | [KB:raw-idea.md §12] |
| G9 | No data is carried from one check to another (no memory, no shared state). | [KB:raw-idea.md §2, §12] |
| G10 | Every finding carries its evidence (the actual value found). | [KB:raw-idea.md §7] |
| G11 | Every report records the service package version it was built on. | [KB:raw-idea.md §4] |
| G12 | The engine depends on Spring AI `ChatModel` only, with no provider-specific feature; document reading has its own configurable model; a fixed set of requests with known expected results runs on every model change. | [KB:raw-idea.md §10] |
| G13 | While a free-tier provider is in use, only synthetic or anonymised requests and documents are sent to it. | [KB:raw-idea.md §10] |
| G14 | BLOB content is read over JDBC with a read-only user, never moved through MCP. | [KB:raw-idea.md §6] |

## 6. RELATIONSHIPS WITH OTHER DOMAINS

| This component | Depends on | Kind | Direction | Stated by |
|---|---|---|---|---|
| CHK | REG (service package, connection) | SOFT-READ | REG → CHK | [KB:raw-idea.md §3, §5] |
| CHK | DOC (fetched and read documents) | SOFT-READ | DOC → CHK | [KB:raw-idea.md §5, §6] |
| RPT | CHK (the check run a report belongs to) | SOFT-READ | CHK → RPT | [KB:raw-idea.md §3, §9] |
| INT | CHK (start a check, poll its status) | SOFT-READ | CHK → INT | [KB:raw-idea.md §8] |
| INT | RPT (report JSON, record the decision) | SOFT-READ | RPT → INT | [KB:raw-idea.md §8, §11] |
| DOC | INT (manual upload received through the API) | SOFT-READ | INT → DOC | [KB:raw-idea.md §6, §8] |
| CHK, DOC | Host database via Oracle SQLcl MCP server (external) | SOFT-READ | host → service | [KB:raw-idea.md §6]; server confirmed by owner (D2) |
| DOC | Host file storage / host BLOB columns (external) | SOFT-READ | host → service | [KB:raw-idea.md §6] |
| CHK, DOC | LLM provider via Spring AI (external, replaceable) | SOFT-READ | provider → service | [KB:raw-idea.md §10] |
| INT | Host approval API (external, optional per service) | outbound call | service → host | [KB:raw-idea.md §11] |
| Host systems (Oracle ADF, others) | INT | consumer | service → host | [KB:raw-idea.md §11] |

All modules run in one deployable and reach each other through injected interfaces (profile `conventions.module_interface: in_process`). The host data lives outside the service's schema, so no host identifier is ever a foreign key.

## 7. STEERING  (read verbatim by every later stage)

### 7.1 Ubiquitous language

| Term | Definition | Do not say | Module code |
|---|---|---|---|
| Check | One independent verification run of one request against one service package; nothing is carried from one check to another. | job, audit, scan | CHK |
| Service Package | The per-service configuration — service knowledge plus service definition — maintained by the service administrator and versioned; adding a service needs no code. | plugin, template, tenant | REG |
| Service Knowledge | The natural-language conditions and rules of a service, read whole by the LLM (no retrieval). | prompt, rules file | REG |
| Service Definition | The structured part of a service package — queries, required documents, fetch mode, optional approval API — executed literally by the engine, never interpreted by the LLM. | — | REG |
| Connection | A named data source defined once outside the service packages and set at activation time per environment. | — | REG |
| Fetch Mode | How a service's documents are obtained: `path`, `blob` or `manual`. | — | DOC |
| Finding | The outcome of one condition in a report — satisfied or not, its evidence and a note for the employee. | issue, violation | RPT |
| Evidence | The actual value found that a finding is based on, shown so the employee can verify it. | — | RPT |
| Overall Status | The report's verdict: `COMPLIANT`, `NOT_COMPLIANT` or `NEEDS_MANUAL_REVIEW`. | — | RPT |
| Host System | The calling application (Oracle ADF or another government system) that starts checks and embeds the employee frontend. | client app | INT |
| Employee Decision | The approve / reject decision the employee takes, recorded beside the report result. | verdict | RPT |
| Approval API | An optional, per-service host endpoint the service calls only after the employee confirms a decision. | — | INT |
| Service Administrator | The person who maintains service packages and connections. | — | REG |
| Employee | The host-system user who reviews the report and takes the decision; identified by the host, recorded with the check. | — | INT |

### 7.2 Bounded contexts

| Context | Owns module codes | Boundary statement |
|---|---|---|
| configuration | REG | What a service IS: its packages, versions and connections. Read by every check, written only by the administrator. |
| verification | CHK, DOC | Running one check: the pipeline and the documents it fetches and reads. Holds no state between checks. |
| record | RPT | What a check FOUND and what the employee DECIDED, kept for the retention period. |
| integration | INT | Everything a host or the employee frontend touches: the REST API and the outbound approval call. |

### 7.3 Module prefixes proposal

| Code | Display | Status |
|---|---|---|
| REG | Service Registry | IN PROFILE |
| CHK | Check Engine | IN PROFILE |
| DOC | Document Access | IN PROFILE |
| RPT | Report Store | IN PROFILE |
| INT | Host Integration | IN PROFILE |

### 7.4 Identifier rules

Later stages build IDs as `{prefix}-{MOD}-{seq}` (seq width 3) with the module codes above. Entity kinds: `config, transactional`. The profile adds no domain-specific atom.

### 7.5 Knowledge sources to cite

- `profiles/aias/knowledge/raw-idea.md` (as amended in its §15, A1–A2)

## 8. RESOLVED DECISIONS

| # | Point | Decision | Recommended by dialogue? | Confirmed by user | Sources |
|---|---|---|---|---|---|
| D1 | Module split (§14 "for domain-profile to confirm") | Keep all five modules unchanged: REG, CHK, DOC, RPT, INT | yes | yes — 2026-10-01 | [KB:raw-idea.md §14] |
| D2 | MCP server for Oracle (§13 Open) | Oracle SQLcl MCP server. The read-only user and views are enforced at its saved connection; bind variables or strict type validation, row limit and timeout are enforced by the engine before every call, so the §6 requirements hold whatever the server does | yes | yes — 2026-10-01 | [KB:raw-idea.md §6]; NO-RESEARCH |
| D3 | LLM provider for real request data (§13 Open) | Decided before go-live, as a go-live gate. Until then the free tier with synthetic / anonymised data only (G13). The gate's acceptance: no training on submitted data, a data-processing agreement, in-region processing, swappable through Spring AI. No design element waits on it | yes | yes — 2026-10-01 | [KB:raw-idea.md §10]; NO-RESEARCH |
| D4 | Report retention (§13 Open) | A report is kept as long as the host keeps the request it verified. The period is configuration; a purge removes older runs with their findings and documents (hard delete). Who may view stored reports is deferred with caller authentication (A2) | yes | yes — 2026-10-01 | [KB:raw-idea.md §9, §15 A2]; NO-RESEARCH |
| D5 | Pilot service (§13 Open) | Scholarship request — the raw idea's own example: explicit numeric conditions, two required documents (TRANSCRIPT, ID_CARD), `path` fetch mode | yes | yes — 2026-10-01 | [KB:raw-idea.md §4] |
| D6 | Frontend (amendment A1) | A web frontend (React + TypeScript) embedded in the host screen replaces the server-rendered report page as the display path; it uses the same REST API as any host | — (owner decision) | yes — 2026-10-01 | [KB:raw-idea.md §15 A1] |
| D7 | Security (amendment A2) | Caller authentication and the security phases are deferred to a later version; the §12 guardrails stay in this version | — (owner decision) | yes — 2026-10-01 | [KB:raw-idea.md §15 A2] |

## 9. RESEARCH LOG

The `research-log` block — `gov.py analyze` reads it (C1.4 sourced; C0.1 holds it to its schema). Web research was unavailable this run; the rows below state that rather than carry unsourced claims.

```yaml name=research-log
rows:
  - {point: "MCP server for Oracle", finding: "Recommendation made from the raw idea's §6 requirements and operator knowledge; not web-researched this run", source: NO-RESEARCH, used_in: "8 D2"}
  - {point: "LLM provider for real data", finding: "Go-live gate criteria derived from the raw idea's §10 free-tier caution; provider terms not web-researched this run", source: NO-RESEARCH, used_in: "8 D3"}
  - {point: "Report retention", finding: "Tied to the host's record retention; no regulation was researched this run", source: NO-RESEARCH, used_in: "8 D4"}
```

## 10. OPEN ITEMS

None. Caller authentication (API key or mTLS) and who may view stored reports are not open items of this version: they are deferred by the owner's amendment A2 and return with the security version.
══════════════════════════════════════════════════════════════════

<<<END INPUT>>>

<<<INPUT: project-registry>>>
# PROJECT REGISTRY — Request Verification Service
══════════════════════════════════════════════════════════════════
Profile            : aias
Registry Version   : 1.0.0
Domain Profile     : analysis/domain/domain-profile.md v1
Last Updated       : 2026-10-01 by P-1
Modules registered : 5   Entity candidates : 6   Open items : 2
══════════════════════════════════════════════════════════════════

## SCHEMA COMPLIANCE MAP

The block `gov.py analyze` reads (C2.2): every category of `factory.yaml → registry.categories`, mapped to the section of this registry that covers it.

```yaml name=compliance-map
categories:
  CAT-1: "1. Identity & versioning"
  CAT-2: "2. Conventions & steering"
  CAT-3: "3. Module / component index"
  CAT-4: "4. Entity ownership"
  CAT-5: "5. Shared entity declarations"
  CAT-6: "6. Structural / implementation registry"
  CAT-7: "7. Cross-module dependency index"
  CAT-8: "8. Open question index"
  CAT-9: "9. Pipeline / progress status"
  CAT-10: "10. Change / event history"
uncovered: []
```

---

## 1. Identity & versioning

| Field | Value | Source |
|---|---|---|
| Platform | Request Verification Service | domain-profile header |
| Profile | aias | profile |
| Form | A standalone Spring Boot service called by host systems over REST; not a library | domain-profile §1; [KB:raw-idea.md §13] |
| Stack | Java 21, Spring Boot 4, Spring AI 2.0 | [KB:raw-idea.md §3, §13] |
| Tenancy | Single tenant | domain-profile §1; [KB:raw-idea.md §13] |
| Tracks | backend, frontend (A1); platform track covers the MCP connection, LLM provider configuration and service database | [KB:raw-idea.md §14, §15 A1] |
| Module interface | in-process — all modules in one deployable, reached through injected interfaces | domain-profile §6 (profile `conventions.module_interface: in_process`) |
| Domain profile | analysis/domain/domain-profile.md v1 (Status FRESH, 2026-10-01) | domain-profile header |

### Version history

| Registry version | Date | Stage | Change | Source |
|---|---|---|---|---|
| 1.0.0 | 2026-10-01 | P-1 | Registry bootstrapped from domain-profile v1 (first creation) | domain-profile v1 |

---

## 2. Conventions & steering

### 2.1 Steering — copied verbatim from domain-profile §7

#### 7.1 Ubiquitous language

| Term | Definition | Do not say | Module code |
|---|---|---|---|
| Check | One independent verification run of one request against one service package; nothing is carried from one check to another. | job, audit, scan | CHK |
| Service Package | The per-service configuration — service knowledge plus service definition — maintained by the service administrator and versioned; adding a service needs no code. | plugin, template, tenant | REG |
| Service Knowledge | The natural-language conditions and rules of a service, read whole by the LLM (no retrieval). | prompt, rules file | REG |
| Service Definition | The structured part of a service package — queries, required documents, fetch mode, optional approval API — executed literally by the engine, never interpreted by the LLM. | — | REG |
| Connection | A named data source defined once outside the service packages and set at activation time per environment. | — | REG |
| Fetch Mode | How a service's documents are obtained: `path`, `blob` or `manual`. | — | DOC |
| Finding | The outcome of one condition in a report — satisfied or not, its evidence and a note for the employee. | issue, violation | RPT |
| Evidence | The actual value found that a finding is based on, shown so the employee can verify it. | — | RPT |
| Overall Status | The report's verdict: `COMPLIANT`, `NOT_COMPLIANT` or `NEEDS_MANUAL_REVIEW`. | — | RPT |
| Host System | The calling application (Oracle ADF or another government system) that starts checks and embeds the employee frontend. | client app | INT |
| Employee Decision | The approve / reject decision the employee takes, recorded beside the report result. | verdict | RPT |
| Approval API | An optional, per-service host endpoint the service calls only after the employee confirms a decision. | — | INT |
| Service Administrator | The person who maintains service packages and connections. | — | REG |
| Employee | The host-system user who reviews the report and takes the decision; identified by the host, recorded with the check. | — | INT |

#### 7.2 Bounded contexts

| Context | Owns module codes | Boundary statement |
|---|---|---|
| configuration | REG | What a service IS: its packages, versions and connections. Read by every check, written only by the administrator. |
| verification | CHK, DOC | Running one check: the pipeline and the documents it fetches and reads. Holds no state between checks. |
| record | RPT | What a check FOUND and what the employee DECIDED, kept for the retention period. |
| integration | INT | Everything a host or the employee frontend touches: the REST API and the outbound approval call. |

#### 7.3 Module prefixes proposal

| Code | Display | Status |
|---|---|---|
| REG | Service Registry | IN PROFILE |
| CHK | Check Engine | IN PROFILE |
| DOC | Document Access | IN PROFILE |
| RPT | Report Store | IN PROFILE |
| INT | Host Integration | IN PROFILE |

#### 7.4 Identifier rules

Later stages build IDs as `{prefix}-{MOD}-{seq}` (seq width 3) with the module codes above. Entity kinds: `config, transactional`. The profile adds no domain-specific atom.

#### 7.5 Knowledge sources to cite

- `profiles/aias/knowledge/raw-idea.md` (as amended in its §15, A1–A2)

### 2.2 Profile echo (read-only, cited as "profile")

| Fact | Value | Agrees with steering? |
|---|---|---|
| Module codes | REG, CHK, DOC, RPT, INT | yes — all five IN PROFILE, none RESERVED-outside-profile |
| Entity kinds | config, transactional | yes |
| Bounded contexts | configuration [REG]; verification [CHK, DOC]; record [RPT]; integration [INT] | yes |
| Knowledge files | profiles/aias/knowledge/raw-idea.md | yes |

### 2.3 Enforcement notes

- **E1** Every later artifact uses the terms of §2.1 (7.1) verbatim; a synonym listed under "do not say" (e.g. job / audit / scan for Check; plugin / template / tenant for Service Package; prompt / rules file for Service Knowledge; issue / violation for Finding; client app for Host System; verdict for Employee Decision) is a consistency finding at the pass gate (`gov.py analyze` checks registry ↔ artifact agreement).
- **E2** IDs follow `{prefix}-{MOD}-{seq}` (seq width 3) with the module codes of this section only (REG, CHK, DOC, RPT, INT).
- **E3** Entities are classified with the kinds `config, transactional`.
- **E4** Sources to cite when a stage resolves an ambiguity: the knowledge sources listed here (`profiles/aias/knowledge/raw-idea.md`, as amended in §15 A1–A2), then the domain-profile itself.
- **E5** Pipeline status per module (§9 "Pipeline / progress status") is maintained by the orchestrator from commits — this engine seeds the rows as NOT STARTED.

### 2.4 Governance decisions log (confirmed only)

Platform-wide governing rules (domain-profile §5):

| # | Decision | Rationale | Source |
|---|---|---|---|
| G1 | The LLM analyses and summarises only. It never writes SQL and never triggers approval; it is given no tool that runs SQL. | Guardrail — SQL and approval stay under engine / employee control | domain-profile §5 G1; [KB:raw-idea.md §6, §12] |
| G2 | Approval is executed only as a result of the employee's action, and only where the service enables the approval API. | The employee stays the decision maker | domain-profile §5 G2; [KB:raw-idea.md §11, §12] |
| G3 | All access to host data uses a read-only database user, preferably limited to specific views. | The service needs no write access to host systems | domain-profile §5 G3; [KB:raw-idea.md §12] |
| G4 | Query parameters are bound or strictly type-validated; SQL is never built from free text. Queries are exactly those written in the service definition. | Injection prevention; literal execution of the service definition | domain-profile §5 G4; [KB:raw-idea.md §6, §12] |
| G5 | File paths are validated to lie inside the allowed storage root before opening. | Path traversal prevention | domain-profile §5 G5; [KB:raw-idea.md §12] |
| G6 | Anything that could not be read appears in the report; nothing is skipped silently. A missing or unreadable required document prevents `COMPLIANT`. | Report completeness | domain-profile §5 G6; [KB:raw-idea.md §7, §12] |
| G7 | Document content is data, never instructions to the model. | Prompt-injection prevention | domain-profile §5 G7; [KB:raw-idea.md §12] |
| G8 | Each check has limits: timeout, maximum rows, maximum file size. | Resource bounds per check | domain-profile §5 G8; [KB:raw-idea.md §12] |
| G9 | No data is carried from one check to another (no memory, no shared state). | Prevents leaking one request's data into another | domain-profile §5 G9; [KB:raw-idea.md §2, §12] |
| G10 | Every finding carries its evidence (the actual value found). | The employee can verify each finding | domain-profile §5 G10; [KB:raw-idea.md §7] |
| G11 | Every report records the service package version it was built on. | Traceability of reports to configuration | domain-profile §5 G11; [KB:raw-idea.md §4] |
| G12 | The engine depends on Spring AI `ChatModel` only, with no provider-specific feature; document reading has its own configurable model; a fixed set of requests with known expected results runs on every model change. | Keeps the LLM provider replaceable | domain-profile §5 G12; [KB:raw-idea.md §10] |
| G13 | While a free-tier provider is in use, only synthetic or anonymised requests and documents are sent to it. | Free tiers may train on submitted data | domain-profile §5 G13; [KB:raw-idea.md §10] |
| G14 | BLOB content is read over JDBC with a read-only user, never moved through MCP. | Base64 over MCP is slow and size-limited | domain-profile §5 G14; [KB:raw-idea.md §6] |

User-confirmed decisions (domain-profile §8):

| # | Point | Decision | Confirmed | Source |
|---|---|---|---|---|
| D1 | Module split | Keep all five modules unchanged: REG, CHK, DOC, RPT, INT | yes — 2026-10-01 | domain-profile §8 D1; [KB:raw-idea.md §14] |
| D2 | MCP server for Oracle | Oracle SQLcl MCP server. Read-only user and views enforced at its saved connection; bind variables or strict type validation, row limit and timeout enforced by the engine before every call | yes — 2026-10-01 | domain-profile §8 D2; [KB:raw-idea.md §6]; NO-RESEARCH |
| D3 | LLM provider for real request data | Decided before go-live, as a go-live gate; until then free tier with synthetic / anonymised data only (G13). Gate acceptance: no training on submitted data, a data-processing agreement, in-region processing, swappable through Spring AI. No design element waits on it | yes — 2026-10-01 | domain-profile §8 D3; [KB:raw-idea.md §10]; NO-RESEARCH |
| D4 | Report retention | A report is kept as long as the host keeps the request it verified; the period is configuration; a purge removes older runs with their findings and documents (hard delete). Who may view stored reports is deferred with caller authentication (A2) | yes — 2026-10-01 | domain-profile §8 D4; [KB:raw-idea.md §9, §15 A2]; NO-RESEARCH |
| D5 | Pilot service | Scholarship request — explicit numeric conditions, two required documents (TRANSCRIPT, ID_CARD), `path` fetch mode | yes — 2026-10-01 | domain-profile §8 D5; [KB:raw-idea.md §4] |
| D6 | Frontend (A1) | A web frontend (React + TypeScript) embedded in the host screen replaces the server-rendered report page as the display path; it uses the same REST API as any host | yes — 2026-10-01 | domain-profile §8 D6; [KB:raw-idea.md §15 A1] |
| D7 | Security (A2) | Caller authentication and the security phases are deferred to a later version; the §12 guardrails stay in this version | yes — 2026-10-01 | domain-profile §8 D7; [KB:raw-idea.md §15 A2] |

Locked decisions from the knowledge source (raw idea §13 "Decided", not to be reopened):

| Topic | Decision | Source |
|---|---|---|
| Configuration | One service package per service, maintained by the service administrator | [KB:raw-idea.md §13] |
| Database access | Read-only through an MCP server, specified at activation | [KB:raw-idea.md §13] |
| Documents | `path`, `blob` and `manual` fetch modes all available, chosen per service | [KB:raw-idea.md §6, §13] |
| Decision maker | The employee; the approval API is an optional second path | [KB:raw-idea.md §13] |
| Storage | Reports kept in the service's own database tables, in a schema separate from the read-only host user | [KB:raw-idea.md §9, §13] |
| LLM | Cloud, free tier for testing, replaceable by configuration | [KB:raw-idea.md §10, §13] |
| Memory, RAG, vector store | Not included | [KB:raw-idea.md §2, §13] |
| Check execution | Asynchronous: the host starts a check and polls for the result | [KB:raw-idea.md §5]; domain-profile §3 |

Out of bounds for this version (domain-profile §1): multi-tenancy; conversation memory; RAG and vector stores; multi-agent orchestration; a full administration UI and an internal permission system; caller authentication (API key or mTLS) and the security phases (A2).

ADRs referenced: none — this run made no decision of its own.

---

## 3. Module / component index

| Module code | Display name | Bounded context | Category | Core / extension | Status | Scope | Source |
|---|---|---|---|---|---|---|---|
| REG | Service Registry | configuration | Foundation | Core | RESERVED | Service packages (service knowledge + service definition), versions, connections | domain-profile §4 row 1, §7.3; D1; profile |
| CHK | Check Engine | verification | Business | Core | RESERVED | The fixed pipeline, deterministic checks, LLM comparison — the only fixed logic | domain-profile §4 row 2, §7.3; D1; profile |
| DOC | Document Access | verification | Business | Core | RESERVED | `path`, `blob`, `manual` fetching; reading PDF, XLS, images | domain-profile §4 row 3, §7.3; D1; profile |
| RPT | Report Store | record | Business | Core | RESERVED | Runs, findings, documents, employee decision; retention purge | domain-profile §4 row 4, §7.3; D1, D4; profile |
| INT | Host Integration | integration | Integration | Core | RESERVED | REST API, employee frontend's API surface, optional approval API | domain-profile §4 row 5, §7.3; D1, D6; profile |

All five codes are in `profile.vocabulary.module_prefixes`; RESERVED = code reserved, module not yet started (RULE-4).

External systems (not modules — no code assigned, recorded for context only): host database via Oracle SQLcl MCP server; host file storage / host BLOB columns; LLM provider via Spring AI; host approval API; host systems (Oracle ADF, others). Source: domain-profile §6.

---

## 4. Entity ownership

Analysis-phase candidates. `CAND-*` refs are registry-local handles, not pipeline IDs; they are replaced by formal IDs when the owning stage registers the element.

| Candidate ref | Entity name | Owner module | Kind | PRIVATE/SHARED | Status | Source |
|---|---|---|---|---|---|---|
| CAND-REG-001 | Service Package (service knowledge + service definition, versioned) | REG | config | SHARED? | CANDIDATE | domain-profile §4 row 1, §7.1, §7.2, G11; [KB:raw-idea.md §4] |
| CAND-REG-002 | Connection | REG | config | SHARED? | CANDIDATE | domain-profile §6 (CHK depends on REG connection), §7.1; [KB:raw-idea.md §4] |
| CAND-CHK-001 | Check (one check run: service, request number, employee, status, result, service version, model, timestamps) | UNCLEAR (CHK or RPT) | transactional | SHARED? | OPEN | domain-profile §7.1 (term "Check" → CHK), §4 row 4 (RPT holds "Runs"); [KB:raw-idea.md §9 CHECK_RUN] — see OQ-1 |
| CAND-RPT-001 | Finding (satisfied flag, evidence, note) | RPT | transactional | PRIVATE | CANDIDATE | domain-profile §4 row 4, §7.1, G10; [KB:raw-idea.md §7, §9 CHECK_FINDING] |
| CAND-RPT-002 | Check Document (type, source mode, read status) | UNCLEAR (RPT or DOC) | transactional | SHARED? | OPEN | domain-profile §4 rows 3–4, §7.1 (Fetch Mode → DOC); [KB:raw-idea.md §9 CHECK_DOCUMENT] — see OQ-2 |
| CAND-RPT-003 | Employee Decision | RPT | transactional | PRIVATE | CANDIDATE | domain-profile §7.1, §7.2 (record context), §4 row 4; [KB:raw-idea.md §9, §11] |

Notes (inferred, not confirmed):
- Service Knowledge and Service Definition (domain-profile §7.1) are registered as parts of CAND-REG-001, not as separate candidates; P2 decides whether they become separate entities.
- Raw idea §9 stores the employee decision as columns of `CHECK_RUN`; whether CAND-RPT-003 is an entity of its own or attributes of CAND-CHK-001 is for P2.
- The verification report itself (Overall Status, findings, documents, metadata) is the composition of CAND-CHK-001, CAND-RPT-001 and CAND-RPT-002 [KB:raw-idea.md §7, §9]; it is not registered as a separate candidate.

---

## 5. Shared entity declarations

| Candidate ref | Entity name | Owner module | Referenced by | Basis | Status | Source |
|---|---|---|---|---|---|---|
| CAND-REG-001 | Service Package | REG | CHK (loads package), RPT (records package version, G11) | Read across modules through the REG interface | CANDIDATE | domain-profile §6 (REG → CHK), §5 G11 |
| CAND-REG-002 | Connection | REG | CHK, DOC (queries / BLOB reads use a connection) | Read across modules through the REG interface | CANDIDATE | domain-profile §6 (REG → CHK; CHK, DOC → host database) |
| CAND-CHK-001 | Check | UNCLEAR (CHK or RPT) | CHK (runs it), RPT (report belongs to it), INT (start / poll) | Referenced by three modules; owner open | OPEN | domain-profile §6 (CHK → RPT, CHK → INT) — see OQ-1 |
| CAND-RPT-002 | Check Document | UNCLEAR (RPT or DOC) | DOC (fetches / reads), RPT (stores), CHK (deterministic required-document check) | Referenced by three modules; owner open | OPEN | domain-profile §6 (DOC → CHK), §4 rows 3–4 — see OQ-2 |

---

## 6. Structural / implementation registry

None yet — filled by P2 / P3.1.

---

## 7. Cross-module dependency index

Derived: `platform/dependency-graph.json` (`gov.py graph`). No dependency row is written in this registry; the relations stated in domain-profile §6 reach P0, which turns them into its `platform-dependencies` block.

---

## 8. Open question index

| Ref | Question | Evidence | Affected rows | Status | Resolution |
|---|---|---|---|---|---|
| OQ-1 | Which module owns the Check (check run) record — CHK, which runs the check and owns the term, or RPT, which stores runs? | Side A: domain-profile §7.1 maps the term "Check" to CHK; §3 / §4 row 2 make CHK run the pipeline. Side B: domain-profile §4 row 4 lists "Runs" in RPT's scope; §6 "RPT depends on CHK (the check run a report belongs to)"; [KB:raw-idea.md §9] puts `CHECK_RUN` with the report tables. | CAND-CHK-001 (§4, §5) | OPEN | To be resolved by P0 dialogue |
| OQ-2 | Which module owns the Check Document record (type, source mode, read status) — RPT, which stores "documents", or DOC, which fetches and reads them? | Side A: domain-profile §4 row 4 lists "documents" in RPT's scope; [KB:raw-idea.md §9] `CHECK_DOCUMENT` is a report table; §7 report "Documents" part. Side B: domain-profile §4 row 3 gives DOC fetching and reading; §7.1 maps "Fetch Mode" to DOC. | CAND-RPT-002 (§4, §5) | OPEN | To be resolved by P0 dialogue |

domain-profile §10 lists no open items; caller authentication and report-viewing rights are deferred by A2, not open (domain-profile §10; D7).

---

## 9. Pipeline / progress status

| Module code | Pipeline status | Source |
|---|---|---|
| REG | NOT STARTED | seeded by P-1 (E5) |
| CHK | NOT STARTED | seeded by P-1 (E5) |
| DOC | NOT STARTED | seeded by P-1 (E5) |
| RPT | NOT STARTED | seeded by P-1 (E5) |
| INT | NOT STARTED | seeded by P-1 (E5) |

Maintained by the orchestrator from commits from here on (E5).

---

## 10. Change / event history

| Date | Event | ID | Ref | Summary |
|---|---|---|---|---|
| 2026-10-01 | BOOTSTRAP | — | — | Registry 1.0.0 created from domain-profile v1: 5 modules, 6 entity candidates (4 shared declarations), 14 governing rules + 7 user decisions + 8 locked knowledge decisions, 2 open items; steering copied (14 terms · 4 contexts · 5 codes, 0 RESERVED-outside-profile); 5 pipeline rows seeded NOT STARTED; 0 structural rows; 0 dependency rows (derived) |

### Extraction report — P-1 run

```
══════════════════════════════════════════════════════════════════
EXTRACTION REPORT — P-1 — 2026-10-01 — profile aias
Input : analysis/domain/domain-profile.md v1
══════════════════════════════════════════════════════════════════
MODULES IDENTIFIED     : + REG Service Registry — context configuration — RESERVED
                         + CHK Check Engine — context verification — RESERVED
                         + DOC Document Access — context verification — RESERVED
                         + RPT Report Store — context record — RESERVED
                         + INT Host Integration — context integration — RESERVED
ENTITY CANDIDATES      : + Service Package — owner REG — config — SHARED?
                         + Connection — owner REG — config — SHARED?
                         + Check — owner UNCLEAR (CHK/RPT) — transactional — SHARED?
                         + Finding — owner RPT — transactional — PRIVATE
                         + Check Document — owner UNCLEAR (RPT/DOC) — transactional — SHARED?
                         + Employee Decision — owner RPT — transactional — PRIVATE
DECISIONS CONFIRMED    : + G1–G14 governing rules — source domain-profile §5
                         + D1–D7 user-confirmed decisions — source domain-profile §8
                         + 8 locked decisions — source [KB:raw-idea.md §13 Decided]
OPEN ITEMS RECORDED    : + OQ-1 owner of Check — evidence domain-profile §4, §7.1; KB §9
                         + OQ-2 owner of Check Document — evidence domain-profile §4, §7.1; KB §9
STEERING COPIED        : 14 terms · 4 contexts · 5 codes (0 RESERVED)
SECTIONS UPDATED       : 1, 2, 3, 4, 5, 7, 8, 9, 10
NOTHING EXTRACTED FOR  : 6 Structural / implementation registry (filled by P2 / P3.1)
══════════════════════════════════════════════════════════════════
```

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
# BRIEF — stage `P1.5` (Contract (outbound promise)) · module CHK · v1 · profile `aias`

Lane `analysis` · implementer ['claude:opus'] · effort high · round 1

## Rules that bind this run
- Questions: **forbidden**. A `[QUESTION]` block is refused. Ambiguity → ADR in `analysis/decisions/CHK/` (`ADR-{MOD}-{seq:03d}.md`): non-breaking → continue; breaking → status BLOCKED and stop.
- Owns IDs: CON — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-CHK-001`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared/analysis/modules/CHK/P1_5/contract-chk.md`
- Write the files directly into the project checkout (you are the operator); the orchestrator reads them on `--complete`.

## Contracts checked by `gov.py analyze` after this stage
- **C13** contract (outbound promise) → database and every consumer: C13.1 exists {'artifact': 'contract'} [CRITICAL]; C13.2 ids-owned {'stage': 'P1.5'} [CRITICAL]; C13.3 no-questions {'stage': 'P1.5'} [CRITICAL]; C13.4 ids-continue {'stage': 'P1.5'} [CRITICAL]; C13.5 traces {'from': 'CON', 'to': ['ENT', 'REQ'], 'min': 1, 'mode': 'any'} [MAJOR]; C13.6 languages {'stage': 'P1.5'} [MAJOR]

---
# ENGINE
# Contract (outbound promise) — ENGINE

```
Engine        : Contract (outbound promise)
Stage id      : P1.5
Pass          : 1
Questions     : forbidden — ambiguity is self-resolved (§4)
Lane          : analysis
Inputs        : srs, registry-srs
Produces      : contract-{mod}.md
Owns IDs      : CON   → `{prefix}-{MOD}-{seq}` (seq width 3)
Published as  : platform/contracts/ (publications.contracts) — read by every OTHER module
Next          : P2
Module        : CHK   Version: 1
Profile       : aias — Request Verification Service
```

This engine writes the module's **outbound promise** — contract-first: what other modules
may depend on before this one is built. It is read by the database and backend stages of
every other module (they plan against it, and their dependency edges become
`CONTRACTED`), and it binds this module's own backend plan, which must honour every item a
consumer depends on (`gov.py analyze` → C7.24 `contract-honoured`). It promises; it never [C:C13.2]
implements.

Completion (write → registry → analyze → commit → publish) is owned by the orchestrator — see
`shared/GOVERNANCE-CORE.md`. 

### IDs this stage assigns

| Atom | Meaning | Traces to |
|---|---|---|
| `CON` | contract item (outbound promise) | ENT, REQ |

---

## 1 — What goes into the contract

```
ENTITY items    : every ENT of the SRS that another module may reference — a SHARED entity,
                  an entity another module's A8 `module-dependencies` block consumes,
                  a lookup another module consumes. Its ENT id and its KEY columns (the business
                  key and the identifier a consumer stores) — not the whole field list. [G]
OPERATION items : every operation another module may call — a read of a shared entity, a
                  lookup of a code, a status check. A SIGNATURE only: name, inputs, output, [G]
                  errors by meaning. No path, no verb, no implementation — the backend
                  stage (P3.1) chooses those and states `Honours: CON-…` on the block
                  that implements the item.
NOTHING ELSE    : private entities, internal operations, tables, endpoints' paths.
```

## 2 — Record format (one per item)

```markdown
### CON-CHK-001 — [what is promised]

Entity    : ENT-CHK-[seq] — key columns: [business key], [identifier] · identifier type: [per the IDENTIFIERS Profile rule: Every table of the service's own schema keys on NUMBER(19) GENERATED BY DEFAULT AS IDENTITY. Host-system identifiers (service request number, employee identity) are stored as strings exactly as the host sent them and are never foreign keys — the host data lives outside this schema.; ONE concrete dialect type, not a logical one — a consumer's HARD-FK column copies it verbatim] [G]
Traces    : ENT-CHK-[seq], REQ-CHK-[seq]

### CON-CHK-002 — [operation]
Signature : [name]([inputs]) → [output] · errors: [not found / forbidden / …] [G]
Entity    : ENT-CHK-[seq]
Traces    : REQ-CHK-[seq]
```

Every item traces to the ENT or REQ it rests on (C13.5). An item with a `Signature`
line is an operation; without one it is an entity promise.

## 3 — Stability

A contract is what others build against. Add freely (ADDITIVE); change or remove an item a
consumer depends on only in a BREAKING version with an ADR — `gov.py graph` raises a [C:C12.2]
resolution event on every consumer edge that cited it.

## 4 — Ambiguity rule (questions are forbidden here) [T:questions-forbidden]

```
Whether something is promised is decided by the SRS and the other modules' A8 blocks:
  NON-BREAKING → ADR, CONTINUE — action: adr · then: continue
  BREAKING     → ADR status BLOCKED, STOP — then: stop
ADR file : analysis/decisions/CHK/ADR-{MOD}-{seq:03d}.md
```

## 5 — Self-check before emitting

- [ ] Every entity another module's A8 block consumes from CHK has an ENTITY item.
- [ ] Every operation item has a `Signature` line and names no path or verb.
- [ ] Every item traces to an ENT or REQ; no private entity is promised.
- [ ] No question raised; sequences continuous.


---
# INPUTS (generated current state)

<<<INPUT: srs>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: registry-srs>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
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
# BRIEF — stage `P2` (Database) · module CHK · v1 · profile `aias`

Lane `analysis` · implementer ['claude:opus'] · effort high · round 1

## Rules that bind this run
- Questions: **forbidden**. A `[QUESTION]` block is refused. Ambiguity → ADR in `analysis/decisions/CHK/` (`ADR-{MOD}-{seq:03d}.md`): non-breaking → continue; breaking → status BLOCKED and stop.
- Owns IDs: DBF, XM — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-CHK-001`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared/analysis/modules/CHK/P2/db-script-chk.md`
- `governance-shared/analysis/modules/CHK/P2/registry-db-chk.md` (registry)
- Write the files directly into the project checkout (you are the operator); the orchestrator reads them on `--complete`.

## Contracts checked by `gov.py analyze` after this stage
- **C5** SRS → database: C5.1 exists {'artifact': 'srs'} [CRITICAL]; C5.2 ears {'kind': 'REQ', 'patterns': 'factory.ids.ears.patterns'} [CRITICAL]; C5.3 traces {'from': 'REQ', 'to': ['US'], 'min': 1} [MAJOR]; C5.4 orphans {'kind': 'REQ', 'referenced_by': ['AC'], 'min': 1} [CRITICAL]; C5.5 traces {'from': 'AC', 'to': ['REQ'], 'min': 1} [MAJOR]; C5.6 traces {'from': 'RULE', 'to': ['REQ'], 'min': 1} [MAJOR]; C5.7 ids-owned {'stage': 'P1'} [CRITICAL]; C5.8 registry-agree {'artifact': 'srs', 'registry': 'registry-srs', 'kinds': ['REQ', 'AC', 'ENT', 'RULE']} [MAJOR]; C5.9 no-questions {'stage': 'P1'} [CRITICAL]; C5.10 languages {'stage': 'P1'} [MAJOR]; C5.11 ids-continue {'stage': 'P1'} [CRITICAL]; C5.12 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED'} [CRITICAL]; C5.13 ambiguity {'artifact': 'srs', 'kinds': ['REQ', 'AC', 'RULE'], 'lines': ['Statement', 'Given', 'When', 'Then'], 'lexicon': 'analyze.maturity.ambiguity_lexicon', 'extend': 'review.ambiguity_lexicon'} [MINOR]; C5.14 ac-measurable {'artifact': 'srs', 'kind': 'AC', 'label': 'Then', 'spec': 'analyze.maturity.measurable'} [MINOR]; C5.15 crud-covered {'artifact': 'srs', 'entity': 'ENT', 'kind': 'REQ', 'statement': 'Statement', 'spec': 'analyze.maturity.crud'} [MINOR]; C5.16 feature-unwanted {'artifact': 'srs', 'group': 'US', 'kind': 'REQ', 'statement': 'Statement', 'pattern': 'unwanted'} [MINOR]; C5.17 glossary {'artifact': ['srs'], 'glossary': 'vocabulary.glossary', 'synonyms': 'vocabulary.glossary_synonyms'} [MINOR]
- **C6** SRS + database → backend execution plan: C6.1 exists {'artifact': 'db-script'} [CRITICAL]; C6.2 traces {'from': 'DBF', 'to': ['REQ', 'ENT'], 'min': 1} [MAJOR]; C6.3 traces {'from': 'XM', 'to': ['REQ'], 'min': 1} [MAJOR]; C6.4 ids-owned {'stage': 'P2'} [CRITICAL]; C6.5 registry-agree {'artifact': 'db-script', 'registry': 'registry-db', 'kinds': ['DBF', 'XM']} [MAJOR]; C6.6 orphans {'kind': 'ENT', 'referenced_by': ['DBF'], 'min': 1} [MAJOR]; C6.7 no-questions {'stage': 'P2'} [CRITICAL]; C6.8 ids-continue {'stage': 'P2'} [CRITICAL]; C6.9 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED', 'bound_in': 'db-script'} [CRITICAL]; C6.10 xm-record-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.11 xm-tier-rule {} [CRITICAL]; C6.12 xm-transition-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.13 graph-acyclic {} [CRITICAL]; C6.14 graph-target-known {} [CRITICAL]; C6.15 db-column-only {'artifact': 'db-script', 'register': ['db-script', 'registry-db']} [MAJOR]
- **C13** contract (outbound promise) → database and every consumer: C13.1 exists {'artifact': 'contract'} [CRITICAL]; C13.2 ids-owned {'stage': 'P1.5'} [CRITICAL]; C13.3 no-questions {'stage': 'P1.5'} [CRITICAL]; C13.4 ids-continue {'stage': 'P1.5'} [CRITICAL]; C13.5 traces {'from': 'CON', 'to': ['ENT', 'REQ'], 'min': 1, 'mode': 'any'} [MAJOR]; C13.6 languages {'stage': 'P1.5'} [MAJOR]

## Blocks this stage emits — each a ```yaml name={name} fence, held to its schema by C0.1 (`gov.py analyze`)
- `dbf-matrix` in `db-script` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"dbf-matrix","description":"P2 db-script §2: the DB field traceability matrix — every DBF the module owns, the physical column it is, the ENT.field and the REQ ids it traces to. The single canonical source of DBF → column (value-agreement C7.10 binds the backend plan to it); the ENT and REQ ids in a row are the record's traces (C6.2, C6.6).","type":"object","required":["rows"],"additionalProperties":false,"properties":{"rows":{"type":"array","minItems":1,"items":{"type":"object","required":["id","table","column","type","entity_field","traces"],"additionalProperties":false,"properties":{"id":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"table":{"type":"string","pattern":"^[A-Za-z_][A-Za-z0-9_.]*$"},"column":{"type":"string","pattern":"^[A-Za-z_][A-Za-z0-9_]*$"},"type":{"type":"string"},"entity_field":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}\\.[A-Za-z_][A-Za-z0-9_]*$"},"traces":{"type":"array","minItems":1,"items":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}},"nullable":{"type":["string","boolean","null"],"description":"NOT NULL / NULL (YAML reads a bare NULL as null), or a boolean"},"default":{"type":["string","number","boolean","null"],"description":"the column default, or nothing"}}}}}}
  ```
- `xm-register` in `db-script` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"xm-register","description":"P2 db-script: every cross-module dependency of this module — one record per XM, the fields of factory.yaml → xm.record (the state machine's own vocabulary; xm-record-valid checks the values)","type":"object","required":["records"],"additionalProperties":false,"properties":{"records":{"type":"array","items":{"type":"object","required":["id","type","target_module","target_entity","traces","state"],"additionalProperties":false,"properties":{"id":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"type":{"type":"string"},"target_module":{"type":"string","pattern":"^[A-Z][A-Z0-9]*$"},"target_entity":{"type":"string"},"traces":{"type":"array","items":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}},"state":{"type":"string"},"contract_ref":{"type":"string"},"workaround":{"type":"string"},"unblock_condition":{"type":"string"},"column":{"type":"string"}}}}}}
  ```

---
# ENGINE
# Database — ENGINE

```
Engine        : Database
Stage id      : P2
Pass          : 1
Questions     : forbidden — ambiguity is self-resolved (§9)
Lane          : analysis
Inputs        : srs, registry-srs, contract, contracts?
Produces      : db-script-{mod}.md · registry-db-{mod}.md (registry)
Owns IDs      : DBF, XM   → `{prefix}-{MOD}-{seq}` (seq width 3)
Dialect       : oracle19c   (profile.stack.db.target_dialect; syntax from profile.stack.db.syntax_map)
Next          : P3.1
Module        : CHK   Version: 1
Profile       : aias — Request Verification Service
```

This engine produces the module's **structural truth**: an executable database script
derived from the SRS, a field-level traceability matrix and the cross-module dependency
register. It invents no business logic and does not redesign SRS meaning; when the script [G]
and the SRS disagree, the SRS governs and the script is corrected. It produces no
execution phases and no implementation sequencing.

Completion (write → registry → analyze → commit) is owned by the orchestrator — see
`shared/GOVERNANCE-CORE.md`. In a delta version (version > 1): read
`_state/current-{artifact}` of the previous version for every input and for this
stage's own artifacts, and emit only ADDED / MODIFIED / REMOVED elements plus the [C:C12.2]
`change-manifest.md` per `shared/VERSIONING.md` — the script of a delta version is a
migration (ALTER / CREATE / DROP for the changed objects only); sequences continue. [C:C12.2]

### IDs this stage assigns

| Atom | Meaning | Traces to |
|---|---|---|
| `DBF` | db field | REQ, ENT |
| `XM` | cross-module dependency | REQ |

---

## 1 — Inputs and entry check

```
  srs             : ✓ present / ✗ MISSING (pipeline error — not a question)
  registry-srs    : ✓ present / ✗ MISSING (pipeline error — not a question)
  contract        : ✓ present / ✗ MISSING (pipeline error — not a question)
  contracts?      : ✓ present / ✗ MISSING (pipeline error — not a question)
  domain-profile STEERING : vocabulary verbatim; identifier rules
  project-registry        : structural registry (names fixed by other modules),
                            shared declarations; the dependency graph (gov.py graph)
  Extracted               : [N] entities → [N] tables · [N] intra-module FKs ·
                            [N] XM candidates (SRS A8) · [N] lookups (SRS A6)
```

Reading protocol for the SRS: PART A entirely — A3 (entities, fields, logical types),
A5 (rules → constraints), A6 (lookups → seed data), A7 (status → check constraints),
A8 (consumed entities → XM). PART B is not read for structure (screens are not tables).

---

## 2 — DB field traceability matrix (`DBF`)

The single canonical source of `DBF` → column → type → SRS origin. Downstream
artifacts (the backend plan's alignment manifest) reference columns **by DBF id only** [C:C7.10]
and never restate column names, types or SRS references. [C:C7.10]

ONE named block (data, not prose — `gov.py analyze` reads it: C6.2 traces, C6.6 orphans, C7.10 value-agreement; C0.1 holds it to its schema), one row per DBF: [C:C0.1]

```yaml name=dbf-matrix
rows:
  - {id: DBF-CHK-001, table: <table>, column: <column>, type: <oracle19c type>, entity_field: ENT-CHK-001.<field>, traces: [REQ-CHK-…], nullable: NOT NULL, default: "—"}
```

Then, for the reader: `Total: [N] DBF ids across [N] tables`.

```
ASSIGNMENT RULES
  - Sequence continuous across the module (not per table); never reused, even for a [C:C6.8]
    removed column.
  - Per table: PK first, then the entity's own columns in SRS order, then FK columns,
    then standard columns (audit fields last).
  - Every column traces to an ENT.field of the SRS AND to ≥ 1 REQ (via the entity's
    requirements); a standard column traces to the profile default that mandates it
    ("profile: entity_defaults.<kind>") and to the ENT.
  - A column with no SRS origin does not exist (NO-COLUMN-INVENTION, §3).
```

---

## 3 — Naming and column rules

```
IDENTIFIER TRANSFORMATION (stated once in the script header, applied everywhere)
  logical field name (SRS)  →  physical column name: one deterministic transformation
  (case + word separator) declared for oracle19c — never two spellings of one field. [C:C7.10]
  Respect the dialect's identifier length limit and reserved words.

TABLE NAMES     : [module code]_[entity abbreviation] — module code from the domain-profile
PRIMARY KEY     : the SRS field named by `{entity}Id` (profile.stack.db.naming.pk_pattern)
FOREIGN KEYS    : the SRS reference field; constraint FK_[LOCAL]_[REF] (FK_[LOCAL]_[REF]_[n] when several)
AUDIT COLUMNS   : createdAt, updatedAt on every table that carries them per its entity kind —
                  filled by the platform, not by a client; user columns hold a [G]
                  principal string, not a numeric FK
INDEXES         : IDX_[TABLE]_[COLUMN] (composite: IDX_[TABLE]_[ABBR1]_[ABBR2])
CONSTRAINTS     : PK_[TABLE] · UQ_[TABLE]_[COL] · CHK_[TABLE]_[COL]
SEQUENCES       : (profile.stack.db.naming.sequence_pattern — not declared) — emitted only when [G]
                  profile.stack.db.pk_generation is `sequence` (§4)

NO-COLUMN-INVENTION (CRITICAL)
  Every column is (1) an SRS field, or (2) a profile default for the entity's kind,
  or (3) derived from an FK / XM. Nothing from generic templates or prior examples.
  Modules declared EXCEPTION in the registry keep their real names as-is.
```

---

## 4 — Table definition rules

```
For every ENT in SRS A3 → one table (consumed SHARED entities are NOT re-created).
Each table block, in order:
  CREATE TABLE (all columns, inline NOT NULL, inline CHECK)
  COMMENT ON TABLE + COMMENT ON COLUMN for every column (the comment cites the DBF id)
  PRIMARY KEY · UNIQUE (from RULEs) · CHECK (from RULEs / status values)
  FK constraints — intra-module only; a cross-module FK is column-only (§5.1) [C:C6.15]
PK GENERATION — profile.stack.db.pk_generation = `identity` (a PROFILE decision, not a [G]
  dialect default; the same database may not carry two PK strategies)
IDENTIFIERS Profile rule: Every table of the service's own schema keys on NUMBER(19) GENERATED BY DEFAULT AS IDENTITY. Host-system identifiers (service request number, employee identity) are stored as strings exactly as the host sent them and are never foreign keys — the host data lives outside this schema. — every PK column and every [G]
  cross-module FK column (§5.1) is typed by this rule and by the target's contract, which states the same.
  Strategy `identity`: the PK column carries the oracle19c identity clause from
  profile.stack.db.syntax_map.identity:
      GENERATED BY DEFAULT AS IDENTITY
  BLOCK 1 stays empty (state "none: every PK uses the identity clause") unless the SRS
  needs a sequence for something other than a PK.
  NEVER a trigger for PK population; NEVER a default that calls a sequence on the PK column.
```

### 4.1 Datatype governance (profile.stack.db.syntax_map → oracle19c)

| Logical type (SRS) | oracle19c syntax |
|---|---|
| pk | NUMBER(19) |
| string | VARCHAR2(n CHAR) |
| boolean | NUMBER(1) |
| timestamp | TIMESTAMP WITH TIME ZONE |
| decimal | NUMBER(p,s) |
| text | CLOB |
| json | CLOB CHECK (col IS JSON) |

(`identity` and `sequence` are not column types — they are the PK-generation clauses of
§4, selected by `profile.stack.db.pk_generation`, and are listed there only.) [G]

Rules: only the syntaxes above (or one stated once in the script header for a logical [G]
type the map lacks); `n` / `p,s` are filled from the SRS field definition; a
deviation carries a governance note citing the SRS field that requires it; the other
declared dialects (none) are not emitted — one dialect per script.

### 4.2 Lookup and reference data

Profile rule: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.
```
Lookup-backed field (SRS A6, control = lookup) → the stored value is the lookup CODE
  (not a numeric key); seed rows for every value the SRS lists, each block citing [G]
  the SRS lookup key; the shared lookup tables (if the platform uses them) are created
  once by the first module that needs them and only seeded afterwards — not [G]
  re-created in a later module's script.
SEED ROWS FOR A TABLE THIS SCRIPT DOES NOT CREATE — required, not optional. The common
  case is that the lookup tables belong to ANOTHER module, and a seed block written only [G]
  for tables this script creates is then EMPTY: nothing anywhere in the pipeline ever
  creates the values, and the module's first create call fails validating a code against
  an empty table. Emit the INSERT block for every key this module owns, against the owning
  module's table by its exact name, and say which module owns it. Whichever module owns the
  table, the module that owns the KEY owns the seed.
Reference entity (SRS decided: its own ENT) → an ordinary table per §4; consumers
  hold an FK.
```

### 4.3 Indexes

```
Mandatory : every FK column; every column used in SRS search / list filters (PART B B2);
            every UNIQUE business key. PK indexes are implicit — not duplicated. [G]
```

---

## 5 — XM register (`XM`) — cross-module dependencies

`XM` is the single identifier for every cross-module dependency in the pipeline.
Its types, states, transitions and record fields are the ONE state machine of
`factory.yaml → xm` (rendered in `shared/XM-PROTOCOL.md`) — never a list of this [L:C3-xm-vocabulary]
engine's own. Assigned here; wired by `P3.1`, whose last phase carries one
integration block per edge (shared/XM-PROTOCOL.md §6) — never re-assigned; never touched by [C:C9.9]
the frontend stage. The factory's lifecycle ends at DELIVERED; the states after it belong to the
consumer repository.

The register is ONE named block (data, not prose — `gov.py graph`, `gov.py analyze` C6.10/C6.12 and the gate read it; C0.1 holds it to its schema; `gov.py graph` appends the edges later stages mint):

```yaml name=xm-register
records:
  - {id: XM-CHK-001, type: HARD-FK, target_module: [CODE], target_entity: ENT-[CODE]-[SEQ], traces: [REQ-CHK-[SEQ]], state: [state], contract_ref: "[contract item, when the state needs it]", workaround: "[when the state needs it]", unblock_condition: "[when the state needs it]", column: "[table] · [FK column / access]"}
```

<!-- RENDER:xm:types -->
Types: `HARD-FK` · `SOFT-READ` — no other type exists. [C:C6.10]
<!-- /RENDER:xm:types -->

<!-- RENDER:xm:record -->
| Field | Column / line label | Required |
|---|---|---|
| `id` | `XM id` | always |
| `type` | `Type` | always |
| `target_module` | `Target module` | always |
| `target_entity` | `Target entity` | always |
| `traces` | `Traces` | always |
| `state` | `State` | always |
| `contract_ref` | `Contract` | state `CONTRACTED` |
| `workaround` | `Workaround` | state `DEFERRED` |
| `unblock_condition` | `Unblock condition` | state `DEFERRED` |
<!-- /RENDER:xm:record -->

```
STATE AT THIS STAGE (one of xm.states.order; a gate never opens on DECLARED) [T:xm-gate]
  the target's published contract (platform/contracts/) carries the item   → CONTRACTED, Contract = its CON id
  the target entity exists in the target module's committed artifacts        → READY
  the target is neither contracted nor built                                  → DEFERRED, with Workaround + Unblock condition
  (whatever the state, a HARD-FK is column-only here — §5.1)
TYPES
  HARD-FK    physical FK constraint on the owner's table
  SOFT-READ  application-level read of another module's table, no FK column; the
             "column / access" cell describes the access pattern
SOURCES
  SRS A8 consumed entities (the `module-dependencies` block — type as classified there);
  RULEs that join by code to another module's table; APIs that read another module's data.
  An XM with no SRS A8 origin is an ORPHAN finding. Audit columns are not XMs. [G]
```

### 5.1 Cross-module FK — column only [C:C6.15]

```
For every HARD-FK XM, whatever its state: create the column (with its DBF id), nullable as the
SRS field says, typed EXACTLY as the target's published contract states its identifier type (the [G]
CON entity item — it is a promise, not a guess: a type inferred here is the ambiguity that stops
a later stage), and put ONE pointer on it — in its column comment or on the line beside it:
  -- constraint → XM-CHK-[n]
Do NOT create the constraint, live or commented. Its text is written once, in full, in the
integration block of the backend plan (P3.1 — shared/XM-PROTOCOL.md §6), which the
executor applies when the target is there. A second copy here is the drift this rule removes
(`gov.py analyze` → C6.15 `db-column-only`, C7.28 `patch-single-source`).
```

### 5.2 SOFT-READ handling

```
For every SOFT-READ XM: register it (Type SOFT-READ); add a commentary block:
  -- XM-CHK-[n] SOFT-READ — this module's [service/query] reads [TARGET].[COLUMN]
  -- from [module] without an FK. Rationale: [from SRS]. Risk: changes to [TARGET]
  -- require impact assessment on [affected requirements].
```

---

## 6 — FK classification (every FK is exactly one of these) [G]

```
INTRA-MODULE FK    both tables in this script → constraint in main DDL; DBF on the column; no XM
CROSS-MODULE FK    the target is another module's table → column + pointer, no constraint (§5.1); XM HARD-FK
SOFT-READ          application read → no constraint by design; XM SOFT-READ (§5.2)
```

---

## 7 — `db-script-{mod}.md` — output structure

```
1. HEADER          module · version · dialect oracle19c · schema prefix (or "none") ·
                   identifier transformation (§3) · date · counts
2. DB FIELD TRACEABILITY MATRIX   (§2 — governance documentation, not SQL)
3. XM REGISTER                    (§5)
4. FULL_DATABASE_SCRIPT           (§7.1 — the ONLY place SQL appears)
5. DECISIONS APPLIED              (DEFAULTs + ADR ids, §9)
6. REGISTRY CONTENT               (§10)
```

### 7.1 FULL_DATABASE_SCRIPT — one consolidated executable

Copy-and-run against a clean schema of oracle19c without editing. Not documentation:
a deployable, inside ONE ```sql fence — the only place SQL appears; every DDL reader [C:C6.15]
(`gov.py analyze` C6.15, C7.28, the tables this script creates) reads that fence and nothing else.
Mandatory block order (guarantees zero dependency errors):

```
BLOCK 1   SEQUENCES [G] (only if the SRS needs one; PK generation uses the identity clause)
BLOCK 2   PARENT TABLES (no FK dependencies; lookup/reference tables DDL only) [G]
BLOCK 3   CHILD TABLES (intra-module FK targets already created; chain A → B → C)
BLOCK 4   COMMENTS (table + every column; each column comment cites its DBF id)
BLOCK 5   CONSTRAINTS  5a PK · 5b UNIQUE · 5c CHECK · 5d intra-module FK (parent PK first)
BLOCK 6   TRIGGERS — audit triggers only when an SRS RULE requires them; no PK triggers [G]
BLOCK 7   INDEXES (non-PK)
BLOCK 8   LOOKUP SEED DATA (INSERT with column lists; COMMIT at the end of the block)
BLOCK 9   VIEWS (CREATE OR REPLACE)
BLOCK 10  FUNCTIONS / PROCEDURES (dialect terminator syntax; only if the SRS needs them) [G]
```

```
SYNTAX RULES (dialect-conditional — the dialect's own syntax comes from
profile.stack.db.syntax_map; the rules below hold for any dialect)
  S-1  Every statement ends with the dialect's terminator; no trailing comma before a
       closing parenthesis; every referenced object has its CREATE in this script.
  S-2  Types: only §4.1 syntaxes; not a type from another dialect. [G]
  S-3  Constraints in ALTER TABLE form (PK / FK / UQ / CHK names per §3); FK declared
       after the parent PK exists.
  S-4  No PK-population trigger; no sequence default on a PK column.
  S-5  Seed INSERTs carry a column list; NULL is not the string 'NULL'; COMMIT after DML. [G]
  S-6  No constraint references another module's table — live or commented (§5.1).
  S-7  Schema prefix: all objects qualified, or none — not mixed. [G]
  S-8  No placeholders: no "...", no "[...]" inside SQL — real names and values only. [G]
```

### 7.2 Self-verification before emitting the script

```
SYNTAX        □ S-1 … S-8 hold for every statement
              □ every type appears in §4.1 (or is declared once in the header)
PK STRATEGY   □ every PK follows profile.stack.db.pk_generation (`identity`) — the identity clause on every
                PK column and no PK sequence
ORDER         □ sequences first (if any) · parents before children · PK before FK ·
                lookup DDL before lookup INSERTs · COMMIT after the last INSERT of a block
COMPLETENESS  □ every SRS entity has a table · every SRS field a column (DBF) ·
                every lookup its seed rows · every index present ·
                shared lookup tables not re-created
CROSS-MODULE  □ every HARD-FK XM has its column and its pointer · no constraint,
                live or commented, references another module's table
TRACE         □ every DBF traces to ENT.field + REQ · every XM traces to REQ and to an
                SRS A8 row · no orphan, no dangling id
```

---

## 8 — Governance recovery

```
A module whose script was produced from an incomplete or corrected SRS, or whose
script arrives after downstream artifacts exist:
  1. Re-run this stage on the current SRS (_state/ current state).
  2. Re-run `gov.py analyze` on the affected artifacts (this script, the backend plan's
     alignment manifest, the registries). Findings are resolved at the next pass gate.
  3. XM rows whose target moved (contract published, built, delivered) → the next state along
     shared/XM-PROTOCOL.md §4 — `gov.py graph` raises the resolution event.
No separate audit stage exists; `analyze` is the recovery check.
```

---

## 9 — Ambiguity rule (questions are forbidden here) [T:questions-forbidden]

```
Structural choices the SRS does not settle (normalisation of a repeating group, a
composite vs surrogate key, an index strategy, a precision):
  NON-BREAKING → ADR, CONTINUE with the chosen best practice
      action: adr · then: continue
  BREAKING (contradicts an SRS REQ / ENT, a registered name, or a gated module's
  structure) → ADR status BLOCKED, STOP the pass
      action: adr · status: BLOCKED · then: stop
ADR file : analysis/decisions/CHK/ADR-{MOD}-{seq:03d}.md  (Context · Decision · Consequences · traces · status)
Details  : shared/GOVERNANCE-CORE.md
```

---

## 10 — `registry-db-{mod}.md` — registry content

```
## REGISTRY — P2 — CHK v1
Tables        : table · ENT id · kind · DBF range              (→ structural registry)
XM index      : the ids of the register block above                (→ gov.py graph)
Lookups       : key · seeded values count · owner
Sequences     : last DBF · last XM
Decisions     : ADR ids (+ BLOCKED, if any)
Event         : "P2 completed: CHK v1 — [N] tables, [N] DBF, [N] XM"
Cascade       : none by hand — `gov.py graph` derives the edges targeting CHK and raises
                their resolution events (shared/XM-PROTOCOL.md §5)
```

---

## 11 — Boundaries

```
OWNS      : DBF, XM · the traceability matrix · the XM register · DDL structure,
            naming and datatype governance for this module
DOES NOT  : POL (P0) · US (P0.5) · REQ (P1) · AC (P1) · ENT (P1) · RULE (P1) · CON (P1.5) · API (P3.1) · QR (P3.1) · UXD (P3.2) · SCR (P3.2) · SCR-REQ (P1) · TC (P4) · FEAT (feature)
            · business logic · execution phases · frontend structure (the frontend stage
            does not read this script; it consumes the API document) [G]
```

---

## 12 — Self-check before emitting

- [ ] Every SRS entity → one table; every consumed SHARED entity → FK / XM, not a table.
- [ ] Every column has a DBF id, a type from §4.1, a comment, and traces (ENT.field + REQ).
- [ ] Every cross-module reference is exactly one FK class (§6) and, unless intra-module, an XM row traced to REQ + SRS A8. [G]
- [ ] Every HARD-FK XM has its column and its pointer `constraint → XM-…`; no cross-module constraint, live or commented.
- [ ] Script block order 1–10 respected; §7.2 checklist passed; script is copy-and-run for oracle19c.
- [ ] PK generation matches `profile.stack.db.pk_generation` = `identity` for **every** table (§4) — no second strategy anywhere.
- [ ] Every RULE that maps to a constraint is present (UNIQUE / CHECK) and named per §3.
- [ ] Every DEFAULT / ADR listed under Decisions applied; no BLOCKED ADR unless the pass stopped.
- [ ] No question raised; sequences continuous.


---
# INPUTS (generated current state)

<<<INPUT: srs>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: registry-srs>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: contract>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: contracts>>>
<<<contract-doc.md>>>
# CONTRACT — Document Access (DOC) — outbound promise
══════════════════════════════════════════════════════════════════
Module : DOC   Version : v1   Profile : aias
Inputs : srs-doc.md, registry-srs-doc.md
Items  : CON 5 (lookup promises 2 · operations 3)
Read by: CHK, INT, RPT (in-process DOC interface — profile `conventions.module_interface: in_process`)
══════════════════════════════════════════════════════════════════

DOC is tier 1 (depends on REG only). Its single entity, the Uploaded Document (ENT-DOC-001), is PRIVATE and is not promised: no other module stores, references or reads it — INT reaches it only through the handover operation (CON-DOC-003) and CHK only through the fetch and end operations (CON-DOC-004, CON-DOC-005). Operations name no path and no verb; P3.1 chooses the implementation and states `Honours: CON-…` on it.

Identifier rule (profile `conventions.identifiers`): every identifier of the service's own schema is `NUMBER(19)` generated by identity. DOC receives the Check's identifier (`NUMBER(19)`, owned by RPT) as a value and never as a foreign key; host identifiers (the request number) are passed as strings exactly as the host sent them.

## Lookup promises

### CON-DOC-001 — Document read status and unreadable reason: the closed outcome codes of a document
Entity    : — (closed enums carried by value on the transient Document Outcome; no DOC table) — key columns: DOCUMENT_READ_STATUS (closed: READ, MISSING, UNREADABLE), UNREADABLE_REASON (closed: OUTSIDE_STORAGE_ROOT, NOT_FOUND, TOO_LARGE, UNSUPPORTED_FORMAT, READING_FAILED, OUT_OF_TIME, SOURCE_QUERY_FAILED, MODEL_NOT_PERMITTED) · identifier type: none — a consumer stores the code as `VARCHAR2(30 CHAR)`
Promise   : every Document Outcome carries exactly one read status from this list; every UNREADABLE outcome carries exactly one reason from this list and a detail text; READ and MISSING outcomes carry no reason. RPT stores the codes it receives through CHK with no runtime read of DOC (ADR-DOC-002, ADR-DOC-007). Adding a code is a new DOC version.
Traces    : REQ-DOC-034, REQ-DOC-035, REQ-DOC-036

### CON-DOC-002 — Fetch mode: the closed list of ways a document is obtained
Entity    : — (profile closed enum carried by value on ENT-REG-002.fetchMode) — key columns: FETCH_MODE (closed: path, blob, manual) · identifier type: none — a consumer stores the code as `VARCHAR2(10 CHAR)`
Promise   : no fourth fetch mode exists; every Document Outcome carries its source mode from this list, equal to the fetch mode of the Check's service package version (REQ-DOC-001). REG, CHK and RPT use the values directly, with no runtime read of DOC (ADR-REG-005).
Traces    : REQ-DOC-001, REQ-DOC-038

## Operations

### CON-DOC-003 — Hand over a file uploaded for a Check (INT → DOC)
Signature : handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, fileContent) → {uploadedDocumentId, documentType, fileName, fileSize, oversized, notice (present only when oversized — RULE-DOC-005 message)} · errors: fetch mode not manual (RULE-DOC-001), document type not of the service (RULE-DOC-002), incomplete upload (RULE-DOC-003), service package version not found (REQ-DOC-003); each error carries the RULE message for INT to show as a ProblemDetail
Entity    : ENT-DOC-001
Notes     : INT passes the service code and version number of the Check with every upload (ADR-DOC-006). An oversized file is accepted as a record without content and later reported UNREADABLE with reason TOO_LARGE. An Uploaded Document is never changed; a corrected file is a new handover (REQ-DOC-023). No caller authentication in this version (raw-idea A2).
Traces    : ENT-DOC-001, REQ-DOC-017, REQ-DOC-020, REQ-DOC-021, REQ-DOC-022, REQ-DOC-043

### CON-DOC-004 — Fetch and read the documents of a Check (CHK → DOC)
Signature : fetchDocuments(checkId, requestNumber, serviceCode, versionNumber) → list of Document Outcome {documentType, sourceMode (CON-DOC-002), readStatus (CON-DOC-001), reason (CON-DOC-001, UNREADABLE only), detail, content (READ only — text, or tables of rows and columns for a spreadsheet)} · errors: service package version not found (REQ-DOC-003)
Entity    : ENT-DOC-001
Notes     : exactly one outcome per fetched or uploaded document plus one MISSING outcome per required document type that no document carries (REQ-DOC-034, REQ-DOC-035); a failure on one document never stops the others (REQ-DOC-037); a failed or over-limit document source query yields UNREADABLE / SOURCE_QUERY_FAILED for every required document type, never an error (REQ-DOC-039); content is data in its own field, apart from any instruction (REQ-DOC-045). The call honours the Check's timeout (REQ-DOC-040). DOC keeps no fetched document and no content after it returns (REQ-DOC-055). Whether an outcome blocks `COMPLIANT` is CHK's decision (ADR-DOC-002).
Traces    : REQ-DOC-001, REQ-DOC-002, REQ-DOC-018, REQ-DOC-034, REQ-DOC-035, REQ-DOC-036, REQ-DOC-037, REQ-DOC-038, REQ-DOC-039, REQ-DOC-045

### CON-DOC-005 — End a Check (CHK → DOC)
Signature : endCheck(checkId) → {deletedCount} · errors: none (a Check with no Uploaded Document answers 0)
Entity    : ENT-DOC-001
Notes     : CHK calls it on every ending path of a Check — report stored, failed or timed out (ADR-DOC-008). Every Uploaded Document of the Check is hard-deleted; repeating the call is harmless.
Traces    : ENT-DOC-001, REQ-DOC-054

## Stability
All 5 items are ADDITIVE in v1. Changing or removing one a consumer depends on requires a BREAKING version with an ADR.
══════════════════════════════════════════════════════════════════

<<<contract-reg.md>>>
# CONTRACT — Service Registry (REG) — outbound promise
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias
Inputs : srs-reg.md, registry-srs-reg.md
Items  : CON 13 (entity promises 6 · operations 7)
Read by: DOC, CHK, RPT, INT (in-process REG interface — profile `conventions.module_interface: in_process`)
══════════════════════════════════════════════════════════════════

REG is the root of the platform graph (tier 0). Every item below is something DOC, CHK, RPT or INT may build against before REG is built. Private data (the Load Result rows, ENT-REG-006) and internal load/activation operations are not promised. Operations name no path and no verb; P3.1 chooses the implementation and states `Honours: CON-…` on it.

Identifier rule (profile `conventions.identifiers`): every identifier of the service's own schema is `NUMBER(19)` generated by identity. Consumers that record a service package version (RPT, through CHK — G11) store the business key `serviceCode` + `versionNumber` as values, not as a foreign key, so that stored reports outlive any change of identifiers.

## Entity promises

### CON-REG-001 — Service Package: the registry of service codes
Entity    : ENT-REG-001 — key columns: serviceCode (business key, `VARCHAR2(100 CHAR)`, unique, immutable), servicePackageId (identifier) · identifier type: NUMBER(19)
Promise   : a service code is valid only if REG holds it; `available` tells whether new Checks may use it (RULE-REG-016). Service codes are never hardcoded by a consumer (POL-REG-013).
Traces    : ENT-REG-001, REQ-REG-001, REQ-REG-006, REQ-REG-010

### CON-REG-002 — Service Package Version: an immutable, resolvable version
Entity    : ENT-REG-002 — key columns: serviceCode + versionNumber (business key; versionNumber `NUMBER(10)`), servicePackageVersionId (identifier) · identifier type: NUMBER(19)
Promise   : the content under a (serviceCode, versionNumber) pair never changes and is never deleted (ADR-REG-003); a consumer that records the pair can always resolve it (CON-REG-009).
Traces    : ENT-REG-002, REQ-REG-019, REQ-REG-021, REQ-REG-026

### CON-REG-003 — Service Query: the queries of a version, exactly as written
Entity    : ENT-REG-003 — key columns: servicePackageVersionId + queryName (business key; queryName `VARCHAR2(100 CHAR)`), serviceQueryId (identifier) · identifier type: NUMBER(19)
Promise   : each query is one SELECT statement whose only parameter is the named bind parameter of the version's `inputName`, and names a connection by `connectionName` (RULE-REG-006, RULE-REG-007). A consumer binds the request number to that parameter; it never edits the SQL text.
Traces    : ENT-REG-003, REQ-REG-029, REQ-REG-032, REQ-REG-033

### CON-REG-004 — Required Document: the document types a version requires
Entity    : ENT-REG-004 — key columns: servicePackageVersionId + documentType (business key; documentType `VARCHAR2(100 CHAR)`, lookup DOCUMENT_TYPE, open), requiredDocumentId (identifier) · identifier type: NUMBER(19)
Promise   : the documentType values equal the host's document type values as the document source query returns them (e.g. TRANSCRIPT, ID_CARD for `scholarship-request`).
Traces    : ENT-REG-004, REQ-REG-037, REQ-REG-057

### CON-REG-005 — Connection: a named, read-only data source of this environment
Entity    : ENT-REG-005 — key columns: connectionName (business key, `VARCHAR2(100 CHAR)`, unique), connectionId (identifier) · identifier type: NUMBER(19)
Promise   : every registered connection is declared read-only (RULE-REG-015); `connectionType` is `mcp` or `jdbc` (lookup CONNECTION_TYPE, closed); `blob` documents are always read through a `jdbc` connection (RULE-REG-010). REG holds a credential reference, never the credential (REQ-REG-054).
Traces    : ENT-REG-005, REQ-REG-046, REQ-REG-054, REQ-REG-055

### CON-REG-006 — Lookups REG masters
Entity    : ENT-REG-001 — key columns: SERVICE_CODE (open, values from ENT-REG-001.serviceCode), CONNECTION_TYPE (closed: mcp, jdbc), DOCUMENT_TYPE (open; seeded TRANSCRIPT, ID_CARD) · identifier type: NUMBER(19)
Promise   : consumers read these values from REG and never redefine them; the fetch mode values (path, blob, manual) are the profile's closed enum carried on ENT-REG-002.fetchMode (ADR-REG-005).
Traces    : ENT-REG-001, ENT-REG-004, ENT-REG-005, REQ-REG-036, REQ-REG-037

## Operations

### CON-REG-007 — Supply the current service package of a service
Signature : getCurrentServicePackage(serviceCode) → read-only package {serviceCode, versionNumber, serviceKnowledge (whole, unaltered), inputName, queries [queryName, connectionName, sqlText], fetchMode, document source {documentSourceQueryName, documentTypeColumn, documentPathColumn | documentContentColumn}, requiredDocumentTypes} · errors: service not available (unknown or withdrawn code — RULE-REG-016), connection not activated (RULE-REG-017)
Entity    : ENT-REG-002
Notes     : the service knowledge is a separate part from the queries and document settings (REQ-REG-018, REQ-REG-030); the package carries no approval API definition (REQ-REG-044) and no request data (REQ-REG-059); it is immutable for the caller (REQ-REG-060).
Traces    : REQ-REG-024, REQ-REG-027, REQ-REG-029, REQ-REG-012, REQ-REG-053

### CON-REG-008 — List the available services
Signature : listServices() → list of {serviceCode, versionNumber, fetchMode, requiredDocumentTypes, approvalEnabled} · errors: none
Entity    : ENT-REG-001
Traces    : REQ-REG-013

### CON-REG-009 — Resolve a stored version
Signature : getServicePackageVersion(serviceCode, versionNumber) → read-only full version {serviceKnowledge, serviceDefinition, queries, fetchMode, requiredDocumentTypes, approvalEnabled} · errors: not found (no such service code or version)
Entity    : ENT-REG-002
Traces    : REQ-REG-025

### CON-REG-010 — Read one service's current version summary
Signature : getService(serviceCode) → {serviceCode, versionNumber, fetchMode, requiredDocumentTypes, approvalEnabled} · errors: not found (RULE-REG-016)
Entity    : ENT-REG-001
Traces    : REQ-REG-014, REQ-REG-015

### CON-REG-011 — Supply a connection by name
Signature : getConnection(connectionName) → {connectionName, connectionType, endpoint, queryTool, dialect, credentialReference, limitedToViews} · errors: not found (connection not activated in this environment)
Entity    : ENT-REG-005
Traces    : REQ-REG-048

### CON-REG-012 — Supply the approval API of a version to the Employee Decision path
Signature : getApprovalApi(serviceCode, versionNumber) → {approvalEnabled, approvalApi (present only when enabled)} · errors: not found (no such service code or version)
Entity    : ENT-REG-002
Notes     : only INT's Employee Decision operation may call this item (raw idea §12; G2; AIAS-4). No other consumer, and never the LLM, receives the approval API definition.
Traces    : REQ-REG-044, REQ-REG-045

### CON-REG-013 — Check whether a service code is available
Signature : isServiceAvailable(serviceCode) → flag · errors: none (an unknown code answers false)
Entity    : ENT-REG-001
Traces    : REQ-REG-012, REQ-REG-001

## Stability
All 13 items are ADDITIVE in v1. Changing or removing one a consumer depends on requires a BREAKING version with an ADR.
══════════════════════════════════════════════════════════════════

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
# BRIEF — stage `P3.1` (Backend Execution Plan) · module CHK · v1 · profile `aias`

Lane `analysis` · implementer ['claude:opus'] · effort high · round 1

## Rules that bind this run
- Questions: **forbidden**. A `[QUESTION]` block is refused. Ambiguity → ADR in `analysis/decisions/CHK/` (`ADR-{MOD}-{seq:03d}.md`): non-breaking → continue; breaking → status BLOCKED and stop.
- Owns IDs: API, QR — ID grammar `{prefix}-{MOD}-{seq}` (seq width 3); never re-number, never restart a sequence.
- ADRs continue the module's stream: the next free id is `ADR-CHK-001`; an existing ADR is never rewritten under its id.
- Read only what this brief contains (generated current state); never open version folders yourself.
- Write exactly these files (complete files):
- `governance-shared/analysis/modules/CHK/P3_1/backend-execution-plan-chk.md`
- `governance-shared/analysis/modules/CHK/P3_1/registry-exec-be-chk.md` (registry)
- `governance-shared/analysis/modules/CHK/P3_1/api-spec-chk.yaml`
- Write the files directly into the project checkout (you are the operator); the orchestrator reads them on `--complete`.

## Contracts checked by `gov.py analyze` after this stage
- **C6** SRS + database → backend execution plan: C6.1 exists {'artifact': 'db-script'} [CRITICAL]; C6.2 traces {'from': 'DBF', 'to': ['REQ', 'ENT'], 'min': 1} [MAJOR]; C6.3 traces {'from': 'XM', 'to': ['REQ'], 'min': 1} [MAJOR]; C6.4 ids-owned {'stage': 'P2'} [CRITICAL]; C6.5 registry-agree {'artifact': 'db-script', 'registry': 'registry-db', 'kinds': ['DBF', 'XM']} [MAJOR]; C6.6 orphans {'kind': 'ENT', 'referenced_by': ['DBF'], 'min': 1} [MAJOR]; C6.7 no-questions {'stage': 'P2'} [CRITICAL]; C6.8 ids-continue {'stage': 'P2'} [CRITICAL]; C6.9 data-source {'kind': 'RULE', 'label': 'Data source', 'resolves_to': ['ENT'], 'deferral': 'DEFERRED', 'bound_in': 'db-script'} [CRITICAL]; C6.10 xm-record-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.11 xm-tier-rule {} [CRITICAL]; C6.12 xm-transition-valid {'artifact': ['db-script', 'registry-db']} [MAJOR]; C6.13 graph-acyclic {} [CRITICAL]; C6.14 graph-target-known {} [CRITICAL]; C6.15 db-column-only {'artifact': 'db-script', 'register': ['db-script', 'registry-db']} [MAJOR]
- **C7** backend execution plan → split: C7.1 markers {'artifact': 'backend-execution-plan', 'track': 'backend', 'plan': 'exec'} [CRITICAL]; C7.2 traces {'from': 'backend-execution-plan', 'blocks': ['PHASE', 'SUB', 'API', 'XM'], 'min': 1} [MAJOR]; C7.3 traces {'from': 'API', 'to': ['REQ', 'DBF'], 'min': 1} [MAJOR]; C7.4 registry-agree {'artifact': 'backend-execution-plan', 'registry': 'registry-exec-be', 'kinds': ['API', 'QR']} [MAJOR]; C7.5 registry-agree {'artifact': 'backend-execution-plan', 'registry': 'registry-db', 'kinds': ['XM'], 'direction': 'registry→artifact'} [MAJOR]; C7.5b registry-agree {'artifact': 'backend-execution-plan', 'registry': 'registry-db', 'kinds': ['XM'], 'direction': 'artifact→registry'} [MAJOR]; C7.6 orphans {'kind': 'REQ', 'referenced_by': ['API', 'DBF'], 'min': 1} [MAJOR]; C7.7 ids-owned {'stage': 'P3.1'} [CRITICAL]; C7.8 no-questions {'stage': 'P3.1'} [CRITICAL]; C7.9 ids-continue {'stage': 'P3.1'} [CRITICAL]; C7.10 value-agreement {'kind': 'DBF', 'binding': 'db-script', 'block': 'dbf-matrix', 'field': 'column', 'against': ['backend-execution-plan']} [CRITICAL]; C7.11 code-format {'artifact': ['backend-execution-plan'], 'format': 'stack.backend.api.error_code_format', 'statuses': 'stack.backend.api.http_statuses'} [MAJOR]; C7.12 xref-resolve {'artifact': ['backend-execution-plan', 'registry-exec-be']} [CRITICAL]; C7.13 refs-exist {'kind': 'ADR', 'dir': 'decisions', 'file_pattern': 'adr_file'} [CRITICAL]; C7.14 paths-resolve {'files': ['manifest_file']} [CRITICAL]; C7.17 xref-surface {'artifact': ['backend-execution-plan'], 'locator': 'stack.backend.api.base_path', 'kinds': ['API']} [MAJOR]; C7.16 forward-refs {'spec': 'forward_fields', 'when': 'profile.forward_fields'} [MAJOR]; C7.22 orphans {'kind': 'QR', 'referenced_by': ['API'], 'min': 1} [MAJOR]; C7.21 bootstrap-complete {'artifact': 'backend-execution-plan', 'block': 'bootstrap', 'spec': 'bootstrap_data', 'when': 'profile.bootstrap_data'} [MAJOR]; C7.23 operation-resolves {'artifact': 'backend-execution-plan', 'resolves_to': 'API', 'actions': 'conventions.security_model.actions', 'declared': {'source': 'srs', 'kind': 'SCR-REQ', 'verbatim': True, 'subject_kind': 'ENT', 'label': 'plan_vocabulary.screen_operations_line', 'subjects_label': 'plan_vocabulary.screen_subjects_line', 'separators': 'plan_vocabulary.operation_separators', 'exclusions': 'plan_vocabulary.exclusion_reasons'}} [MAJOR]; C7.20 operation-resolves {'artifact': 'backend-execution-plan', 'resolves_to': 'API', 'actions': 'conventions.security_model.actions', 'declared': {'kind': 'ENT', 'label': 'plan_vocabulary.operations_line', 'separators': 'plan_vocabulary.operation_separators'}, 'matrix': {'block': 'permission-matrix', 'permission': 'conventions.security_model.permission_pattern'}} [MAJOR]; C7.19 required-writer {'kind': 'DBF', 'declared_in': 'db-script', 'required_marker': 'stack.db.required_marker', 'writer_kind': 'API', 'writer_in': 'backend-execution-plan', 'writer_labels': ['plan_vocabulary.request_line', 'plan_vocabulary.effect_line'], 'exempt_names': 'stack.db.naming.audit_fields', 'exempt_pattern': 'stack.db.naming.pk_pattern', 'exclusions': 'plan_vocabulary.exclusion_reasons'} [MAJOR]; C7.18 count-agrees {'spec': 'declared_totals', 'block': 'totals', 'when': 'profile.declared_totals'} [MAJOR]; C7.15 verdict-agrees {'artifact': ['backend-execution-plan'], 'spec': 'self_check', 'when': 'profile.self_check'} [CRITICAL]; C7.24 contract-honoured {'honoured_in': ['backend-execution-plan', 'registry-exec-be']} [CRITICAL]; C7.25 intc-last {'artifact': 'backend-execution-plan', 'track': 'backend'} [MAJOR]; C7.26 xm-block-complete {'artifact': 'backend-execution-plan', 'track': 'backend'} [CRITICAL]; C7.27 no-foreign-scatter {'artifact': 'backend-execution-plan', 'track': 'backend'} [MAJOR]; C7.28 patch-single-source {'artifact': 'backend-execution-plan', 'track': 'backend', 'db': 'db-script'} [MAJOR]; C7.29 api-spec-agree {'artifact': 'backend-execution-plan', 'spec': 'api-spec', 'kind': 'API'} [MAJOR]; C7.30 api-spec-errors {'artifact': 'backend-execution-plan', 'spec': 'api-spec'} [MAJOR]; C7.31 api-spec-valid {'spec': 'api-spec', 'paging': 'stack.backend.api.paging_params', 'auth': 'stack.backend.api.auth'} [CRITICAL]
- **C8** API document (the backend plan's endpoints) → frontend: C8.1 exists {'artifact': 'api-spec'} [CRITICAL]

## Blocks this stage emits — each a ```yaml name={name} fence, held to its schema by C0.1 (`gov.py analyze`)
- `error-catalog` in `backend-execution-plan` — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"error-catalog","description":"P3.1 backend plan §7: every runtime error code the module can raise — its rule (or PLATFORM-STD), the endpoints that raise it, its HTTP status, its trigger and its message per language (factory.yaml → api_spec.catalog)","type":"object","required":["rows"],"additionalProperties":false,"properties":{"rows":{"type":"array","items":{"type":"object","required":["code","http","api"],"additionalProperties":false,"properties":{"code":{"type":"string","minLength":1},"rule":{"type":"string"},"api":{"type":"array","items":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}},"http":{"type":"integer","minimum":100,"maximum":599},"trigger":{"type":"string"},"messages":{"type":"object","additionalProperties":{"type":"string"}},"adr":{"type":"string"}}}}}}
  ```
- `totals` in `backend-execution-plan` (optional) — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"totals","description":"P3.1 backend plan index: the totals the plan states, one per id kind — a count the plan asserts and count-agrees holds to the rows it heads","type":"object","propertyNames":{"pattern":"^[A-Z][A-Z0-9-]*$"},"additionalProperties":{"type":"integer","minimum":0}}
  ```
- `permission-matrix` in `backend-execution-plan` (optional) — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"permission-matrix","description":"P3.1 backend plan R5: one row per screen — the entity, the endpoints serving it, and per action whether it is granted and by which permission name (analyze: operation-resolves)","type":"object","required":["rows"],"additionalProperties":false,"properties":{"rows":{"type":"array","items":{"type":"object","required":["screen","entity","api","actions"],"additionalProperties":false,"properties":{"screen":{"type":"string","minLength":1},"entity":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"},"api":{"type":"array","items":{"type":"string","pattern":"^[A-Z][A-Z0-9-]*-[A-Z][A-Z0-9]*-\\d{3,}$"}},"actions":{"type":"object","additionalProperties":{"type":"object","required":["granted"],"additionalProperties":false,"properties":{"granted":{"type":"boolean"},"permission":{"type":"string"}}}}}}}}}
  ```
- `bootstrap` in `backend-execution-plan` (optional) — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"bootstrap","description":"P3.1 backend plan R5: the rows that must exist before any endpoint of the module can succeed — each named, each with who produces it (analyze: bootstrap-complete)","type":"object","required":["rows"],"additionalProperties":false,"properties":{"rows":{"type":"array","items":{"type":"object","required":["kind","name","source"],"additionalProperties":false,"properties":{"kind":{"type":"string","minLength":1},"name":{"type":"string","minLength":1},"source":{"type":"string","minLength":1},"note":{"type":"string"}}}}}}
  ```
- `self-check` in `backend-execution-plan`, `frontend-execution-plan` (optional) — JSON Schema, verbatim:
  ```json
  {"$schema":"https://json-schema.org/draft/2020-12/schema","title":"self-check","description":"an execution plan's verdict about itself — written by the orchestrator from the analyze report, never by the author (analyze: verdict-agrees)","type":"object","required":["findings","clean"],"additionalProperties":false,"properties":{"findings":{"type":"integer","minimum":0},"clean":{"type":"boolean"},"examined_nothing":{"type":"array","items":{"type":"string"}}}}
  ```

---
# ENGINE
```
ENGINE        : P3.1 — Backend Execution Plan
PASS / TRACK  : pass 1 · track backend · lane analysis · questions forbidden
MODULE        : CHK · v1 · profile aias (Request Verification Service)
READS         : srs · db-script · registry-srs · registry-db · contract · contracts?   (all from _state/ — generated current state)
PRODUCES      : backend-execution-plan-chk.md · registry-exec-be-chk.md · api-spec-chk.yaml
OWNS IDS      : API, QR
NEXT          : P3.2   (the orchestrator owns the completion protocol — shared/GOVERNANCE-CORE.md)
BOUNDARY      : analysis-only — this engine writes specifications, not code [G]
```

# Backend Execution Plan — engine reference

## 0. Position and authority

This engine turns the module's **functional truth** (the SRS) and **structural truth** (the
db-script) into one agent-ready backend execution plan. It reads its inputs from
`_state/` only (the generated current state — never a raw `v{N}/` folder), and it [T:brief-state-only]
never invents business meaning, tables, columns, rules or IDs. [C:C7.7]

- Upstream artifacts govern. A conflict between this plan and the SRS or db-script is a
  **finding**, never a silent resolution. [C:C7.10]
- Questions are `forbidden` at this stage. Ambiguity is resolved by the rule in
  `factory.yaml → ambiguity` (see §12): non-breaking → ADR (`ADR`) and
  `continue`; breaking → ADR with status
  `BLOCKED` and `stop`.
- Every block in the plan carries `traces=` to the upstream IDs it implements (§6.0). The
  traceability matrix built by `gov.py analyze` must be CLEAN before the pass gate opens.
- The plan is the **sole backend input** of the implementation agent. The frontend stage and
  the test stage read `api-spec-chk.yaml` (§7.5) — the API document this stage derives from the
  plan — never this plan's prose and never a delivered backend; what the implementation later [C:C9.5]
  publishes is held to that document by `api-verify`.

**Delta versions** (v2+) emit only ADDED / MODIFIED / REMOVED blocks plus [C:C12.2]
`change-manifest.md` against the baseline in `_state/`; IDs
continue their sequence and are never renumbered. Rules: shared/VERSIONING.md. [C:C7.9]

## 1. Inputs

| Input | Read from | Use |
|---|---|---|
| `srs` | `_state/current-srs.md` | authoritative functional truth — REQ/AC/ENT/RULE, screens, permissions, lookup keys |
| `db-script` | `_state/current-db-script.md` | authoritative structural truth — tables, columns (DBF), constraints, XM register |
| `registry-srs` | `_state/current-registry-srs.md` | ID ranges already assigned, shared entities, existing lookups, module prefix |
| `registry-db` | `_state/current-registry-db.md` | ID ranges already assigned, shared entities, existing lookups, module prefix |
| `contract` | `_state/current-contract.md` | authoritative structural truth — tables, columns (DBF), constraints, XM register |
| `contracts?` | `_state/current-contracts?.md` | authoritative structural truth — tables, columns (DBF), constraints, XM register |
| `analysis/domain/` steering + `profile.knowledge.files` | `profiles/aias/knowledge/raw-idea.md` | primary sources cited when a best-practice choice must be made (§12) |

Business policies are not read directly: client policies are embedded in `RULE-*` inside
the SRS. A RULE sourced from a client policy is never resolved unilaterally — a conflict is a [T:blocked-adr]
breaking ambiguity (§12).

If the db-script is absent the run is **GOVERNANCE REDUCED**: declare it in the plan header,
produce a functional-only plan, mark every DB binding `PENDING`, and record an ADR. Never
downgrade silently.

## 2. Mandatory extraction and binding (§2A)

### 2A.0 The fundamental rule

Before writing any phase content, extract and **bind** every concrete value from the inputs.
A plan containing a placeholder (`[TABLE_NAME]`, `[LOOKUP_KEY]`, "uses a sequence",
"see SRS") is incomplete and fails the alignment self-check (§9).

```
NO-INVENTION RULE
  Every table, column, constraint, index and PK-generation object used anywhere in the plan
  MUST exist in the db-script and be cited by its DBF-* (or the exact object name the [C:C7.10]
  db-script declares). Base fields (audit, flag, PK) come from the db-script — not from
  memory, not from templates. Naming conventions are read from the profile:
    flag suffix   : (profile.stack.db.naming.flag_suffix — not declared)
    audit fields  : createdAt, updatedAt
    PK pattern    : {entity}Id
    PK generation : `identity` (profile.stack.db.pk_generation)
    target dialect: oracle19c (profile.stack.db.target_dialect)
```

### 2A.1 Pre-generation extraction table

Emit this table first in the run (it is not part of the plan file; it is the working set
every phase binds from):

```
PRE-GENERATION EXTRACTION — CHK v1
── FROM srs ──────────────────────────────────────────────────────────────
ENTITIES      ENT-CHK-<seq> │ exact name │ kind ∈ config | transactional
REQUIREMENTS  REQ-CHK-<seq> │ EARS text  │ its AC-CHK-<seq> list (Given/When/Then)
RULES         RULE-CHK-<seq> │ scope ENT │ trigger │ statement │ message per language (en, ar) │ source
SCREENS       every screen entry the SRS declares │ type │ owning ENT
PERMISSIONS   the SRS permission matrix (roles × screens × actions)
LOOKUPS       every lookup key the SRS names, exactly as written — rule: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded. [G]
── FROM db-script ────────────────────────────────────────────────────────
TABLES        ENT → exact table name
PK GENERATION exact object the db-script declares per table — strategy `identity`: the identity clause as written for oracle19c
COLUMNS       exact column name │ DBF-CHK-<seq> │ declared type │ null │ default
CONSTRAINTS   exact FK / UNIQUE / CHECK constraint names ; INDEXES exact names
XM            XM-CHK-<seq> │ type │ local column (DBF) │ target module · target ENT │ state │ contract item
              (every one becomes ONE block of `CROSS-MOD` — R7; nowhere else)
── FROM registries ───────────────────────────────────────────────────────
SHARED ENTITIES consumed (owner module, reached via which XM) — not redeclared [G]
EXISTING LOOKUP KEYS (reuse — do not create a duplicate) [G]
ID RANGES already used for API, QR (continue the sequence)
──────────────────────────────────────────────────────────────────────────
Any row that cannot be filled → §2A.3.
```

### 2A.2 Binding rules

| Binding | Rule | Forbidden → Required |
|---|---|---|
| PK generation | every PK reference names the exact object from the db-script — strategy `identity` [G] | "auto-generated" → the exact identity clause as declared |
| Column names | every field reference cites the exact column + `DBF-*`; the implementer maps property → column through the DB Alignment Manifest (§4) | a camelCase invention → `DBF-*` lookup |
| Rule text | every RULE cited in a phase carries its full statement, trigger and message in every language (en, ar) — the plan is self-contained | "applies RULE-… see SRS" → full text inline |
| Lookup keys | the exact key string from the SRS, confirmed against the db-script column that stores it; endpoint per the base path `/api/v1/{resource}` | a parameter placeholder → the literal key |
| Endpoints | every path is an instance of `/api/v1/{resource}`; verbs mean `POST`=create (start a check, upload documents, record a decision), `GET`=read | an ad-hoc path → the base-path pattern |

### 2A.3 Extraction failure

A value that cannot be confirmed from the inputs is not invented: [G]

| Case | Action |
|---|---|
| SRS entity has no table in the db-script | mark the entity `PENDING DB` in the plan (GOVERNANCE REDUCED for that entity) + ADR |
| Lookup key / message text / business-code format missing | mark the field `PENDING` with the ADR id; the Error Catalog row carries the ADR id instead of text |
| PK generation object missing for a table | flag in the data phase; QR entry notes `generation: not confirmed`; ADR |
| Two upstream sources contradict | breaking ambiguity → ADR `BLOCKED`, run stops (§12) |

## 3. Plan Index

The plan opens with an index — one table per element family, every row bound from §2A.1:

```
EXECUTION PLAN INDEX — CHK v1 — backend-execution-plan-chk.md
Profile: aias · dialect: oracle19c · framework: profile.stack.backend.framework
Open ADRs: <n> — decisions/CHK/

ENTITY REGISTRY   ENT-*  │ name │ table │ business code (if any) │ operations
FIELD REGISTRY    DBF-*  │ property │ read-only? │ ENT-*
API REGISTRY      API-*  │ operation │ verb │ path │ traces (REQ-*, DBF-*)
RULE REGISTRY     RULE-* │ name │ scope │ ENT-* │ message in every language ✓/✗
SCREEN REGISTRY   screen │ type │ ENT-* │ permission names
QRC SUMMARY       QR-*   │ operation │ phase │ ENT-*         (agent reference only — §5) [G]
DB ALIGNMENT      see manifest (§4) — ALIGNED ✓ / issues: <n>
INTEGRATION       <n> edges — one block each in `CROSS-MOD`, the last phase (R7)
SECURITY          <n> screens × <n> roles
```

The totals the profile asks this plan to state are ONE block (data, not prose — `gov.py analyze` →
`count-agrees` holds every count to the rows it heads; C0.1 holds the block to its schema): [C:C7.18]

```yaml name=totals
DBF: <the number of distinct `DBF-*` ids in `db-script`>
XM: <the number of distinct `XM-*` ids in `db-script`>
API: <the number of distinct `API-*` ids in `backend-execution-plan`>
QR: <the number of distinct `QR-*` ids in `backend-execution-plan`>
```
Count the rows and state that number once, here. A hand-counted total was the smallest
completeness defect this factory shipped and the one nothing in it counted.

## 4. DB Alignment Manifest

The manifest is the canonical binding between plan fields and db-script fields. It contains
**only** these columns — column names, DB types and SRS references are *sourced by lookup* [C:C7.10]
from the db-script, never reproduced here (duplicating them is a contract violation — [C:C7.10]
shared/ARTIFACT-CONTRACTS.md):

```
DB ALIGNMENT MANIFEST — CHK v1
DBF-*            │ ENT-*          │ plan property │ plan type │ XM-* (if FK crosses modules) │ status
DBF-CHK-001 │ ENT-CHK-001 │ <property>    │ <type>    │ —                            │ ✓
DBF-CHK-007 │ ENT-CHK-001 │ <property>    │ <type>    │ XM-CHK-001               │ ✓
Legend  ✓ aligned · ✗ type mismatch (finding)
A cross-module column is aligned like any other: the id is the whole reference — the
constraint behind it, and what it waits for, are that edge's block in `CROSS-MOD` (R7).
Derived / computed properties (no DBF) are listed with DBF = "— (derived)" and an ADR id.
A required column that no endpoint writes carries its reason on the row (system-generated · derived · DEFERRED) — see R3 and `gov.py analyze` → `required-writer`.
```

## 5. Query Reference Catalog (QR-*)

The QRC expresses the **retrieval and persistence intent** of every repository operation as
pseudo-SQL. It is a logical specification, not executable code: the implementer rewrites [G]
every entry with the real entity classes, mapped property names and the project's query
strategy. Copy-pasting a QR entry into production code is a violation.

- Format: `QR-CHK-{seq}` (3-digit sequence, continuous across the module).
- Assigned while writing the data and service phases; every API with a DB operation cites its QR, **and** every QR is cited by ≥1 API — the catalog is checked in both directions (C7.22).
- Ordering / paging use the profile's envelope: (profile.stack.backend.api.paging — not declared); responses are wrapped in (profile.stack.backend.api.envelope — not declared).

```
QR-CHK-<seq> — <operation name>
Phase        : <p.key of the phase that defines it>
API          : API-CHK-<seq> — the endpoint that reaches this query (a query reached only through [C:C7.22]
               another QR names the `API-*` at the head of that chain). A QR no `API-*` block cites is a
               query nobody runs: `gov.py analyze` → `orphans` (C7.22)
Entity       : ENT-CHK-<seq>
Operation    : FIND_ONE | FIND_ALL | FIND_BY_CRITERIA | SAVE | UPDATE | DELETE | COUNT | EXISTS | NATIVE | AGGREGATE
Intent       : <what business question this answers / what it must return or change>
Logical spec : SELECT … FROM <exact table> [JOIN <table> ON …] WHERE <conditions from RULE-*> [ORDER BY …] [page/size]
Join         : NONE | required — ADR-<id> (why)
Transaction  : READ_ONLY (reads) | READ_WRITE (writes) | REQUIRES_NEW — ADR-<id> if non-default
Locking      : NONE | <the read that must not be repeatable>: a query whose result is decided on
               and then written back says here what stops a second caller deciding on the same
               result. A read-then-write with no answer on this line is a race, written down
Pagination   : YES (per profile) | NO
Filters      : <field: EXACT | LIKE | DATE_RANGE | SET>
Result shape : full entity | projection <fields> | count
Null handling: <per optional field>
```

Two simultaneous requests are the case a specification forgets: the only occurrence of the word [G]
in the whole factory used to be inside the cross-module contract, so the endpoint block and the
catalog entry asked nothing and two races shipped — a unique document number allocated from a
read-then-write, and a guard both parallel requests passed. This is **not** mechanically
checkable and no check pretends to verify it: the question is asked here, and scored at the gate
(`reviewers/pass-review.md`).

Standard operation defaults (apply unless a QR entry overrides them):

| Operation | Default |
|---|---|
| FIND_ONE by PK | read-only; not found → error per `ProblemDetail (RFC 9457) → {type, title, status, detail, code}` with the catalog row for "not found" |
| FIND_BY_CRITERIA | read-only; filters + allowed sort fields declared per search; empty result → success with empty content, not "not found" [G] |
| SAVE | read-write; PK and audit fields system-set; business code as the SRS states |
| UPDATE | read-write; immutable fields (PK, business code, audit) excluded from the request |
| `DELETE` | usage check first (can-delete / can-deactivate); blocked → catalog error; allowed → `hard` per profile.stack.db.delete_semantics; the other semantics only where the SRS mandates it [G] |
| EXISTS | read-only uniqueness check; excludes the current PK on update |

Join governance: single-table responses do not join; display names of lookup values are **not** joined — the backend returns the stored code and the frontend resolves the label (Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.); parent data or cross-entity filters require a join **and** an ADR; cross-entity aggregation may need a native query — say why. [G]

## 6. Phase content

### 6.0 Markers, thresholds, traces — read before writing any phase

Marker grammar (`factory.markers`, schema v2, syntax `html-comment`):
`<!-- KIND:ID:START [traces=…] -->` … `<!-- KIND:ID:END -->`. Kinds that may appear in a
`backend` execution plan:

| Kind | Level | Allowed parents | Notes |
|---|---|---|---|
| `PHASE` | 1 | — (top level) [T:marker-foreign-kind] | keys from `profile.tracks.<track>.plans.<plan>.phases` |
| `SUB` | 2 | PHASE [T:marker-foreign-kind] | id = `{PHASE-KEY}-{LABEL}` — always phase-qualified |
| `API` | 3 | PHASE, SUB [T:marker-foreign-kind] | one atom `API-*` = one dedicated block |
| `XM` | 3 | PHASE, SUB [T:marker-foreign-kind] | one atom `XM-*` = one dedicated block |

Rules:
- The first line you write for a phase **is** its `PHASE` START marker; the last line is its
  END marker. Content and markers are one action — not "write, then wrap". [G]
- `traces=`: **every** PHASE, SUB and atom block carries
  `traces=` listing the upstream IDs it implements (comma-separated, grammar
  `{prefix}-{MOD}-{seq}`, 3-digit seq). Obligations from `factory.ids`:
  `API` → REQ + DBF; `XM` → REQ (XM is minted upstream and only placed here). A PHASE block traces to the union of its children. [C:C7.2]
- Split unit is `SUB or PHASE` — never an atom, except in `CROSS-MOD`, [T:split-verify]
  which is split per edge: one package per `XM-*` block (R7). Check the
  threshold **while** writing: if the count is already at threshold from §2A.1, open the first
  SUB before its first block. Never write flat and split later.
- Unknown phase key → the toolkit **refuses** (`refuse`). The key [T:phase-unknown]
  is `p.key`, never the display name (the [T:phase-unknown]
  autofix normalises `+ _ space --` to `-` only when unambiguous — do not rely on it). [T:autofix-bounded]
- Any heading containing the word PHASE uses exactly one profile key. Index, manifest, catalog [T:phase-unknown]
  and self-check sections are not phases: distinct headings, no marker, placed before the
  first PHASE or after the last END.
- Full protocol: shared/MARKER-PROTOCOL.md.

Phase table for `profile.tracks.backend.plans.exec` (the plan is organised in exactly this order): [G]

| # | Key | Display | Split rule | Atoms carried |
|---|---|---|---|---|
| 1 | `CORE` | CORE [T:never-split] | never split | none |
| 2 | `DATA-DOM` | DATA+DOM [T:never-split] | SUB by engine self-check; labels `DATA-DOM-CONFIG`, `DATA-DOM-TRANSACTIONAL` | none |
| 3 | `PORTS` | PORTS+ADAPTERS [T:never-split] | SUB by engine self-check; labels `PORTS-QUERY`, `PORTS-DOCUMENT`, `PORTS-MODEL` | none |
| 4 | `SVC-API` | SVC+API [T:never-split] | SUB when API count >= 8 — grouped COMMAND / QUERY / VIEW; labels `SVC-API-COMMAND`, `SVC-API-QUERY`, `SVC-API-VIEW` | `API-*` blocks |
| 5 | `ALIGN-BE` | ALIGN-BE [T:never-split] | never split | none |
| 6 | `CROSS-MOD` | CROSS-MODULE [T:never-split] | per edge (integration — the last phase) | `XM-*` blocks — one per edge, one package each |


### 6.1 Content roles

The profile names the phases; this engine supplies the content **by role**. Match each
phase to the roles its display name declares (a display such as "SVC+API" declares the
service and API roles). The integration role is not read from a name: it is the phase the
profile flags `integration: true` — `CROSS-MOD` — and it is the last phase. A
phase whose display matches no role is filled as the profile describes it. Atom placement is
data-driven: `API-*` blocks go in the phase whose `split_threshold.kind` is `API`; every
`XM-*` block goes in `CROSS-MOD` and nowhere else (R7).

**One place for another module.** Outside `CROSS-MOD` this plan names another module only by [C:C7.27]
this module's own `XM-*` id (reference-by-id): never the target's table, endpoint, [C:C7.27]
service, operation or id. An endpoint that reads the target says `integrate (XM-CHK-<seq>)`;
what that read is, and how it is wired, is the edge's block (`gov.py analyze` → C7.27).

**R1 — Core / configuration (architecture policies).** Declared once, applies to the module:
- Type mapping oracle19c → language types, stated once as a table (from `profile.stack.db.syntax_map` rows, PK column type included) — a deviation needs an ADR. The table states column types only; the PK-generation clause is not a type (§2A). [G]
- Runtime error-code format: `{MOD}-{http}[-{SLUG}]` (profile.stack.backend.api.error_code_format) — state this string verbatim and make **every** Error Catalog row (§7) an instance of it; the declared format and the emitted codes come from this one profile value, never from free text. [C:C7.11]
- Lookup values: Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded..
- Workflow engine: **forbidden**.
- Search contract: the sort fields each screen allows — read from that screen's SRS filter
  list, not widened here; paging per profile; an empty result is success. [G]
- Languages: every named entity carries a name per language (en, ar).
If nothing module-specific applies, write "Standard configuration — no module-specific abstractions".

**R2 — Data + domain.** One entity block per `ENT-*`, every value bound (§2A):
```
### ENT-CHK-<seq> — <exact name>      kind: <config|transactional>
BINDINGS   table <exact> · PK <column, DBF> · PK generation `identity` → <exact identity clause> · db-script version
DEFAULT FIELDS per kind (profile.conventions.entity_defaults): transactional → createdAt, updatedAt
FIELDS     DBF-* │ property │ column (exact) │ type (oracle19c) │ null │ read-only │ constraint │ label per language (en/ar)
DTO MEMBERSHIP  create-request excludes / update-request excludes / response includes (PK, business code, audit, flag stated explicitly)
LOOKUP FIELDS  property │ column (DBF) │ exact lookup key │ endpoint (base path /api/v1/{resource}) — stores the code, not a numeric FK [G]
DOMAIN RULES   RULE-* full text: trigger · statement · message per language · scope (CREATE|UPDATE|DELETE|ALL) · DB enforcement (constraint name | app-level) · owner layer
STATE MACHINE  (if status-bearing) status column (DBF) · values · initial · transitions (trigger, actor) · terminal · invalid-transition RULE
CROSS-MODULE   XM-* ids touching this entity — the id only; the target and how it is reached are its block (R7) [C:C7.27]
REPOSITORY OPS → QR-* list (FIND_ONE, FIND_BY_CRITERIA, SAVE, UPDATE, EXISTS, …)
```
Grouping for `DATA-DOM`: when the entity count justifies a split (engine self-check — not
marker-countable), group under `SUB:DATA-DOM-CONFIG / SUB:DATA-DOM-TRANSACTIONAL`.
Grouping for `PORTS`: when the entity count justifies a split (engine self-check — not
marker-countable), group under `SUB:PORTS-QUERY / SUB:PORTS-DOCUMENT / SUB:PORTS-MODEL`.

**R3 — Service + API.** One `API-*` block per endpoint, each its own atom marker:
```
<!-- API:API-CHK-<seq>:START traces=REQ-CHK-<seq>,DBF-CHK-<seq> -->
### API-CHK-<seq> — <operation>
Entity       : ENT-CHK-<seq>   (the entity whose OPERATIONS line this endpoint answers)
Endpoint     : <instance of /api/v1/{resource}>   verb: <POST|GET>
Layers       : <entry layer → method> ; <service layer → method>        (names per R1)
Request      : path params · query params (filter names = properties from R2) · body DTO fields — each names its `DBF-*` (type, required, constraint) · excluded system fields
Response     : status · DTO fields · paginated? (per profile) · envelope per profile
Validations  : RULE-* full text (statement, trigger, message per language) — every RULE listed here has a catalog row (§7)
Errors       : catalog rows this endpoint can raise (code, HTTP, RULE-*)
Orchestration : load → validate (RULE-*) → integrate (XM-*) → persist (QR-*, table, generation object)   — WHAT in sequence, layer placement per R1; every column this endpoint writes that the request does not carry (a flag it flips, a status it advances) names its `DBF-*` here
Repository   : QR-* · operation · join (NONE | ADR) · transaction
Concurrency  : NONE — this endpoint neither allocates a unique value nor reads-then-writes
               | <the guard, named>: what two simultaneous requests must not both be allowed to
               do, and what makes that impossible (a unique constraint, a locking read, an
               atomic allocation). Required for every endpoint that allocates a unique value or
               decides on a value it then writes. "Validated first" is not an answer: two
               requests both validate, both pass, both write.
Security     : screen · permission name — enforced before processing
Localization : every message in en + ar; every name field per language
<!-- API:API-CHK-<seq>:END -->
```
**Honour this module's own contract.** Every API block implementing one of its operation
items states `Honours: CON-CHK-<seq>` (`gov.py analyze` → C7.24 `contract-honoured`).

**Every operation the SRS names needs an endpoint.** Each screen requirement's `Operations`
line is the module's demand, read from the SRS — never from this plan, which cannot both [C:C7.23]
state its own demand and be the proof it was met. Every operation on it is closed here, in
one of three ways and no fourth: an `API-*` that names the operation **and** the `ENT-*` the
screen names on its `Entities` line; or an explicit exclusion carrying one of the profile's stated reasons; or an
ADR that leaves it for a later version — and the gate stays shut. An operation the plan never [C:C7.23]
mentions is the gap this closes: it used to surface at the frontend stage, two stages and one
gate later, when the screen had no endpoint to call. Every `API-*` block therefore carries its
`Entity` line (the `Entities` the screen names) — without it no operation can be resolved to it at all. `gov.py analyze` →
`operation-resolves`.

A `Operations` or `OPERATIONS` line separates the operations it lists with one of
`,` · `;` · `/` · `·` and nothing else. The operation WORDS are this
project's own — the check reads them verbatim, so it never has to anticipate them — but it [C:C7.23]
splits the line on these separators, and one written with any other yields no operation at
all. That failure is silent from the author's side: the clause reports having examined
nothing rather than having failed to read the line.


**Every required column needs a writer.** A column the db-script declares NOT NULL and the
platform does not fill itself must be named by at least one `API-*` block, on its `Request` line
(the caller supplies it) or its `Orchestration` line (the endpoint sets it). A required column no
endpoint writes cannot be satisfied on a fresh deployment, so the first call to every endpoint
that depends on it fails — and no shape check can see it, because the manifest lists the column
and the endpoint lists its fields and nothing joins the two. Where no endpoint should write it,
say so on the manifest row with one of the profile's stated reasons (system-generated · derived · DEFERRED); silence is the defect. `gov.py analyze` → `required-writer`.

Completeness rules: a RULE whose SRS `Data source` is DEFERRED is **not** enforced here — it is
listed once in the entity block as `DEFERRED (no declaration surface in v1)` with no catalog
row, no QR and no enforcing endpoint, because the data its check would read has no column to read
from; every other RULE in Validations ↔ a catalog row (RULE-ERR-CARRY); infrastructure
errors (not found, forbidden, server) are catalog rows with RULE = `PLATFORM-STD` and an ADR; [G]
repository deviations (eager fetch, compound update, native query) need an ADR. Business code
(if any) is excluded from create/update bodies and present in every response. No hard-coded [G]
role checks in services — permission names only. [G]

**R5 — Security (backend half).** No security model is declared in `profile.conventions.security_model`; write "no permission model — endpoints are open per the SRS" and cite the REQs that say so.

**R6 — Alignment (self-check).** The ALIGN table of §9, written as the phase content of the
alignment-role phase (never split). If the profile declares no alignment-role phase, the [T:never-split]
table is trailing content placed before `CROSS-MOD` opens — the integration phase is always the last. [C:C7.25]

**R7 — Integration (the last phase, `CROSS-MOD`).** The ONE place this plan speaks about another
module. One block per `XM` edge — every edge the db-script register declares (C7.5) and every
one this stage mints — and each block is the only thing an executor reads to wire that edge: [C:C7.26]
no "see the db-script", no row elsewhere to reconcile it with. The lines, in this order, all
mandatory (`factory.yaml → integration`): [C:C7.26]

<!-- RENDER:integration -->
| Line (in this order) | Carries |
|---|---|
| `target` | `{target_module} · {target_entity}` — the owner's module code and `ENT` id (xm.record) |
| `type` | `HARD-FK` · `SOFT-READ` |
| `contract` | `contract-{mod}.md#{item}` — the target's published contract item |
| `requires` | data, not prose: `{target}:DELIVERED` |
| `do` | complete, runnable content — `ddl_patch` · `adapter` · `config` (`ddl_patch` required for `HARD-FK`) |
| `tests` | `TC-*` / `AC-*` ids that verify the edge (non-empty) |
| `if_not_met` | `skip-block; record in execution-state.json → deferred_xm; continue` |

- **Where**: the phase the profile flags `integration: true` in the `exec` plan of every track carrying the `XM` atom — exactly one, the **last** phase; one block per edge. [C:C7.25]
- **Database stage**: column-only — the column, no constraint to a foreign table, and one comment `constraint → XM-…`; the constraint text lives only in the block. [C:C6.15] [C:C7.28]
- **Every other phase**: reference-by-id — this module's `XM-*` id, never the target's tables, endpoints, services or ids. [C:C7.27]
- **Split**: one package per block at `<delivery partition>/integration/XM-…` with `package.json` (`xm`, `target`, `requires`, `tests`, `acceptance`), listed under `manifest.json → status.split.integration` — never counted toward the module's delivery; the factory records no state for it. [T:graph-derived-state]
<!-- /RENDER:integration -->

```
<!-- XM:XM-CHK-<seq>:START traces=REQ-CHK-<seq> -->
### XM-CHK-<seq> — <dependency>
target     : TARGET · ENT-TARGET-<seq>
type       : HARD-FK | SOFT-READ
contract   : contract-target.md#CON-TARGET-<seq>
requires   : TARGET:DELIVERED
do         :
  ddl_patch : [C:C7.26] ALTER TABLE <this module's table> ADD CONSTRAINT FK_<LOCAL>_<REF> FOREIGN KEY (<column — DBF-CHK-<seq>>) REFERENCES <target table> (<target key>);   (HARD-FK)
  adapter   : [C:C7.26] <the complete adapter — written out, never a pointer elsewhere>
  config    : [C:C7.26] <the complete config — written out, never a pointer elsewhere>
tests      : AC-CHK-<seq>, TC-CHK-<seq>
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-CHK-<seq>:END -->
```

- **target / contract.** Plan against what the target PROMISES — its published contract (the
  `contracts` input) — never against a guess at its implementation: the owner's module code and [C:C7.12]
  its `ENT` id, and the contract item the edge depends on (`CON-*`, or the `ENT-*` the contract
  exposes). A target that has published no contract yet is recorded in the register with its
  workaround (shared/XM-PROTOCOL.md §4) and the line anchors on the owner's `ENT-*`;
  `gov.py analyze` → `xref-resolve` decides whether it resolves.
- **requires** is data, never a sentence. The factory does not wait for it and does not check [C:C7.26]
  it at run time: the executor reads it, and when it does not hold follows `if_not_met`.
- **do** is complete. The constraint text is written here in full, generated from this module's
  column (`DBF-*`) and the target's contract — the db-script carries only the column and its [C:C6.15]
  pointer `constraint → XM-CHK-<seq>` (column-only); the text exists nowhere
  else (`gov.py analyze` → `patch-single-source`). The adapter is
  the target module's published in-process interface, injected (`profile.conventions.module_interface: in_process`) —
  name the interface and the operation on it. This platform is ONE deployable: there is no
  HTTP client, no base path, no timeout and no network error path, so no Error Catalog row
  (§7) may describe a network failure that cannot occur.
  A structural (foreign-key) row's access is the key itself and follows from its
  classification, not from this line. Whatever the mechanism, the thing named must be one
  the target module really publishes — `gov.py analyze` → `xref-resolve` resolves every
  foreign id against that module's own registry. If it does not exist yet, the register records the edge with its workaround — never a prose [C:C7.12]
  promise such as "through the target's read APIs".
- **tests** name what proves the edge: the `AC-*` of the requirements it traces — the criteria
  the test stage derives the edge's `TC-*` from — and those `TC-*` once they exist. Never empty.
- **Minting.** Where this stage's own content — a security role, a rule turned into a runtime
  check — is the first thing that reads another module's data, **mint** the `XM-*` here as
  one more block, continuing the atom's sequence: the register was frozen before that dependency
  existed, and a dependency that may not be written down is one that nothing tracks. The
  orchestrator back-registers the minted edge into `_state`'s register in the same
  run, with the state the dependency graph derives for it (shared/XM-PROTOCOL.md §3); a minted
  edge left out of the register is a finding (`gov.py analyze` → C7.5b), minting itself is not.
- **Inbound** dependencies are not written here: every module's edges toward CHK are in the
  dependency graph (`gov.py graph` → `platform/dependency-graph.json`), and what CHK promises them
  is its published contract (P1.5). The dependency's own state lives in the register
  (shared/XM-PROTOCOL.md §4); this phase carries no state at all.

### 6.2 Phase-by-phase instructions

#### PHASE 1 — `CORE` (CORE)
- Open with `<!-- PHASE:CORE:START traces=… -->`, close with `<!-- PHASE:CORE:END -->`.
- Content: the roles in §6.1 whose words appear in "CORE"; otherwise as the profile describes this phase.
- Split: [T:split-verify] never — level-1 only, no SUB.
- Atoms: none — entity/rule blocks carry no marker of their own.

#### PHASE 2 — `DATA-DOM` (DATA+DOM)
- Open with `<!-- PHASE:DATA-DOM:START traces=… -->`, close with `<!-- PHASE:DATA-DOM:END -->`.
- Content: the roles in §6.1 whose words appear in "DATA+DOM"; otherwise as the profile describes this phase.
- Split: [T:split-verify] by engine self-check, labels `DATA-DOM-CONFIG`, `DATA-DOM-TRANSACTIONAL`.
- Atoms: none — entity/rule blocks carry no marker of their own.

#### PHASE 3 — `PORTS` (PORTS+ADAPTERS)
- Open with `<!-- PHASE:PORTS:START traces=… -->`, close with `<!-- PHASE:PORTS:END -->`.
- Content: the roles in §6.1 whose words appear in "PORTS+ADAPTERS"; otherwise as the profile describes this phase.
- Split: [T:split-verify] by engine self-check, labels `PORTS-QUERY`, `PORTS-DOCUMENT`, `PORTS-MODEL`.
- Atoms: none — entity/rule blocks carry no marker of their own.

#### PHASE 4 — `SVC-API` (SVC+API)
- Open with `<!-- PHASE:SVC-API:START traces=… -->`, close with `<!-- PHASE:SVC-API:END -->`.
- Content: the roles in §6.1 whose words appear in "SVC+API"; otherwise as the profile describes this phase.
- Split: [T:split-verify] open `<!-- SUB:SVC-API-<LABEL>:START traces=… -->` groups when the `API` count is >= 8, grouped COMMAND / QUERY / VIEW; labels `SVC-API-COMMAND`, `SVC-API-QUERY`, `SVC-API-VIEW`. Every atom then sits inside a SUB — no orphan atoms beside SUBs.
- Atoms: one `API-*` marker pair per atom, `traces=` on each.

#### PHASE 5 — `ALIGN-BE` (ALIGN-BE)
- Open with `<!-- PHASE:ALIGN-BE:START traces=… -->`, close with `<!-- PHASE:ALIGN-BE:END -->`.
- Content: the roles in §6.1 whose words appear in "ALIGN-BE"; otherwise as the profile describes this phase.
- Split: [T:split-verify] never — level-1 only, no SUB.
- Atoms: none — entity/rule blocks carry no marker of their own.

#### PHASE 6 — `CROSS-MOD` (CROSS-MODULE)
- Open with `<!-- PHASE:CROSS-MOD:START traces=… -->`, close with `<!-- PHASE:CROSS-MOD:END -->`.
- Content: the roles in §6.1 whose words appear in "CROSS-MODULE"; otherwise as the profile describes this phase.
- Split: [T:split-verify] per edge — no SUB; the splitter emits one package per `XM-*` block (R7). This is the last phase.
- Atoms: one `XM-*` marker pair per edge, `traces=` on each, every line of R7 in order.

## 7. Error Catalog

Canonical, produced with the service/API role, kept in **one** location (a pointer elsewhere
is fine; a second table is a duplicate). Envelope: `ProblemDetail (RFC 9457) → {type, title, status, detail, code}`.

ONE block (data, not prose — `gov.py analyze` → `code-format`, `api-spec-errors` and the API document's derivation read it; C0.1 holds it to its schema):
```yaml name=error-catalog
rows:
  - {code: "<runtime value per envelope>", rule: RULE-CHK-<seq> | PLATFORM-STD, api: [API-CHK-<seq>], http: <status>, trigger: "<when it fires>", messages: {en: "<message>", ar: "<message>"}, adr: "<ADR id when rule is PLATFORM-STD>"}
```
- The `http` value is **not free text**: it holds one of
  `200`, `201`, `202`, `400`, `401`, `403`, `404`, `409`, `413`, `415`, `422`, `500`, `502`, `504` (profile.stack.backend.api.http_statuses) — the statuses this
  platform can actually emit. A row on any other status is raisable by no code path in the
  platform, however well-formed its code string is; `gov.py analyze` → `code-format` refuses it. [C:C7.11]
  If a row genuinely needs a status outside the set, that is a platform change and an ADR, never a [C:C7.11]
  catalog row written anyway.
- Every RULE that produces a user-facing message has a row; message text is copied
  character-perfect from the SRS in every language (en, ar); a missing
  language → `PENDING ADR-<id>`, not invented. [G]
- Downstream consumers (frontend plan, test plan, api-verify) cite the **code**; they do not [G]
  reproduce message text.
- The runtime code format (as the framework serialises it) is stated once in R1 as
  `{MOD}-{http}[-{SLUG}]` so that api-verify can assert on it. The declaration and the rows are the
  same fact: a code that is not an instance of the declared format, or a declared format
  no row obeys, is a finding (`gov.py analyze` → `code-format`), never a convention to [C:C7.11]
  inherit into the next module.

## 7.5 API document — `api-spec-chk.yaml`

Emitted in the same run, **after** the plan and the catalog, as `api-spec-chk.yaml`: an
**OPENAPI 3.1.0** document DERIVED from R3 and §7, never written beside [C:C7.29]
them. `gov.py analyze` proves the two agree (C7.29 `api-spec-agree`, C7.30 `api-spec-errors`,
C7.31 `api-spec-valid`); the frontend stage binds its plan to it and the test stage cites it, so
neither waits for a built backend; the frontend executor serves it from a mock server
(`mock: api-spec-chk.yaml` in the frontend plan's `api-surface` block — the factory emits the
document and the line, it runs nothing); `api-verify` holds the delivered api-docs to it.

The derivation, line by line — every value is READ from the plan or the profile:

| Source | Becomes |
|---|---|
| every `API-*` block (R3) | exactly one operation under `paths`: the `Endpoint` line gives the path (as written — an instance of `/api/v1/{resource}`) and the method (its `verb:`); the block's id on `x-api-id`, its `traces=` on `x-traces`, its heading as `operationId` / `summary` [C:C7.29] |
| the block's `Request` line | `parameters` (path · query, `required` as the line says) and, for a body-bearing method, `requestBody` — every field typed from its `DBF-*` through the db-script's type (`syntax_map`) |
| the block's `Response` line | the success response under its status with the DTO fields as a schema, envelope `none declared — none in the document`; a paginated response carries `x-paginated: true` — this profile declares no `paging_params`, so no paging parameter is stated (none is invented) |
| every §7 row that names the block (its `API-*` column / the block's `Errors` line) | one response keyed by the row's `HTTP`, the row's code listed under `x-error-codes` — one row, ≥1 operation; a row naming no block is answered by every operation that can raise it |
| the profile's security scheme | this profile declares none (`profile.stack.backend.api.auth`) — the document carries no security block and states nothing about authentication |

Coverage the document must reach (`factory.api_spec.required`): `paths` · `operations` · `request_schemas` · `response_schemas` · `error_responses` · `paging` · `auth`.
A value the plan does not state is a **plan defect**: fix the block or the catalog, never guess in [C:C7.29]
the document. An operation with no block, a block with no operation, a method or path the two state
differently, a catalog row no response answers, a document the standard rejects — each is a C7.29 /
C7.30 / C7.31 finding, and CRITICAL keeps the gate closed. A delta version re-emits the document
**whole** (it is generated — `fold: replace` in `factory.yaml`), never as a delta of records. [T:fold-carry]

## 8. Security

Covered by R5 (§6.1).
Review check: `profile.review.extra_checks` rows whose stage is `P3.1`:
- `AIAS-3` (CRITICAL): the LLM is given no tool that runs SQL or triggers approval; queries run only through the query port from the service definition
- `AIAS-4` (CRITICAL): the approval API is invoked only from the employee-decision operation, and only where the service enables it
- `AIAS-5` (CRITICAL): query parameters are bound or strictly type-validated, and every file path is validated inside the allowed storage root before opening
- `AIAS-6` (CRITICAL): document content reaches the model only as delimited data, never as instructions
- `AIAS-7` (MAJOR): every check declares its timeout, maximum rows and maximum file size
- `AIAS-8` (MAJOR): a missing or unreadable required document appears in the report and prevents COMPLIANT — never skipped silently
- `AIAS-9` (MAJOR): the engine depends on Spring AI ChatModel only — no provider-specific feature — and document reading uses its own configurable model

## 9. Alignment self-check (ALIGN)

Validates the plan **against itself and its bindings**. Runs automatically after the last
content phase; a ✗ is fixed in the plan before the run ends (the fix is an ADR if it was a
choice).

ALIGN is the *prose* half of the check and it is not the authority. The mechanical half is
`gov.py analyze`, which resolves — across artifacts, and across modules — exactly the things [G]
a prose pass reads past:

| Mechanical check | What it resolves |
|---|---|
| `traces` | every `PHASE`/`SUB`/atom block carries `traces=`, and every `API-*` traces to its `REQ-*` and its `DBF-*` |
| `orphans` (REQ) | every `REQ-*` is covered by at least one `API-*` or `DBF-*` |
| `registry-agree` | every `XM-*` the register declares is placed here, and every `XM-*` minted here is back-registered into it |
| `intc-last` | `CROSS-MOD` is the last PHASE, and every `XM-*` block sits inside it |
| `xm-block-complete` | every `XM-*` block carries every line of R7, in order: `requires` in its declared shape, the constraint text for a type that needs it, `tests` non-empty |
| `no-foreign-scatter` | outside `CROSS-MOD` no line names another module's table, operation, endpoint or id — only this module's `XM-*` ids [C:C7.27] |
| `patch-single-source` | the constraint text of an edge exists only in its block — not in the db-script, not in another phase [C:C7.28] |
| `value-agreement` | the physical column a `DBF-*` names in this plan is character-identical to the one the db-script declares for it (registry row, `CREATE TABLE`, `COMMENT ON`) |
| `code-format` | every Error Catalog code is an instance of the format R1 declares (`{MOD}-{http}[-{SLUG}]`), and its HTTP status is one the platform can emit |
| `api-spec-agree` | every `API-*` block is exactly one operation of `api-spec-chk.yaml` and every operation one block, agreeing on method and path [C:C7.29] |
| `api-spec-errors` | every Error Catalog row is answered by an operation of `api-spec-chk.yaml` with the row's status and code |
| `api-spec-valid` | `api-spec-chk.yaml` validates against the OPENAPI 3.1.0 schema and reaches every item of `factory.api_spec.required` |
| `data-source` | every `RULE-*` this plan turns into a runtime check has a declared source for the data the check *reads* — or an explicit deferral |
| `xref-resolve` | every id of another module cited here is defined in that module's own registry |
| `operation-resolves` | every operation an SRS screen names is built by an `API-*`, or the plan states why it is not |
| `refs-exist` | every `ADR-*` file this plan cites by path exists on disk in `analysis/decisions/CHK/` |
| `paths-resolve` | every path the generated manifest and execution state emit resolves to something that exists |
| `orphans` (QR) | every catalogued query is reached by at least one `API-*` — the direction the QRC's own four assertions never ran, so a query nobody runs was invisible to all of them [C:C7.22] |
| `count-agrees` | every total this plan states equals the rows it heads, and two statements of the same total agree with each other |
| `required-writer` | every column the db-script requires is written by at least one endpoint's request or orchestration, or carries a stated reason why not |
| `operation-resolves` | every operation an entity declares is answered by an `API-*`, and every marked permission-matrix cell names an `API-*` and the permission for that action |

| `verdict-agrees` | the `self-check` block does not claim fewer findings than `analyze` produced for this plan |

**Every row of ALIGN names the check that backs it, and there are no other rows.** The
block used to assert traceability, binding, manifest integrity and coverage in prose that no
check could falsify; four of those rows were false in a delivered module and the verdict beneath
them read clean. A self-check row nothing can falsify is worse than no row — it
manufactures confidence — so a row whose assertion no named check examines was **deleted**, not
softened. Do not add one back: if a dimension matters and no check covers it, the fix is a clause
in `shared/ARTIFACT-CONTRACTS.md`, not a sentence here.

Each row's mark is therefore **the analyze report's result for that check**, copied, not an
independent judgement: you do not adjudicate a row the machine already decided. Where the report
says a clause examined nothing, that row is written `— examined nothing`, never ✓: no finding over [C:C7.15]
an empty set is not a pass, and the COVERAGE row carries the report's own list.

**Do not author the verdict.** The verdict is the `self-check` block below the table — `findings`
and `clean` — and the orchestrator writes it from the analyze report after the stage completes
(`gov.py` → `_stamp_verdict`); `verdict-agrees` refuses any hand-written verdict that [C:C7.15]
claims fewer findings than the machine produced. A self-check that always prints [C:C7.15]
clean transfers false confidence downstream and is worse than no self-check —
so the count is no longer a thing a model is asked to be honest about. Write the block with
`findings: 0, clean: true` as a placeholder; it is overwritten.

```
ALIGN — CHK v1
row               backing check        assertion
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF
COVERED           orphans              every REQ is covered by ≥1 API or DBF
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares for it
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not
QRC (§5)          orphans              every catalogued query is reached by ≥1 API
API (R3)          code-format          every catalog code is an instance of the declared format and carries a status the platform can emit
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-chk.yaml and every operation one block, agreeing on method and path
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation of api-spec-chk.yaml with its status and code
DOCUMENT VALID    api-spec-valid       api-spec-chk.yaml validates against OPENAPI 3.1.0 and reaches every required item
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from, or is deferred
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase, and nothing else names its target
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's own registry
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why it is not
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/CHK/
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves to something that exists
COVERAGE          (the report)         the clauses the analyze report lists as having examined nothing — verbatim, or `none`
```
```yaml name=self-check
findings: 0          # written by the orchestrator from the analyze report — leave it alone
clean: true
```
Coverage tables (ENT/DBF → phases → QR → XM; RULE → API → catalog code; XM → requires →
tests) close the section.

## 10. Registry update — `registry-exec-be-chk.md`

Written in the same run, after ALIGN (categories: shared/REGISTRY-SCHEMA.md):

```
REGISTRY — P3.1 — CHK v1
ID RANGES        API-CHK-<first>..<last> · QR-CHK-<first>..<last>
ENTITIES / TABLES bound   · lookups reused / new (keys)
INTEGRATION      XM ids, one block each in CROSS-MOD · requires per edge
CATALOG          code count · rules without message → ADR ids
API DOCUMENT     api-spec-chk.yaml · operations <n> = API blocks <n> · error responses <n> = catalog rows <n>
ALIGN            verdict as stamped · findings fixed
ADRs             decisions/CHK/ADR-CHK-<seq> … (status)
TRACEABILITY     REQ covered by ≥1 API/DBF: <n>/<total> · orphan REQ: <list — a gate blocker>
```

## 11. Structural self-check (toolkit)

Before the run ends:
```
[ ] every profile key in §6.0 has exactly one PHASE START/END pair, in profile order [T:marker-duplicate]
[ ] every API-*/XM-* mentioned anywhere has exactly ONE dedicated marker pair [T:marker-duplicate]
[ ] `CROSS-MOD` is the LAST phase; every XM block sits in it and carries every R7 line in order
[ ] no other phase names a foreign table, endpoint, service or id — only this module's XM-* ids [C:C7.27]
[ ] every SUB id is {PHASE-KEY}-{LABEL}; identical labels under different phases stay distinct
[ ] every PHASE/SUB/atom carries traces=
[ ] no heading label repeats; trailing content (§9–§10 when not a phase) sits after the last PHASE END
[ ] thresholds were checked while writing, not retrofitted
```
Then run the toolkit validation — a non-zero exit is blocking:
```
gov.py split --track backend --module CHK --version 1 --dry-run
```
`gov.py analyze` (traceability matrix, EARS, marker validity, registry ↔ artifact agreement)
runs before the gate `P3.2`; CRITICAL findings keep the gate closed.

## 12. Ambiguity rule (no questions here)

`factory.yaml → ambiguity`, stated once in shared/GOVERNANCE-CORE.md:
- **non-breaking** (does not contradict a locked decision or a REQ) → choose the best-practice
  answer using `profile.knowledge.files` + `analysis/domain/` steering, write
  `analysis/decisions/CHK/ADR-CHK-{seq:03d}.md` (Context / Decision /
  Consequences / traces) and **continue**;
- **breaking** (contradicts a locked decision or a REQ) → ADR with status
  `BLOCKED`, then **stop**; the
  orchestrator surfaces it at the next human point.
Every "STOP and ask" of earlier engine generations is replaced by this rule.

## 13. Boundaries and hand-off

| Owns (mints) | References (read-only) | Never touches |
|---|---|---|
| `API-*`, `QR-*`; DB Alignment Manifest; Error Catalog; QRC; ALIGN rows; ADRs it raises | `POL-*` (P0), `US-*` (P0.5), `REQ-*` (P1), `AC-*` (P1), `ENT-*` (P1), `RULE-*` (P1), `DBF-*` (P2), `XM-*` (P2), `CON-*` (P1.5), `SCR-REQ-*` (P1), `FEAT-*` (feature) | frontend/UX atoms (`UXD`, `SCR` — P3.2), `TC-*` (P4), any code, framework annotations, executable queries, test artifacts |

Hand-off (the orchestrator prints it): the plan + registry are split by the toolkit into
`/backend-execution/` inside the
shared repo after the `P3.2` verdict — nothing is copied anywhere; the implementer reads
it where it was written, at the commit its own repo pins. The implementer reads
the plan in order (index → manifest → ADRs → phases in profile order → QRC → catalog), rewrites
every QR, implements security per R5, and publishes its api-docs, which `api-verify` holds to
`api-spec-chk.yaml` (C11.4) — the frontend did not wait for them. Each `CROSS-MOD` block becomes its own package [G]
(`integration/XM-CHK-<seq>` in the delivery partition, `package.json` beside it);
the implementer runs it when its `requires` holds and otherwise follows its `if_not_met`. The
module is delivered by its other packages — the factory neither waits for nor tracks these.


---
# INPUTS (generated current state)

<<<INPUT: srs>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: db-script>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: registry-srs>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: registry-db>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: contract>>>
(MISSING — the orchestrator refuses to run this stage until it exists)
<<<END INPUT>>>

<<<INPUT: contracts>>>
<<<contract-doc.md>>>
# CONTRACT — Document Access (DOC) — outbound promise
══════════════════════════════════════════════════════════════════
Module : DOC   Version : v1   Profile : aias
Inputs : srs-doc.md, registry-srs-doc.md
Items  : CON 5 (lookup promises 2 · operations 3)
Read by: CHK, INT, RPT (in-process DOC interface — profile `conventions.module_interface: in_process`)
══════════════════════════════════════════════════════════════════

DOC is tier 1 (depends on REG only). Its single entity, the Uploaded Document (ENT-DOC-001), is PRIVATE and is not promised: no other module stores, references or reads it — INT reaches it only through the handover operation (CON-DOC-003) and CHK only through the fetch and end operations (CON-DOC-004, CON-DOC-005). Operations name no path and no verb; P3.1 chooses the implementation and states `Honours: CON-…` on it.

Identifier rule (profile `conventions.identifiers`): every identifier of the service's own schema is `NUMBER(19)` generated by identity. DOC receives the Check's identifier (`NUMBER(19)`, owned by RPT) as a value and never as a foreign key; host identifiers (the request number) are passed as strings exactly as the host sent them.

## Lookup promises

### CON-DOC-001 — Document read status and unreadable reason: the closed outcome codes of a document
Entity    : — (closed enums carried by value on the transient Document Outcome; no DOC table) — key columns: DOCUMENT_READ_STATUS (closed: READ, MISSING, UNREADABLE), UNREADABLE_REASON (closed: OUTSIDE_STORAGE_ROOT, NOT_FOUND, TOO_LARGE, UNSUPPORTED_FORMAT, READING_FAILED, OUT_OF_TIME, SOURCE_QUERY_FAILED, MODEL_NOT_PERMITTED) · identifier type: none — a consumer stores the code as `VARCHAR2(30 CHAR)`
Promise   : every Document Outcome carries exactly one read status from this list; every UNREADABLE outcome carries exactly one reason from this list and a detail text; READ and MISSING outcomes carry no reason. RPT stores the codes it receives through CHK with no runtime read of DOC (ADR-DOC-002, ADR-DOC-007). Adding a code is a new DOC version.
Traces    : REQ-DOC-034, REQ-DOC-035, REQ-DOC-036

### CON-DOC-002 — Fetch mode: the closed list of ways a document is obtained
Entity    : — (profile closed enum carried by value on ENT-REG-002.fetchMode) — key columns: FETCH_MODE (closed: path, blob, manual) · identifier type: none — a consumer stores the code as `VARCHAR2(10 CHAR)`
Promise   : no fourth fetch mode exists; every Document Outcome carries its source mode from this list, equal to the fetch mode of the Check's service package version (REQ-DOC-001). REG, CHK and RPT use the values directly, with no runtime read of DOC (ADR-REG-005).
Traces    : REQ-DOC-001, REQ-DOC-038

## Operations

### CON-DOC-003 — Hand over a file uploaded for a Check (INT → DOC)
Signature : handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, fileContent) → {uploadedDocumentId, documentType, fileName, fileSize, oversized, notice (present only when oversized — RULE-DOC-005 message)} · errors: fetch mode not manual (RULE-DOC-001), document type not of the service (RULE-DOC-002), incomplete upload (RULE-DOC-003), service package version not found (REQ-DOC-003); each error carries the RULE message for INT to show as a ProblemDetail
Entity    : ENT-DOC-001
Notes     : INT passes the service code and version number of the Check with every upload (ADR-DOC-006). An oversized file is accepted as a record without content and later reported UNREADABLE with reason TOO_LARGE. An Uploaded Document is never changed; a corrected file is a new handover (REQ-DOC-023). No caller authentication in this version (raw-idea A2).
Traces    : ENT-DOC-001, REQ-DOC-017, REQ-DOC-020, REQ-DOC-021, REQ-DOC-022, REQ-DOC-043

### CON-DOC-004 — Fetch and read the documents of a Check (CHK → DOC)
Signature : fetchDocuments(checkId, requestNumber, serviceCode, versionNumber) → list of Document Outcome {documentType, sourceMode (CON-DOC-002), readStatus (CON-DOC-001), reason (CON-DOC-001, UNREADABLE only), detail, content (READ only — text, or tables of rows and columns for a spreadsheet)} · errors: service package version not found (REQ-DOC-003)
Entity    : ENT-DOC-001
Notes     : exactly one outcome per fetched or uploaded document plus one MISSING outcome per required document type that no document carries (REQ-DOC-034, REQ-DOC-035); a failure on one document never stops the others (REQ-DOC-037); a failed or over-limit document source query yields UNREADABLE / SOURCE_QUERY_FAILED for every required document type, never an error (REQ-DOC-039); content is data in its own field, apart from any instruction (REQ-DOC-045). The call honours the Check's timeout (REQ-DOC-040). DOC keeps no fetched document and no content after it returns (REQ-DOC-055). Whether an outcome blocks `COMPLIANT` is CHK's decision (ADR-DOC-002).
Traces    : REQ-DOC-001, REQ-DOC-002, REQ-DOC-018, REQ-DOC-034, REQ-DOC-035, REQ-DOC-036, REQ-DOC-037, REQ-DOC-038, REQ-DOC-039, REQ-DOC-045

### CON-DOC-005 — End a Check (CHK → DOC)
Signature : endCheck(checkId) → {deletedCount} · errors: none (a Check with no Uploaded Document answers 0)
Entity    : ENT-DOC-001
Notes     : CHK calls it on every ending path of a Check — report stored, failed or timed out (ADR-DOC-008). Every Uploaded Document of the Check is hard-deleted; repeating the call is harmless.
Traces    : ENT-DOC-001, REQ-DOC-054

## Stability
All 5 items are ADDITIVE in v1. Changing or removing one a consumer depends on requires a BREAKING version with an ADR.
══════════════════════════════════════════════════════════════════

<<<contract-reg.md>>>
# CONTRACT — Service Registry (REG) — outbound promise
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias
Inputs : srs-reg.md, registry-srs-reg.md
Items  : CON 13 (entity promises 6 · operations 7)
Read by: DOC, CHK, RPT, INT (in-process REG interface — profile `conventions.module_interface: in_process`)
══════════════════════════════════════════════════════════════════

REG is the root of the platform graph (tier 0). Every item below is something DOC, CHK, RPT or INT may build against before REG is built. Private data (the Load Result rows, ENT-REG-006) and internal load/activation operations are not promised. Operations name no path and no verb; P3.1 chooses the implementation and states `Honours: CON-…` on it.

Identifier rule (profile `conventions.identifiers`): every identifier of the service's own schema is `NUMBER(19)` generated by identity. Consumers that record a service package version (RPT, through CHK — G11) store the business key `serviceCode` + `versionNumber` as values, not as a foreign key, so that stored reports outlive any change of identifiers.

## Entity promises

### CON-REG-001 — Service Package: the registry of service codes
Entity    : ENT-REG-001 — key columns: serviceCode (business key, `VARCHAR2(100 CHAR)`, unique, immutable), servicePackageId (identifier) · identifier type: NUMBER(19)
Promise   : a service code is valid only if REG holds it; `available` tells whether new Checks may use it (RULE-REG-016). Service codes are never hardcoded by a consumer (POL-REG-013).
Traces    : ENT-REG-001, REQ-REG-001, REQ-REG-006, REQ-REG-010

### CON-REG-002 — Service Package Version: an immutable, resolvable version
Entity    : ENT-REG-002 — key columns: serviceCode + versionNumber (business key; versionNumber `NUMBER(10)`), servicePackageVersionId (identifier) · identifier type: NUMBER(19)
Promise   : the content under a (serviceCode, versionNumber) pair never changes and is never deleted (ADR-REG-003); a consumer that records the pair can always resolve it (CON-REG-009).
Traces    : ENT-REG-002, REQ-REG-019, REQ-REG-021, REQ-REG-026

### CON-REG-003 — Service Query: the queries of a version, exactly as written
Entity    : ENT-REG-003 — key columns: servicePackageVersionId + queryName (business key; queryName `VARCHAR2(100 CHAR)`), serviceQueryId (identifier) · identifier type: NUMBER(19)
Promise   : each query is one SELECT statement whose only parameter is the named bind parameter of the version's `inputName`, and names a connection by `connectionName` (RULE-REG-006, RULE-REG-007). A consumer binds the request number to that parameter; it never edits the SQL text.
Traces    : ENT-REG-003, REQ-REG-029, REQ-REG-032, REQ-REG-033

### CON-REG-004 — Required Document: the document types a version requires
Entity    : ENT-REG-004 — key columns: servicePackageVersionId + documentType (business key; documentType `VARCHAR2(100 CHAR)`, lookup DOCUMENT_TYPE, open), requiredDocumentId (identifier) · identifier type: NUMBER(19)
Promise   : the documentType values equal the host's document type values as the document source query returns them (e.g. TRANSCRIPT, ID_CARD for `scholarship-request`).
Traces    : ENT-REG-004, REQ-REG-037, REQ-REG-057

### CON-REG-005 — Connection: a named, read-only data source of this environment
Entity    : ENT-REG-005 — key columns: connectionName (business key, `VARCHAR2(100 CHAR)`, unique), connectionId (identifier) · identifier type: NUMBER(19)
Promise   : every registered connection is declared read-only (RULE-REG-015); `connectionType` is `mcp` or `jdbc` (lookup CONNECTION_TYPE, closed); `blob` documents are always read through a `jdbc` connection (RULE-REG-010). REG holds a credential reference, never the credential (REQ-REG-054).
Traces    : ENT-REG-005, REQ-REG-046, REQ-REG-054, REQ-REG-055

### CON-REG-006 — Lookups REG masters
Entity    : ENT-REG-001 — key columns: SERVICE_CODE (open, values from ENT-REG-001.serviceCode), CONNECTION_TYPE (closed: mcp, jdbc), DOCUMENT_TYPE (open; seeded TRANSCRIPT, ID_CARD) · identifier type: NUMBER(19)
Promise   : consumers read these values from REG and never redefine them; the fetch mode values (path, blob, manual) are the profile's closed enum carried on ENT-REG-002.fetchMode (ADR-REG-005).
Traces    : ENT-REG-001, ENT-REG-004, ENT-REG-005, REQ-REG-036, REQ-REG-037

## Operations

### CON-REG-007 — Supply the current service package of a service
Signature : getCurrentServicePackage(serviceCode) → read-only package {serviceCode, versionNumber, serviceKnowledge (whole, unaltered), inputName, queries [queryName, connectionName, sqlText], fetchMode, document source {documentSourceQueryName, documentTypeColumn, documentPathColumn | documentContentColumn}, requiredDocumentTypes} · errors: service not available (unknown or withdrawn code — RULE-REG-016), connection not activated (RULE-REG-017)
Entity    : ENT-REG-002
Notes     : the service knowledge is a separate part from the queries and document settings (REQ-REG-018, REQ-REG-030); the package carries no approval API definition (REQ-REG-044) and no request data (REQ-REG-059); it is immutable for the caller (REQ-REG-060).
Traces    : REQ-REG-024, REQ-REG-027, REQ-REG-029, REQ-REG-012, REQ-REG-053

### CON-REG-008 — List the available services
Signature : listServices() → list of {serviceCode, versionNumber, fetchMode, requiredDocumentTypes, approvalEnabled} · errors: none
Entity    : ENT-REG-001
Traces    : REQ-REG-013

### CON-REG-009 — Resolve a stored version
Signature : getServicePackageVersion(serviceCode, versionNumber) → read-only full version {serviceKnowledge, serviceDefinition, queries, fetchMode, requiredDocumentTypes, approvalEnabled} · errors: not found (no such service code or version)
Entity    : ENT-REG-002
Traces    : REQ-REG-025

### CON-REG-010 — Read one service's current version summary
Signature : getService(serviceCode) → {serviceCode, versionNumber, fetchMode, requiredDocumentTypes, approvalEnabled} · errors: not found (RULE-REG-016)
Entity    : ENT-REG-001
Traces    : REQ-REG-014, REQ-REG-015

### CON-REG-011 — Supply a connection by name
Signature : getConnection(connectionName) → {connectionName, connectionType, endpoint, queryTool, dialect, credentialReference, limitedToViews} · errors: not found (connection not activated in this environment)
Entity    : ENT-REG-005
Traces    : REQ-REG-048

### CON-REG-012 — Supply the approval API of a version to the Employee Decision path
Signature : getApprovalApi(serviceCode, versionNumber) → {approvalEnabled, approvalApi (present only when enabled)} · errors: not found (no such service code or version)
Entity    : ENT-REG-002
Notes     : only INT's Employee Decision operation may call this item (raw idea §12; G2; AIAS-4). No other consumer, and never the LLM, receives the approval API definition.
Traces    : REQ-REG-044, REQ-REG-045

### CON-REG-013 — Check whether a service code is available
Signature : isServiceAvailable(serviceCode) → flag · errors: none (an unknown code answers false)
Entity    : ENT-REG-001
Traces    : REQ-REG-012, REQ-REG-001

## Stability
All 13 items are ADDITIVE in v1. Changing or removing one a consumer depends on requires a BREAKING version with an ADR.
══════════════════════════════════════════════════════════════════

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

