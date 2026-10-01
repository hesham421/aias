# PLATFORM SUMMARY — Request Verification Service
══════════════════════════════════════════════════════════════════
Profile : aias   Domain profile : v1   Registry : v1.0.0
══════════════════════════════════════════════════════════════════

## OVERVIEW
The Request Verification Service is a standalone Spring Boot service (Java 21, Spring Boot 4, Spring AI 2.0) that a host system calls over REST to verify one government service request before an employee approves it [KB:raw-idea.md §1, §3]. For each Check it loads the service package of the requested service, runs the queries the service definition names through a read-only MCP connection, fetches the request's documents by the service's fetch mode (`path`, `blob` or `manual`), reads them by type, runs the deterministic checks in code, lets the LLM compare the data and document content with the service knowledge, and stores a structured, evidence-backed report in the service's own schema [KB:raw-idea.md §5, §7, §9]. The employee reviews the report in a React frontend embedded in the host screen and records the Employee Decision; where a service enables it, the service then calls the host's Approval API — only after the employee confirms [KB:raw-idea.md §11, §15 A1]. The service never makes the decision. Every service it verifies is configuration, not code, so the platform's foundation is the Service Registry that holds those packages and the connections they use (domain-profile §2, §4). Caller authentication is deferred to a later version; the §12 guardrails are part of this version (domain-profile D7).

## MODULES
| #   | Code | Module | Bounded context | Layer | Type | Status |
|-----|------|--------|-----------------|-------|------|--------|
| 0.1 | REG | Service Registry | configuration | L1 | master data (configuration) | NEW |
| 1.1 | DOC | Document Access | verification | L2 | engine | NEW |
| 2.1 | CHK | Check Engine | verification | L2 | engine | NEW |
| 3.1 | RPT | Report Store | record | L3 | transactional | NEW |
| 4.1 | INT | Host Integration | integration | L4 | integration | NEW |

Status: NEW (Phase 2 produces) · EXISTING (Phase 2 extends) · EXCEPTION (read as-is)
Numbering: [tier].[sequence within tier] — the user requests Phase 2 by this number.
All five codes are IN PROFILE (domain-profile §7.3; D1). No module is past P0 in the registry's pipeline status, so none is EXISTING or EXCEPTION (project-registry §9). Phase 2 in this run: 0.1 REG.

Ownership of the run records (closes registry OQ-1 and OQ-2 — owner statement, raw idea §14 "Runs, findings, documents" under RPT):
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

Reconciliation with domain-profile §6 (owner graph kept; no edge changed):
- §6 row "DOC depends on INT (manual upload received through the API), direction INT → DOC" states the data flow of a manual upload. In code INT receives the upload and hands it to DOC through DOC's interface, so the compile-time edge is INT → DOC (INT depends_on DOC), which is the owner's graph. Reading the row as DOC depends_on INT would create the cycle DOC → INT → DOC. Decision #3.
- §6 row "RPT depends on CHK" agrees with the owner's graph. The owner's OQ-1 answer makes CHK write the run results through RPT's interface; that runtime call is carried by a result port that CHK declares and RPT implements, so the compile-time edge stays RPT → CHK and the graph stays acyclic. Decision #4 (ADR-REG-002).
- DOC depends_on REG is the owner's statement; §6 does not list it and does not contradict it (DOC reads the fetch mode and required documents from the service definition and the `blob` connection from REG).

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
| LLM provider for real request data | go-live gate (D3); no design element waits on it — free tier with synthetic / anonymised data only until then (G13) |

## RESOLVED DECISIONS (this phase)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Owner of the Check run record (registry OQ-1) | RPT owns the Check run record; CHK runs the pipeline and writes through RPT's interface (ADR-REG-001) | yes — owner statement 2026-10-01 | [KB:raw-idea.md §14] "Runs, findings, documents" under RPT; project-registry §8 OQ-1 |
| 2 | Owner of the Check Document record (registry OQ-2) | RPT owns the Check Document record and the findings; DOC fetches and reads documents but does not own their stored rows (ADR-REG-001) | yes — owner statement 2026-10-01 | [KB:raw-idea.md §14, §9]; project-registry §8 OQ-2 |
| 3 | Direction of the DOC ↔ INT relation of domain-profile §6 | INT depends_on DOC (INT hands manual uploads to DOC's interface); the §6 row states data flow, not a DOC → INT code dependency | yes — owner dependency statement 2026-10-01 | domain-profile §6; owner platform-dependency input |
| 4 | CHK writes to RPT while RPT depends_on CHK | Keep the owner's graph; CHK declares a result port, RPT implements it, so the compile-time edge stays RPT → CHK and the graph is acyclic (ADR-REG-002) | recommended — confirmed by owner at prd-approval 2026-10-01 | domain-profile §6; owner OQ-1 answer; profile `conventions.module_interface: in_process` |
| 5 | Platform tiers and build order | REG 0 · DOC 1 · CHK 2 · RPT 3 · INT 4, as the owner stated | yes — owner statement 2026-10-01 | owner platform-dependency input; domain-profile §6 |

## OPEN ITEMS
None — platform scope fully determined.

## NEXT STEP
Reply with a plain instruction to adjust, or with a module number to start Phase 2.
5 modules, 0 exceptions. Phase 2 for 0.1 REG follows in this run.
