<!-- source: PHASE:INT-XM -->
<!-- traces: API-INT-004, REQ-INT-025, REQ-INT-028, REQ-INT-030, XM-INT-001 -->
<!-- PHASE:INT-XM:START traces=REQ-INT-025,REQ-INT-028,REQ-INT-030,XM-INT-001 -->
## PHASE INT-XM

Target module REG (one edge — 1 TC, no SUB).

<!-- TC:TC-INT-044:START traces=XM-INT-001,REQ-INT-025,REQ-INT-028,REQ-INT-030,API-INT-004 -->
### TC-INT-044 — The decision path answers in the standard form when the approval definition cannot be read
Derived from : XM-INT-001 (REQ-INT-025, REQ-INT-028, REQ-INT-030)
Exercises    : API-INT-004 POST /api/v1/checks/{checkId}/decision
Rule / code  : PLATFORM-STD → INT-500
Package      : XM-INT-001
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: the target read fails: the Service Registry's approval definition read (CON-REG-012, ENT-REG-002) answers not-found for the Check's service code and version; a Check of that version is COMPLETED and undecided; the stub host Approval API records every call (ADR-INT-019 (5))
Host data    : none
Steps        : 1. POST /api/v1/checks/{checkId}/decision with employeeDecision "APPROVED", decidedBy <any>. 2. Read the Check (GET /api/v1/checks/{checkId}).
Expected     : 1 → a defined result, not an unhandled failure: HTTP 500, application/problem+json, code `INT-500`, detail en: "The request could not be completed because of an unexpected error." · ar: PENDING ADR-INT-017; the host receives no call; the service log names the Check identifier. 2 → the Check holds no decision.
Test data    : checkId, decidedBy: placeholders (GRACEFUL-DEGRADATION — XM-INT-001 SOFT-READ)
<!-- TC:TC-INT-044:END -->
<!-- PHASE:INT-XM:END -->
