## REGISTRY — P3.1 — INT v1

ID RANGES        API-INT-001 … API-INT-008 (API-INT-005 … API-INT-008 added — ADR-INT-020) · QR: none (INT has no repository — ADR-INT-017)
ENTITIES / TABLES bound: none of INT's own (ADR-INT-007, ADR-INT-015) · read bindings DBF-INT-001 … DBF-INT-010 (owners' columns) · lookups reused: CHECK_STATUS (CHK), EMPLOYEE_DECISION (RPT), SERVICE_CODE, DOCUMENT_TYPE (consumed) · new: none
INTEGRATION      XM-INT-001 — one block in CROSS-MOD · requires REG:DELIVERED; INT → CHK, INT → DOC and INT → RPT are platform edges reached through their in-process operations (ADR-INT-016); INT → DOC binds CON-DOC-003 (hand over) and CON-DOC-006 (list the uploaded documents — ADR-INT-023)
CATALOG          23 codes — 6 INT codes (INT-400-REQUEST-INVALID, INT-409-CHECK-NOT-AWAITING-DOCUMENTS, INT-413-UPLOAD-TOO-LARGE, INT-500, INT-502-APPROVAL-API-FAILED, INT-504-APPROVAL-API-TIMED-OUT) · 17 pass-through codes of CHK (5), DOC (6 — DOC-409-CHECK-ENDED and DOC-422-UPLOAD-LIMIT-REACHED added by ADR-INT-025, closing PF-5 for INT) and RPT (6) · ar messages PENDING ADR-INT-017
API DOCUMENT     api-spec-int.yaml · operations 8 = API blocks 8 · error responses 23 catalog rows answered
ALIGN            verdict as stamped by the orchestrator
ADRs             ADR-INT-017, ADR-INT-020, ADR-INT-023, ADR-INT-025, ADR-INT-026 (ACCEPTED) · gap closed: API-INT-007's adapter binds Document Access's published listing operation CON-DOC-006 (ADR-INT-023 closes ADR-INT-020 (4))
CONTRACT         Honours: CON-INT-001 (API-INT-001), CON-INT-002 (API-INT-002), CON-INT-003 (API-INT-003), CON-INT-004 (API-INT-004), CON-INT-005 (API-INT-005), CON-INT-006 (API-INT-006), CON-INT-007 (API-INT-007), CON-INT-008 (API-INT-008)
TRACEABILITY     REQ covered by ≥1 API/DBF: 66/66 · orphan REQ: none

| API | Operation | Verb | Path |
|---|---|---|---|
| API-INT-001 | Start a Check | POST | /api/v1/checks |
| API-INT-002 | Hand over an uploaded document | POST | /api/v1/checks/{checkId}/documents |
| API-INT-003 | Confirm the uploads | POST | /api/v1/checks/{checkId}/upload-confirmation |
| API-INT-004 | Record an Employee Decision | POST | /api/v1/checks/{checkId}/decision |
| API-INT-005 | Read a Check and its report | GET | /api/v1/check-reports/{checkId} |
| API-INT-006 | List the Checks of a request | GET | /api/v1/check-reports |
| API-INT-007 | List the uploaded documents of a Check | GET | /api/v1/checks/{checkId}/documents |
| API-INT-008 | Read the required document types of a Check's version | GET | /api/v1/checks/{checkId}/required-document-types |
