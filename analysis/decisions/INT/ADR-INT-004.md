# ADR-INT-004 — An Employee Decision is executed through the Approval API only when it is APPROVED, on a COMPLETED Check with no decision yet, and the Check's service package version enables the Approval API; INT calls it once, before recording, and a failed or timed-out call records nothing so the employee can retry; a REJECTED decision never calls it
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: af2b07574c5c
traces      : POL-INT-007, POL-INT-008, POL-INT-009, POL-INT-010, POL-INT-011

## Decision
Raw idea §11 option 2: where the host exposes an Approval API, the service calls it after the employee confirms and records the report the approval was based on; §12 and G2: approval only as a result of the employee's action, only where the service enables it. REG supplies the approval API of a version to INT's decision path only (CON-REG-012); RPT records the decision with approvalApiExecuted and refuses the flag on a REJECTED decision (CON-RPT-006, RULE-RPT-014), and recommends 'call first, then record; a failed call records nothing' (ADR-RPT-003). Options: A) record first, call after — a recorded APPROVED decision the host never executed; B) call first, record after with approvalApiExecuted = true. Recommended B, guarded so the Approval API is never called for a Check RPT would refuse: INT reads the Check first (CON-RPT-003) and calls the Approval API only when the Check is COMPLETED and carries no decision. A failure (non-2xx) answers 502 and a timeout 504, recording nothing; the employee may decide again. If RPT still refuses after a successful call (a concurrent decision recorded in between), the refusal is answered with RPT's code and the call is logged with the Check identifier so the operator can reconcile with the host. Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §11, §12]; domain-profile G2; CON-REG-012, CON-RPT-003, CON-RPT-006; ADR-RPT-003; profile 502/504.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
