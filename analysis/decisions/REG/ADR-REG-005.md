# ADR-REG-005 — REG uses the profile's closed fetch-mode enum directly and takes no dependency on DOC
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: REG        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: c5400255ad48
traces      : POL-REG-008

## Decision
The Fetch Mode term is DOC vocabulary (domain-profile §7.1), but each service definition carries the value (POL-REG-008) and REG is tier 0 with no dependencies. The profile fixes fetch mode as a closed enum (path | blob | manual) owned by the service (conventions.lookups). Recommended: REG accepts exactly those three values in a service definition from the closed enum, with no runtime read of DOC, so no REG -> DOC edge exists; DOC interprets the value.

Status: recommended — pending owner confirmation at prd-approval. Sources: profile conventions.lookups; [KB:raw-idea.md §6].

Source      : governance-shared/analysis/modules/REG/_state/briefs/pass-1.md (P0 operator run)
