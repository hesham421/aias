# ADR-REG-003 — Service package versions are never changed in place; a new Check uses the current version and every loaded version stays resolvable
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: REG        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 74e8a8e615b1
traces      : POL-REG-005

## Decision
POL-REG-005 and G11 require every report to record the service package version it was built on. A version number is only traceable if the content under it never changes. Recommended: once a version is loaded it is never edited in place — a change is a new version; a new Check uses the current version of its service; every version that was loaded stays resolvable so a stored report's version can be traced back to its service knowledge and service definition. Alternative rejected: record the number only and allow in-place edits — the recorded version would no longer identify what the Check used.

Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §4]; domain-profile §5 G11.

Source      : governance-shared/analysis/modules/REG/_state/briefs/pass-1.md (P0 operator run)
