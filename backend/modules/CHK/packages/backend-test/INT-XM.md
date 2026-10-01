<!-- source: PHASE:INT-XM -->
<!-- traces: REQ-CHK-005, REQ-CHK-006, REQ-CHK-007, REQ-CHK-008, REQ-CHK-011, REQ-CHK-013, REQ-CHK-015, REQ-CHK-019, REQ-CHK-057, XM-CHK-001, XM-CHK-002, XM-CHK-003, XM-CHK-004, XM-CHK-005 -->
<!-- PHASE:INT-XM:START traces=REQ-CHK-005,REQ-CHK-006,REQ-CHK-007,REQ-CHK-008,REQ-CHK-011,REQ-CHK-013,REQ-CHK-015,REQ-CHK-019,REQ-CHK-057,XM-CHK-001,XM-CHK-002,XM-CHK-003,XM-CHK-004,XM-CHK-005 -->
## PHASE INT-XM

One GRACEFUL-DEGRADATION TC per SOFT-READ edge (target REG; 5 TCs ≤ 8, so no SUB). Each exercises CHK's own in-process operation with REG:DELIVERED met and the target read empty or failing; when REG:DELIVERED is not met, the edge's block is skipped and recorded (if_not_met).

<!-- TC:TC-CHK-096:START traces=REQ-CHK-005,REQ-CHK-007,XM-CHK-001 -->
### TC-CHK-096 — XM-CHK-001 degraded — REG has no package for the code: start refused with a defined rejection
Derived from : XM-CHK-001 (REQ-CHK-005, REQ-CHK-007)
Exercises    : CheckEngine.startCheck(serviceCode, requestNumber, employeeId) — in-process, honours CON-CHK-004 (no HTTP operation — ADR-CHK-017)
Rule / code  : RULE-CHK-001 → CHK-422-SERVICE-NOT-AVAILABLE
Package      : XM-CHK-001
Scenario     : INTEGRATION · data class EDGE · language en
Preconditions: requires REG:DELIVERED met; REG's isServiceAvailable (CON-REG-013) answers false for the code `scholarship-request` because no package of it is loaded (empty read).
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Call startCheck with service code `scholarship-request`, request number `1001`, employee `E-2041`.
Expected     : A typed ServiceNotAvailableException with code CHK-422-SERVICE-NOT-AVAILABLE (RULE-CHK-001), never an unhandled error; the result port receives 0 create calls; 0 Active Checks. If REG:DELIVERED is not met, the block is skipped and recorded in deferred_xm (if_not_met).
Test data    : service code `scholarship-request`, request 1001, employee E-2041 (from AC-CHK-001)
<!-- TC:TC-CHK-096:END -->

<!-- TC:TC-CHK-097:START traces=REQ-CHK-007,REQ-CHK-008,REQ-CHK-057,XM-CHK-002 -->
### TC-CHK-097 — XM-CHK-002 degraded — the recorded version cannot be resolved on resume: FAILED with INTERNAL_ERROR
Derived from : XM-CHK-002 (REQ-CHK-007, REQ-CHK-008, REQ-CHK-057)
Exercises    : CheckEngine.confirmUploads(checkId) — in-process, honours CON-CHK-005 (ADR-CHK-017)
Rule / code  : RULE-CHK-007 → failure reason INTERNAL_ERROR
Package      : XM-CHK-002
Scenario     : INTEGRATION · data class EDGE · language en
Preconditions: requires REG:DELIVERED met; Check 502 of `fee-waiver` version 2 is AWAITING_DOCUMENTS; REG's getServicePackageVersion (CON-REG-009) answers not found for version 2.
Host data    : SERVICE_CODE fee-waiver — present, available — created by REG's start-up load of the package folder services/fee-waiver/ (REQ-REG-005, REQ-REG-006 — ADR-CHK-020), shown by GET /api/v1/services/fee-waiver
Steps        : 1. Call confirmUploads(502).
Expected     : Check 502 ends FAILED with reason INTERNAL_ERROR and detail en: "Check 502 could not be run: version 2 of "fee-waiver" could not be resolved." (RULE-CHK-007) · ar: PENDING ADR-CHK-018 (not asserted — ADR-CHK-020); Document Access receives 1 end-of-Check notice; the Active Check is deleted.
Test data    : Check 502, version 2 (from AC-CHK-059)
<!-- TC:TC-CHK-097:END -->

<!-- TC:TC-CHK-098:START traces=REQ-CHK-011,REQ-CHK-015,XM-CHK-003 -->
### TC-CHK-098 — XM-CHK-003 degraded — the version carries only its document source query: the Check still ends once
Derived from : XM-CHK-003 (REQ-CHK-011, REQ-CHK-012, REQ-CHK-015)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : XM-CHK-003
Scenario     : INTEGRATION · data class EDGE · language en
Preconditions: requires REG:DELIVERED met; REG's package for the Check's version lists only the query `attachments`, which is its document source query (empty set of queries for CHK).
Host data    : none
Steps        : 1. Start a Check of request 1001 and run it to its end.
Expected     : 0 service queries are sent; the Check reaches the document step and exactly one ending (COMPLETED with an Overall Status or FAILED with one reason), never staying RUNNING; its Active Check is deleted.
Test data    : query `attachments` (from AC-CHK-012)
<!-- TC:TC-CHK-098:END -->

<!-- TC:TC-CHK-099:START traces=REQ-CHK-019,XM-CHK-004 -->
### TC-CHK-099 — XM-CHK-004 degraded — the version lists no required document type: no document finding, defined report
Derived from : XM-CHK-004 (REQ-CHK-019, REQ-CHK-020, REQ-CHK-021, REQ-CHK-022)
Exercises    : CheckPipeline.run, reached through CheckEngine.startCheck — in-process (ADR-CHK-017)
Rule / code  : —
Package      : XM-CHK-004
Scenario     : INTEGRATION · data class EDGE · language en
Preconditions: requires REG:DELIVERED met; REG's package answers an empty set of required document types for the Check's version.
Host data    : none
Steps        : 1. Run a Check of request 1001 to its end.
Expected     : The report contains 0 required-document findings; the findings of the comparison are verified as usual; the Check reaches exactly one ending (never an unhandled error).
Test data    : request 1001 (from AC-CHK-001)
<!-- TC:TC-CHK-099:END -->

<!-- TC:TC-CHK-100:START traces=REQ-CHK-006,REQ-CHK-013,XM-CHK-005 -->
### TC-CHK-100 — XM-CHK-005 degraded — REG returns no connection settings: start refused as not activated
Derived from : XM-CHK-005 (REQ-CHK-006, REQ-CHK-013, REQ-CHK-014)
Exercises    : CheckEngine.startCheck(serviceCode, requestNumber, employeeId) — in-process, honours CON-CHK-004 (no HTTP operation — ADR-CHK-017)
Rule / code  : REQ-CHK-006 → CHK-422-CONNECTION-NOT-ACTIVATED
Package      : XM-CHK-005
Scenario     : INTEGRATION · data class EDGE · language en
Preconditions: requires REG:DELIVERED met; REG's getConnection (CON-REG-011) answers not found for `main-db`, named by version 3 of `scholarship-request`.
Host data    : SERVICE_CODE scholarship-request — present, available — created by REG's start-up load of the package folder services/scholarship-request/ (REQ-REG-005, REQ-REG-006; REG publishes no add-value call — ADR-CHK-020), shown by GET /api/v1/services/scholarship-request
Steps        : 1. Call startCheck with service code `scholarship-request`, request number `1001`, employee `E-2041`.
Expected     : A typed ConnectionNotActivatedException with code CHK-422-CONNECTION-NOT-ACTIVATED and message en: "The service "scholarship-request" cannot be checked: connection "main-db" is not activated in this environment." · ar: PENDING ADR-CHK-018 (not asserted — ADR-CHK-020); the result port receives 0 create calls.
Test data    : connection `main-db` (from AC-CHK-007)
<!-- TC:TC-CHK-100:END -->
<!-- PHASE:INT-XM:END -->
