## MODULE REGISTRY — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module Code    : DOC   (profile.vocabulary.module_prefixes)
Bounded context: verification
Layer / Type   : L2 / engine     Execution tier : 1.1
Source         : NEW
Knowledge      : profiles/aias/knowledge/raw-idea.md §2, §5, §6, §7, §10, §12, §15 A1–A2; domain-profile §4 row 3, §5, §6, §7, §8
Readiness      : READY
══════════════════════════════════════════════════════════════════

Scope: obtaining the documents of one Check by the service's fetch mode — `path` (host file storage), `blob` (host BLOB columns over a read-only JDBC connection) or `manual` (files the employee uploaded, handed over by INT) — and reading each one by its format: text extraction for PDF, table extraction for XLS, and a separate document-reading step with its own configurable model (OCR or a vision model) for scans and images [KB:raw-idea.md §5 step 3–4, §6, §10]. DOC gives the Check Engine an outcome for every document — read, missing or unreadable — and the read content as data; it holds no state between Checks (domain-profile §7.2 verification context, G9). DOC owns none of the stored report rows: the Check Document record is RPT's (ADR-REG-001).

ENTITIES OWNED   (names only — entity IDs are assigned by P1)
| Entity | Kind (config / transactional) | PRIVATE / SHARED | Source |
|---|---|---|---|
| Uploaded Document (a file the employee uploaded for one Check in `manual` fetch mode, with the document type the employee gave it; held only until that Check ends) | transactional | PRIVATE | [KB:raw-idea.md §6 `manual`, §8 `POST /checks/{id}/documents`, §15 A1]; owner statement "manual upload arrives via INT which hands it to DOC"; ADR-DOC-003 |

Not owned by DOC (ground truth from the REG run): the Check Document record (type, source mode, read status) and the Check run record are RPT's (ADR-REG-001; platform-summary decisions #1, #2). The allowed storage root is DOC's environment setting and the per-Check limits (timeout, maximum rows, maximum file size) are platform configuration — neither is an entity (ADR-REG-006).

LOOKUPS OWNED    (value lists this module masters)
| Lookup key | Description | Initial values (only those the user named) | Source |
|---|---|---|---|
| Fetch mode | How a service's documents are obtained — closed enum; REG carries the value in each service definition by value (ADR-REG-005) | `path`, `blob`, `manual` | domain-profile §7.1 (Fetch Mode → DOC); profile `conventions.lookups`; [KB:raw-idea.md §4, §6]; ADR-REG-005 |
| Document read status | The outcome DOC gives for each document of a Check — closed enum; RPT stores the value it receives through CHK, with no runtime read of DOC | `READ`, `MISSING`, `UNREADABLE` | [KB:raw-idea.md §7] "What was read, what is missing, what could not be read"; §15 A1 "documents read / missing / unreadable"; profile `conventions.lookups`; ADR-DOC-002 |
Rule (profile): Overall status (COMPLIANT | NOT_COMPLIANT | NEEDS_MANUAL_REVIEW), fetch mode (path | blob | manual) and document read status are closed enums owned by the service; service codes come only from the service registry and are never hardcoded.

LOOKUPS CONSUMED (from other modules — each is a SOFT-READ candidate)
| Lookup key | Owner code |
|---|---|
| Service code | REG |
| Document type (open; pilot values `TRANSCRIPT`, `ID_CARD`) | REG |
| Connection type (`mcp`, `jdbc`) | REG |

SHARED ENTITIES CONSUMED
| Entity | Owner code | HARD-FK / SOFT-READ | Why |
|---|---|---|---|
| Service Package (version in use: fetch mode, document source query and its columns, required document types) | REG | SOFT-READ | DOC fetches by the service's fetch mode and reports every required document type that has no document (REG CON-REG-004, CON-REG-007, CON-REG-009) |
| Connection | REG | SOFT-READ | the `mcp` connection of a `path` document source query and the read-only `jdbc` connection of a `blob` one (ADR-REG-004; REG CON-REG-005, CON-REG-011) |
| Check (identifier only) | RPT | SOFT-READ — value only, no FK, no read | an Uploaded Document belongs to exactly one Check; DOC (tier 1) receives the Check's identifier from its caller and never reads RPT (tier 3) (ADR-DOC-003) |

DEPENDENCIES
| Module code | HARD-FK / SOFT-READ | What is consumed |
|---|---|---|
| REG | SOFT-READ | service package version (fetch mode, document source, required document types) and connections, through the in-process REG interface |
ROOT: NO

EXTERNAL SOURCES (not modules — no edge)
| Source | Access | Source |
|---|---|---|
| Host file storage | read-only, only inside the allowed storage root | [KB:raw-idea.md §6, §12]; G5 |
| Host BLOB columns | read-only, over a `jdbc` connection, never through MCP | [KB:raw-idea.md §6]; G3, G14 |
| Host database via the Oracle SQLcl MCP server | read-only, through the platform MCP query channel, for the `path` document source query | domain-profile §6; D2; ADR-DOC-001 |
| Document-reading model (OCR or vision) via Spring AI | its own configurable model, replaceable by configuration | [KB:raw-idea.md §10]; G12, G13 |

CONSUMERS (context — the edges belong to the consuming modules)
| Module code | What it reads from DOC | Source |
|---|---|---|
| CHK | the documents of a Check: one outcome per document (read / missing / unreadable, with the reason) and the read content as data | domain-profile §6 (DOC → CHK); [KB:raw-idea.md §5] |
| INT | hands each manual upload of a Check to DOC | owner statement; domain-profile §6; platform-summary decision #3 |

POLICIES (business-policies-doc.md)
| Policy | Short name |
|---|---|
| POL-DOC-001 | Documents only by the service's fetch mode |
| POL-DOC-002 | `path` documents read at the path host data returns |
| POL-DOC-003 | Paths validated inside the allowed storage root |
| POL-DOC-004 | The storage root is DOC's environment setting |
| POL-DOC-005 | `blob` documents over a read-only JDBC connection, never MCP |
| POL-DOC-006 | `manual` mode uses only the employee's uploads |
| POL-DOC-007 | An upload belongs to one Check |
| POL-DOC-008 | Each document read by its format |
| POL-DOC-009 | Scans and images read by a separately configured model |
| POL-DOC-010 | Every document gets an outcome |
| POL-DOC-011 | Unreadable documents reported, never skipped |
| POL-DOC-012 | Maximum file size |
| POL-DOC-013 | Document content is data, never instructions |
| POL-DOC-014 | Host documents are never changed |
| POL-DOC-015 | No document carried from one Check to another |
| POL-DOC-016 | Free-tier document-reading model gets only synthetic or anonymised documents |

AUTO-DECISIONS
AUTO: DOC owns no stored report row; the Check Document record is RPT's and CHK carries DOC's outcomes to it  FROM: ADR-REG-001; platform-summary decision #2  IF WRONG: DOC owns the Check Document record and RPT reads it from DOC (would need an RPT → DOC edge)
AUTO: The allowed storage root is DOC's environment setting; the maximum file size and the Check timeout are platform configuration that DOC applies  FROM: ADR-REG-006; [KB:raw-idea.md §12]; profile tracks.backend CORE  IF WRONG: per-service limits in the service definition (REG RULE-REG-012 rejects them today)
AUTO: Fetch mode values are the closed list `path | blob | manual`; no other mode exists  FROM: ADR-REG-005; profile `conventions.lookups`  IF WRONG: a new mode is a new version of DOC and REG
AUTO: The `blob` JDBC source is a REG connection of type `jdbc`, read-only; DOC holds no data source of its own  FROM: ADR-REG-004; POL-REG-012  IF WRONG: DOC holds its own JDBC data source
AUTO: DOC runs the document source query of the service definition itself — `path` through the platform MCP query channel, `blob` over the `jdbc` connection — exactly as written, with the request number bound  FROM: domain-profile §6, G4, G14; ADR-DOC-001  IF WRONG: CHK runs the `path` document source query and hands DOC the type / path rows
AUTO: Document read status is the closed list READ · MISSING · UNREADABLE, with a reason for every document not read  FROM: [KB:raw-idea.md §7, §15 A1]; ADR-DOC-002  IF WRONG: a finer status list (e.g. a separate status for an oversized file) replaces the reason
AUTO: Spreadsheets are the XLS family (`.xls` and `.xlsx`); a PDF without extractable text is a scan and goes to the document-reading step; any other format is UNREADABLE with the reason "unsupported format"  FROM: [KB:raw-idea.md §2 "other extensions may appear", §5 step 4]; G6; ADR-DOC-004  IF WRONG: P1 adds a reader for a named extra format
AUTO: The regression set of known requests run on every model change (G12) covers the document-reading model, and is a test-stage practice, not a DOC policy  FROM: [KB:raw-idea.md §10]; G12  IF WRONG: P0 adds a DOC policy for it
AUTO: DOC has no screen of its own; the employee uploads in the embedded frontend through INT's API, and INT hands the file to DOC  FROM: [KB:raw-idea.md §15 A1]; domain-profile §4 row 5 (INT: "employee frontend's API surface"); owner statement; ADR-DOC-003  IF WRONG: P3.2 assigns the upload screen to DOC's frontend track

RESOLVED DECISIONS (dialogue, this module)
| # | Point | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Does DOC own the Check Document record | No — RPT owns it; DOC fetches and reads (ADR-REG-001) | yes — owner statement 2026-10-01 | [KB:raw-idea.md §9, §14] |
| 2 | DOC's tier and dependencies | Tier 1, depends_on [REG] | yes — owner statement 2026-10-01 | owner platform-dependency input |
| 3 | Where the storage root and the limits live | Storage root: DOC environment setting; timeout, rows, max file size: platform configuration (ADR-REG-006) | yes — owner statement 2026-10-01 | ADR-REG-006 |
| 4 | Where the `blob` JDBC source lives | A REG connection of type `jdbc`, read-only (ADR-REG-004, POL-REG-012) | yes — owner statement 2026-10-01 | ADR-REG-004 |
| 5 | How manual uploads reach DOC | INT receives them and hands them to DOC | yes — owner statement 2026-10-01 | owner input; domain-profile §6 |
| 6 | Who runs the document source query | DOC itself — `path` through the platform MCP query channel, `blob` over the `jdbc` connection (ADR-DOC-001) | recommended — pending owner confirmation at prd-approval | domain-profile §6, G4, G14; REG REQ-REG-040 |
| 7 | Document read status values and their meaning | READ · MISSING (a required document type with no document) · UNREADABLE (listed or uploaded but not fetched or not read, with the reason); one document's failure never stops the others (ADR-DOC-002) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §7, §12, §15 A1]; G6 |
| 8 | What DOC keeps of a manual upload, and for how long | An Uploaded Document, PRIVATE to DOC, bound to one Check by the Check's identifier as a value, with the document type the employee gave it, discarded when that Check ends (ADR-DOC-003) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §6, §8, §12]; G9 |
| 9 | Format routing for formats the owner did not name | XLS family = `.xls` and `.xlsx`; a PDF with no extractable text = a scan; any other format = UNREADABLE "unsupported format" (ADR-DOC-004) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §2, §5]; G6 |
══════════════════════════════════════════════════════════════════

✓ Document Access — P0 complete
  Next : P0.5 reads platform-summary.md · module-registry-doc.md · business-policies-doc.md
  Precondition for P0.5: every module in `depends_on` has a published contract or a passed gate — REG has a published contract (CON-REG-001 … CON-REG-013)
  Another module? 2.1 CHK (next in `gov.py plan-order`)
