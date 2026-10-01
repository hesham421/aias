# ADR-INT-009 — The Approval API is called with the method and path of the version's approval API definition, the request number filled into the path as one encoded value, against the host base address of the environment's configuration, within a configured timeout, once — never retried automatically; any 2xx answer is success
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: 204b82afd504
traces      : POL-INT-009, POL-INT-010

## Decision
Raw idea §4 shows `approval: {enabled, api: POST /requests/{requestId}/approve}`; REG supplies `approvalEnabled` and the `approvalApi` text (CON-REG-012). The host's base address differs per environment, like connections (§4 'set at activation time for each environment'). An approval is not idempotent on the host side, so an automatic retry could approve twice. Options: A) retry on failure — risk of double execution; B) one call; failure → 502, timeout → 504, the employee retries deliberately. Recommended B. The request number is substituted as a single URL-encoded path value, never concatenated as free text (G4 spirit); the call body carries the Check identifier and the deciding employee so the host can link the approval to the report it was based on (§11). The timeout is platform configuration (recommended default 10 seconds). No credential handling is specified in this version (A2). Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §4, §11, §12, §15 A2]; CON-REG-012; profile 502/504.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
