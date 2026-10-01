## BUSINESS POLICIES — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module   : DOC     Source of truth : user vision text + dialogue resolutions
Read by  : P0.5 (every user story cites the policies it serves)
══════════════════════════════════════════════════════════════════

CLIENT-SPECIFIC POLICIES   (only from user text or confirmed dialogue answers)

POL-DOC-001 — Documents only by the service's fetch mode
  Statement : The system shall obtain the documents of a Check only by the fetch mode its service definition names, one of `path`, `blob` or `manual`.
  Pattern   : ubiquitous
  Trigger   : Fetch documents
  Rationale : All three modes are available and chosen per service; there is no other way in for a document.
  Source    : [KB:raw-idea.md §6] "All three modes are available, chosen per service"; §13 Decided "Documents"; owner statement (fixed list path | blob | manual, ADR-REG-005)
  Status    : CONFIRMED

POL-DOC-002 — `path` documents read at the path host data returns
  Statement : Where a service's fetch mode is `path`, the system shall read each document from host file storage at the path that the service definition's document source query returns for the request.
  Pattern   : optional
  Trigger   : Fetch documents
  Rationale : The file is in storage and its path is in the host database; the service package itself holds no file location.
  Source    : [KB:raw-idea.md §6] "Read from storage by the path the query returns"; domain-profile §8 D5 (the pilot uses `path`); ADR-DOC-001
  Status    : CONFIRMED

POL-DOC-003 — Paths validated inside the allowed storage root
  Statement : If a document's path does not resolve to a location inside the allowed storage root, then the system shall not open the file and shall report the document as unreadable.
  Pattern   : unwanted
  Trigger   : Fetch documents
  Rationale : Paths come from host data; opening anything outside the storage root would expose other files.
  Source    : [KB:raw-idea.md §12] "File paths are validated to be inside the allowed storage root before opening"; domain-profile §5 G5; owner statement
  Status    : CONFIRMED

POL-DOC-004 — The storage root is DOC's environment setting
  Statement : The system shall take the allowed storage root only from the document access setting of the environment, never from a service package, a host request or document data.
  Pattern   : ubiquitous
  Trigger   : Activate / Fetch documents
  Rationale : The boundary that protects host storage must not be movable by the data it protects against.
  Source    : owner statement (storage root belongs to DOC's environment settings, ADR-REG-006); [KB:raw-idea.md §12]
  Status    : CONFIRMED

POL-DOC-005 — `blob` documents over a read-only JDBC connection, never MCP
  Statement : Where a service's fetch mode is `blob`, the system shall read the document content over the service's read-only `jdbc` connection and never through the MCP server.
  Pattern   : optional
  Trigger   : Fetch documents
  Rationale : Binary content moved through MCP travels as Base64 text, which is slow and size-limited; host access stays read-only.
  Source    : [KB:raw-idea.md §6] "Read the column directly over JDBC with a read-only user … BLOBs are not moved through MCP"; domain-profile §5 G3, G14; owner statement (ADR-REG-004, POL-REG-012)
  Status    : CONFIRMED

POL-DOC-006 — `manual` mode uses only the employee's uploads
  Statement : Where a service's fetch mode is `manual`, the system shall use only the documents uploaded for that Check and shall not access host file storage or host BLOB columns.
  Pattern   : optional
  Trigger   : Fetch documents
  Rationale : Manual mode exists for services that are not allowed direct access; the report is marked accordingly.
  Source    : [KB:raw-idea.md §6] "`manual` — The service is not allowed direct access — The employee uploads the files; the report is marked accordingly"
  Status    : CONFIRMED

POL-DOC-007 — An upload belongs to one Check
  Statement : When the host integration hands over a document uploaded for a Check, the system shall keep it for that Check only, together with the document type the employee gave it.
  Pattern   : event
  Trigger   : Upload document
  Rationale : Manual upload arrives through the host integration's API; a document uploaded for one request must never serve another.
  Source    : owner statement "manual upload arrives via INT which hands it to DOC"; [KB:raw-idea.md §8 `POST /checks/{id}/documents`, §15 A1]; ADR-DOC-003
  Status    : CONFIRMED

POL-DOC-008 — Each document read by its format
  Statement : The system shall read each fetched document by its format: text extraction for PDF, table extraction for XLS spreadsheets, and the document-reading step for scans and images.
  Pattern   : ubiquitous
  Trigger   : Read documents
  Rationale : Each format carries its content differently; the Check needs the content, not the file.
  Source    : [KB:raw-idea.md §5 step 4] "text extraction for PDF, table extraction for XLS, OCR or a vision model for scans and images"; owner statement; ADR-DOC-004
  Status    : CONFIRMED

POL-DOC-009 — Scans and images read by a separately configured model
  Statement : The system shall read scans and images in a document-reading step whose model is configured separately from the model that compares data with the service knowledge and is replaceable by configuration alone.
  Pattern   : ubiquitous
  Trigger   : Read documents / Change model
  Rationale : The provider must stay replaceable, and document reading needs its own model choice.
  Source    : [KB:raw-idea.md §10] "Document reading (OCR or vision) is a separate step with its own configurable model"; domain-profile §5 G12; owner statement
  Status    : CONFIRMED

POL-DOC-010 — Every document gets an outcome
  Statement : The system shall give the Check an outcome for every document of the request and for every required document type that has no document: read, missing or unreadable.
  Pattern   : ubiquitous
  Trigger   : Fetch documents / Read documents
  Rationale : The report shows what was read, what is missing and what could not be read; a missing or unreadable required document prevents `COMPLIANT`.
  Source    : [KB:raw-idea.md §7] "What was read, what is missing, what could not be read"; §15 A1; domain-profile §5 G6; ADR-DOC-002
  Status    : CONFIRMED

POL-DOC-011 — Unreadable documents reported, never skipped
  Statement : If a document cannot be fetched or read, then the system shall report it as unreadable with the reason and continue with the remaining documents of the Check.
  Pattern   : unwanted
  Trigger   : Fetch documents / Read documents
  Rationale : Anything that could not be read appears in the report; nothing is skipped silently.
  Source    : [KB:raw-idea.md §12] "Anything that could not be read appears in the report. It is never skipped silently"; domain-profile §5 G6; owner statement; ADR-DOC-002
  Status    : CONFIRMED

POL-DOC-012 — Maximum file size
  Statement : If a document is larger than the maximum file size of the platform configuration, then the system shall not read its content and shall report it as unreadable.
  Pattern   : unwanted
  Trigger   : Fetch documents / Upload document
  Rationale : Each Check has limits; an oversized file must not exhaust the service.
  Source    : [KB:raw-idea.md §12] "Each check has limits: timeout, maximum rows, maximum file size"; domain-profile §5 G8; owner statement (per-check limits are platform configuration, ADR-REG-006)
  Status    : CONFIRMED

POL-DOC-013 — Document content is data, never instructions
  Statement : The system shall hand document content to the Check only as data, kept apart from any instruction, and shall never act on an instruction written inside a document.
  Pattern   : ubiquitous
  Trigger   : Read documents
  Rationale : A document is evidence to be compared, not a source of commands for the model.
  Source    : [KB:raw-idea.md §12] "Document content is treated as data, never as instructions to the model"; domain-profile §5 G7; owner statement
  Status    : CONFIRMED

POL-DOC-014 — Host documents are never changed
  Statement : The system shall access host file storage and host BLOB columns read-only and shall never change, move or delete a host document.
  Pattern   : ubiquitous
  Trigger   : Fetch documents
  Rationale : The service needs no write access to any host system.
  Source    : [KB:raw-idea.md §6, §11, §12] "read-only database user"; "The service needs no write access to any system"; domain-profile §5 G3
  Status    : CONFIRMED

POL-DOC-015 — No document carried from one Check to another
  Statement : The system shall keep no fetched document, uploaded document or read content of one Check for use by any other Check, and shall discard them when their Check ends.
  Pattern   : ubiquitous
  Trigger   : Check ends
  Rationale : Carrying state between requests risks leaking one request's data into another.
  Source    : [KB:raw-idea.md §2, §12] "No data is carried from one check to another"; domain-profile §5 G9, §7.2 verification context; ADR-DOC-003
  Status    : CONFIRMED

POL-DOC-016 — Free-tier document-reading model gets only synthetic or anonymised documents
  Statement : While a free-tier provider is configured as the document-reading model, the system shall send it only synthetic or anonymised documents.
  Pattern   : state
  Trigger   : Read documents / Change model
  Rationale : Free tiers may use submitted data for model training; real documents wait for the go-live provider decision.
  Source    : [KB:raw-idea.md §10] "Only synthetic or anonymised requests and documents are sent while a free provider is in use"; domain-profile §5 G13, §8 D3
  Status    : CONFIRMED

CUSTOM LOOKUP VALUES   (values the user named that the standard lists lack)
| Lookup key | Added values | Source |
|---|---|---|
| Fetch mode | `path`, `blob`, `manual` | [KB:raw-idea.md §4, §6]; owner statement (ADR-REG-005) |
| Document read status | `READ`, `MISSING`, `UNREADABLE` | [KB:raw-idea.md §7, §15 A1] "read / missing / unreadable"; ADR-DOC-002 |

SCOPE EXCEPTIONS   (explicit exclusions or non-standard scope)
| Excluded / Deferred | Statement | Activation trigger | Source |
|---|---|---|---|
| Per-service limits | The storage root and the limits are not set per service; one environment setting and one platform configuration apply to every Check | A later version with a stated per-service need | ADR-REG-006; REG RULE-REG-012 |
| Document storage for later viewing | DOC keeps no document after its Check ends; the report records each document's outcome, not the file | A later version that needs the employee to reopen uploaded files from the service | [KB:raw-idea.md §2, §12]; G9; ADR-DOC-003 |
| Caller authentication on uploads | Uploads arrive through INT without caller authentication in this version | Security version (A2) | [KB:raw-idea.md §15 A2]; D7 |
| Retrieval over document content (RAG, vector store) | Read content goes whole to the Check; no indexing | Same trigger as the platform RAG deferral | [KB:raw-idea.md §2] |

RESOLVED DECISIONS (dialogue, this module)
| # | Question | Recommended answer | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Where the storage root, the maximum file size and the blob JDBC source come from | Storage root: DOC environment setting (POL-DOC-004); maximum file size: platform configuration (POL-DOC-012); blob source: REG `jdbc` connection, read-only (POL-DOC-005) | yes — owner statement 2026-10-01 (ADR-REG-004, ADR-REG-006) | ADR-REG-004; ADR-REG-006 |
| 2 | Who runs the document source query of `path` and `blob` | DOC — `path` through the platform MCP query channel, `blob` over the `jdbc` connection (ADR-DOC-001) | recommended — pending owner confirmation at prd-approval | domain-profile §6, G4, G14 |
| 3 | What "missing" and "unreadable" mean and whether one failure stops the others | MISSING: a required document type with no document; UNREADABLE: listed or uploaded but not fetched or read — outside the root, not found, too large, unsupported format, failed reading, out of time — always with the reason; the remaining documents are still processed (ADR-DOC-002) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §7, §12]; G6 |
| 4 | How long DOC keeps an uploaded document, and how it knows the document type | Until its Check ends, then discarded; the employee gives the document type of each upload (ADR-DOC-003) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §6, §12]; G9 |
| 5 | XLS vs XLSX, scanned PDFs, other formats | `.xls` and `.xlsx` both read by table extraction; a PDF with no extractable text goes to the document-reading step; any other format reported unreadable "unsupported format" (ADR-DOC-004) | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §2, §5]; G6 |
| 6 | Does the free-tier rule (G13) bind document reading | Yes — POL-DOC-016; the go-live provider gate (D3) covers the document-reading model too | yes — owner text [KB:raw-idea.md §10] "requests and documents" | [KB:raw-idea.md §10]; D3 |
══════════════════════════════════════════════════════════════════
