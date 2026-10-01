# ADR-DOC-001 — DOC runs the service definition's document source query itself: `path` through the platform MCP query channel, `blob` over the read-only jdbc connection
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: DOC        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 34abaf07aa34
traces      : POL-DOC-002, POL-DOC-005

## Decision
The service definition names a document source query and its type and location columns (REG CON-REG-007). In `blob` mode that query returns the content column, so it must run over the read-only `jdbc` connection, never through MCP (G14; REG REQ-REG-040, RULE-REG-010). Options: A) CHK runs every query, including the `path` document source query through MCP, and hands DOC the type / path rows, while DOC runs only the `blob` query — splits document sourcing across two modules and makes CHK know which query must not go through MCP; B) DOC runs the document source query itself in both modes, exactly as written with the request number bound (G4): through the MCP query channel for `path`, over the `jdbc` connection for `blob`. Recommended B. The MCP query channel (the raw idea's QueryExecutor) is platform infrastructure — profile platform track "the MCP connection" — used by CHK and DOC and owned by neither, which matches domain-profile §6 "CHK, DOC → host database via Oracle SQLcl MCP server" and adds no module edge (DOC stays tier 1, depends_on [REG]).

Reviewer challenge: CHK may still run the same query for its own data, duplicating a call. Answer: harmless for `path`; for `blob` CHK must leave the document source query to DOC — recorded for the CHK analysis. Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §4, §6]; domain-profile §5 G4, G14, §6; ADR-REG-004.

Source      : governance-shared/analysis/modules/DOC/_state/briefs/pass-1.md (P0 operator run)
