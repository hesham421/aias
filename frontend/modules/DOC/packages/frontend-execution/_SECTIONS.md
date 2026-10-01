<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND EXECUTION PLAN — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module : DOC   Version : v1   Profile : aias   Framework : react-ts-vite (react-router · tanstack-query · react-hook-form · zod; lazy chunk per screen)
Inputs : srs-doc.md · prd-doc.md · api-spec-doc.yaml · registry-srs-doc.md · registry-exec-be-doc.md · ui-ux-spec-doc.md · flow-diagram-doc.md
Screens : 0 (DOC has no screen — ADR-DOC-003, ADR-DOC-013)   UXD : 0   Open ADRs : 0 BLOCKED — decisions applied: ADR-DOC-003, ADR-DOC-011, ADR-DOC-012, ADR-DOC-013
══════════════════════════════════════════════════════════════════

## Scope of this plan

DOC owns no screen. The employee frontend (raw-idea amendment A1) belongs to INT, whose Document upload and Upload confirmation screens show the files handed over for a Check. DOC's frontend surface is therefore only what that frontend consumes from DOC's API: the one read API-DOC-001 (`GET /api/v1/uploaded-documents?checkId=`), its model, its validators, one shared query with its facade, and the module entry exporting them. The sub-bearing phases F1–F4 carry no SUB — a SUB is qualified by a screen, and there is none — and each says so (ADR-DOC-013). No screen, route, form, permission or field is added here.

## API binding — §3.0

Bound to `api-spec-doc.yaml` (the document the backend plan derived; served by the frontend executor's mock server until the backend is delivered). Shapes are read there by `x-api-id`, never restated.

```yaml name=api-surface
mock: api-spec-doc.yaml
bindings:
  - {req: REQ-DOC-017, api: [API-DOC-001]}
  - {req: REQ-DOC-018, api: [API-DOC-001]}
  - {req: REQ-DOC-043, api: [API-DOC-001]}
  - {req: REQ-DOC-056, api: [API-DOC-001]}
  - {req: REQ-DOC-064, api: [API-DOC-001]}
unmapped: []
```

Reconciliation against the SRS:
- Every REQ that needs an HTTP operation has one: the five REQs above (the Uploaded Documents of a Check — created by the handover, read only for that Check, oversized without content, never another Check's, listed without content — REQ-DOC-064, whose in-process form CON-DOC-006 INT calls, ADR-DOC-017). The other 59 REQs are fulfilled by the in-process `DocumentAccess` operations called by CHK and INT and need no HTTP operation (ADR-DOC-011).
- Every operation of the document maps to a REQ: API-DOC-001 → REQ-DOC-017, REQ-DOC-018, REQ-DOC-043, REQ-DOC-056, REQ-DOC-064. `unmapped` is empty.
- Runtime codes → RULE: none. API-DOC-001's two codes, `DOC-400-CHECK-ID-REQUIRED` and `DOC-500`, are PLATFORM-STD rows with no RULE (ADR-DOC-012), so the `codes` list is omitted. The upload-handover rule codes (RULE-DOC-001 … RULE-DOC-003, RULE-DOC-005, RULE-DOC-009, RULE-DOC-010) reach the frontend through INT's upload endpoint and are bound in INT's plan.











## Index

| Phase | Blocks | SUBs |
|---|---|---|
| F1 | UPLOADED-DOCUMENT-SUMMARY | none (0 screens) |
| F2 | UPLOADED-DOCUMENTS-QUERY, UPLOADED-DOCUMENTS-FACADE | none (0 screens) |
| F3 | UPLOADED-DOCUMENTS-VALIDATORS | none (0 screens) |
| F4 | DOC-FRONTEND-ENTRY | none (0 screens) |
| ALIGN-FE | ALIGN table, operations coverage | never split |

## Hand-off

The implementer reads this plan in phase order, `api-spec-doc.yaml` for every shape (served by its mock server), and INT's frontend plan for the screens that consume DOC-FRONTEND-ENTRY. No route, component, permission or field is added beyond the blocks above; a gap is an ADR.
