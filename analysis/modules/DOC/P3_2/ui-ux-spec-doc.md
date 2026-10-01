# UI/UX SPEC — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module : DOC   Version : v1   Profile : aias
Inputs : srs-doc.md · prd-doc.md · api-spec-doc.yaml · registry-srs-doc.md · registry-exec-be-doc.md
Counts : SCR 0 · UXD 0 · ADR ADR-DOC-013 (new)
══════════════════════════════════════════════════════════════════

## Screens

None. The SRS declares no screen for DOC (PART B "Not applicable", 0 SCR-REQ — ADR-DOC-003), so no `SCR-*` block is written: each block copies an SRS screen entry, and there is none to copy. Nothing in this spec adds a field, a rule or a permission the SRS does not have.

The employee frontend of raw-idea amendment A1 is owned by INT. The screens that show DOC data — the Document upload screen and the Upload confirmation screen of INT's SRS — are specified in INT's ui-ux-spec, together with their composition, states and the cross-module display dependencies (`UXD-INT-*`) for the Uploaded Document fields they render.

## Cross-module display dependencies

None minted. A `UXD-*` is keyed by the module that owns the displaying screen; DOC owns no screen, so it displays no foreign data. The reverse dependency — INT's screens rendering DOC's Uploaded Document fields read through API-DOC-001 — is INT's to mint (ADR-DOC-013).

## DOC data the frontend may render (reference for the consuming screens)

The fields of ENT-DOC-001 that API-DOC-001 returns, with their SRS labels. The response shape is the `UploadedDocumentSummary` schema of `api-spec-doc.yaml`; this table only carries the labels.

| Field | Label (en) | Label (ar) | Read-only |
|---|---|---|---|
| uploadedDocumentId | Uploaded document id | PENDING ADR-DOC-012 | yes |
| documentType | Document type | PENDING ADR-DOC-012 | yes |
| fileName | File name | PENDING ADR-DOC-012 | yes |
| fileSize | File size | PENDING ADR-DOC-012 | yes |
| oversized | Oversized | PENDING ADR-DOC-012 | yes |
| createdAt | Created at | PENDING ADR-DOC-012 | yes |

The file content is never returned (REQ-DOC-043, RULE-DOC-005). No field is editable: an Uploaded Document is never changed (REQ-DOC-023, RULE-DOC-004).

## Permissions

No permission model — screens open per the SRS; caller authentication is deferred (raw-idea A2). The SRS access summary names no role check for DOC.

## Reconciliation

```
RECONCILIATION — DOC v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → no flow (see flow-diagram-doc.md)
B2 no RULE-* contradicts a flow/spec outcome                           → none (0 screens)
B3 every field/permission on a screen exists in the SRS               → no screen; the field reference above copies ENT-DOC-001 labels verbatim
B4 every screen entry of the SRS has exactly one SCR-* block          → 0 SRS screen entries · 0 SCR blocks
RESULT  reconciled 0 · reworked 0 · ADRs ADR-DOC-013
```
