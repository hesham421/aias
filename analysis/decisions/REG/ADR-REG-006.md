# ADR-REG-006 — The allowed storage root is DOC's environment setting and the check limits are platform configuration; neither is REG data
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: REG        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: cd6176970c86
traces      : —

## Decision
G5 (file paths inside the allowed storage root) and G8 (timeout, maximum rows, maximum file size per Check) need configured values. Recommended: the storage root is an environment setting of DOC, which opens the files; the limits are platform configuration applied to every Check (profile backend phase CORE: "configuration, limits (timeout / rows / file size)"). Neither becomes part of the service definition or a Connection in this version. Alternative: per-service limits in the service definition — no source states a per-service need.

Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §12]; domain-profile §5 G5, G8; profile tracks.backend CORE.

Source      : governance-shared/analysis/modules/REG/_state/briefs/pass-1.md (P0 operator run)
