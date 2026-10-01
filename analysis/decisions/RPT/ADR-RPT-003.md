# ADR-RPT-003 — The Employee Decision is recorded on the Check Run beside the result — APPROVED or REJECTED, once, only on a COMPLETED Check, with the deciding employee as sent, the time and whether it was executed through the Approval API — handed over by INT
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: RPT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T14:00:00+00:00
Dialogue-key: 47f36b853e7e
traces      : POL-RPT-002, POL-RPT-013, POL-RPT-014, POL-RPT-015, POL-RPT-016, POL-RPT-018

## Decision
Raw idea §9 puts the employee decision in `CHECK_RUN`; §11 has two paths — the host notifies the service of the decision (default) or the service calls the host's Approval API after the employee confirms and records the report it was based on. Registry candidate CAND-RPT-003 asked whether the decision is an entity of its own. Options: A) a separate Employee Decision record — allows several decisions per Check, which nothing asks for; B) decision attributes on the Check Run, as §9 states. Recommended B, with: values `APPROVED` and `REJECTED` (domain-profile §7.1 "approve / reject"), a closed list RPT masters; the deciding employee's identity stored exactly as the host sent it, which may differ from the employee who started the Check; the decision time; and a flag telling whether the decision was executed through the service's Approval API. One decision per Check — a second is refused and the first kept; the decision is accepted only for a COMPLETED Check (a running or failed Check has no result to stand beside). INT hands the decision over through an in-process operation RPT offers; whether and when to call the Approval API is INT's (G2) — RPT never calls it. Recommended order for INT: call the Approval API first where enabled, then record the decision with the flag set; a failed approval call records nothing so the employee can retry. Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §8, §9, §11, §12]; domain-profile §5 G2, §7.1; profile `conventions.identifiers`.

Source      : governance-shared/analysis/modules/RPT/_state/briefs/pass-1.md (P0 operator run)
