# ADR-DOC-002 — Document read status is the closed list READ · MISSING · UNREADABLE with a reason for every document not read; one document's failure never stops the others
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: DOC        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 4745f0579c57
traces      : POL-DOC-003, POL-DOC-010, POL-DOC-011, POL-DOC-012

## Decision
The raw idea §7 and A1 name three document outcomes — read, missing, could not be read — and the profile makes document read status a closed enum. Recommended: DOC masters the closed list `READ`, `MISSING`, `UNREADABLE`. MISSING = a required document type of the service version for which no document was listed by the host or uploaded. UNREADABLE = a document that was listed or uploaded but could not be fetched or read — path outside the storage root, file not found, larger than the maximum file size, unsupported format, failed extraction or document reading, or no time left within the Check's timeout — always with the reason. A failure on one document is reported and the remaining documents are still processed. RPT stores the value it receives through CHK, with no runtime read of DOC (same pattern as ADR-REG-005), so no RPT → DOC edge exists. Whether a MISSING or UNREADABLE required document blocks `COMPLIANT` is CHK's deterministic check (G6), not DOC's.

Alternative rejected: a separate status per failure cause — the owner named three outcomes; the cause is carried as the reason. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §7, §12, §15 A1]; domain-profile §5 G6; profile conventions.lookups.

Source      : governance-shared/analysis/modules/DOC/_state/briefs/pass-1.md (P0 operator run)
