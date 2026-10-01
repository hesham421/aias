# ADR-CHK-004 — A manual-mode Check waits in AWAITING_DOCUMENTS until the employee confirms the uploads, bounded by an upload window of the platform configuration
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CHK        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 99b578d19d90
traces      : POL-CHK-023, POL-CHK-024

## Decision
In `manual` fetch mode the employee uploads the documents for a Check that already exists (raw idea §8 `POST /checks/{id}/documents`), DOC binds each upload to the Check's identifier (ADR-DOC-003) and deletes them when the Check ends (ADR-DOC-008). Running the pipeline immediately would find no upload, report every required type MISSING, end the Check and delete any later upload. Recommended: a Check of a `manual` service is created with status `AWAITING_DOCUMENTS` and its pipeline does not run until the employee confirms, through INT, that the uploads are complete; it then becomes `RUNNING`, and the Check timeout counts from that point. The waiting time is bounded by an upload window held in the platform configuration beside the other per-Check limits (ADR-REG-006); when it elapses unconfirmed, the Check ends `FAILED` with reason `UPLOAD_WINDOW_EXPIRED` and DOC is told the Check ended. `path` and `blob` Checks start `RUNNING` directly.

Alternative rejected: run automatically after the first upload — the employee may have more files to add. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §6, §8]; ADR-DOC-003, ADR-DOC-008; ADR-REG-006.

Source      : governance-shared/analysis/modules/CHK/_state/briefs/pass-1.md (P0 operator run)
