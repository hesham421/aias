<!-- source: PHASE:F2 / SUB:F2-SCR-INT-004 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-022, AC-INT-023, AC-INT-024, AC-INT-071, AC-INT-073, API-INT-003, API-INT-005, API-INT-007, API-INT-008, REQ-INT-006, REQ-INT-018, REQ-INT-019, REQ-INT-020, REQ-INT-061, REQ-INT-063, REQ-INT-064, SCR-INT-004, UXD-INT-007, UXD-INT-008 -->
<!-- SUB:F2-SCR-INT-004:START traces=REQ-INT-006,REQ-INT-018,REQ-INT-019,REQ-INT-020,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-022,AC-INT-023,AC-INT-024,AC-INT-071,AC-INT-073,API-INT-003,API-INT-005,API-INT-007,API-INT-008,UXD-INT-007,UXD-INT-008,SCR-INT-004 -->
### F2 — SCR-INT-004 Upload confirmation

```yaml name=screen-hooks
screen: SCR-INT-004
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-INT-005], cache_key: "['check', checkId]", errors: "RPT-404-CHECK-NOT-FOUND → page message; INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: UPLOADED-DOCUMENTS-QUERY, kind: read, api: [API-INT-007], cache_key: "['uploaded-documents', checkId]", errors: "INT-400-REQUEST-INVALID, INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: REQUIRED-TYPES-QUERY, kind: read, api: [API-INT-008], cache_key: "['required-document-types', checkId]", errors: "RPT-404-CHECK-NOT-FOUND, INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: CONFIRM-SAVE, kind: mutation, api: [API-INT-003], errors: "CHK-404-CHECK-NOT-FOUND, CHK-409-CHECK-NOT-AWAITING-DOCUMENTS, INT-400-REQUEST-INVALID, INT-500 → page message per §3.0", loading: LOCAL, invalidation: "['check', checkId], ['checks-of-request', {serviceCode, requestNumber}]"}
  - {hook: CONFIRMATION-FACADE, kind: facade, api: [API-INT-005, API-INT-007, API-INT-008, API-INT-003], loading: LOCAL}
```

### CONFIRM-SAVE — API-INT-003            traces=API-INT-003,REQ-INT-018,REQ-INT-019
Kind mutation — body `{}` (UploadConfirmationRequest); 202 → navigate to SCR-INT-002, whose CHECK-QUERY follows the
Check again (RUNNING). Invalidation: ['check', checkId], ['checks-of-request', {serviceCode, requestNumber}].
### CONFIRMATION-FACADE — SCR-INT-004       traces=REQ-INT-020
Composes CHECK-QUERY, UPLOADED-DOCUMENTS-QUERY, REQUIRED-TYPES-QUERY (shared hooks of SCR-INT-003), CONFIRM-SAVE ·
owns: the uploaded list, `missingTypes` (F3 derivation), `canConfirm` (lists loaded) · operation: `confirm()`.
<!-- SUB:F2-SCR-INT-004:END -->
