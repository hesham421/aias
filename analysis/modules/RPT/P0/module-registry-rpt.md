## MODULE REGISTRY — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module Code    : RPT   (profile.vocabulary.module_prefixes)
Bounded context: record
Layer / Type   : L3 / transactional     Execution tier : 3.1
Source         : NEW
Knowledge      : profiles/aias/knowledge/raw-idea.md §3, §7, §8, §9, §11, §12, §13, §15 A1–A2; domain-profile §3, §4 row 4, §5, §6, §7, §8 D4
Readiness      : READY
══════════════════════════════════════════════════════════════════

Scope: keeping what a Check FOUND and what the employee DECIDED (domain-profile §7.2 record context). RPT implements all six operations of the Check result port that CHK declares (ADR-REG-002, ADR-CHK-011; CON-CHK-006 … CON-CHK-011): it creates the Check run record and hands back its identifier, follows the Check's status, stores the completed report whole — Overall Status, one finding per condition with its evidence and note, one Check Document row per document outcome, the service queries that could not be read, and the metadata — or the failure reason of a failed Check, reads one Check back and lists the unfinished Checks. It offers Host Integration the reads of a Check's report, of the Checks of a request and of the decision agreement per service package version, and the recording of the Employee Decision beside the result [KB:raw-idea.md §9]. It removes reports older than the configured retention period by hard delete (domain-profile D4). RPT never runs a Check, never reads a document, never calls the Approval API (G2) and never calls CHK, DOC, REG or INT at run time.

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity | Kind (config / transactional) | PRIVATE / SHARED | Source |
|---|---|---|---|
| Check Run (one per Check: service code, service package version, fetch mode, request number, employee, status, Overall Status, failure reason, comparison model, timestamps, Employee Decision) | transactional | SHARED — its identifier travels by value to CHK, DOC and INT; no other module stores its rows | [KB:raw-idea.md §9 CHECK_RUN]; ADR-REG-001; CON-CHK-006; ADR-RPT-001, ADR-RPT-003 |
| Finding (one per condition: condition, outcome, evidence, note) | transactional | PRIVATE | [KB:raw-idea.md §7, §9 CHECK_FINDING]; G10; CON-CHK-008; ADR-RPT-001 |
| Check Document (one per document outcome: document type, source mode, read status, unreadable reason, detail) | transactional | PRIVATE | [KB:raw-idea.md §7, §9 CHECK_DOCUMENT]; ADR-REG-001; CON-CHK-008; ADR-RPT-001 |
| Unread Query (one per service query whose data could not be read: query name, detail) | transactional | PRIVATE | [KB:raw-idea.md §12] "never skipped silently"; G6; CON-CHK-008 `unreadQueries`; ADR-CHK-014; ADR-RPT-001 |

Not an entity of its own: the Employee Decision is recorded on the Check Run beside the result, as [KB:raw-idea.md §9] places it in `CHECK_RUN` (registry candidate CAND-RPT-003 folded into the Check Run — ADR-RPT-003). The report retention period is platform configuration, not an entity (ADR-RPT-004).

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| Employee Decision | The approve / reject decision the employee takes, recorded beside the report result — closed enum | recommended: `APPROVED`, `REJECTED` (ADR-RPT-003) | domain-profile §7.1 "Employee Decision — the approve / reject decision"; [KB:raw-idea.md §9, §11]; ADR-RPT-003 |
Rule (profile): Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| Check status (`AWAITING_DOCUMENTS`, `RUNNING`, `COMPLETED`, `FAILED`) | CHK (CON-CHK-001, by value through the result port) |
| Overall Status (`COMPLIANT`, `NOT_COMPLIANT`, `NEEDS_MANUAL_REVIEW`) | CHK (CON-CHK-001, profile closed enum, by value) |
| Finding outcome (`SATISFIED`, `NOT_SATISFIED`, `UNDETERMINED`) | CHK (CON-CHK-002, by value) |
| Check failure reason (`TIMED_OUT`, `MODEL_UNAVAILABLE`, `MODEL_OUTPUT_INVALID`, `MODEL_NOT_PERMITTED`, `UPLOAD_WINDOW_EXPIRED`, `INTERRUPTED`, `INTERNAL_ERROR`) | CHK (CON-CHK-003, by value) |
| Document read status (`READ`, `MISSING`, `UNREADABLE`) and unreadable reason (`OUTSIDE_STORAGE_ROOT`, `NOT_FOUND`, `TOO_LARGE`, `UNSUPPORTED_FORMAT`, `READING_FAILED`, `OUT_OF_TIME`, `SOURCE_QUERY_FAILED`, `MODEL_NOT_PERMITTED`) | DOC (CON-DOC-001, by value through CHK's result port — no runtime read of DOC) |
| Fetch mode (`path`, `blob`, `manual`) | DOC (CON-DOC-002, profile closed enum, by value) |
| Service code | REG (by value, as carried by CHK — never a foreign key; CON-REG-002) |
| Document type (open; pilot values `TRANSCRIPT`, `ID_CARD`) | REG (by value, as carried by CHK) |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| Check result (status, report, failure reason — values of the result port) | CHK | SOFT-READ — RPT implements CHK's port; values arrive by call, nothing is read from CHK | the content of every stored report (CON-CHK-006 … CON-CHK-011; ADR-REG-002) |
| Service Package version (business key: service code + version number) | REG | none — stored as values, never a foreign key; no runtime read | every report records the version it was built on (G11; CON-REG-002) |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| CHK | SOFT-READ | the Check result port RPT implements and the closed lists CHK carries by value (CON-CHK-001 … CON-CHK-003, CON-CHK-006 … CON-CHK-011), through the in-process interface |
ROOT: NO

CONSUMERS (context — the edges belong to the consuming modules)
| Module code | What it reads from RPT | Source |
|---|---|---|
| CHK | calls RPT's implementation of its own result port (no CHK → RPT edge — ADR-REG-002) | ADR-REG-002, ADR-CHK-011 |
| INT | reads a Check's status and report, the Checks of a request and the decision agreement; records the Employee Decision | domain-profile §6 (RPT → INT); [KB:raw-idea.md §8, §11]; ADR-RPT-003, ADR-RPT-005 |

POLICIES (business-policies-rpt.md)
| Policy | Short name |
|---|---|
| POL-RPT-001 | Every started Check gets a stored Check run |
| POL-RPT-002 | Host identifiers kept exactly as sent |
| POL-RPT-003 | A Check's status only moves forward |
| POL-RPT-004 | A completed report is stored whole or not at all |
| POL-RPT-005 | Only the codes of the closed lists are stored |
| POL-RPT-006 | A completed Check has a result, a failed Check a reason |
| POL-RPT-007 | Nothing unread is dropped from the report |
| POL-RPT-008 | No document content or query results kept |
| POL-RPT-009 | An ended Check's report never changes |
| POL-RPT-010 | Unfinished Checks answered to the Check Engine |
| POL-RPT-011 | A Check's status and report readable |
| POL-RPT-012 | The Checks of a request listed |
| POL-RPT-013 | Employee Decision recorded beside the result |
| POL-RPT-014 | One Employee Decision per Check |
| POL-RPT-015 | Decisions only on a completed Check |
| POL-RPT-016 | Execution through the Approval API recorded |
| POL-RPT-017 | Decision agreement per service package version |
| POL-RPT-018 | The Report Store never approves |
| POL-RPT-019 | Reports kept for the retention period |
| POL-RPT-020 | Purge removes the whole Check run |
| POL-RPT-021 | No retention period, no purge |
| POL-RPT-022 | Unfinished Checks never purged |
| POL-RPT-023 | Every Check stored independently |

AUTO-DECISIONS
AUTO: RPT owns the Check Run, Finding, Check Document and Unread Query records and implements all six operations of CHK's result port  FROM: ADR-REG-001, ADR-REG-002 (owner-confirmed); CON-CHK-006 … CON-CHK-011  IF WRONG: CHK keeps its own run table (would reopen ADR-REG-001)
AUTO: The closed codes of CHK and DOC are stored as values with no lookup table and no runtime read of either module  FROM: ADR-CHK-001, ADR-CHK-016; CON-DOC-001 "RPT stores the codes it receives through CHK with no runtime read of DOC"; same pattern as ADR-REG-010, ADR-DOC-010  IF WRONG: RPT seeds lookup tables (duplicates the contracts' lists)
AUTO: The request number and the employee identity are stored as strings exactly as sent, never foreign keys; the service package version is stored as service code + version number  FROM: profile `conventions.identifiers`; CHK contract identifier rule; CON-REG-002  IF WRONG: none — the host data lives outside this schema
AUTO: Delete semantics are hard; a report leaves only by the retention purge  FROM: profile `stack.db.delete_semantics: hard`; domain-profile D4  IF WRONG: soft delete (contradicts the profile)
AUTO: RPT has no screen of its own; the employee sees the report and records the decision in the embedded frontend through INT's API  FROM: [KB:raw-idea.md §15 A1]; domain-profile §4 row 5  IF WRONG: P3.2 assigns a screen to RPT's frontend track

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Does RPT own the Check run record, findings and Check Document record | Yes, and it implements all six operations of CHK's result port (ADR-REG-001, ADR-REG-002, ADR-RPT-001) | yes — owner confirmed ADR-REG-001, ADR-REG-002 | [KB:raw-idea.md §9, §14] |
| 2 | Where the unread service queries of a completed report are kept | In an Unread Query record of their own beside the findings and Check Documents, so the report keeps everything that could not be read (ADR-RPT-001) | recommended — confirmed at prd-approval | [KB:raw-idea.md §12]; G6; CON-CHK-008; ADR-CHK-014 |
| 3 | How the Check status may change and whether an ended report may change | Only forward — created RUNNING or AWAITING_DOCUMENTS, then RUNNING, then COMPLETED or FAILED once; an ended report is final and only the Employee Decision is added (ADR-RPT-002) | recommended — confirmed at prd-approval | [KB:raw-idea.md §9]; ADR-CHK-015; CON-CHK-001 |
| 4 | Is the Employee Decision an entity of its own, which values, how often, on which Checks | Recorded on the Check Run beside the result; `APPROVED` or `REJECTED`; once per Check; only on a COMPLETED Check; with the deciding employee as sent, the time and whether it was executed through the Approval API (ADR-RPT-003) | recommended — confirmed at prd-approval | [KB:raw-idea.md §9, §11]; domain-profile §7.1; G2 |
| 5 | How the retention of D4 is applied | Report retention period in days is platform configuration; a scheduled purge hard-deletes Check runs that ended longer ago than the period, with all their records; no period configured → nothing is purged; unfinished Checks are never purged (ADR-RPT-004) | yes — owner D4 (period is configuration, hard delete); details recommended — confirmed at prd-approval | domain-profile §8 D4; profile `delete_semantics: hard` |
| 6 | Which reads RPT offers and who may view them | One Check's status and report, the Checks of a request (service code + request number, newest first) and the decision agreement per service package version, offered in-process to INT; no viewer restriction in this version (ADR-RPT-005) | recommended — confirmed at prd-approval; viewer restriction deferred by owner (D4, A2) | [KB:raw-idea.md §8, §9, §15 A1, A2]; domain-profile D4 |
══════════════════════════════════════════════════════════════════

✓ Report Store — P0 complete
  Next : P0.5 reads platform-summary.md · module-registry-rpt.md · business-policies-rpt.md
  Precondition for P0.5: every module in `depends_on` has a published contract or a passed gate — CHK (CON-CHK-001 … CON-CHK-011) has a published contract
  Another module? 4.1 INT (next in `gov.py plan-order`)
