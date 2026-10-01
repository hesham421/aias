# ADR-CHK-001 — CHK masters the closed lists it produces — Check status, finding outcome and Check failure reason — and carries them and the profile's Overall Status by value through its result port
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CHK        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 6c3d3eb93f7b
traces      : POL-CHK-015, POL-CHK-017, POL-CHK-019, POL-CHK-020

## Decision
The raw idea names the Overall Status values (§7), a Check status a host polls (§5, §8 "Return the status") and a CHECK_RUN row holding both "status" and "result" (§9). The domain-profile maps the terms Overall Status and Finding to RPT vocabulary, but the values are produced by CHK, and RPT depends on CHK (owner graph; ADR-REG-002), so CHK cannot read a list RPT masters without a cycle. Options: A) RPT masters the lists — needs CHK → RPT, a cycle; B) each value is a profile closed enum used by value by both modules (pattern of ADR-REG-005, ADR-DOC-002) and the type lives on the result port CHK declares. Recommended B: CHK masters the Check status (`AWAITING_DOCUMENTS`, `RUNNING`, `COMPLETED`, `FAILED`), the finding outcome (`SATISFIED`, `NOT_SATISFIED`, `UNDETERMINED`, ADR-CHK-002) and the Check failure reason (`TIMED_OUT`, `MODEL_UNAVAILABLE`, `MODEL_OUTPUT_INVALID`, `MODEL_NOT_PERMITTED`, `UPLOAD_WINDOW_EXPIRED`, `INTERRUPTED`, `INTERNAL_ERROR`, ADR-CHK-005, ADR-CHK-006), and carries the profile's Overall Status (`COMPLIANT`, `NOT_COMPLIANT`, `NEEDS_MANUAL_REVIEW`) on the same port; RPT stores the codes it receives. A `COMPLETED` Check always carries an Overall Status; a `FAILED` Check never does and always carries a failure reason.

Reviewer challenge: the glossary puts Overall Status under RPT. Answer: the term and the stored value stay RPT's; only the definition of the closed list sits on CHK's port, as fetch mode sits in the profile and is carried by REG. Adding a value is a new CHK version. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §5, §7, §8, §9]; profile conventions.lookups; ADR-REG-002.

Source      : governance-shared/analysis/modules/CHK/_state/briefs/pass-1.md (P0 operator run)
