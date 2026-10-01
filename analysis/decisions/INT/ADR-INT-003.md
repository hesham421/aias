# ADR-INT-003 — A refusal raised by CHK, DOC or RPT reaches the caller in the platform's ProblemDetail form with its owner's code, HTTP status and message unchanged; INT mints INT codes only for what INT itself decides
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: 74412291c778
traces      : POL-INT-003, POL-INT-005, POL-INT-010

## Decision
The contracts hand INT typed refusals 'for INT to show as a ProblemDetail': CHK's five (start incomplete, service not available, connection not activated, Check not found, Check not waiting for documents — CON-CHK-004, CON-CHK-005), DOC's four upload refusals (CON-DOC-003) and RPT's five decision refusals (CON-RPT-006). Each owner already gives each refusal a code in the profile format `{MOD}-{http}[-{SLUG}]` (ADR-CHK-018, ADR-DOC-012, ADR-RPT-013). Options: A) INT re-codes every refusal as INT-… — a second list that drifts from the owner's; B) INT passes the owner's code, status and message through unchanged and adds INT codes only for its own decisions: a request body it cannot read, an upload to a Check not waiting for documents, an upload above the transport limit, the Approval API failing (502) or not answering in time (504), and an unexpected server failure. Recommended B. Status: recommended — confirmed at prd-approval. Sources: profile `stack.backend.api.error_envelope`, `error_code_format`, `http_statuses`; CON-CHK-004, CON-CHK-005, CON-DOC-003, CON-RPT-006; ADR-RPT-013.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
