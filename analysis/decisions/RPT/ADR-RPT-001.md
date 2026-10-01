# ADR-RPT-001 — RPT implements all six operations of CHK's Check result port and stores each report by value in four records — Check Run, Finding, Check Document and Unread Query — whole or not at all, holding only the codes of CHK's and DOC's closed lists
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: RPT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T14:00:00+00:00
Dialogue-key: 96c4fff98148
traces      : POL-RPT-001, POL-RPT-004, POL-RPT-005, POL-RPT-006, POL-RPT-007, POL-RPT-008, POL-RPT-010

## Decision
ADR-REG-001 gives RPT the Check run record, the findings and the Check Document record; ADR-REG-002 and ADR-CHK-011 make CHK declare the result port (CON-CHK-006 create, CON-CHK-007 mark RUNNING, CON-CHK-008 complete, CON-CHK-009 fail, CON-CHK-010 read one, CON-CHK-011 list unfinished) that RPT implements. Raw idea §9 names three tables; CON-CHK-008 also carries `unreadQueries [queryName, detail]` (ADR-CHK-014), which none of the three holds. Options: A) three records, unread queries folded into the Check Document rows — mixes documents with queries and needs a fake document type; B) unread queries as a text column on the Check Run — loses one-row-per-query and the report structure; C) a fourth record, Unread Query, one row per unread service query. Recommended C. The completed report — Overall Status, findings, document outcomes, unread queries, metadata — is stored in one transaction or not at all; a failure answers CHK's "not stored" error and CHK fails the Check INTERNAL_ERROR (CON-CHK-008). Every code is stored as its value: Check status, Overall Status, finding outcome and failure reason from CHK (CON-CHK-001 … CON-CHK-003), document read status with its 8 unreadable reasons and the fetch mode from DOC (CON-DOC-001, CON-DOC-002) — a code outside its list is refused; there is no lookup table and no runtime read of CHK, DOC or REG. No document content and no query results are stored (CON-CHK-008 carries none).

Reviewer challenge: should RPT re-check CHK's derivation of the Overall Status? Answer: no — the derivation is CHK's promise (CON-CHK-001); RPT checks only the shape it can see (a COMPLETED Check has one Overall Status, a FAILED one a reason and no findings; a reason only on UNREADABLE documents). Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §7, §9, §12]; ADR-REG-001, ADR-REG-002, ADR-CHK-011, ADR-CHK-014, ADR-CHK-016; CON-DOC-001.

Source      : governance-shared/analysis/modules/RPT/_state/briefs/pass-1.md (P0 operator run)
