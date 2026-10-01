## REGISTRY — P3.1 — DOC v1

ID RANGES        API-DOC-001 · QR-DOC-001
ENTITIES / TABLES bound: ENT-DOC-001 DOC_UPLOADED_DOC, ENT-DOC-002 DOC_ENDED_CHECK · lookups reused: FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON (DOC, service code enums), DOCUMENT_TYPE, SERVICE_CODE (consumed) · new: none
INTEGRATION      XM-DOC-001, XM-DOC-002, XM-DOC-003, XM-DOC-004 — one block each in CROSS-MOD · requires REG:DELIVERED for every edge
CATALOG          2 codes (DOC-400-CHECK-ID-REQUIRED, DOC-500) · 6 in-process rejection codes (DOC-400-INCOMPLETE-UPLOAD, DOC-404-SERVICE-VERSION-NOT-FOUND, DOC-422-FETCH-MODE-NOT-MANUAL, DOC-422-DOCUMENT-TYPE-NOT-OF-SERVICE, DOC-409-CHECK-ENDED, DOC-422-UPLOAD-LIMIT-REACHED) · ar messages PENDING ADR-DOC-012
API DOCUMENT     api-spec-doc.yaml · operations 1 = API blocks 1 · error responses 2 = catalog rows 2
ALIGN            verdict as stamped by the orchestrator
ADRs             ADR-DOC-011 (ACCEPTED), ADR-DOC-012 (ACCEPTED); applied ADR-DOC-015, ADR-DOC-016, ADR-DOC-017
CONTRACT         Honours: CON-DOC-003, CON-DOC-004, CON-DOC-005, CON-DOC-006 (CON-DOC-001, CON-DOC-002 are lookup promises carried by the enums of CORE)
TRACEABILITY     REQ covered by ≥1 API/DBF: 64/64 · orphan REQ: none
