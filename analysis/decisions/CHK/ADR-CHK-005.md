# ADR-CHK-005 — Unread query data gives a report that cannot be COMPLIANT; a Check that cannot finish ends FAILED with a reason and no Overall Status; unfinished Checks are ended at start-up; every ending path notifies DOC
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CHK        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: ce511ccdd355
traces      : POL-CHK-018, POL-CHK-019, POL-CHK-020, POL-CHK-021, POL-CHK-022

## Decision
G6 forbids skipping anything silently; G8 sets a timeout and maximum rows per Check; ADR-DOC-008 makes CHK notify DOC on every ending path. Recommended: (a) a service query that fails, or returns more rows than the maximum rows, is recorded in the report as not read — its data is never truncated silently — and the Check still produces a report that cannot be COMPLIANT (ADR-CHK-002), consistent with DOC's SOURCE_QUERY_FAILED rule (ADR-DOC-007). (b) A Check that cannot complete its pipeline ends `FAILED` with exactly one reason and no Overall Status: `TIMED_OUT` (Check timeout of the platform configuration), `MODEL_UNAVAILABLE` (the comparison model cannot be reached), `MODEL_OUTPUT_INVALID` (the structured output does not fit the fixed report structure), `MODEL_NOT_PERMITTED` (ADR-CHK-006), `UPLOAD_WINDOW_EXPIRED` (ADR-CHK-004), `INTERRUPTED`, `INTERNAL_ERROR`. No partial overall status is stored. (c) At service start, CHK asks its result port for the Checks left `AWAITING_DOCUMENTS` or `RUNNING` by an earlier run and ends each one `FAILED` with reason `INTERRUPTED`; the result port therefore carries this read, implemented by RPT, keeping CHK free of any RPT dependency (ADR-REG-002). (d) Every ending path — COMPLETED or FAILED for any reason — sends DOC the end-of-Check notice (CON-DOC-005).

Alternative rejected: a timed-out Check stores a partial report as NEEDS_MANUAL_REVIEW — the employee could read an incomplete set of findings as the full picture. Resuming interrupted Checks is a scope exception. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §5, §12]; domain-profile §5 G6, G8, G9; ADR-DOC-007, ADR-DOC-008; ADR-REG-006.

Source      : governance-shared/analysis/modules/CHK/_state/briefs/pass-1.md (P0 operator run)
