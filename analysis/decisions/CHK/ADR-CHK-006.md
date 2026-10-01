# ADR-CHK-006 — The free-tier rule for the comparison model is enforced from the model's declared tier and the environment's data class, the same facts DOC uses
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CHK        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: ff170b5ab40c
traces      : POL-CHK-027

## Decision
G13 lets a free-tier provider receive only synthetic or anonymised requests and documents, and the service cannot tell anonymised data from real data. DOC made its document-reading model testable with a declared model tier (FREE or APPROVED) and the environment's data class (SYNTHETIC or REAL) (ADR-DOC-009). Recommended: the comparison model configuration declares its tier, FREE or APPROVED (FREE when not declared), and CHK reads the same environment data class DOC reads (REAL when not declared). While the tier is FREE and the data class is REAL, CHK sends nothing to the comparison model and the Check ends `FAILED` with reason `MODEL_NOT_PERMITTED`. The go-live provider decision (D3) switches the tier to APPROVED by configuration alone; P1 places the data class setting so both modules read one value.

Alternative rejected: produce a report from the deterministic findings only — it would look like a complete report while the service knowledge was never compared. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §10]; domain-profile §5 G13, §8 D3; ADR-DOC-009.

Source      : governance-shared/analysis/modules/CHK/_state/briefs/pass-1.md (P0 operator run)
