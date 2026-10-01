# PLATFORM SUMMARY — Request Verification Service
══════════════════════════════════════════════════════════════════
Profile : aias   Domain profile : v1   Registry : v1.0.0
══════════════════════════════════════════════════════════════════

## OVERVIEW
The Request Verification Service is a standalone Spring Boot service (Java 21, Spring Boot 4, Spring AI 2.0) that a host system calls over REST to verify one government service request before an employee approves it [KB:raw-idea.md §1, §3]. For each Check it loads the service package of the requested service, runs the queries the service definition names through a read-only MCP connection, fetches the request's documents by the service's fetch mode (`path`, `blob` or `manual`), reads them by type, runs the deterministic checks in code, lets the LLM compare the data and document content with the service knowledge, and stores a structured, evidence-backed report in the service's own schema [KB:raw-idea.md §5, §7, §9]. The employee reviews the report in a React frontend embedded in the host screen and records the Employee Decision; where a service enables it, the service then calls the host's Approval API — only after the employee confirms [KB:raw-idea.md §11, §15 A1]. The service never makes the decision. Every service it verifies is configuration, not code, so the platform's foundation is the Service Registry that holds those packages and the connections they use (domain-profile §2, §4). Document Access, tier 1, is where host files and BLOBs enter the service under the storage-root, file-size, read-only and "document content is data" guardrails [KB:raw-idea.md §6, §12]. The Check Engine, tier 2, is the only fixed logic: it runs every Check through the same pipeline and hands its status, report and failure reason to the Report Store through the result port it declares (ADR-REG-002, CON-CHK-006 … CON-CHK-011). The Report Store, tier 3, is the record context: it keeps what a Check FOUND — the Overall Status, one finding per condition with its evidence, the documents read / missing / unreadable, the service queries that could not be read and the metadata — and what the employee DECIDED beside it, which gives the direct accuracy measure of [KB:raw-idea.md §9]; it keeps each report for as long as the host keeps the verified request, as a configured period, and then removes it by hard delete (domain-profile D4). Caller authentication, and with it who may view stored reports, is deferred to a later version; the §12 guardrails are part of this version (domain-profile D4, D7).

## MODULES
| #   | Code | Module | Bounded context | Layer | Type | Status |
|-----|------|--------|-----------------|-------|------|--------|
| 0.1 | REG | Service Registry | configuration | L1 | master data (configuration) | EXISTING |
| 1.1 | DOC | Document Access | verification | L2 | engine | EXISTING |
| 2.1 | CHK | Check Engine | verification | L2 | engine | EXISTING |
| 3.1 | RPT | Report Store | record | L3 | transactional | NEW |
| 4.1 | INT | Host Integration | integration | L4 | integration | NEW |

Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is)
Numbering: [tier].[sequence within tier] — the user requests Phase 2 by this number.
All five codes are IN PROFILE (domain-profile §7.3; D1). REG, DOC and CHK have prior module registries (analysis/modules/REG/P0/module-registry-reg.md, analysis/modules/DOC/P0/module-registry-doc.md, analysis/modules/CHK/P0/module-registry-chk.md) and published contracts (CON-REG-001 … CON-REG-013, CON-DOC-001 … CON-DOC-005, CON-CHK-001 … CON-CHK-011); they are read as ground truth, not re-analysed here (engine §1 STEP C). The project registry's pipeline status still shows every module NOT STARTED because the orchestrator merges pipeline rows on completion. Phase 2 in this run: 3.1 RPT.

Ownership of the run records (closed by the REG run — owner statement, raw idea §14 "Runs, findings, documents" under RPT; ADR-REG-001):
- RPT owns the Check run record, the findings and the Check Document record, and records the Employee Decision beside the result.
- CHK runs the pipeline and writes its results through the result port it declares and RPT implements (ADR-REG-002, ADR-CHK-011; CON-CHK-006 … CON-CHK-011).
- DOC fetches and reads documents but does not own their stored rows.
- INT reads the reports from RPT and hands the Employee Decision to RPT (domain-profile §6 "INT depends on RPT").

## DEPENDENCIES — data, not prose (`gov.py graph` reads this named block; the build order is DERIVED from it; C0.1 holds it to its schema)
```yaml name=platform-dependencies
modules:
  REG: {tier: 0, depends_on: []}
  DOC: {tier: 1, depends_on: [REG]}
  CHK: {tier: 2, depends_on: [REG, DOC]}
  RPT: {tier: 3, depends_on: [CHK]}
  INT: {tier: 4, depends_on: [CHK, RPT, DOC]}
```
One entry per module of the platform, every one with its tier; `depends_on` lists the modules it reads from (a lower tier never depends on a higher one — `xm.tier_rule`). The build order, the waves and the critical path are computed from this block (`gov.py plan-order`); never written by hand.

Reconciliation with domain-profile §6 (owner graph kept; no edge changed — identical to the REG, DOC and CHK runs' platform summaries):
- §6 row "DOC depends on INT (manual upload received through the API), direction INT → DOC" states the data flow of a manual upload. In code INT receives the upload and hands it to DOC through DOC's interface, so the compile-time edge is INT → DOC (INT depends_on DOC), which is the owner's graph. Decision #3.
- §6 row "RPT depends on CHK" agrees with the owner's graph; CHK's writes to RPT go through a result port CHK declares and RPT implements (ADR-REG-002). Decision #4.
- DOC depends_on REG is the owner's statement; DOC reads the fetch mode, document source and required document types of the service definition and the `jdbc` connection for `blob` from REG (ADR-REG-004, ADR-REG-005).
- §6 row "CHK, DOC → host database via Oracle SQLcl MCP server" is kept: the MCP query channel is platform infrastructure used by CHK and DOC, owned by neither module, so it adds no module edge. Decision #6 (ADR-DOC-001).
- CHK depends_on [REG, DOC] is the owner's statement and agrees with §6 rows "CHK depends on REG" and "CHK depends on DOC". Decision #7.
- RPT depends_on [CHK] only: the document read status, unreadable reason and fetch mode codes RPT stores arrive by value through CHK's result port, and the service code and version number are stored as values; RPT reads the REG and DOC contracts as specifications only and calls neither module at run time (CON-DOC-001, CON-DOC-002 "with no runtime read of DOC"; ADR-RPT-001). Decision #8.
- §6 row "INT depends on RPT (report JSON, record the decision)" agrees with the owner's graph; RPT offers INT the report reads and the decision recording and never calls INT (ADR-RPT-003, ADR-RPT-005). Decision #8.

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
|---|---|
| Workflow engine | profile: `forbidden` — the check pipeline is fixed code [KB:raw-idea.md §5] |
| Caller authentication (API key or mTLS) and the security phases | owner amendment A2 (D7) — returns with the security version; the owner already has the solution |
| Who may view stored reports | deferred with caller authentication (D4, A2); in this version every caller reaching INT reads any stored report (ADR-RPT-005) |
| Full administration UI and an internal permission system | out of scope [KB:raw-idea.md §2] — the service administrator maintains service packages directly |
| Multi-tenancy | single-tenancy deployment [KB:raw-idea.md §2, §13] |
| Conversation memory | a Check is one independent run (G9) [KB:raw-idea.md §2] |
| RAG and a vector store | service knowledge fits whole in the prompt; activation trigger: one service's knowledge grows to hundreds of pages [KB:raw-idea.md §2] |
| Multi-agent orchestration | out of scope [KB:raw-idea.md §2] |
| Server-rendered report page (`GET /checks/{id}/view`) | superseded by the employee frontend (A1, D6) |
| LLM provider for real request data | go-live gate (D3); no design element waits on it — free tier with synthetic / anonymised data only until then (G13) |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Owner of the Check run record (registry OQ-1) | RPT owns the Check run record; CHK runs the pipeline and writes through RPT's implementation of CHK's result port (ADR-REG-001) | yes — owner statement 2026-10-01 | [KB:raw-idea.md §14]; project-registry §8 OQ-1 |
| 2 | Owner of the Check Document record (registry OQ-2) | RPT owns the Check Document record and the findings; DOC fetches and reads documents but does not own their stored rows (ADR-REG-001) | yes — owner statement 2026-10-01 | [KB:raw-idea.md §14, §9]; project-registry §8 OQ-2 |
| 3 | Direction of the DOC ↔ INT relation of domain-profile §6 | INT depends_on DOC (INT hands manual uploads to DOC's interface); the §6 row states data flow, not a DOC → INT code dependency | yes — owner statement 2026-10-01 | domain-profile §6; owner platform-dependency input |
| 4 | CHK writes to RPT while RPT depends_on CHK | Keep the owner's graph; CHK declares a result port, RPT implements it (ADR-REG-002) | yes — owner confirmed ADR-REG-002 | domain-profile §6; ADR-REG-002 |
| 5 | Platform tiers and build order | REG 0 · DOC 1 · CHK 2 · RPT 3 · INT 4, as the owner stated | yes — owner statement 2026-10-01 | owner platform-dependency input; domain-profile §6 |
| 6 | Who runs the document source query | DOC runs it itself; the query channel is platform infrastructure, so no module edge is added (ADR-DOC-001) | yes — owner confirmed ADR-DOC-001 | domain-profile §6, G4, G14; ADR-DOC-001 |
| 7 | CHK's tier and dependencies | Tier 2, depends_on [REG, DOC]; CHK reaches RPT only through its own result port and never reads INT | yes — owner statement 2026-10-01 | owner platform-dependency input; ADR-REG-002 |
| 8 | RPT's tier and dependencies | Tier 3, depends_on [CHK] only; DOC's and REG's codes are stored by value with no runtime read of either module; INT reaches RPT for the reports and the Employee Decision (ADR-RPT-001, ADR-RPT-003, ADR-RPT-005) | yes — owner statement 2026-10-01 (graph); ADRs confirmed at prd-approval | owner platform-dependency input; domain-profile §6; CON-DOC-001, CON-DOC-002 |

## OPEN ITEMS
None — platform scope fully determined.

## NEXT STEP
Reply with a plain instruction to adjust, or with a module number to start Phase 2.
5 modules, 0 exceptions. Phase 2 for 3.1 RPT follows in this run.
