# ADR-INT-006 — The employee frontend is INT's host-facing frontend, opened from the host screen for one request with the service code, request number and employee identity the host passes; its four jobs — the Checks of a request, the report, the manual upload and the decision — are separate screens using only the REST API every host uses
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: 0dd46977757d
traces      : POL-INT-012, POL-INT-013, POL-INT-014, POL-INT-015, POL-INT-016, POL-INT-018

## Decision
A1 gives the employee, in a React + TypeScript frontend embedded in the host screen: the checks of a request, the report (overall status, findings with evidence, documents read / missing / unreadable), manual document upload and recording the decision; it consumes the same REST API as any host. Domain-profile §4 row 5 puts 'employee frontend's API surface' in INT and §7.2 makes the integration context 'everything a host or the employee frontend touches'. RPT has no screen (RPT module registry AUTO-DECISION). Options: A) one screen doing everything — mixes the upload and the decision in one submit, against `screen_composition`; B) four jobs, one screen each, with the host passing the request context at launch; a running Check's status is followed by polling RPT's read (raw idea §5). Recommended B. The frontend shows the report as RPT stores it: the Overall Status is CHK's derivation (a missing or unreadable required document already prevents COMPLIANT — CON-CHK-001), and the frontend never presents COMPLIANT beside a missing or unreadable required document (AIAS-11). Screen design belongs to P3.2. Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §5, §15 A1]; domain-profile §4, §7.2; profile `stack.frontend`, `screen_composition`; review AIAS-11.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
