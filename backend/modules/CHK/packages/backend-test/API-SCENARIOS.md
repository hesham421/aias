<!-- source: PHASE:TEST-PLAN-BE / SUB:API-SCENARIOS -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-CHK-001, AC-CHK-002, AC-CHK-003, AC-CHK-008, AC-CHK-009, AC-CHK-010, AC-CHK-012, AC-CHK-014, AC-CHK-017, AC-CHK-018, AC-CHK-019, AC-CHK-020, AC-CHK-021, AC-CHK-025, AC-CHK-035, AC-CHK-036, AC-CHK-038, AC-CHK-039, AC-CHK-046, AC-CHK-047, AC-CHK-049, AC-CHK-058, AC-CHK-059, AC-CHK-063, AC-CHK-066, AC-CHK-067, AC-CHK-069, AC-CHK-070, AC-CHK-071, AC-CHK-079, AC-CHK-080, AC-CHK-081, AC-CHK-085, API-CHK-001, REQ-CHK-001, REQ-CHK-002, REQ-CHK-003, REQ-CHK-007, REQ-CHK-008, REQ-CHK-009, REQ-CHK-011, REQ-CHK-013, REQ-CHK-016, REQ-CHK-017, REQ-CHK-018, REQ-CHK-019, REQ-CHK-020, REQ-CHK-024, REQ-CHK-034, REQ-CHK-035, REQ-CHK-037, REQ-CHK-038, REQ-CHK-044, REQ-CHK-045, REQ-CHK-047, REQ-CHK-056, REQ-CHK-057, REQ-CHK-061, REQ-CHK-063, REQ-CHK-064, REQ-CHK-066, REQ-CHK-067, REQ-CHK-068, REQ-CHK-076, REQ-CHK-077, REQ-CHK-078, REQ-CHK-082 -->
<!-- SUB:API-SCENARIOS:START traces=AC-CHK-001,AC-CHK-002,AC-CHK-003,AC-CHK-008,AC-CHK-009,AC-CHK-010,AC-CHK-012,AC-CHK-014,AC-CHK-017,AC-CHK-018,AC-CHK-019,AC-CHK-020,AC-CHK-021,AC-CHK-025,AC-CHK-035,AC-CHK-036,AC-CHK-038,AC-CHK-039,AC-CHK-046,AC-CHK-047,AC-CHK-049,AC-CHK-058,AC-CHK-059,AC-CHK-063,AC-CHK-066,AC-CHK-067,AC-CHK-069,AC-CHK-070,AC-CHK-071,AC-CHK-079,AC-CHK-080,AC-CHK-081,AC-CHK-085,REQ-CHK-001,REQ-CHK-002,REQ-CHK-003,REQ-CHK-007,REQ-CHK-008,REQ-CHK-009,REQ-CHK-011,REQ-CHK-013,REQ-CHK-016,REQ-CHK-017,REQ-CHK-018,REQ-CHK-019,REQ-CHK-020,REQ-CHK-024,REQ-CHK-034,REQ-CHK-035,REQ-CHK-037,REQ-CHK-038,REQ-CHK-044,REQ-CHK-045,REQ-CHK-047,REQ-CHK-056,REQ-CHK-057,REQ-CHK-061,REQ-CHK-063,REQ-CHK-064,REQ-CHK-066,REQ-CHK-067,REQ-CHK-068,REQ-CHK-076,REQ-CHK-077,REQ-CHK-078,REQ-CHK-082 -->
### SUB API-SCENARIOS — happy paths of the CheckEngine operations and API-CHK-001

<!-- TC:TC-CHK-054:START traces=AC-CHK-001,REQ-CHK-001 -->
### TC-CHK-054 — Starting a Check returns its identifier before any service query is sent
Derived from : AC-CHK-001  (REQ-CHK-001)
Exercises    : CheckEngine.startCheck(serviceCode, requestNumber, employeeId) — in-process, honours CON-CHK-004 (no HTTP operation — ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: `scholarship-request` version 3 (fetch mode `path`, queries `request_details` on connection `main-db` (type `mcp`, read-only) and `attachments` (its document source query), input name `requestId`, required TRANSCRIPT and ID_CARD) is available; the result port double answers the new Check run with identifier 501; the query channel double records call times.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request · DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present as the required document types of scholarship-request version 3 — created by the same package load (service definition `required: [TRANSCRIPT, ID_CARD]`), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Call startCheck with service code `scholarship-request`, request number `1001` and employee identity `E-2041`.
Expected     : The identifier 501 is returned before any service query is sent; the result port holds 1 Check run with status RUNNING.
Test data    : service code `scholarship-request`, request number `1001`, employee `E-2041`, identifier 501 (from the AC)
<!-- TC:TC-CHK-054:END -->

<!-- TC:TC-CHK-055:START traces=AC-CHK-002,REQ-CHK-002 -->
### TC-CHK-055 — The request number and the employee identity are handed over exactly as sent
Derived from : AC-CHK-002  (REQ-CHK-002)
Exercises    : CheckEngine.startCheck(serviceCode, requestNumber, employeeId) — in-process, honours CON-CHK-004 (no HTTP operation — ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class EDGE · language en
Preconditions: `scholarship-request` version 3 (fetch mode `path`, queries `request_details` on connection `main-db` (type `mcp`, read-only) and `attachments` (its document source query), input name `requestId`, required TRANSCRIPT and ID_CARD); the result port double records the create call.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request · DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present as the required document types of scholarship-request version 3 — created by the same package load (service definition `required: [TRANSCRIPT, ID_CARD]`), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Call startCheck with request number `00-1001/A` and employee identity ` e.2041 `.
Expected     : The result port receives request number "00-1001/A" and employee identity " e.2041 " unchanged (no trim, no case change).
Test data    : request number `00-1001/A`, employee identity ` e.2041 ` (from the AC)
<!-- TC:TC-CHK-055:END -->

<!-- TC:TC-CHK-056:START traces=AC-CHK-003,REQ-CHK-003 -->
### TC-CHK-056 — A `path` Check is marked RUNNING and runs in the background
Derived from : AC-CHK-003  (REQ-CHK-003)
Exercises    : CheckEngine.startCheck(serviceCode, requestNumber, employeeId) — in-process, honours CON-CHK-004 (no HTTP operation — ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: `scholarship-request` version 3 (fetch mode `path`, queries `request_details` on connection `main-db` (type `mcp`, read-only) and `attachments` (its document source query), input name `requestId`, required TRANSCRIPT and ID_CARD).
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request · DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present as the required document types of scholarship-request version 3 — created by the same package load (service definition `required: [TRANSCRIPT, ID_CARD]`), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Start a Check of request 1001.
Expected     : The Check is set to status RUNNING and its first service query is sent after the identifier has been returned.
Test data    : fetch mode `path`, request 1001 (from the AC)
<!-- TC:TC-CHK-056:END -->

<!-- TC:TC-CHK-057:START traces=AC-CHK-008,REQ-CHK-007 -->
### TC-CHK-057 — The current version is loaded and recorded with the new Check run
Derived from : AC-CHK-008  (REQ-CHK-007)
Exercises    : CheckEngine.startCheck(serviceCode, requestNumber, employeeId) — in-process, honours CON-CHK-004 (no HTTP operation — ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The current version of `scholarship-request` is 3; the result port double records the create call.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Start a Check of request 1001.
Expected     : The create call carries service code "scholarship-request" and version number 3.
Test data    : version 3 (from the AC)
<!-- TC:TC-CHK-057:END -->

<!-- TC:TC-CHK-058:START traces=AC-CHK-009,REQ-CHK-008 -->
### TC-CHK-058 — A `manual` Check keeps its start version when a newer one becomes current
Derived from : AC-CHK-009  (REQ-CHK-008)
Exercises    : CheckEngine.confirmUploads(checkId) — in-process, honours CON-CHK-005 (ADR-CHK-017)
Rule / code  : RULE-CHK-007
Package      : SVC-API
Scenario     : STATE · data class VALID · language en
Preconditions: A `manual` Check started on version 3 of `scholarship-request` is AWAITING_DOCUMENTS; version 4 is loaded meanwhile.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Confirm the uploads. 2. Let the pipeline run.
Expected     : The queries, required document types and service knowledge used equal those of version 3, resolved by service code and version number 3 (getServicePackageVersion, not the current version).
Test data    : versions 3 and 4 (from the AC)
<!-- TC:TC-CHK-058:END -->

<!-- TC:TC-CHK-059:START traces=AC-CHK-010,REQ-CHK-009 -->
### TC-CHK-059 — Two services of different fetch modes run the same six steps
Derived from : AC-CHK-010  (REQ-CHK-009)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Two available services `scholarship-request` (fetch mode `path`) and `fee-waiver` (fetch mode `blob`); every double answers successfully.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request · SERVICE_CODE fee-waiver — present, available — created by REG's start-up load of the package folder services/fee-waiver/ (REQ-REG-005, REQ-REG-006 — ADR-CHK-020), shown by GET /api/v1/services/fee-waiver
Steps        : 1. Run one Check of each service to its end.
Expected     : Both Checks record the same 6 steps in the same order (version, queries, documents, deterministic checks, comparison, hand-over) and both reports have the same structure.
Test data    : the two services and fetch modes of the AC
<!-- TC:TC-CHK-059:END -->

<!-- TC:TC-CHK-060:START traces=AC-CHK-012,REQ-CHK-011 -->
### TC-CHK-060 — Only the version's non-source queries are sent, with their SQL text unaltered
Derived from : AC-CHK-012  (REQ-CHK-011)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : HAPPY · data class VALID · language en
Preconditions: `scholarship-request` version 3 (fetch mode `path`, queries `request_details` on connection `main-db` (type `mcp`, read-only) and `attachments` (its document source query), input name `requestId`, required TRANSCRIPT and ID_CARD); the query channel double records each SQL text.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request · DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present as the required document types of scholarship-request version 3 — created by the same package load (service definition `required: [TRANSCRIPT, ID_CARD]`), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Start a Check of request 1001 and let it run its service queries.
Expected     : Exactly 1 query is sent, `request_details`, and its SQL text equals the stored sqlText character for character.
Test data    : queries `request_details` and `attachments` (from the AC)
<!-- TC:TC-CHK-060:END -->

<!-- TC:TC-CHK-061:START traces=AC-CHK-014,REQ-CHK-013 -->
### TC-CHK-061 — A service query goes only through the MCP query channel of its read-only connection
Derived from : AC-CHK-014  (REQ-CHK-013)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : RULE-CHK-002
Package      : PORTS-QUERY
Scenario     : HAPPY · data class VALID · language en
Preconditions: `request_details` names connection `main-db` of type `mcp`, declared read-only; the query tool double of `main-db` and every other data source record calls.
Host data    : none
Steps        : 1. Let the query run.
Expected     : 1 call is sent to the query tool of `main-db`; 0 calls reach any other data source; 0 JDBC connections are opened by CHK.
Test data    : connection `main-db`, type `mcp`, read-only (from the AC)
<!-- TC:TC-CHK-061:END -->

<!-- TC:TC-CHK-062:START traces=AC-CHK-017,REQ-CHK-016 -->
### TC-CHK-062 — Each query call carries the maximum rows and the time left before the timeout
Derived from : AC-CHK-017  (REQ-CHK-016)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-QUERY
Scenario     : BOUNDARY · data class BOUNDARY · language en
Preconditions: The maximum rows is 1000, the Check timeout is 300 seconds and the Check has run for 40 seconds.
Host data    : none
Steps        : 1. Let the query `request_details` be sent.
Expected     : The call carries a row limit of 1000 (requested as 1001 to detect an over-limit answer) and a timeout of at most 260 seconds.
Test data    : maximum rows 1000, timeout 300 seconds, 40 seconds elapsed (from the AC)
<!-- TC:TC-CHK-062:END -->

<!-- TC:TC-CHK-063:START traces=AC-CHK-018,REQ-CHK-017 -->
### TC-CHK-063 — Documents are fetched once from Document Access with the Check's identifiers
Derived from : AC-CHK-018  (REQ-CHK-017)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : HAPPY · data class VALID · language en
Preconditions: A running Check 501 of request 1001 on `scholarship-request` version 3.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Let its service queries run.
Expected     : The Document Access fetch operation is called once with checkId 501, requestNumber "1001", serviceCode "scholarship-request" and versionNumber 3.
Test data    : Check 501, request 1001, version 3 (from the AC)
<!-- TC:TC-CHK-063:END -->

<!-- TC:TC-CHK-064:START traces=AC-CHK-019,REQ-CHK-018 -->
### TC-CHK-064 — The Check Engine opens no file and no JDBC connection itself
Derived from : AC-CHK-019  (REQ-CHK-018)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-DOCUMENT
Scenario     : HAPPY · data class VALID · language en
Preconditions: A running Check of a `path` service whose document source query lists `2026/1001/transcript.pdf`; file-system and JDBC access of CHK's classes are observed.
Host data    : none
Steps        : 1. Run the Check to its end.
Expected     : The Check Engine opens 0 files and 0 JDBC connections; the document content used equals the content returned by Document Access.
Test data    : path `2026/1001/transcript.pdf` (from the AC)
<!-- TC:TC-CHK-064:END -->

<!-- TC:TC-CHK-065:START traces=AC-CHK-020,REQ-CHK-019 -->
### TC-CHK-065 — Each required document type gives exactly one finding
Derived from : AC-CHK-020  (REQ-CHK-019)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : RULE-CHK-004
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Version 3 requires TRANSCRIPT and ID_CARD.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present as the required document types of scholarship-request version 3 — created by the same package load (service definition `required: [TRANSCRIPT, ID_CARD]`), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Let the deterministic checks run.
Expected     : The report contains exactly 2 required-document findings, one for TRANSCRIPT and one for ID_CARD.
Test data    : required types TRANSCRIPT and ID_CARD (from the AC)
<!-- TC:TC-CHK-065:END -->

<!-- TC:TC-CHK-066:START traces=AC-CHK-021,REQ-CHK-020 -->
### TC-CHK-066 — One READ outcome satisfies a required document type
Derived from : AC-CHK-021  (REQ-CHK-020)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : RULE-CHK-004
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Document Access returns 2 outcomes of type ID_CARD: one READ and one UNREADABLE with reason NOT_FOUND.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present as the required document types of scholarship-request version 3 — created by the same package load (service definition `required: [TRANSCRIPT, ID_CARD]`), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Let the deterministic checks run.
Expected     : The ID_CARD finding's outcome is SATISFIED.
Test data    : 2 ID_CARD outcomes (from the AC)
<!-- TC:TC-CHK-066:END -->

<!-- TC:TC-CHK-067:START traces=AC-CHK-025,REQ-CHK-024 -->
### TC-CHK-067 — The model states value, location, comparison and limit of an explicit condition
Derived from : AC-CHK-025  (REQ-CHK-024)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The service knowledge states "the GPA must be at least 3.0"; the query result of `request_details` has GPA 3.4.
Host data    : none
Steps        : 1. Let the comparison model's output be received and verified.
Expected     : The finding for that condition contains value found "3.4", location "request_details.GPA", comparison ">=" and limit "3.0"; its outcome is SATISFIED.
Test data    : GPA 3.4, limit 3.0 (from the AC)
<!-- TC:TC-CHK-067:END -->

<!-- TC:TC-CHK-068:START traces=AC-CHK-035,REQ-CHK-034 -->
### TC-CHK-068 — The whole service knowledge is the only service instruction of the model input
Derived from : AC-CHK-035  (REQ-CHK-034)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-MODEL
Scenario     : HAPPY · data class VALID · language en
Preconditions: Version 3's service knowledge is 4 200 characters long; the `ChatModel` double records the messages.
Host data    : none
Steps        : 1. Let the model input be built.
Expected     : The instruction (system) part contains the 4 200 characters unaltered and no other service text (only the fixed engine framing).
Test data    : service knowledge of 4 200 characters (from the AC)
<!-- TC:TC-CHK-068:END -->

<!-- TC:TC-CHK-069:START traces=AC-CHK-036,REQ-CHK-035 -->
### TC-CHK-069 — Query results and document content appear only between the data delimiters
Derived from : AC-CHK-036  (REQ-CHK-035)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-MODEL
Scenario     : HAPPY · data class VALID · language en
Preconditions: The query `request_details` returns 1 row; Document Access returns 2 READ outcomes; the `ChatModel` double records the messages.
Host data    : none
Steps        : 1. Let the model input be built.
Expected     : The row and the 2 contents appear only between the data delimiters of the user part; the instruction part contains 0 characters of them.
Test data    : 1 row, 2 READ outcomes (from the AC)
<!-- TC:TC-CHK-069:END -->

<!-- TC:TC-CHK-070:START traces=AC-CHK-038,REQ-CHK-037 -->
### TC-CHK-070 — The model call carries the fixed output schema and its output is validated
Derived from : AC-CHK-038  (REQ-CHK-037)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-MODEL
Scenario     : HAPPY · data class VALID · language en
Preconditions: A running Check reaching the comparison step; the `ChatModel` double records the prompt.
Host data    : none
Steps        : 1. Let the comparison model be called.
Expected     : The call carries 1 output schema with the 4 fields condition, outcome, evidence and note, and the output is validated against it.
Test data    : none beyond the Check of AC-CHK-001
<!-- TC:TC-CHK-070:END -->

<!-- TC:TC-CHK-071:START traces=AC-CHK-039,REQ-CHK-038 -->
### TC-CHK-071 — The report has one finding per condition and per required document type
Derived from : AC-CHK-039  (REQ-CHK-038)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The service knowledge of version 3 states 3 conditions and version 3 requires 2 document types.
Host data    : none
Steps        : 1. Run the Check to completion.
Expected     : The report contains exactly 5 findings: one per condition and one per required document type.
Test data    : 3 conditions, 2 required types (from the AC)
<!-- TC:TC-CHK-071:END -->

<!-- TC:TC-CHK-072:START traces=AC-CHK-046,REQ-CHK-044 -->
### TC-CHK-072 — A decided Overall Status is handed to the result port with the Check COMPLETED
Derived from : AC-CHK-046  (REQ-CHK-044)
Exercises    : CheckEndingService.complete / fail — the single ending path, in-process (ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Check 501 has 5 findings, 2 document outcomes and Overall Status NOT_COMPLIANT.
Host data    : none
Steps        : 1. Let the Overall Status be decided.
Expected     : The result port receives 1 complete call for Check 501 with status COMPLETED, Overall Status NOT_COMPLIANT, 5 findings and 2 document outcomes.
Test data    : Check 501 (from the AC)
<!-- TC:TC-CHK-072:END -->

<!-- TC:TC-CHK-073:START traces=AC-CHK-047,REQ-CHK-045 -->
### TC-CHK-073 — The report metadata names version, fetch mode, model, times and employee
Derived from : AC-CHK-047  (REQ-CHK-045)
Exercises    : CheckEndingService.complete / fail — the single ending path, in-process (ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Check 501 of `scholarship-request` version 3, fetch mode `path`, comparison model `gemini-flash-lite`, employee `E-2041`.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Let its report be handed to the result port.
Expected     : The metadata equals serviceCode "scholarship-request", versionNumber 3, fetchMode "path", model "gemini-flash-lite", employee "E-2041", with a start time earlier than the end time.
Test data    : values of the AC
<!-- TC:TC-CHK-073:END -->

<!-- TC:TC-CHK-074:START traces=AC-CHK-049,REQ-CHK-047 -->
### TC-CHK-074 — Document outcomes are handed over without their content
Derived from : AC-CHK-049  (REQ-CHK-047)
Exercises    : CheckEndingService.complete / fail — the single ending path, in-process (ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Document Access returns 1 READ TRANSCRIPT outcome with 3 000 characters of content.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present as the required document types of scholarship-request version 3 — created by the same package load (service definition `required: [TRANSCRIPT, ID_CARD]`), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Let the report be handed to the result port.
Expected     : The TRANSCRIPT outcome handed over contains documentType, sourceMode, readStatus READ and 0 characters of content.
Test data    : 3 000 characters of content (from the AC)
<!-- TC:TC-CHK-074:END -->

<!-- TC:TC-CHK-075:START traces=AC-CHK-058,REQ-CHK-056 -->
### TC-CHK-075 — A `manual` Check waits in AWAITING_DOCUMENTS and runs no query
Derived from : AC-CHK-058  (REQ-CHK-056)
Exercises    : CheckEngine.startCheck(serviceCode, requestNumber, employeeId) — in-process, honours CON-CHK-004 (no HTTP operation — ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: `fee-waiver` version 2 has fetch mode `manual`.
Host data    : SERVICE_CODE fee-waiver — present, available — created by REG's start-up load of the package folder services/fee-waiver/ (REQ-REG-005, REQ-REG-006 — ADR-CHK-020), shown by GET /api/v1/services/fee-waiver
Steps        : 1. Start a Check of `fee-waiver`.
Expected     : The Check run is created with status AWAITING_DOCUMENTS; 0 service queries are sent.
Test data    : service `fee-waiver` version 2 (from the AC)
<!-- TC:TC-CHK-075:END -->

<!-- TC:TC-CHK-076:START traces=AC-CHK-059,REQ-CHK-057 -->
### TC-CHK-076 — Confirming the uploads runs the pipeline on the recorded version
Derived from : AC-CHK-059  (REQ-CHK-057)
Exercises    : CheckEngine.confirmUploads(checkId) — in-process, honours CON-CHK-005 (ADR-CHK-017)
Rule / code  : RULE-CHK-010
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Check 502 of `fee-waiver` version 2 is AWAITING_DOCUMENTS.
Host data    : SERVICE_CODE fee-waiver — present, available — created by REG's start-up load of the package folder services/fee-waiver/ (REQ-REG-005, REQ-REG-006 — ADR-CHK-020), shown by GET /api/v1/services/fee-waiver
Steps        : 1. Call confirmUploads(502).
Expected     : Check 502 is set to RUNNING (markRunning) and Document Access is asked for the documents of Check 502 on version 2.
Test data    : Check 502, version 2 (from the AC)
<!-- TC:TC-CHK-076:END -->

<!-- TC:TC-CHK-077:START traces=AC-CHK-063,REQ-CHK-061 -->
### TC-CHK-077 — A COMPLIANT Check sends exactly one end-of-Check notice
Derived from : AC-CHK-063  (REQ-CHK-061)
Exercises    : CheckEndingService.complete / fail — the single ending path, in-process (ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Check 501 completes with Overall Status COMPLIANT.
Host data    : none
Steps        : 1. Let the report be handed to the result port.
Expected     : Document Access receives exactly 1 end-of-Check notice for Check 501, after the complete call.
Test data    : Check 501 (from the AC)
<!-- TC:TC-CHK-077:END -->

<!-- TC:TC-CHK-078:START traces=AC-CHK-066,REQ-CHK-063 -->
### TC-CHK-078 — Two concurrent Checks share no data
Derived from : AC-CHK-066  (REQ-CHK-063)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Check 501 of request 1001 and Check 504 of request 1002 run at the same time with distinct query results and documents.
Host data    : none
Steps        : 1. Let both reach the comparison step.
Expected     : The model input of Check 504 contains 0 values from the query results or documents of request 1001.
Test data    : Checks 501 and 504, requests 1001 and 1002 (from the AC)
<!-- TC:TC-CHK-078:END -->

<!-- TC:TC-CHK-079:START traces=AC-CHK-067,REQ-CHK-064 -->
### TC-CHK-079 — Each model call carries no earlier conversation
Derived from : AC-CHK-067  (REQ-CHK-064)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-MODEL
Scenario     : HAPPY · data class VALID · language en
Preconditions: Check 501 of request 1001 was completed a minute earlier.
Host data    : none
Steps        : 1. Let Check 505 of request 1001 call the comparison model.
Expected     : The call contains 1 instruction part and 1 data part and 0 messages or outputs of Check 501.
Test data    : Checks 501 and 505 (from the AC)
<!-- TC:TC-CHK-079:END -->

<!-- TC:TC-CHK-080:START traces=AC-CHK-069,REQ-CHK-066 -->
### TC-CHK-080 — A second Check of the same request starts independently
Derived from : AC-CHK-069  (REQ-CHK-066)
Exercises    : CheckEngine.startCheck(serviceCode, requestNumber, employeeId) — in-process, honours CON-CHK-004 (no HTTP operation — ADR-CHK-017)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Check 501 of request 1001 is RUNNING; the result port double answers the next create call with 506.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Start a new Check of request 1001 on `scholarship-request`.
Expected     : A new Check identifier 506 is returned; Check 501 stays RUNNING; Check 506 runs its own service queries.
Test data    : Checks 501 and 506 (from the AC)
<!-- TC:TC-CHK-080:END -->

<!-- TC:TC-CHK-081:START traces=AC-CHK-070,REQ-CHK-067 -->
### TC-CHK-081 — Switching the provider by configuration needs no source change
Derived from : AC-CHK-070  (REQ-CHK-067)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-MODEL
Scenario     : HAPPY · data class VALID · language en
Preconditions: The comparison model configuration names provider A; environment data class SYNTHETIC.
Host data    : none
Steps        : 1. Change aias.check.comparison-model.provider and .model to provider B. 2. Restart the service and run a Check.
Expected     : Checks complete with the comparison model identifier of provider B; 0 source files of the Check Engine differ.
Test data    : providers A and B (placeholders, as in the AC)
<!-- TC:TC-CHK-081:END -->

<!-- TC:TC-CHK-082:START traces=AC-CHK-071,REQ-CHK-068 -->
### TC-CHK-082 — The comparison model is taken from configuration and recorded
Derived from : AC-CHK-071  (REQ-CHK-068)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : PORTS-MODEL
Scenario     : HAPPY · data class VALID · language en
Preconditions: The comparison model configuration declares model identifier `gemini-flash-lite` and tier FREE; aias.documents.data-class = SYNTHETIC.
Host data    : none
Steps        : 1. Let a Check reach the comparison step.
Expected     : The call is sent to `gemini-flash-lite`; the report metadata records model "gemini-flash-lite".
Test data    : model `gemini-flash-lite`, tier FREE, data class SYNTHETIC (from the AC)
<!-- TC:TC-CHK-082:END -->

<!-- TC:TC-CHK-083:START traces=AC-CHK-079,REQ-CHK-076,API-CHK-001 -->
### TC-CHK-083 — The Active Check of a new `manual` Check carries its upload-window deadline
Derived from : AC-CHK-079  (REQ-CHK-076)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}
Rule / code  : RULE-CHK-009
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The upload window is 60 minutes; a Check of `fee-waiver` (fetch mode `manual`) is created at 10:00 with identifier 502.
Host data    : SERVICE_CODE fee-waiver — present, available — created by REG's start-up load of the package folder services/fee-waiver/ (REQ-REG-005, REQ-REG-006 — ADR-CHK-020), shown by GET /api/v1/services/fee-waiver
Steps        : 1. Start the Check (CheckEngine.startCheck). 2. Call GET /api/v1/active-checks/502.
Expected     : 1 Active Check exists with checkId 502, checkStatus AWAITING_DOCUMENTS and deadlineAt 11:00; the GET answers 200 with ActiveCheckView {checkId 502, checkStatus AWAITING_DOCUMENTS, deadlineAt 11:00}.
Test data    : upload window 60 minutes, 10:00, identifier 502 (from the AC)
<!-- TC:TC-CHK-083:END -->

<!-- TC:TC-CHK-084:START traces=AC-CHK-080,REQ-CHK-077,API-CHK-001 -->
### TC-CHK-084 — Confirmation moves the Active Check to RUNNING with the timeout deadline
Derived from : AC-CHK-080  (REQ-CHK-077)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}
Rule / code  : RULE-CHK-009, RULE-CHK-010
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The Check timeout is 300 seconds; the Active Check of Check 502 is AWAITING_DOCUMENTS.
Host data    : none
Steps        : 1. Call confirmUploads(502) at 10:20:00. 2. Call GET /api/v1/active-checks/502.
Expected     : The Active Check of Check 502 is updated to checkStatus RUNNING with deadlineAt 10:25:00; the GET answers 200 with those values.
Test data    : timeout 300 seconds, confirmation at 10:20:00 (from the AC)
<!-- TC:TC-CHK-084:END -->

<!-- TC:TC-CHK-085:START traces=AC-CHK-081,REQ-CHK-078,API-CHK-001 -->
### TC-CHK-085 — A completed Check has no Active Check any more
Derived from : AC-CHK-081  (REQ-CHK-078)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}
Rule / code  : — (CHK-404-ACTIVE-CHECK-NOT-FOUND, PLATFORM-STD)
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Check 501 has an Active Check.
Host data    : none
Steps        : 1. Let Check 501 complete with Overall Status COMPLIANT. 2. Call GET /api/v1/active-checks/501.
Expected     : 0 Active Checks with checkId 501 exist; the GET answers 404 with ProblemDetail code CHK-404-ACTIVE-CHECK-NOT-FOUND.
Test data    : Check 501 (from the AC)
<!-- TC:TC-CHK-085:END -->

<!-- TC:TC-CHK-086:START traces=AC-CHK-085,REQ-CHK-082,API-CHK-001 -->
### TC-CHK-086 — The Active Check holds only identifier, status and deadline
Derived from : AC-CHK-085  (REQ-CHK-082)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: A RUNNING Check 501 of request 1001 with 2 READ documents.
Host data    : none
Steps        : 1. Read the Active Check of 501 (CHK_ACTIVE_CHECK row and GET /api/v1/active-checks/501).
Expected     : The row contains checkId 501, checkStatus RUNNING, deadlineAt and the standard fields only; the GET body has exactly checkId, checkStatus, deadlineAt; 0 values of request 1001 or its documents appear in either.
Test data    : Check 501, request 1001 (from the AC)
<!-- TC:TC-CHK-086:END -->
<!-- SUB:API-SCENARIOS:END -->
