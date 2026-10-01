# ADR-INT-005 — A manual upload carries one file of one document type, is accepted only while the Check is AWAITING_DOCUMENTS, and is handed to Document Access with the Check's service code and version read from the Report Store; the employee confirms the uploads as a separate action
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: d0fd9053299c
traces      : POL-INT-004, POL-INT-005, POL-INT-006

## Decision
Raw idea §6 `manual`: the employee uploads the files and the report is marked accordingly; §8 `POST /checks/{id}/documents`. DOC requires the service code and version number of the Check with every handover and refuses a non-manual service, a type not of the service and an incomplete upload (CON-DOC-003, ADR-DOC-006); CHK continues a manual Check only when INT confirms the uploads (CON-CHK-005). DOC does not know the Check's status. Options for status: A) accept uploads at any time — a file uploaded to a running or ended Check is never read and is deleted at the end (CON-DOC-005), silently; B) accept only while the Check is AWAITING_DOCUMENTS, read from RPT (CON-RPT-003), and refuse otherwise with an INT code. Recommended B. One file per upload keeps each refusal attached to one file; the employee uploads each document then confirms (screen_composition: upload and confirm are separate submits). Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §6, §8, §12]; CON-DOC-003, CON-CHK-005, CON-RPT-003; profile `conventions.screen_composition`.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
