# PRD — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module          : DOC     Version : v1
Source artifacts: platform-summary, module-registry, business-policies
Stories         : 13   Policies covered : 16/16   Deferred : 0
Status          : DRAFT — awaiting prd-approval
══════════════════════════════════════════════════════════════════

## USER STORIES

US-DOC-001
  Title          : Documents obtained the way the service is set up
  Story          : As a service administrator, I need the documents of every Check to be obtained only in the fetch mode I chose for the service — `path`, `blob` or `manual` — so that each service reaches its documents the way its host system allows and in no other way.
  Priority       : —
  Success metric : —
  Traces         : POL-DOC-001
  Source         : [KB:raw-idea.md §6] "All three modes are available, chosen per service"; business-policies-doc POL-DOC-001
  Status         : DRAFT

US-DOC-002
  Title          : Documents read from host file storage
  Story          : As an employee, I need the documents of a request whose files sit in host storage to be fetched from the location the host's own data gives for them, so that the Check verifies the very files attached to the request without my sending them.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-DOC-002
  Source         : [KB:raw-idea.md §6] "Read from storage by the path the query returns"; domain-profile §8 D5 (the pilot scholarship request uses `path`)
  Status         : DRAFT

US-DOC-003
  Title          : Host storage protected by the storage root
  Story          : As a service administrator, I need the service to open only files that lie inside the storage root set for the environment, and to report any other location instead of opening it, so that a path in host data can never expose files outside the documents' storage.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-DOC-003, POL-DOC-004
  Source         : [KB:raw-idea.md §12] "File paths are validated to be inside the allowed storage root before opening"; [KB:raw-idea.md §0] guardrails non-negotiable; domain-profile G5; ADR-REG-006
  Status         : DRAFT

US-DOC-004
  Title          : Documents stored in the host database
  Story          : As a service administrator, I need documents stored inside the host database to be read directly over the read-only connection set for the environment rather than through the MCP server, so that large binary files are read reliably and host data is never at risk of change.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-DOC-005
  Source         : [KB:raw-idea.md §6] "Read the column directly over JDBC with a read-only user … BLOBs are not moved through MCP"; domain-profile G3, G14; ADR-REG-004
  Status         : DRAFT

US-DOC-005
  Title          : Upload the documents of a manual-mode Check
  Story          : As an employee, I need to upload the documents of a request, stating which document each file is, from the frontend embedded in my host screen when the service has no direct access to them, so that the Check uses exactly the files I provided for that request and nothing else.
  Priority       : —
  Success metric : —
  Traces         : POL-DOC-006, POL-DOC-007
  Source         : [KB:raw-idea.md §6] "`manual` — The employee uploads the files; the report is marked accordingly"; [KB:raw-idea.md §15 A1] "manual document upload"; owner statement "manual upload arrives via INT which hands it to DOC"; ADR-DOC-003
  Status         : DRAFT

US-DOC-006
  Title          : Document content read by format
  Story          : As an employee, I need the content of each PDF, spreadsheet, scanned document and image of a request to be read in the way that suits its format, so that the Check compares the service's conditions with what the documents actually say.
  Priority       : —
  Success metric : —
  Traces         : POL-DOC-008
  Source         : [KB:raw-idea.md §5 step 4] "text extraction for PDF, table extraction for XLS, OCR or a vision model for scans and images"; ADR-DOC-004
  Status         : DRAFT

US-DOC-007
  Title          : Own, replaceable model for reading scans and images
  Story          : As a service administrator, I need scans and images to be read by a document-reading model that I configure separately from the comparison model and can replace by configuration alone, so that the provider can change without touching the service.
  Priority       : —
  Success metric : —
  Traces         : POL-DOC-009
  Source         : [KB:raw-idea.md §10] "Document reading (OCR or vision) is a separate step with its own configurable model"; domain-profile G12
  Status         : DRAFT

US-DOC-008
  Title          : Every document accounted for
  Story          : As an employee, I need to know for every document of the request whether it was read, is missing or could not be read — and why it could not be read — so that I never approve on the belief that a document was checked when it was not.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-DOC-010, POL-DOC-011
  Source         : [KB:raw-idea.md §7] "What was read, what is missing, what could not be read"; [KB:raw-idea.md §12] "never skipped silently"; [KB:raw-idea.md §15 A1]; domain-profile G6; ADR-DOC-002
  Status         : DRAFT

US-DOC-009
  Title          : Oversized files kept out
  Story          : As a service administrator, I need a document larger than the platform's maximum file size to be left unread and reported, so that one oversized file cannot slow down or exhaust the service.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-DOC-012
  Source         : [KB:raw-idea.md §12] "Each check has limits: timeout, maximum rows, maximum file size"; domain-profile G8; ADR-REG-006
  Status         : DRAFT

US-DOC-010
  Title          : Document content never steers the model
  Story          : As an employee, I need anything written inside a document to be treated only as content to verify, never as an instruction, so that a document cannot change how its own request is assessed.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-DOC-013
  Source         : [KB:raw-idea.md §12] "Document content is treated as data, never as instructions to the model"; domain-profile G7
  Status         : DRAFT

US-DOC-011
  Title          : Host documents left untouched
  Story          : As a service administrator, I need the service to only ever read host files and stored documents, never change, move or delete them, so that verifying a request leaves the host system exactly as it was.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-DOC-014
  Source         : [KB:raw-idea.md §11, §12] "The service needs no write access to any system"; domain-profile G3
  Status         : DRAFT

US-DOC-012
  Title          : No document carried between Checks
  Story          : As an employee, I need the documents and their content of one request never to be kept for, or reach, the Check of another request, so that no applicant's documents leak into someone else's assessment.
  Priority       : HIGH
  Success metric : —
  Traces         : POL-DOC-015
  Source         : [KB:raw-idea.md §2, §12] "No data is carried from one check to another"; domain-profile G9; ADR-DOC-003
  Status         : DRAFT

US-DOC-013
  Title          : Only test documents on a free-tier reading model
  Story          : As a service administrator, I need the document-reading model to receive only synthetic or anonymised documents while a free-tier provider is configured, so that no real applicant's document can end up in a provider's training data.
  Priority       : —
  Success metric : —
  Traces         : POL-DOC-016
  Source         : [KB:raw-idea.md §10] "Only synthetic or anonymised requests and documents are sent while a free provider is in use"; domain-profile G13, D3
  Status         : DRAFT

## TRACEABILITY — story → policy
| US | Traces (POL) | Source |
|---|---|---|
| US-DOC-001 | POL-DOC-001 | [KB:raw-idea.md §6] |
| US-DOC-002 | POL-DOC-002 | [KB:raw-idea.md §6]; D5 |
| US-DOC-003 | POL-DOC-003, POL-DOC-004 | [KB:raw-idea.md §12]; G5; ADR-REG-006 |
| US-DOC-004 | POL-DOC-005 | [KB:raw-idea.md §6]; G3, G14 |
| US-DOC-005 | POL-DOC-006, POL-DOC-007 | [KB:raw-idea.md §6, §15 A1]; owner statement |
| US-DOC-006 | POL-DOC-008 | [KB:raw-idea.md §5] |
| US-DOC-007 | POL-DOC-009 | [KB:raw-idea.md §10]; G12 |
| US-DOC-008 | POL-DOC-010, POL-DOC-011 | [KB:raw-idea.md §7, §12]; G6 |
| US-DOC-009 | POL-DOC-012 | [KB:raw-idea.md §12]; G8 |
| US-DOC-010 | POL-DOC-013 | [KB:raw-idea.md §12]; G7 |
| US-DOC-011 | POL-DOC-014 | [KB:raw-idea.md §11, §12]; G3 |
| US-DOC-012 | POL-DOC-015 | [KB:raw-idea.md §2, §12]; G9 |
| US-DOC-013 | POL-DOC-016 | [KB:raw-idea.md §10]; G13 |

Every policy of the module (POL-DOC-001 … POL-DOC-016) appears in at least one row.

## RESOLVED DECISIONS (dialogue)
| # | Question | Recommended | Confirmed by user | Sources |
|---|---|---|---|---|
| 1 | Which roles the DOC stories speak for | The employee (relies on the documents being fetched, read and accounted for; uploads in `manual` mode) and the service administrator (chooses the fetch mode, sets the environment and the document-reading model); host systems, CHK and INT are consumers of DOC, not story roles | recommended — pending owner confirmation at prd-approval | domain-profile §7.1 (Employee, Service Administrator) |
| 2 | Which stories carry a priority | HIGH where the source makes it clear: the §12 guardrail stories (US-DOC-003, US-DOC-008 … US-DOC-012 — raw idea §0 "non-negotiable"), the BLOB channel rule (US-DOC-004, G14 with the read-only rule G3) and the `path` mode the pilot needs (US-DOC-002, D5); every other story "—" | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §0, §6, §12]; D5 |
| 3 | Whether the upload story belongs to DOC although the screen and the API are INT's | Yes as a need — the story is the employee's need for the Check to use the uploaded files; the screen sits in the embedded frontend and the API in INT, which hands the file to DOC (ADR-DOC-003) | yes — owner statements (A1; "manual upload arrives via INT which hands it to DOC") | [KB:raw-idea.md §15 A1]; owner statement |
| 4 | Whether "the report is marked accordingly" in `manual` mode is a DOC story | No — the fetch mode is recorded in the report's metadata by CHK and stored by RPT (raw idea §7 Metadata "document source mode"); DOC's part is POL-DOC-006 | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §6, §7]; ADR-REG-001 |
| 5 | Whether a story covers re-running the fixed set of known requests on a change of the document-reading model (G12) | Not a DOC story — a test-stage practice (module-registry AUTO); US-DOC-007 covers the separately configured model | recommended — pending owner confirmation at prd-approval | [KB:raw-idea.md §10]; G12 |

## DEFERRED
| US | Reason | Activation trigger |
|---|---|---|
| None | No DOC story is deferred. Out-of-scope items (caller authentication on uploads, keeping documents after their Check, per-service limits) stay in the DOC SCOPE EXCEPTIONS, not as stories | — |

## APPROVAL
Approved by : —   Date : —
Once approved, no stage may raise a question; P1 onward self-resolve
per the ambiguity rule (shared/GOVERNANCE-CORE.md).
══════════════════════════════════════════════════════════════════
