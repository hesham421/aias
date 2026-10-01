<!-- source: PHASE:DATA-DOM -->
<!-- traces: DBF-INT-001, DBF-INT-002, DBF-INT-003, DBF-INT-004, DBF-INT-005, DBF-INT-006, DBF-INT-007, DBF-INT-008, DBF-INT-009, DBF-INT-010, REQ-INT-011, REQ-INT-034, REQ-INT-035 -->
<!-- PHASE:DATA-DOM:START traces=REQ-INT-011,REQ-INT-034,REQ-INT-035,DBF-INT-001,DBF-INT-002,DBF-INT-003,DBF-INT-004,DBF-INT-005,DBF-INT-006,DBF-INT-007,DBF-INT-008,DBF-INT-009,DBF-INT-010 -->
## PHASE DATA-DOM — DATA+DOM

No entity block: INT declares no entity and creates no table (ADR-INT-007, ADR-INT-013, ADR-INT-015). The domain is a set of immutable value objects (Java records) and two guards.

### Value objects
| Record | Fields (DBF where bound) | Built from |
|---|---|---|
| `CheckSnapshot` | checkId (DBF-INT-001), status (DBF-INT-002), serviceCode (DBF-INT-003), versionNumber (DBF-INT-004), requestNumber (DBF-INT-005), employeeDecision (DBF-INT-007, nullable) | the Report Store's read of a Check (PORTS) |
| `ApprovalDefinition` | enabled (DBF-INT-009), method, pathTemplate (both parsed from DBF-INT-010) | XM-INT-001 |
| `StartCheckCommand` | serviceCode (DBF-INT-003), requestNumber (DBF-INT-005), employeeId (DBF-INT-006) — exactly as received | API-INT-001 body |
| `UploadCommand` | checkId (DBF-INT-001), documentType, fileName (text), bytes | API-INT-002 multipart |
| `DecisionCommand` | checkId (DBF-INT-001), employeeDecision (DBF-INT-007), decidedBy (DBF-INT-008) — exactly as received | API-INT-004 body |

### Domain rules (owner layer: domain classes `UploadGuard`, `ApprovalGuard`)
- RULE-INT-001 — Uploads only while the Check waits for documents · trigger: on upload · scope: CREATE (upload)
  - statement: The system shall prevent handing an upload to Document Access when the Check's status is not AWAITING_DOCUMENTS.
  - message (en): Documents can be uploaded only while Check {checkId} is waiting for documents; its status is {status}. · message (ar): PENDING ADR-INT-017
  - data source: DBF-INT-002 · enforcement: app-level (`UploadGuard`) → `INT-409-CHECK-NOT-AWAITING-DOCUMENTS`
- RULE-INT-002 — A decision is complete before any Approval API call · trigger: on record decision (before the Approval API call) · scope: CREATE (decision, approval path)
  - statement: The system shall prevent calling the Approval API when the decision request carries no decision code of EMPLOYEE_DECISION or no deciding employee.
  - message (en): The decision was not recorded: `{value}` is not APPROVED or REJECTED. / The decision was not recorded: the deciding employee is missing. · message (ar): PENDING ADR-INT-017
  - data source: DBF-INT-007, DBF-INT-008 (request values) · enforcement: app-level (`ApprovalGuard`) → `RPT-400-DECISION-INCOMPLETE` (the Report Store's own code — ADR-INT-010)
- RULE-INT-003 — Approval only on a completed, undecided Check · trigger: on record decision (before the Approval API call) · scope: CREATE (decision, approval path)
  - statement: The system shall prevent calling the Approval API when the Check's status is not COMPLETED or the Check already holds an Employee Decision.
  - message (en): Check {checkId} is not completed; a decision can only be recorded on a completed Check. / Check {checkId} already has an Employee Decision. · message (ar): PENDING ADR-INT-017
  - data source: DBF-INT-002, DBF-INT-007 · enforcement: app-level (`ApprovalGuard`) → `RPT-409-CHECK-NOT-COMPLETED` / `RPT-409-DECISION-ALREADY-RECORDED` (ADR-INT-010)
- RULE-INT-004 — The frontend is opened for one request — enforced by the employee frontend (P3.2) before any call; no backend endpoint, no catalog row.

STATE MACHINE  none of INT's own; INT reads the Check status (the Check Engine's closed list) and never changes it.
CROSS-MODULE   XM-INT-001 (approval definition).
REPOSITORY OPS none (no QR — ADR-INT-017).
<!-- PHASE:DATA-DOM:END -->
