## MODULE REGISTRY — Host Integration (INT)
══════════════════════════════════════════════════════════════════
Module Code    : INT   (profile.vocabulary.module_prefixes)
Bounded context: integration
Layer / Type   : L4 / integration     Execution tier : 4.1
Source         : NEW
Knowledge      : profiles/aias/knowledge/raw-idea.md §5, §6, §8, §11, §12, §13, §15 A1–A2; domain-profile §1, §3, §4 row 5, §5 G2, G9, §6, §7, §8 D6, D7
Readiness      : READY
══════════════════════════════════════════════════════════════════

Scope: everything a host system or the employee frontend touches (domain-profile §7.2 integration context). INT offers the four write operations that orchestrate other modules — start a Check (CHK, CON-CHK-004), hand over a manual upload (DOC, CON-DOC-003, with the Check's service code and version read from RPT, CON-RPT-003), confirm the uploads of a `manual` Check (CHK, CON-CHK-005) and record the Employee Decision (RPT, CON-RPT-006), calling the host's Approval API first where the Check's service package version enables it (REG, CON-REG-012) [KB:raw-idea.md §5, §8, §11]. It owns the employee frontend embedded in the host screen — the Checks of a request, the report, the manual upload and the decision (A1, ADR-INT-006) — which uses the same REST API as every host. Every read (a Check and its report, the Checks of a request, the decision agreement, the services, the uploaded documents, the active Check) stays with the module that owns the data and already serves it (ADR-INT-001). INT never runs a Check, never reads a document, never stores a report and never approves anything on its own (G2).

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity | Kind (config / transactional) | PRIVATE / SHARED | Source |
|---|---|---|---|
| None — Host Integration keeps no records of its own; the Check run and the Employee Decision are RPT's, the Uploaded Document is DOC's | — | — | ADR-INT-007; ADR-REG-001; CON-RPT-001; CON-DOC-003 |

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| None — INT masters no value list; every code it passes is owned by CHK, DOC, RPT or REG | — | — | ADR-INT-003, ADR-INT-007 |
Rule (profile): Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| Check status (`AWAITING_DOCUMENTS`, `RUNNING`, `COMPLETED`, `FAILED`) | CHK (CON-CHK-001, by value) |
| Overall Status (`COMPLIANT`, `NOT_COMPLIANT`, `NEEDS_MANUAL_REVIEW`) | CHK (CON-CHK-001, by value — shown by the frontend as stored) |
| Finding outcome (`SATISFIED`, `NOT_SATISFIED`, `UNDETERMINED`) | CHK (CON-CHK-002, by value) |
| Check failure reason | CHK (CON-CHK-003, by value) |
| Document read status and unreadable reason | DOC (CON-DOC-001, by value) |
| Fetch mode (`path`, `blob`, `manual`) | DOC (CON-DOC-002, by value) |
| Employee Decision (`APPROVED`, `REJECTED`) | RPT (CON-RPT-002, by value) |
| Service code, document type | REG (CON-REG-006 — never hardcoded) |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| Check Run (its identifier, status, service code, version number, request number, decision — through CON-RPT-003) | RPT | SOFT-READ — the Check identifier travels by value; nothing is stored | the upload needs the Check's service code and version and its status; the decision path needs its status, request number and existing decision (CON-RPT-001, CON-RPT-003) |
| Service Package Version (approval API definition — through CON-REG-012) | REG | SOFT-READ — in-process read, nothing stored | whether a version enables the Approval API and which host endpoint it names (ADR-INT-008) |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| CHK | SOFT-READ | start a Check (CON-CHK-004), confirm the uploads (CON-CHK-005) and the closed lists CHK carries (CON-CHK-001 … CON-CHK-003), in-process |
| RPT | SOFT-READ | read a Check (CON-RPT-003), record an Employee Decision (CON-RPT-006), the Check identifier and decision list (CON-RPT-001, CON-RPT-002), in-process |
| DOC | SOFT-READ | hand over an uploaded file (CON-DOC-003) and DOC's closed lists (CON-DOC-001, CON-DOC-002), in-process |
| REG | SOFT-READ | the approval API of a version (CON-REG-012), in-process — declared at entity level in the SRS; the owner's platform row stays [CHK, RPT, DOC] (ADR-INT-008) |
ROOT: NO

EXTERNAL
| System | What INT does with it | Source |
|---|---|---|
| Host systems (Oracle ADF, others) | call INT's REST API and embed the employee frontend; pass the employee identity, recorded as sent | [KB:raw-idea.md §8, §11, §15 A1, A2] |
| Host Approval API (optional per service) | called by INT once, after the employee confirms an approve decision, where the version enables it | [KB:raw-idea.md §11, §12]; ADR-INT-004, ADR-INT-009 |

POLICIES (business-policies-int.md)
| Policy | Short name |
|---|---|
| POL-INT-001 | A Check started at the host's request, answered at once |
| POL-INT-002 | The employee identity handed on as sent |
| POL-INT-003 | Refusals explained in the owner's words |
| POL-INT-004 | Manual uploads handed to Document Access |
| POL-INT-005 | Uploads only while the Check waits for documents |
| POL-INT-006 | Confirmed uploads continue the Check |
| POL-INT-007 | The Employee Decision handed to the Report Store |
| POL-INT-008 | Approval only from the employee's decision |
| POL-INT-009 | The Approval API only where the service enables it |
| POL-INT-010 | A failed approval records nothing |
| POL-INT-011 | A rejection never calls the Approval API |
| POL-INT-012 | The Checks of a request on the host screen |
| POL-INT-013 | Every finding shown beside its evidence |
| POL-INT-014 | Never shown COMPLIANT while a required document is missing or unreadable |
| POL-INT-015 | A running Check followed without reloading |
| POL-INT-016 | One REST API for hosts and the frontend |
| POL-INT-017 | Host Integration keeps nothing of its own |
| POL-INT-018 | Upload, confirmation and decision are separate actions |

AUTO-DECISIONS
AUTO: INT owns no entity and no table  FROM: ADR-INT-007; ADR-REG-001 (RPT owns the run records); CON-DOC-003 (DOC owns the Uploaded Document)  IF WRONG: an INT audit entity is added in a later version
AUTO: Refusals of CHK, DOC and RPT keep their owner's code and status in ProblemDetail  FROM: ADR-INT-003; ADR-CHK-018, ADR-DOC-012, ADR-RPT-013 ("INT maps … to ProblemDetail")  IF WRONG: INT re-codes them (a second list to maintain)
AUTO: The request number and employee identities are passed exactly as the host sent them, never verified against a directory  FROM: profile `conventions.identifiers`; [KB:raw-idea.md §15 A2]  IF WRONG: none in this version — caller authentication returns with the security version
AUTO: The reads the frontend needs are served by their owners (RPT, REG, DOC); INT adds no read façade  FROM: ADR-INT-001; ADR-RPT-005  IF WRONG: INT adds GET operations duplicating RPT's
AUTO: The employee frontend belongs to INT's frontend track  FROM: domain-profile §4 row 5, §7.2; [KB:raw-idea.md §15 A1]; RPT module registry AUTO-DECISION (RPT has no screen)  IF WRONG: P3.2 assigns the screens to another module

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Which HTTP operations INT owns | The four writes that orchestrate other modules; every read stays with its owner; no server-rendered page (ADR-INT-001) | recommended — confirmed at prd-approval | [KB:raw-idea.md §8, §15 A1]; ADR-RPT-005 |
| 2 | How a Check start is answered and how the employee is identified | Accepted at once, asynchronous, with the Check identifier and status; identity passed as sent; no caller authentication (ADR-INT-002) | recommended — confirmed at prd-approval; authentication deferred by owner (A2) | [KB:raw-idea.md §5, §8, §15 A2] |
| 3 | How other modules' refusals reach the caller | Owner's code, status and message unchanged in ProblemDetail; INT codes only for INT's own decisions (ADR-INT-003) | recommended — confirmed at prd-approval | profile error envelope; CON-CHK-004, CON-CHK-005, CON-DOC-003, CON-RPT-006 |
| 4 | Order and guards of the Approval API call | Approve decision only, on a COMPLETED undecided Check, where the version enables it; call first, record after; failure or timeout records nothing (502 / 504); rejection never calls it (ADR-INT-004) | recommended — confirmed at prd-approval | [KB:raw-idea.md §11, §12]; ADR-RPT-003 |
| 5 | When a manual upload is accepted | One file per upload, only while the Check is AWAITING_DOCUMENTS, handed to DOC with the Check's service code and version; confirmation separate (ADR-INT-005) | recommended — confirmed at prd-approval | [KB:raw-idea.md §6, §8]; CON-DOC-003, CON-CHK-005 |
| 6 | Scope of the employee frontend | Opened from the host screen for one request; four jobs, one screen each; REST API only (ADR-INT-006) | yes — owner A1 (scope); split recommended — confirmed at prd-approval | [KB:raw-idea.md §15 A1]; profile `screen_composition` |
| 7 | Does INT keep records | No entity and no table (ADR-INT-007) | recommended — confirmed at prd-approval | domain-profile G9; CON-RPT-006 |
| 8 | How INT reads the approval API of a version | In-process CON-REG-012; owner platform row kept; entity-level edge in the SRS (ADR-INT-008) | recommended — confirmed at prd-approval | CON-REG-012; ADR-REG-008 |
| 9 | How the Approval API is called | Method and path from the definition, request number as one encoded value, environment base address, configured timeout, one call, 2xx success (ADR-INT-009) | recommended — confirmed at prd-approval | [KB:raw-idea.md §4, §11] |
══════════════════════════════════════════════════════════════════

✓ Host Integration — P0 complete
  Next : P0.5 reads platform-summary.md · module-registry-int.md · business-policies-int.md
  Precondition for P0.5: every module in `depends_on` has a published contract or a passed gate — CHK (CON-CHK-001 … CON-CHK-011), RPT (CON-RPT-001 … CON-RPT-006), DOC (CON-DOC-001 … CON-DOC-005) and REG (CON-REG-001 … CON-REG-013) have published contracts
  Another module? None — INT is the last module of `gov.py plan-order`
