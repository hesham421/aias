# ADR-CHK-007 — Several Checks of the same request are allowed and fully independent; each Check uses the service package version current at its start for its whole life
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CHK        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 8a6ea39e972c
traces      : POL-CHK-003, POL-CHK-025

## Decision
Amendment A1 shows the employee "the checks of a request", so a request may be checked more than once (for example after a document is corrected or a manual upload is redone). G9 forbids carrying data between Checks; G11 and ADR-REG-003 require the recorded version to identify what the Check used. Recommended: a new Check for a request that already has Checks is accepted and run independently — no result, data, model conversation or upload of an earlier Check is reused, and no Check is deduplicated or cancelled by a newer one. A Check takes the version that is current when it starts and keeps it until it ends, even if a newer version is loaded meanwhile. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §2, §4, §12, §15 A1]; domain-profile §5 G9, G11; ADR-REG-003.

Source      : governance-shared/analysis/modules/CHK/_state/briefs/pass-1.md (P0 operator run)
