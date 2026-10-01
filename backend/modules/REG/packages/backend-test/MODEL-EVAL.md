<!-- source: PHASE:TEST-PLAN-BE / SUB:MODEL-EVAL -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-REG-019, AC-REG-028, AC-REG-031, AC-REG-060, API-REG-001, REQ-REG-018, REQ-REG-027, REQ-REG-030, REQ-REG-058 -->
<!-- SUB:MODEL-EVAL:START traces=AC-REG-019,AC-REG-028,AC-REG-031,AC-REG-060,REQ-REG-018,REQ-REG-027,REQ-REG-030,REQ-REG-058,API-REG-001 -->
## SUB MODEL-EVAL

What the model is given — the service knowledge a Check hands the LLM: whole, apart from every query and document reference, and with the pilot's thresholds as digits (the known-result request set of raw idea §10 runs against these). (4 TCs)

<!-- TC:TC-REG-019:START traces=AC-REG-019,REQ-REG-018 -->
### TC-REG-019 — Service knowledge supplied apart from queries and document references
Derived from : AC-REG-019  (REQ-REG-018)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` is current.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The supplied package returns the service knowledge in its own field, and that field contains 0 query texts and 0 document references.
Test data    : service code `scholarship-request`, version 3
<!-- TC:TC-REG-019:END -->

<!-- TC:TC-REG-028:START traces=AC-REG-028,REQ-REG-027 -->
### TC-REG-028 — The whole service knowledge is supplied unaltered
Derived from : AC-REG-028  (REQ-REG-027)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : BOUNDARY · data class BOUNDARY · language en
Preconditions: The knowledge file of version 3 of `scholarship-request` has 12000 characters.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The supplied service knowledge has 12000 characters and equals the stored serviceKnowledge.
Test data    : knowledge file of 12000 characters
<!-- TC:TC-REG-028:END -->

<!-- TC:TC-REG-031:START traces=AC-REG-031,REQ-REG-030,API-REG-001 -->
### TC-REG-031 — Queries only through the query part; no SQL in the service knowledge
Derived from : AC-REG-031  (REQ-REG-030)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: Version 3 of `scholarship-request` is current.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. A Check requests the service package through the in-process `getCurrentServicePackage("scholarship-request")`.
Expected     : The query part returns 2 queries and the serviceKnowledge field contains 0 SQL texts.
Test data    : service code `scholarship-request`, version 3
<!-- TC:TC-REG-031:END -->

<!-- TC:TC-REG-060:START traces=AC-REG-060,REQ-REG-058 -->
### TC-REG-060 — Pilot numeric conditions are written as digits
Derived from : AC-REG-060  (REQ-REG-058)
Exercises    : in-process `ServiceRegistry` interface (SVC-API; no HTTP operation — ADR-REG-011)
Rule / code  : —
Package      : SVC-API
Scenario     : HAPPY · data class VALID · language en
Preconditions: The delivered service knowledge of `scholarship-request`.
Host data    : none — package folders and the activation configuration are test fixtures of the environment (ADR-REG-007, ADR-REG-009); every SERVICE_CODE and DOCUMENT_TYPE value named here enters the registry through the fixture folder itself
Steps        : 1. Read the delivered service knowledge (in-process `getCurrentServicePackage("scholarship-request").serviceKnowledge`).
Expected     : Each numeric condition contains its threshold as digits and 0 numeric conditions state their threshold in words.
Test data    : the delivered knowledge file
<!-- TC:TC-REG-060:END -->

<!-- SUB:MODEL-EVAL:END -->
