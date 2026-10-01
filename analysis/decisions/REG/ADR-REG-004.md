# ADR-REG-004 — The read-only JDBC data source for blob fetching is a REG Connection of type jdbc
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: REG        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 09f562588a75
traces      : POL-REG-010, POL-REG-012

## Decision
The raw idea §6 reads BLOB columns directly over JDBC with a read-only user, never through MCP (G14), and §4 defines connections once, outside the service packages, set per environment (POL-REG-010, POL-REG-011). Recommended: that JDBC data source is a Connection of type `jdbc` held in REG beside the `mcp` connections, so it is set at activation per environment and bound by the read-only rule (POL-REG-012). Alternative rejected: DOC holds its own data source — a second, unregistered place for host-data credentials.

Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §4, §6]; domain-profile §5 G3, G14.

Source      : governance-shared/analysis/modules/REG/_state/briefs/pass-1.md (P0 operator run)
