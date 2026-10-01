# ADR-INT-007 — Host Integration keeps no records of its own — no entity and no table; what INT does is recorded by the module that owns the fact, and the outcome of an Approval API call is recorded only as the decision's approvalApiExecuted flag in the Report Store
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: 656c3bc3a03c
traces      : POL-INT-017

## Decision
INT's four operations each end in another module's record: the Check run (RPT, through CHK), the Uploaded Document (DOC), the Employee Decision (RPT). Options: A) an INT log table of requests or approval calls — a second copy of facts RPT already holds, a retention rule of its own, and data carried beyond one request (G9); B) no INT storage; a failed Approval API call is answered to the employee and written to the application log with the Check identifier, and a successful one is recorded by RPT with the decision. Recommended B. If a later version needs an audit of approval calls, it adds an entity then. Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §9, §11, §12]; domain-profile G9; CON-RPT-006; ADR-RPT-003.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
