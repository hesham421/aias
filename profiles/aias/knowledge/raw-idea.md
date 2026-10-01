# Request Verification Service — Raw Idea (project `aias`)

As of 2026-10-01. Author: Hesham Ezzat.
Save as: `governance-shared/profiles/aias/knowledge/raw-idea.md` and list it under `knowledge.files` in `profiles/aias.yaml`.

## 0. How the factory should read this file

- Section 13 "Decided" is locked input. `domain-profile`, P0 and the PRD must not reopen those points.
- Section 13 "Open" is the complete set of questions to resolve, by research and a recommended answer.
- Section 12 "Guardrails" are non-negotiable and should become formal requirements in P1.
- YAML and endpoint examples illustrate intent. Final names, schemas and contracts are for the analysis stages to define.

## 1. Purpose

A standalone service that verifies a government service request before an employee approves it. It collects the request's data and attached documents, compares them with the conditions of the service, and returns a short report: compliant or not, and where the problems are.

The employee stays the decision maker. The service informs the decision and never makes it.

The service is generic. It covers many services, each with its own conditions, data queries and documents, all defined in configuration. It supersedes the earlier "Reusable Agentic AI Library" idea, which was broader than the first real need.

## 2. Scope

In scope:

- A standalone Spring Boot service, called by host systems over REST.
- Per-service configuration: a knowledge file plus a structured definition, maintained by the service administrator. Employees never supply knowledge files.
- Read-only access to request data through an MCP server (Oracle expected).
- Document retrieval by file path, by database BLOB, or by manual upload.
- Reading PDF, XLS and image documents (other extensions may appear).
- A structured verification report, stored in a database and shown to the employee inside the host system.
- Optional execution of an approval API, per service, triggered by the employee.

Out of scope for now:

- Multi-tenancy.
- Conversation memory.
- RAG and a vector store.
- Multi-agent orchestration.
- A full administration UI and an internal permission system.

Memory is excluded because a check is one independent run, and carrying state between requests risks leaking one request's data into another. RAG is excluded because each service's knowledge fits whole in the prompt, which is more reliable for compliance than retrieving fragments. RAG becomes relevant only if one service's knowledge grows to hundreds of pages.

## 3. Architecture

Stack: Java 21, Spring Boot 4, Spring AI 2.0.

```text
Host system (Oracle ADF or other)
        |  REST
        v
Request Verification Service
  - Service Registry   loads each service package
  - Check Engine       fixed pipeline (the only fixed logic)
  - Report Store       saves the report and the employee decision
        |
        +--> MCP server        read-only queries on Oracle
        +--> Documents         storage path, BLOB or manual upload
        +--> LLM provider      through Spring AI, replaceable
        +--> Service database  runs, findings, documents
```

Each dependency sits behind an interface so it can be replaced without touching the engine.

## 4. Service package (the variable part)

Everything that differs between services lives in one package per service. Adding a service needs no code as long as it uses existing check types.

```text
services/<service-code>/
  knowledge.md     conditions and rules in natural language (read by the LLM)
  service.yaml     queries, required documents, fetch mode, optional approval API
```

The two files are separate on purpose. SQL and file locations are executed literally by the engine; only the conditions are interpreted by the LLM. Each package carries a version, and every report records the version it was built on.

```yaml
service: scholarship-request
version: 3
input: requestId

queries:
  request_details:
    connection: main-db
    sql: >
      SELECT r.status, r.gpa, s.national_id
      FROM requests r JOIN students s ON s.id = r.student_id
      WHERE r.id = :requestId
  attachments:
    connection: main-db
    sql: >
      SELECT a.doc_type, a.file_path
      FROM request_attachments a
      WHERE a.request_id = :requestId

documents:
  source: attachments
  type_column: doc_type
  fetch: path            # path | blob | manual
  path_column: file_path
  required: [TRANSCRIPT, ID_CARD]

approval:
  enabled: false
  api: POST /requests/{requestId}/approve
```

Connections are defined once, outside the service packages, and set at activation time for each environment.

```yaml
connections:
  main-db:
    type: mcp
    endpoint: ...
    query_tool: run_query
    dialect: oracle
```

## 5. Check flow (the fixed part)

1. The host system sends the service code, the request number and the employee's identity.
2. The engine loads the service package and runs its queries through the MCP connection.
3. It fetches the documents using the service's fetch mode.
4. It reads each document by type: text extraction for PDF, table extraction for XLS, OCR or a vision model for scans and images.
5. It runs the deterministic checks in code: required documents present, explicit values and dates.
6. The LLM compares the data and document content with the service knowledge.
7. The engine stores the structured report and makes it available to the host system.
8. The employee takes the action in the host system, or through the approval API where it is enabled.

A check takes time, so it runs asynchronously: the host starts it and then polls for the result.

## 6. Data and document access

Queries and documents use separate channels, each behind its own interface.

Queries go through `QueryExecutor`. The first implementation, `McpQueryExecutor`, calls the MCP server's query tool using the Spring AI MCP client. The engine calls MCP to run the queries written in `service.yaml`. The LLM is never given a tool that runs SQL.

Documents go through `DocumentFetcher`. All three modes are available, chosen per service.

| Mode | Used when | How |
| --- | --- | --- |
| `path` | The file is in storage and its path is in the database | Read from storage by the path the query returns |
| `blob` | The file is stored inside the database | Read the column directly over JDBC with a read-only user |
| `manual` | The service is not allowed direct access | The employee uploads the files; the report is marked accordingly |

BLOBs are not moved through MCP, because binary content would travel as Base64 text inside a message, which is slow and size-limited.

Requirements for the MCP server chosen at activation: bind-variable support or strict parameter type validation in the engine, structured (JSON) results, a row limit and a timeout.

## 7. Report model

The report has a fixed structure for every service, produced as structured output and stored as data.

| Part | Content |
| --- | --- |
| Overall status | `COMPLIANT`, `NOT_COMPLIANT` or `NEEDS_MANUAL_REVIEW` |
| Findings | One per condition: satisfied or not, the evidence (actual value found), and a note for the employee |
| Documents | What was read, what is missing, what could not be read |
| Metadata | Service version, document source mode, model used, time, employee |

Every finding carries its evidence so the employee can verify it. A required document that is missing or unreadable prevents a `COMPLIANT` status.

## 8. API

| Endpoint | Purpose |
| --- | --- |
| `POST /checks` | Start a check for a service code and request number |
| `GET /checks/{id}` | Return the status and the report as JSON |
| `GET /checks/{id}/view` | Return a ready report page to embed in the host screen |
| `POST /checks/{id}/documents` | Upload documents in manual mode |
| `POST /checks/{id}/decision` | Record the employee's decision; call the approval API if the service enables it |

The calling system authenticates itself (API key or mTLS) and passes the employee's identity, which is recorded with the check.

## 9. Persistence

Reports are stored in database tables in a schema owned by the service, separate from the read-only user that reaches host data.

| Table | Holds |
| --- | --- |
| `CHECK_RUN` | One row per check: service, request number, employee, status, result, service version, model, timestamps, employee decision |
| `CHECK_FINDING` | One row per condition: satisfied flag, evidence, note |
| `CHECK_DOCUMENT` | One row per document: type, source mode, read status |

Storing the employee's decision beside the report result gives a direct measure of accuracy: where the two disagree, the service knowledge or a check needs attention.

## 10. LLM strategy

The provider is cloud-based for now and must be replaceable through configuration alone.

- Testing: a free cloud tier. Gemini Flash-Lite through Google AI Studio is the starting candidate. Free-tier limits change often.
- Test data only: free tiers may use submitted data for model training. Only synthetic or anonymised requests and documents are sent while a free provider is in use.
- Real data: the provider for real requests, cloud or inside the network, is decided before go-live.

Rules that keep the provider replaceable:

- The engine depends on Spring AI's `ChatModel` only, with no provider-specific features.
- Document reading (OCR or vision) is a separate step with its own configurable model.
- A fixed set of test requests with known expected results is run on every model change.

## 11. Host integration

The service is reached from Oracle ADF applications, and from other government systems later, over HTTP only. It is not embedded in ADF.

Display. The first version embeds the ready report page (`/checks/{id}/view`) inside the ADF screen. The same report is available as JSON, so it can later be rendered with ADF components or shown in the request log without changing the service.

Approval. Two options, chosen per service:

1. Default: the employee approves in the host system as today. The host notifies the service of the decision for the record. The service needs no write access to any system.
2. Optional: where the host exposes an approval API, the service calls it after the employee confirms, and records the report the approval was based on.

## 12. Guardrails

- The LLM analyses and summarises. It does not write SQL and does not trigger approval.
- Approval is executed only as a result of the employee's action.
- All access to host data uses a read-only database user, preferably limited to specific views.
- Query parameters are bound or strictly type-validated; SQL is never built from free text.
- File paths are validated to be inside the allowed storage root before opening.
- Anything that could not be read appears in the report. It is never skipped silently.
- Document content is treated as data, never as instructions to the model.
- Each check has limits: timeout, maximum rows, maximum file size.
- No data is carried from one check to another.

## 13. Decisions and open items

Decided:

| Topic | Decision |
| --- | --- |
| Form | A standalone service, not a library |
| Stack | Java 21, Spring Boot 4, Spring AI 2.0 |
| Tenancy | Single tenant |
| Configuration | One package per service, maintained by the administrator |
| Database access | Read-only through an MCP server, specified at activation |
| Documents | `path`, `blob` and `manual` modes all available |
| Decision maker | The employee; approval API is an optional second path |
| Display | Embedded report page in ADF first; JSON available for native display or the request log |
| Storage | Reports kept in the service's own database tables |
| LLM | Cloud, free tier for testing, replaceable by configuration |
| Memory, RAG, vector store | Not included |
| Frontend | None. Backend service only; the user interface belongs to the host system |

Open:

- Which MCP server to use for Oracle, confirmed against the requirements in section 6.
- Which LLM provider is permitted for real request data.
- How the host system authenticates to the service: API key or mTLS.
- Report retention period and who may view stored reports.
- The first service to implement as the pilot.

## 14. Proposed module split (for `domain-profile` to confirm)

| Code | Module | Scope |
| --- | --- | --- |
| `REG` | Service Registry | Service packages, versions, connections |
| `CHK` | Check Engine | The fixed pipeline, deterministic checks, LLM comparison |
| `DOC` | Document Access | `path`, `blob` and `manual` fetching; reading PDF, XLS and images |
| `RPT` | Report Store | Runs, findings, documents, employee decision |
| `INT` | Host Integration | REST API, server-rendered report page, optional approval API |

Tracks: backend only. This project has no frontend track and no frontend modules; the factory must not produce a frontend execution plan. The report page is plain HTML rendered by the backend itself, and any upload form lives in the host system. The platform track covers the MCP connection, the LLM provider configuration and the service database.
