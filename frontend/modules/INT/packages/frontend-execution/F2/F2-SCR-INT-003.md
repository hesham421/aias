<!-- source: PHASE:F2 / SUB:F2-SCR-INT-003 -->
<!-- context: F2-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-013, AC-INT-015, AC-INT-016, AC-INT-017, AC-INT-018, AC-INT-020, AC-INT-021, AC-INT-071, AC-INT-072, AC-INT-073, AC-INT-074, AC-INT-075, AC-INT-076, AC-INT-077, API-INT-002, API-INT-005, API-INT-007, API-INT-008, REQ-INT-006, REQ-INT-009, REQ-INT-010, REQ-INT-011, REQ-INT-012, REQ-INT-013, REQ-INT-014, REQ-INT-015, REQ-INT-016, REQ-INT-017, REQ-INT-019, REQ-INT-061, REQ-INT-063, REQ-INT-064, REQ-INT-065, SCR-INT-003, UXD-INT-005, UXD-INT-006 -->
<!-- SUB:F2-SCR-INT-003:START traces=REQ-INT-006,REQ-INT-009,REQ-INT-010,REQ-INT-011,REQ-INT-012,REQ-INT-013,REQ-INT-014,REQ-INT-015,REQ-INT-016,REQ-INT-017,REQ-INT-019,REQ-INT-061,REQ-INT-063,REQ-INT-064,AC-INT-013,AC-INT-015,AC-INT-016,AC-INT-017,AC-INT-018,AC-INT-020,AC-INT-021,AC-INT-071,AC-INT-072,AC-INT-073,AC-INT-074,API-INT-002,API-INT-005,API-INT-007,API-INT-008,UXD-INT-005,UXD-INT-006,SCR-INT-003,REQ-INT-065,AC-INT-075,AC-INT-076,AC-INT-077 -->
### F2 — SCR-INT-003 Document upload

```yaml name=screen-hooks
screen: SCR-INT-003
hooks:
  - {hook: CHECK-QUERY, kind: read, api: [API-INT-005], cache_key: "['check', checkId]", errors: "RPT-404-CHECK-NOT-FOUND → page message; INT-500 → error state", loading: LOCAL, invalidation: "—"}
  - {hook: REQUIRED-TYPES-QUERY, kind: read, api: [API-INT-008], cache_key: "['required-document-types', checkId]", errors: "RPT-404-CHECK-NOT-FOUND, INT-500 → no choice offered, error state", loading: LOCAL, invalidation: "—"}
  - {hook: UPLOADED-DOCUMENTS-QUERY, kind: read, api: [API-INT-007], cache_key: "['uploaded-documents', checkId]", errors: "INT-400-REQUEST-INVALID, INT-500 → list error state", loading: LOCAL, invalidation: "—"}
  - {hook: UPLOAD-SAVE, kind: mutation, api: [API-INT-002], errors: "INT-409-CHECK-NOT-AWAITING-DOCUMENTS, INT-413-UPLOAD-TOO-LARGE, DOC-400-INCOMPLETE-UPLOAD, DOC-404-SERVICE-VERSION-NOT-FOUND, DOC-422-FETCH-MODE-NOT-MANUAL, DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE, DOC-409-CHECK-ENDED, DOC-422-UPLOAD-LIMIT-REACHED, RPT-404-CHECK-NOT-FOUND, INT-400-REQUEST-INVALID, INT-500 → per §3.0", loading: LOCAL, invalidation: "['uploaded-documents', checkId], ['check', checkId]"}
  - {hook: UPLOAD-FACADE, kind: facade, api: [API-INT-005, API-INT-008, API-INT-007, API-INT-002], loading: LOCAL}
```

### CHECK-QUERY — API-INT-005            traces=API-INT-005,REQ-INT-011,REQ-INT-016
The shared read of the Check: its `status` decides whether the form is offered
(AWAITING_DOCUMENTS). No refetch interval on this screen.
### REQUIRED-TYPES-QUERY — API-INT-008     traces=API-INT-008,REQ-INT-016,REQ-INT-064
Kind read query — the required document types of the version the Check runs on (API-INT-008 in `api-spec-int.yaml`) ·
key ['required-document-types', checkId] · options shape: the `requiredDocumentTypes` codes (DOCUMENT_TYPE, shown as
codes) · ONE hook shared with SCR-INT-004 · long-lived cache (stale time = session — a Check's version never changes).
### UPLOADED-DOCUMENTS-QUERY — API-INT-007     traces=API-INT-007,REQ-INT-017
Kind read query — the shape is the document's (API-INT-007 in `api-spec-int.yaml`) · Cache key ['uploaded-documents', checkId] · Errors → the list's error state · Loading LOCAL · defaults.
### UPLOAD-SAVE — API-INT-002            traces=API-INT-002,REQ-INT-006,REQ-INT-009,REQ-INT-011,REQ-INT-013,REQ-INT-014,REQ-INT-019,REQ-INT-065
Kind mutation — multipart body (documentType, file); the service code and version are never sent (REQ-INT-010).
201 → the receipt's `notice` (when `oversized`) is shown as received (REQ-INT-013); the upload never confirms (REQ-INT-019).
Errors       : per §3.0 — INT-409 (RULE-INT-001) form message; INT-413 / DOC-400 inline on file; DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE inline on documentType; DOC-409-CHECK-ENDED and DOC-422-UPLOAD-LIMIT-REACHED form message (ADR-INT-025) — DOC-409 also invalidates ['check', checkId] so the form is withdrawn once the read shows the Check ended
Same type    : an upload of a type already listed is sent like any other; the earlier upload stays listed (REQ-INT-065)
Invalidation : ['uploaded-documents', checkId], ['check', checkId]
### UPLOAD-FACADE — SCR-INT-003
Composes the four hooks · owns: the choices (each marked `alreadyUploaded` when the list holds an upload of that type — REQ-INT-065), the list, the last receipt notice, `canUpload` (status AWAITING_DOCUMENTS
and choices loaded), derived loading · operation: `upload(values)` → resets the file field on success, keeps it on refusal.
<!-- SUB:F2-SCR-INT-003:END -->
