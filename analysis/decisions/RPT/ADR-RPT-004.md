# ADR-RPT-004 — Retention is one report retention period of the platform configuration; a scheduled purge hard-deletes every Check run ended longer ago than the period with all its records; no period configured means nothing is purged; unfinished Checks are never purged
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: RPT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T14:00:00+00:00
Dialogue-key: beb33f8f1cf5
traces      : POL-RPT-019, POL-RPT-020, POL-RPT-021, POL-RPT-022

## Decision
Owner decision D4: a report is kept as long as the host keeps the request it verified; the period is configuration; a purge removes older runs with their findings and documents by hard delete. Points beyond D4: the unit and scope of the period, what "older" is measured from, when the purge runs, and the behaviour without a setting. Recommended: one period in days for every service, set in the platform configuration beside the per-Check limits (pattern of ADR-REG-006); age is measured from the Check's end time, so a Check that ended is kept the full period; the purge runs on a schedule of the platform configuration (default once a day) and deletes, in one transaction per Check run, its findings, Check Documents, unread queries and the run itself with its Employee Decision; a Check still AWAITING_DOCUMENTS or RUNNING is never purged (CHK still writes to it and ends it — CON-CHK-011); with no period configured the purge deletes nothing. Each purge run reports how many Check runs it removed. Rejected: per-service periods (no stated need — scope exception); soft delete or archiving (contradicts D4 and profile `delete_semantics: hard`). Status: owner D4 confirmed; details recommended — confirmed at prd-approval. Sources: domain-profile §8 D4; profile stack.db.delete_semantics; ADR-REG-006.

Source      : governance-shared/analysis/modules/RPT/_state/briefs/pass-1.md (P0 operator run)
