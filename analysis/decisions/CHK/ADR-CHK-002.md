# ADR-CHK-002 — A finding is SATISFIED, NOT_SATISFIED or UNDETERMINED, and the Overall Status is NOT_COMPLIANT over NEEDS_MANUAL_REVIEW over COMPLIANT
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CHK        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: cb2b65054bd0
traces      : POL-CHK-008, POL-CHK-013, POL-CHK-014, POL-CHK-015, POL-CHK-018

## Decision
The raw idea gives a finding "satisfied or not" and the report three overall statuses (§7), and says a missing or unreadable required document prevents COMPLIANT (G6). It does not say when NEEDS_MANUAL_REVIEW applies. An Overall Status of NEEDS_MANUAL_REVIEW needs a per-condition basis: a condition whose evidence could not be obtained is neither satisfied nor proven unsatisfied. Recommended: (a) a finding outcome is `SATISFIED`, `NOT_SATISFIED` or `UNDETERMINED`; UNDETERMINED when the evidence is not in the Check's data or documents (ADR-CHK-003), a document it needs is UNREADABLE, or the service query it needs was not read. (b) Each required document type is a condition: a MISSING document is NOT_SATISFIED; an UNREADABLE one is UNDETERMINED. (c) The Overall Status is `NOT_COMPLIANT` if any finding is NOT_SATISFIED; otherwise `NEEDS_MANUAL_REVIEW` if any finding is UNDETERMINED or any service query was not read; otherwise `COMPLIANT`. A definite failure outranks uncertainty because the request cannot become compliant by reviewing the uncertain part.

Alternative rejected: an unreadable required document gives NOT_COMPLIANT — it would tell the employee the applicant failed a condition when only the service failed to read the document; the employee can look at the document directly. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §7, §12]; domain-profile §5 G6, G10; ADR-DOC-002, ADR-DOC-007.

Source      : governance-shared/analysis/modules/CHK/_state/briefs/pass-1.md (P0 operator run)
