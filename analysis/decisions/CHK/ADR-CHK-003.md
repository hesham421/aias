# ADR-CHK-003 — Explicit values and dates are decided in code from the model's stated value, comparison and limit, each verified against the Check's data and the service knowledge; unverifiable evidence makes the finding UNDETERMINED
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CHK        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: ce74b474741d
traces      : POL-CHK-009, POL-CHK-013, POL-CHK-014

## Decision
The raw idea §5 step 5 runs "the deterministic checks in code: required documents present, explicit values and dates", but the REG contract supplies no declared check rules: the service package carries the service knowledge, queries, fetch mode, document source and required document types only (CON-REG-007). Options: A) defer explicit value and date checks until REG declares check types — contradicts the owner's §5 text for v1; B) require REG to add check declarations now — a REG change outside this module, against a published contract; C) let the model, which reads the service knowledge whole, state each explicit condition in its structured output as the value found, where it was found, the comparison and the limit, and let code (1) verify the value appears in the Check's query results or document content, (2) verify the limit appears in the service knowledge, and (3) recompute the comparison, overriding the model's outcome when they differ. Recommended C: the deterministic part of the outcome is computed by code, no REG change is needed, and evidence grounding (step 1) applies to every finding, not only explicit ones. A finding whose evidence or limit cannot be verified is UNDETERMINED (ADR-CHK-002). Required documents present and readable are decided in code directly from REG's required document types and DOC's outcomes.

Reviewer challenge: the model could still misread which limit applies. Answer: the limit must be literally present in the service knowledge, the comparison is recomputed, and the fixed known-result request set (POL-CHK-028) catches systematic misreadings on every model change. Declared check types remain a scope exception for a later REG version. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §4, §5, §7]; domain-profile §5 G10; CON-REG-007.

Source      : governance-shared/analysis/modules/CHK/_state/briefs/pass-1.md (P0 operator run)
