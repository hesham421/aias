# PROJECT REGISTRY — Request Verification Service
══════════════════════════════════════════════════════════════════
Profile            : aias
Registry Version   : 1.0.0
Domain Profile     : analysis/domain/domain-profile.md v1
Last Updated       : 2026-10-01 by P-1 (registry step of the DOC v1 analysis-gate revise: OQ-1, OQ-2 resolved; 6 platform findings recorded)
Modules registered : 5   Entity candidates : 6   Open items : 0 (OQ-1, OQ-2 RESOLVED) · platform findings OPEN : 6
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
| OQ-1 | Which module owns the Check (check run) record — CHK, which runs the check and owns the term, or RPT, which stores runs? | Side A: domain-profile §7.1 maps the term "Check" to CHK; §3 / §4 row 2 make CHK run the pipeline. Side B: domain-profile §4 row 4 lists "Runs" in RPT's scope; §6 "RPT depends on CHK (the check run a report belongs to)"; [KB:raw-idea.md §9] puts `CHECK_RUN` with the report tables. | CAND-CHK-001 (§4, §5) | RESOLVED | RPT owns the Check run record; CHK runs the pipeline and writes through RPT's interface — platform-summary RESOLVED DECISIONS #1 (owner statement 2026-10-01), ADR-REG-001 |
| OQ-2 | Which module owns the Check Document record (type, source mode, read status) — RPT, which stores "documents", or DOC, which fetches and reads them? | Side A: domain-profile §4 row 4 lists "documents" in RPT's scope; [KB:raw-idea.md §9] `CHECK_DOCUMENT` is a report table; §7 report "Documents" part. Side B: domain-profile §4 row 3 gives DOC fetching and reading; §7.1 maps "Fetch Mode" to DOC. | CAND-RPT-002 (§4, §5) | RESOLVED | RPT owns the Check Document record and the findings; DOC fetches and reads documents but owns no stored Check Document row — platform-summary RESOLVED DECISIONS #2 (owner statement 2026-10-01), ADR-REG-001 |

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
| 2026-10-01 | RESOLUTION | OQ-1, OQ-2 | ADR-REG-001; platform-summary RESOLVED DECISIONS #1, #2 | §8 OQ-1 and OQ-2 set RESOLVED (resolved at P0 by owner statement; bookkeeping applied at the DOC v1 analysis-gate revise, finding G4) |
| 2026-10-01 | PLATFORM-FINDING | PF-1 … PF-6 | DOC v1 analysis gate (findings G2, G5) | 6 CAT-10 rows recorded below, all OPEN — obligations DOC's ADRs place on CHK, INT and RPT |

### Platform findings (CAT-10)

Findings a module-scoped stage recorded that are not that module's to settle (shared/REGISTRY-SCHEMA.md §4). Recorded, not fixed, by the finding module; closed only by the owner of the fix.

| Ref | Finding | Evidence | Found by | Belongs to | Status |
|---|---|---|---|---|---|
| PF-1 | CHK must leave the `blob` document source query to DOC and never run or duplicate it (it returns BLOB content, which must not cross MCP — G14); duplicating the `path` query is harmless | ADR-DOC-001 (reviewer challenge "recorded for the CHK analysis"); contract-doc.md CON-DOC-004 | DOC · P0 · v1 | CHK (its analysis / backend plan) | OPEN |
| PF-2 | INT must pass the Check's service code and version number with every upload handover | ADR-DOC-006 (Consequences "recorded for the INT analysis"); contract-doc.md CON-DOC-003 | DOC · P1 · v1 | INT (its upload operation) | OPEN |
| PF-3 | CHK must send the end-of-Check notice (`endCheck`) on every ending path — report stored, failed, timed out | ADR-DOC-008 (Consequences "recorded for CHK"); contract-doc.md CON-DOC-005 | DOC · P1 · v1 | CHK (its pipeline end paths) | OPEN |
| PF-4 | RPT's database must carry the CHECK constraints for DOCUMENT_READ_STATUS (3 values) and UNREADABLE_REASON (8 values) on its own Check Document columns | ADR-DOC-010; db-script-doc.md BLOCK 8; contract-doc.md CON-DOC-001 | DOC · P2 · v1 | RPT (its P2 db-script) | OPEN |
| PF-5 | INT's backend plan must list DOC's in-process rejection codes in its own error catalog and map them to ProblemDetail — DOC-400-INCOMPLETE-UPLOAD, DOC-404-SERVICE-VERSION-NOT-FOUND, DOC-422-FETCH-MODE-NOT-MANUAL, DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE, and since the revise DOC-409-CHECK-ENDED, DOC-422-UPLOAD-LIMIT-REACHED | ADR-DOC-012 (Consequences "recorded for INT"); ADR-DOC-015, ADR-DOC-016; backend-execution-plan-doc.md in-process rejection codes | DOC · P3.1 · v1 | INT (its P3.1 error catalog) | OPEN |
| PF-6 | Ordering guarantee: no upload handover for a Check may reach DOC after that Check's end-of-Check notice — INT hands over only while the Check awaits documents, CHK sends the notice only once the Check accepts no more documents (DOC refuses and sweeps as a safety net) | ADR-DOC-015; contract-doc.md CON-DOC-003, CON-DOC-005 | DOC · P1 · v1 (analysis-gate finding G2) | CHK and INT (Check lifecycle / upload confirmation) | OPEN |

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
