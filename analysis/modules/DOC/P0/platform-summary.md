# PLATFORM SUMMARY — Request Verification Service
══════════════════════════════════════════════════════════════════
Profile : aias   Domain profile : v1   Registry : v1.0.0
══════════════════════════════════════════════════════════════════

## OVERVIEW
The Request Verification Service is a standalone Spring Boot service (Java 21, Spring Boot 4, Spring AI 2.0) that a host system calls over REST to verify one government service request before an employee approves it [KB:raw-idea.md §1, §3]. For each Check it loads the service package of the requested service, runs the queries the service definition names through a read-only MCP connection, fetches the request's documents by the service's fetch mode (`path`, `blob` or `manual`), reads them by type, runs the deterministic checks in code, lets the LLM compare the data and document content with the service knowledge, and stores a structured, evidence-backed report in the service's own schema [KB:raw-idea.md §5, §7, §9]. The employee reviews the report in a React frontend embedded in the host screen and records the Employee Decision; where a service enables it, the service then calls the host's Approval API — only after the employee confirms [KB:raw-idea.md §11, §15 A1]. The service never makes the decision. Every service it verifies is configuration, not code, so the platform's foundation is the Service Registry that holds those packages and the connections they use (domain-profile §2, §4). Document Access, the next tier, is where host files and BLOBs enter the service: it is the module that applies the storage-root, file-size, read-only and "document content is data" guardrails before any document reaches the Check Engine [KB:raw-idea.md §6, §12]. Caller authentication is deferred to a later version; the §12 guardrails are part of this version (domain-profile D7).

## MODULES
| #   | Code | Module | Bounded context | Layer | Type | Status |
|-----|------|--------|-----------------|-------|------|--------|
| 0.1 | REG | Service Registry | configuration | L1 | master data (configuration) | EXISTING |
| 1.1 | DOC | Document Access | verification | L2 | engine | NEW |
| 2.1 | CHK | Check Engine | verification | L2 | engine | NEW |
| 3.1 | RPT | Report Store | record | L3 | transactional | NEW |
| 4.1 | INT | Host Integration | integration | L4 | integration | NEW |

Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is)
Numbering: [tier].[sequence within tier] — the user requests Phase 2 by this number.
All five codes are IN PROFILE (domain-profile §7.3; D1). REG has a prior module registry (analysis/modules/REG/P0/module-registry-reg.md) and is read as ground truth, not re-analysed here (engine §1 STEP C); the project registry's pipeline status still shows every module NOT STARTED because the orchestrator merges pipeline rows on completion. Phase 2 in this run: 1.1 DOC.

Ownership of the run records (closed by the REG run — owner statement, raw idea §14 "Runs, findings, documents" under RPT; ADR-REG-001):
- RPT owns the Check run record, the findings and the Check Document record.
- CHK runs the pipeline and writes its results through RPT's interface.
- DOC fetches and reads documents but does not own their stored rows.

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

Reconciliation with domain-profile §6 (owner graph kept; no edge changed — identical to the REG run's platform summary):
- §6 row "DOC depends on INT (manual upload received through the API), direction INT → DOC" states the data flow of a manual upload. In code INT receives the upload and hands it to DOC through DOC's interface, so the compile-time edge is INT → DOC (INT depends_on DOC), which is the owner's graph. Decision #3.
- §6 row "RPT depends on CHK" agrees with the owner's graph; CHK's writes to RPT go through a result port CHK declares and RPT implements (ADR-REG-002). Decision #4.
- DOC depends_on REG is the owner's statement; DOC reads the fetch mode, document source and required document types of the service definition and the `jdbc` connection for `blob` from REG (ADR-REG-004, ADR-REG-005; REG contract CON-REG-004, CON-REG-005, CON-REG-007, CON-REG-011).
- §6 row "CHK, DOC → host database via Oracle SQLcl MCP server" is kept: the MCP query channel is platform infrastructure (profile platform track "the MCP connection") used by CHK and DOC, owned by neither module, so it adds no module edge. Decision #6 (ADR-DOC-001).

## DEFERRED (not in scope for this version)
| Item | Reason / activation trigger |
|---|---|
| Workflow engine | profile: `forbidden` — the check pipeline is fixed code [KB:raw-idea.md §5] |
| Caller authentication (API key or mTLS) and the security phases | owner amendment A2 (D7) — returns with the security version; the owner already has the solution |
| Who may view stored reports | deferred with caller authentication (D4, A2) |
| Full administration UI and an internal permission system | out of scope [KB:raw-idea.md §2] — the service administrator maintains service packages directly |
| Multi-tenancy | single-tenancy deployment [KB:raw-idea.md §2, §13] |
| Conversation memory | a Check is one independent run (G9) [KB:raw-idea.md §2] |
| RAG and a vector store | service knowledge fits whole in the prompt; activation trigger: one service's knowledge grows to hundreds of pages [KB:raw-idea.md §2] |
| Multi-agent orchestration | out of scope [KB:raw-idea.md §2] |
| Server-rendered report page (`GET /checks/{id}/view`) | superseded by the employee frontend (A1, D6) |
| LLM provider for real request data | go-live gate (D3); no design element waits on it — free tier with synthetic / anonymised data only until then (G13); the gate covers the document-reading model as well |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Owner of the Check run record (registry OQ-1) | RPT owns the Check run record; CHK runs the pipeline and writes through RPT's interface (ADR-REG-001) | yes — owner statement 2026-10-01 | [KB:raw-idea.md §14]; project-registry §8 OQ-1 |
| 2 | Owner of the Check Document record (registry OQ-2) | RPT owns the Check Document record and the findings; DOC fetches and reads documents but does not own their stored rows (ADR-REG-001) | yes — owner statement 2026-10-01 | [KB:raw-idea.md §14, §9]; project-registry §8 OQ-2 |
| 3 | Direction of the DOC ↔ INT relation of domain-profile §6 | INT depends_on DOC (INT hands manual uploads to DOC's interface); the §6 row states data flow, not a DOC → INT code dependency | yes — owner statement 2026-10-01 ("manual upload arrives via INT which hands it to DOC") | domain-profile §6; owner platform-dependency input |
| 4 | CHK writes to RPT while RPT depends_on CHK | Keep the owner's graph; CHK declares a result port, RPT implements it (ADR-REG-002) | recommended — pending owner confirmation at prd-approval | domain-profile §6; ADR-REG-002 |
| 5 | Platform tiers and build order | REG 0 · DOC 1 · CHK 2 · RPT 3 · INT 4, as the owner stated | yes — owner statement 2026-10-01 | owner platform-dependency input; domain-profile §6 |
| 6 | Who runs the document source query, given §6 "CHK, DOC → host database via MCP" and G14 | DOC runs the service definition's document source query itself — through the platform MCP query channel for `path`, over the read-only `jdbc` connection for `blob`; the query channel is platform infrastructure, not a CHK or DOC asset, so no module edge is added (ADR-DOC-001) | recommended — pending owner confirmation at prd-approval | domain-profile §6, G4, G14; [KB:raw-idea.md §6]; ADR-REG-004; REG REQ-REG-040 |

## OPEN ITEMS
None — platform scope fully determined.

## NEXT STEP
Reply with a plain instruction to adjust, or with a module number to start Phase 2.
5 modules, 0 exceptions. Phase 2 for 1.1 DOC follows in this run.
