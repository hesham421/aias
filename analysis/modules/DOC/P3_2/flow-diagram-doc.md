# FLOW DIAGRAM — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module : DOC   Version : v1   Profile : aias
Inputs : srs-doc.md · prd-doc.md · api-spec-doc.yaml · registry-srs-doc.md · registry-exec-be-doc.md
Counts : FLOW 0 · SCR 0 · UXD 0 · ADR ADR-DOC-013 (new)
══════════════════════════════════════════════════════════════════

## Navigation flows

None. DOC has no screen of its own: the SRS carries 0 SCR-REQ and its PART B is "Not applicable" (ADR-DOC-003). A flow must start at an SRS-backed `SCR-*`; with none, any flow written here would invent navigation (ADR-DOC-013).

The one employee-facing need among DOC's stories — US-DOC-005, uploading the documents of a `manual` Check — is navigated entirely inside the embedded frontend owned by INT (raw-idea amendment A1): its Document upload and Upload confirmation screens call INT's upload endpoint, INT hands each file to DOC in-process, and those screens list the result through DOC's read API-DOC-001 (`GET /api/v1/uploaded-documents?checkId=`). The flows, screens and their navigation belong to INT's flow diagram.

## What the frontend consumes from DOC

| Consumer (owner) | DOC operation | Purpose | Story |
|---|---|---|---|
| INT's document upload and upload confirmation screens | API-DOC-001 | list the Uploaded Documents of one Check (document type, file name, file size, oversized flag, upload time — never the content) | US-DOC-005, US-DOC-009, US-DOC-012 |

Every other DOC story (US-DOC-001 … US-DOC-004, US-DOC-006 … US-DOC-008, US-DOC-010, US-DOC-011, US-DOC-013) is served by in-process operations called by CHK and INT (ADR-DOC-011); the employee sees their outcomes in the report rendered by INT from RPT's data.

## Reconciliation

```
RECONCILIATION — DOC v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → no flow written; US-DOC-005 has REQ/AC but no DOC screen — navigation is INT's (ADR-DOC-013)
B2 no RULE-* contradicts a flow/spec outcome                           → none to contradict (0 flows)
B3 every field/permission on a screen exists in the SRS               → no screen
B4 every screen entry of the SRS has exactly one SCR-* block          → 0 SRS screen entries · 0 SCR blocks
RESULT  reconciled 0 · reworked 0 · ADRs ADR-DOC-013
```
