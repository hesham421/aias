## MODULE REGISTRY — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module Code    : CHK   (profile.vocabulary.module_prefixes)
Bounded context: verification
Layer / Type   : L2 / engine     Execution tier : 2.1
Source         : NEW
Knowledge      : profiles/aias/knowledge/raw-idea.md §1, §2, §5, §6, §7, §9, §10, §12, §15 A1–A2; domain-profile §3, §4 row 2, §5, §6, §7, §8
Readiness      : READY
══════════════════════════════════════════════════════════════════

Scope: running one Check per request as the fixed, asynchronous pipeline of [KB:raw-idea.md §5] — accept the Check and hand back its identifier at once, load the current service package version, run the service definition's queries through the platform MCP query channel with the request number bound, obtain the documents and their read status from Document Access, run the deterministic checks in code (required documents present and readable, explicit values and dates), let the LLM compare the data and document content with the service knowledge through Spring AI `ChatModel` with structured output, derive the Overall Status, and write the Check's status, report and failure reason through the result port that CHK declares and RPT implements (ADR-REG-002). CHK holds no state between Checks (domain-profile §7.2 verification context, G9), owns none of the stored report rows (ADR-REG-001), and never calls the Approval API (G2).

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity | Kind (config / transactional) | PRIVATE / SHARED | Source |
|---|---|---|---|
| None — CHK owns no stored entity. The Check run record, the findings and the Check Document record are RPT's and are written through CHK's result port; a running Check's working data lives only for that Check | — | — | ADR-REG-001; ADR-REG-002; domain-profile §7.2 (verification context holds no state between checks), G9 |

Not owned by CHK (ground truth from the REG and DOC runs): the Check run record, findings and Check Document record (RPT, ADR-REG-001); the Uploaded Document (DOC, ADR-DOC-003); the per-Check limits — timeout, maximum rows, maximum file size — and the manual upload window, which are platform configuration, not entities (ADR-REG-006; ADR-CHK-004).

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| Overall status | The report's overall result, decided by CHK and carried by value through CHK's result port; RPT stores it with no runtime read of CHK beyond the port it implements. The term stays Report Store vocabulary (domain-profile §7.1) | `COMPLIANT`, `NOT_COMPLIANT`, `NEEDS_MANUAL_REVIEW` | [KB:raw-idea.md §7]; profile `conventions.lookups`; ADR-CHK-001 |
| Check status | Where a Check stands in its life — closed enum, carried by value to RPT; the status a host polls | recommended: `AWAITING_DOCUMENTS`, `RUNNING`, `COMPLETED`, `FAILED` (ADR-CHK-001, ADR-CHK-004) | [KB:raw-idea.md §5 "the host starts it and then polls", §8 "Return the status", §9 CHECK_RUN "status, result"]; ADR-CHK-001 |
| Finding outcome | The outcome of one condition — closed enum, carried by value to RPT | recommended: `SATISFIED`, `NOT_SATISFIED`, `UNDETERMINED` (ADR-CHK-002) | [KB:raw-idea.md §7] "satisfied or not"; ADR-CHK-002 |
| Check failure reason | Why a Check ended `FAILED` — closed enum, carried by value to RPT | recommended: `TIMED_OUT`, `MODEL_UNAVAILABLE`, `MODEL_OUTPUT_INVALID`, `MODEL_NOT_PERMITTED`, `UPLOAD_WINDOW_EXPIRED`, `INTERRUPTED`, `INTERNAL_ERROR` (ADR-CHK-005, ADR-CHK-006) | [KB:raw-idea.md §12] "never skipped silently"; G6, G8; ADR-CHK-005 |
Rule (profile): Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| Service code | REG |
| Document type (open; pilot values `TRANSCRIPT`, `ID_CARD`) | REG |
| Connection type (`mcp`, `jdbc`) | REG |
| Fetch mode (`path`, `blob`, `manual`) | DOC (profile closed enum, by value — CON-DOC-002) |
| Document read status (`READ`, `MISSING`, `UNREADABLE`) and unreadable reason | DOC (CON-DOC-001) |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| Service Package (current version: service knowledge, input name, queries, fetch mode, document source, required document types) | REG | SOFT-READ | CHK loads the package at the start of a Check, uses that version for the whole Check and records it (CON-REG-002, CON-REG-003, CON-REG-007, CON-REG-013; G11; ADR-REG-003) |
| Connection | REG | SOFT-READ | the `mcp` connection each service query names, read-only (CON-REG-005, CON-REG-011; G3) |
| Document Outcome (transient — one per document of the Check, with read status, reason and content as data) | DOC | SOFT-READ — returned by value, never stored by CHK | the documents the deterministic checks and the LLM comparison need (CON-DOC-004) |
| Check (identifier, status) | RPT | none — CHK declares the result port RPT implements, so CHK never depends on RPT | CHK creates, advances and ends the Check run record through its own port (ADR-REG-002; ADR-CHK-008) |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| REG | SOFT-READ | service availability, the current service package version and connections, through the in-process REG interface (CON-REG-007, CON-REG-011, CON-REG-013) |
| DOC | SOFT-READ | fetch and read the documents of a Check; end a Check (CON-DOC-004, CON-DOC-005), through the in-process DOC interface |
ROOT: NO

EXTERNAL SOURCES (not modules — no edge)
| Source | Access | Source |
|---|---|---|
| Host database via the Oracle SQLcl MCP server | read-only, through the platform MCP query channel, for every service query except the document source query (ADR-DOC-001); row limit and timeout applied by the engine before every call | domain-profile §6, D2; G3, G4, G8 |
| LLM provider via Spring AI `ChatModel` (comparison model) | no tools, structured output, replaceable by configuration; free tier only with synthetic or anonymised data | [KB:raw-idea.md §10]; G1, G12, G13; ADR-CHK-006 |

CONSUMERS (context — the edges belong to the consuming modules)
| Module code | What it reads from CHK | Source |
|---|---|---|
| RPT | implements CHK's result port: stores the Check run record, its status, findings, Check Document rows, metadata and failure reason by value; answers which Checks are unfinished | ADR-REG-001, ADR-REG-002; ADR-CHK-005 |
| INT | starts a Check; confirms the uploads of a `manual` Check are complete; polls the status and report from RPT | domain-profile §6 (CHK → INT); [KB:raw-idea.md §5, §8]; ADR-CHK-004 |

POLICIES (business-policies-chk.md)
| Policy | Short name |
|---|---|
| POL-CHK-001 | A Check starts at once and runs in the background |
| POL-CHK-002 | Only available services are checked |
| POL-CHK-003 | One service package version per Check, recorded |
| POL-CHK-004 | The same fixed pipeline for every Check |
| POL-CHK-005 | Queries run exactly as written, the request number bound |
| POL-CHK-006 | Host data read-only, document source query left to Document Access |
| POL-CHK-007 | Documents only through Document Access |
| POL-CHK-008 | Missing or unreadable required document prevents COMPLIANT |
| POL-CHK-009 | Explicit values and dates decided in code |
| POL-CHK-010 | The model compares only and has no tools |
| POL-CHK-011 | Service knowledge is the only instruction |
| POL-CHK-012 | Structured output in the fixed report structure |
| POL-CHK-013 | One finding per condition, with evidence and a note |
| POL-CHK-014 | Evidence must be found in the Check's own data |
| POL-CHK-015 | Overall status derived from the findings |
| POL-CHK-016 | Report metadata |
| POL-CHK-017 | Results stored only through the Report Store |
| POL-CHK-018 | Unread query data reported, never skipped |
| POL-CHK-019 | Check timeout |
| POL-CHK-020 | A Check that cannot finish fails with a reason, never a guessed status |
| POL-CHK-021 | Document Access told of every Check's end |
| POL-CHK-022 | Unfinished Checks closed at start-up |
| POL-CHK-023 | A `manual` Check waits for the employee's uploads |
| POL-CHK-024 | Upload window |
| POL-CHK-025 | Every Check independent |
| POL-CHK-026 | Comparison model through `ChatModel` only |
| POL-CHK-027 | Free-tier comparison model gets only synthetic or anonymised data |
| POL-CHK-028 | Known-result request set on every model change |
| POL-CHK-029 | The Check Engine never approves |

AUTO-DECISIONS
AUTO: CHK owns no stored entity; RPT owns the run, findings and Check Document rows and CHK writes them through its result port  FROM: ADR-REG-001, ADR-REG-002 (owner-confirmed)  IF WRONG: CHK owns the Check run record and RPT reads it (would need an RPT → CHK read of CHK tables, still acyclic)
AUTO: CHK runs every service query except the document source query, which DOC runs in every fetch mode  FROM: ADR-DOC-001 (owner-confirmed); G14  IF WRONG: CHK also runs the `path` document source query for its own data
AUTO: CHK sends DOC the end-of-Check notice on every ending path — report stored, failed, timed out, upload window expired, interrupted  FROM: ADR-DOC-008 (owner-confirmed)  IF WRONG: DOC purges uploads by age (DOC would need its own retention rule)
AUTO: The Check timeout and the maximum rows are platform configuration applied to every Check; the manual upload window joins them  FROM: ADR-REG-006 (owner-confirmed); profile tracks.backend CORE "limits (timeout / rows / file size)"  IF WRONG: per-service limits in the service definition (REG RULE-REG-012 rejects them today)
AUTO: The overall status, Check status, finding outcome and failure reason are closed lists CHK masters and carries by value through its result port; RPT stores the codes  FROM: ADR-CHK-001; same pattern as ADR-REG-005, ADR-DOC-002  IF WRONG: RPT masters them, which would need CHK → RPT (cycle)
AUTO: The comparison model identifier recorded in the report metadata is the one CHK used; the document-reading model is DOC's configuration and is not returned by CON-DOC-004  FROM: [KB:raw-idea.md §7 "model used", §10]; CON-DOC-004  IF WRONG: DOC adds the reading model to its Document Outcome in a new version
AUTO: The fixed known-result request set (G12) is a CHK policy because CHK owns the comparison model; its test cases belong to the MODEL-EVAL test phase  FROM: [KB:raw-idea.md §10]; profile tracks.backend test MODEL-EVAL; AIAS-10  IF WRONG: it stays a test-stage practice only
AUTO: CHK has no screen of its own; the employee starts and follows Checks in the embedded frontend through INT's API  FROM: [KB:raw-idea.md §15 A1]; domain-profile §4 row 5  IF WRONG: P3.2 assigns a screen to CHK's frontend track

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Does CHK own the Check run record | No — RPT owns it; CHK writes through its result port (ADR-REG-001, ADR-REG-002) | yes — owner confirmed ADRs | [KB:raw-idea.md §9, §14] |
| 2 | CHK's tier and dependencies | Tier 2, depends_on [REG, DOC] | yes — owner statement 2026-10-01 | owner platform-dependency input |
| 3 | Who runs the document source query; end-of-Check notice | DOC runs it; CHK sends DOC the end-of-Check notice on every ending path (ADR-DOC-001, ADR-DOC-008) | yes — owner confirmed ADRs | ADR-DOC-001; ADR-DOC-008 |
| 4 | Where the per-Check limits live | Platform configuration (ADR-REG-006) | yes — owner confirmed ADR | ADR-REG-006 |
| 5 | Which closed lists CHK masters and how RPT gets them | Overall status, Check status, finding outcome, failure reason — mastered by CHK, carried by value through the result port (ADR-CHK-001) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §7, §8, §9]; ADR-REG-002 |
| 6 | How the Overall Status follows from the findings | NOT_COMPLIANT if any condition is not satisfied (a missing required document is a condition not satisfied); otherwise NEEDS_MANUAL_REVIEW if any condition is undetermined, a required document is unreadable or a service query was not read; otherwise COMPLIANT (ADR-CHK-002) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §7]; G6 |
| 7 | How "explicit values and dates" are checked in code when the service definition declares no check rules (CON-REG-007) | The model states each explicit condition as value found, comparison and limit; code verifies the value is in the Check's data, the limit is in the service knowledge, and recomputes the comparison; any mismatch makes the finding UNDETERMINED (ADR-CHK-003) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §5 step 5, §7]; G10; CON-REG-007 |
| 8 | How a `manual` Check gets its uploads before it runs | The Check waits in AWAITING_DOCUMENTS until the employee confirms the uploads through INT; the wait is bounded by an upload window of the platform configuration (ADR-CHK-004) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §6, §8]; ADR-DOC-003, ADR-DOC-008 |
| 9 | How a Check ends when something goes wrong | Unread service queries are reported and block COMPLIANT; timeout, model unavailable, invalid model output, model not permitted, interruption and internal errors end the Check FAILED with a reason and no overall status; unfinished Checks are ended at start-up (ADR-CHK-005) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §12]; G6, G8 |
| 10 | How the free-tier rule binds the comparison model | Same declared facts as DOC: the model's tier and the environment's data class; FREE with REAL data → no model call, Check FAILED MODEL_NOT_PERMITTED (ADR-CHK-006) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §10]; G13; ADR-DOC-009 |
| 11 | Several Checks of one request; version in use | Allowed, each independent; a Check uses the version current at its start for its whole life (ADR-CHK-007) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §15 A1 "the checks of a request"]; G9, G11; ADR-REG-003 |
| 12 | What starting a Check requires and returns | An available service code, a request number and the employee's identity, kept as the host sent them; CHK creates the Check run record through its result port and returns its identifier at once; an unavailable service is refused with no Check created (ADR-CHK-008) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §5 step 1, §8]; CON-REG-013; profile `conventions.identifiers` |
══════════════════════════════════════════════════════════════════

✓ Check Engine — P0 complete
  Next : P0.5 reads platform-summary.md · module-registry-chk.md · business-policies-chk.md
  Precondition for P0.5: every module in `depends_on` has a published contract or a passed gate — REG (CON-REG-001 … CON-REG-013) and DOC (CON-DOC-001 … CON-DOC-005) have published contracts
  Another module? 3.1 RPT (next in `gov.py plan-order`)
