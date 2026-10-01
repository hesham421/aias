<!-- source: PHASE:INT-XM -->
<!-- traces: REQ-DOC-002, REQ-DOC-003, REQ-DOC-004, REQ-DOC-012, REQ-DOC-014, REQ-DOC-020, REQ-DOC-021, REQ-DOC-035, REQ-DOC-051, REQ-DOC-052, XM-DOC-001, XM-DOC-002, XM-DOC-003, XM-DOC-004 -->
<!-- PHASE:INT-XM:START traces=REQ-DOC-002,REQ-DOC-003,REQ-DOC-004,REQ-DOC-012,REQ-DOC-014,REQ-DOC-020,REQ-DOC-021,REQ-DOC-035,REQ-DOC-051,REQ-DOC-052,XM-DOC-001,XM-DOC-002,XM-DOC-003,XM-DOC-004 -->
## PHASE INT-XM

Target module REG — one GRACEFUL-DEGRADATION TC per SOFT-READ edge (XM-DOC-001 … XM-DOC-004).
<!-- TC:TC-DOC-066:START traces=XM-DOC-001,REQ-DOC-002,REQ-DOC-003,REQ-DOC-020 -->
### TC-DOC-066 — REG version read fails — DOC returns its defined not-found result
Derived from : XM-DOC-001 (REQ-DOC-002, REQ-DOC-003, REQ-DOC-020)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011); in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003)
Rule / code  : REQ-DOC-003 → DOC-404-SERVICE-VERSION-NOT-FOUND (in-process)
Package      : XM-DOC-001
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: REG:DELIVERED (requires met). The REG interface `getServicePackageVersion` (CON-REG-009) answers not found for service `<service>` version `<n>`. When REG is not delivered the XM-DOC-001 package is skipped and recorded in execution-state.json → deferred_xm (if_not_met) — the case does not run.
Host data    : none
Steps        : 1. Call fetchDocuments for a Check naming `<service>` version `<n>`. 2. Call handOverUpload for a Check naming `<service>` version `<n>` with a file and a document type `<type>`.
Expected     : fetchDocuments ends with `ServiceVersionNotFoundException` (DOC-404-SERVICE-VERSION-NOT-FOUND) and fetches nothing; handOverUpload is refused with the same code and creates 0 Uploaded Documents; no unhandled exception, no 500.
Test data    : `<service>`, `<n>`, `<type>` are placeholders — the XM block names no values.
<!-- TC:TC-DOC-066:END -->
<!-- TC:TC-DOC-067:START traces=XM-DOC-002,REQ-DOC-004,REQ-DOC-012,REQ-DOC-051 -->
### TC-DOC-067 — REG read yields no document source query — every required type SOURCE_QUERY_FAILED
Derived from : XM-DOC-002 (REQ-DOC-004, REQ-DOC-012, REQ-DOC-051)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : — (ADR-DOC-007 outcome)
Package      : XM-DOC-002
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: REG:DELIVERED. The version returned by `getServicePackageVersion` (CON-REG-009) for service `<service>` requires 2 document types `<type-1>`, `<type-2>` and carries no query whose name equals its documentSourceQueryName (empty read of ENT-REG-003). If REG is not delivered the package is deferred (if_not_met).
Host data    : none
Steps        : 1. Call fetchDocuments for a Check on that version. 2. Count the MCP query channel calls and `jdbc` sessions.
Expected     : 2 outcomes with read status UNREADABLE and reason SOURCE_QUERY_FAILED (REQ-DOC-039, ADR-DOC-014); 0 MCP calls, 0 `jdbc` sessions; no unhandled exception.
Test data    : placeholders only — the XM block names no values.
<!-- TC:TC-DOC-067:END -->
<!-- TC:TC-DOC-068:START traces=XM-DOC-003,REQ-DOC-021,REQ-DOC-035 -->
### TC-DOC-068 — REG read yields an empty required-type set — defined result, no MISSING outcome
Derived from : XM-DOC-003 (REQ-DOC-021, REQ-DOC-035)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011); in-process `DocumentAccess.handOverUpload(checkId, serviceCode, versionNumber, documentType, fileName, bytes)` (CON-DOC-003)
Rule / code  : RULE-DOC-002 → DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE (in-process)
Package      : XM-DOC-003
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: REG:DELIVERED. The version of service `<service>` read through CON-REG-009 has an empty set of required document types (empty read of ENT-REG-004); its fetch mode is `manual`; Check `<checkId>` runs it and has 1 Uploaded Document of type `<type>` handed over earlier. If REG is not delivered the package is deferred (if_not_met).
Host data    : none
Steps        : 1. Call fetchDocuments for Check `<checkId>`. 2. Call handOverUpload for Check `<checkId>` with a file and document type `<type>`.
Expected     : fetchDocuments returns 1 outcome for the uploaded document and 0 MISSING outcomes; handOverUpload is refused with DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE (RULE-DOC-002) and creates 0 rows; no unhandled exception, no 500.
Test data    : placeholders only — the XM block names no values.
<!-- TC:TC-DOC-068:END -->
<!-- TC:TC-DOC-069:START traces=XM-DOC-004,REQ-DOC-012,REQ-DOC-014,REQ-DOC-052 -->
### TC-DOC-069 — REG connection read fails — every required type SOURCE_QUERY_FAILED
Derived from : XM-DOC-004 (REQ-DOC-012, REQ-DOC-014, REQ-DOC-052)
Exercises    : in-process `DocumentAccess.fetchDocuments(checkId, requestNumber, serviceCode, versionNumber, deadline)` (CON-DOC-004) — no HTTP operation (ADR-DOC-011)
Rule / code  : — (ADR-DOC-007 outcome)
Package      : XM-DOC-004
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: REG:DELIVERED. The version of service `<service>` requires 2 document types `<type-1>`, `<type-2>`; its document source query names connection `<connection>`, for which `getConnection` (CON-REG-011) answers not found. If REG is not delivered the package is deferred (if_not_met).
Host data    : none
Steps        : 1. Call fetchDocuments for a Check on that version. 2. Count the MCP query channel calls and `jdbc` sessions.
Expected     : 2 outcomes with read status UNREADABLE and reason SOURCE_QUERY_FAILED (stated by XM-DOC-004, ADR-DOC-007); the query is not run (0 MCP calls, 0 `jdbc` sessions); no unhandled exception.
Test data    : placeholders only — the XM block names no values.
<!-- TC:TC-DOC-069:END -->
<!-- PHASE:INT-XM:END -->
