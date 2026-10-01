# ADR-DOC-003 — A manual upload is a DOC-private Uploaded Document bound to one Check by value, typed by the employee, and discarded when that Check ends; DOC has no screen of its own
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: DOC        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 57d3555f2f69
traces      : POL-DOC-006, POL-DOC-007, POL-DOC-015

## Decision
Owner statement: manual upload arrives via INT, which hands it to DOC. The upload and the Check's reading of it happen at different times (the Check is asynchronous), so the file must be held in between. Recommended: DOC owns a transactional, PRIVATE Uploaded Document holding the file and the document type the employee gave it (one of the service's document types, so MISSING can be decided). It carries the Check's identifier as a value supplied by INT — no foreign key and no read of RPT, which owns the Check run record at tier 3. It is used by that Check only and discarded when the Check ends; the report keeps the Check Document record (RPT) of what was read. The upload is offered to the employee in the embedded frontend (A1) through INT's API (domain-profile §4 row 5); DOC has no screen of its own.

Alternatives rejected: keep uploads until the retention purge — stores personal documents with no stated need and risks reuse across Checks (G9); RPT stores the file — RPT owns report rows, not document content. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §6, §8, §12, §15 A1]; domain-profile §5 G9; ADR-REG-001.

Source      : governance-shared/analysis/modules/DOC/_state/briefs/pass-1.md (P0 operator run)
