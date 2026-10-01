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
