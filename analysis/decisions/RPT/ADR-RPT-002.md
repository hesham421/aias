# ADR-RPT-002 — A Check run's status only moves forward — created RUNNING or AWAITING_DOCUMENTS, then RUNNING, then COMPLETED or FAILED exactly once — and an ended Check's report is never changed; only the Employee Decision is added
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: RPT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T14:00:00+00:00
Dialogue-key: 5562125d692d
traces      : POL-RPT-003, POL-RPT-009

## Decision
CON-CHK-001 declares COMPLETED and FAILED final; ADR-CHK-015 makes every Check end exactly once on CHK's side. RPT is the store of record and must not depend only on its caller's discipline. Options: A) accept every port call as sent — a duplicated or late call could overwrite a report the employee already acted on; B) guard the transitions in RPT. Recommended B: allowed transitions are AWAITING_DOCUMENTS → RUNNING, RUNNING → RUNNING (CON-CHK-007 is called again when a `path` / `blob` pipeline starts; it only sets the running time), RUNNING → COMPLETED, AWAITING_DOCUMENTS or RUNNING → FAILED. Any other change — including a second end — is refused and the stored row is kept. Once a Check has ended, its status, result, findings, Check Documents, unread queries and metadata are never updated; the Employee Decision (ADR-RPT-003) is the only later write, and the purge (ADR-RPT-004) the only delete. Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §9, §11 "records the report the approval was based on"]; CON-CHK-001; ADR-CHK-015.

Source      : governance-shared/analysis/modules/RPT/_state/briefs/pass-1.md (P0 operator run)
