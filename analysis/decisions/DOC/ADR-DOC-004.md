# ADR-DOC-004 — XLS covers .xls and .xlsx, a PDF without extractable text is read as a scan, and any other format is reported unreadable as unsupported
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: DOC        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 8291a334e680
traces      : POL-DOC-008, POL-DOC-009, POL-DOC-011

## Decision
The owner names PDF (text extraction), XLS (table extraction) and scans / images (OCR or a vision model, a separate step with its own configurable model) and says other extensions may appear. Recommended: the XLS family covers both `.xls` and `.xlsx`; a PDF that yields no extractable text is treated as a scan and goes to the document-reading step; images go to the document-reading step, whose model (an OCR engine or a vision model reached through Spring AI) is chosen by configuration; any other format is not guessed at — it is reported UNREADABLE with the reason "unsupported format" (G6), never skipped.

Reviewer challenge: sending every text-less PDF to the vision model raises cost and, on a free tier, data exposure. Answer: G13 / POL-DOC-016 already restrict the free tier to synthetic or anonymised documents, and the file-size limit bounds cost. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §2, §5 step 4, §10]; domain-profile §5 G6, G12, G13.

Source      : governance-shared/analysis/modules/DOC/_state/briefs/pass-1.md (P0 operator run)
