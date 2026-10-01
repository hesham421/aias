# ADR-INT-001 — INT's host-facing surface is four write operations — start a Check, hand over a manual upload, confirm the uploads, record the Employee Decision — and every read stays with the module that owns the data; no server-rendered report page is planned
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: 9a549066d179
traces      : POL-INT-001, POL-INT-004, POL-INT-006, POL-INT-007, POL-INT-016

## Decision
Raw idea §8 lists five endpoints; A1 replaces `GET /checks/{id}/view` with the employee frontend, and the frontend uses the same REST API as any host. RPT already serves the reads of a Check and its report, of the Checks of a request and of the decision agreement over HTTP (ADR-RPT-005, ADR-RPT-006); REG serves its service reads, DOC the list of uploaded documents and CHK the active-Check read. Options: A) INT re-exposes every read as a façade — two paths for one fact, two error catalogs, drift; B) INT owns only what no other module serves over HTTP: the four writes that need orchestration across modules (start → CHK CON-CHK-004; upload → DOC CON-DOC-003; confirm → CHK CON-CHK-005; decision → REG CON-REG-012, host Approval API, RPT CON-RPT-006). Recommended B. The server-rendered page is not planned (A1, domain-profile D6). Status: recommended — confirmed at prd-approval. Sources: [KB:raw-idea.md §8, §11, §15 A1]; domain-profile §6, §8 D6; ADR-RPT-005.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
