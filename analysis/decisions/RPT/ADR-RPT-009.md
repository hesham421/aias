# ADR-RPT-009 — INT hands over an Employee Decision as Check identifier, decision code, deciding employee and an Approval API flag; RPT stamps the recording time with its own clock and refuses the flag on a REJECTED decision
Status      : ACCEPTED
Stage       : P1        Module: RPT        Version: v1
Context     : ADR-RPT-003 fixed where and how often the decision is recorded. Left open: what INT passes, whose clock dates the decision, and whether a rejection can be executed through the Approval API, which the raw idea describes only as an approve endpoint (§4 `POST /requests/{requestId}/approve`, §11).
Decision    : Inputs: checkId, employeeDecision (EMPLOYEE_DECISION), decidedBy (text exactly as the host sent it), approvalApiExecuted (yes / no, always given). decidedAt is RPT's time of recording (DEFAULT). approvalApiExecuted true is accepted only with APPROVED (RULE-RPT-014). The answer is the Check with its recorded decision.
Alternatives rejected: INT passes the decision time — a second clock the record cannot verify; allowing a REJECTED decision executed through the Approval API — no host endpoint for it is described.
Consequences: P1.5 publishes the operation with these inputs; INT must call the Approval API before recording and record nothing when the call fails (ADR-RPT-003).
traces      : US-RPT-010, REQ-RPT-032, REQ-RPT-035, REQ-RPT-036, REQ-RPT-039, RULE-RPT-013, RULE-RPT-014
