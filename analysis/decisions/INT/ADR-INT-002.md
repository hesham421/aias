# ADR-INT-002 — Starting a Check is accepted asynchronously — INT answers at once with the Check identifier and its first status — and the employee identity the host passes is handed on exactly as sent, with no caller authentication in this version
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: d717a14bbae3
traces      : POL-INT-001, POL-INT-002

## Decision
Raw idea §5: the host sends the service code, the request number and the employee's identity; a Check takes time, so the host starts it and polls. §8 says the calling system authenticates itself and passes the employee's identity; A2 defers the authentication part. Options for the answer: A) hold the request until the report exists — breaks §5 and the host's request timeout; B) answer as soon as CHK has created the Check run (CON-CHK-004 returns checkId and RUNNING or AWAITING_DOCUMENTS) with the profile's status 202 and a pointer to RPT's read of that Check. Recommended B. Identity: INT passes the employee identity and, at decision time, the deciding employee exactly as the host sent them (profile `conventions.identifiers`); INT neither verifies them against a directory nor adds any caller check — caller authentication returns with the security version (A2, D7). Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §5, §8, §15 A2]; profile `stack.backend.api.http_statuses` (202); CON-CHK-004.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
