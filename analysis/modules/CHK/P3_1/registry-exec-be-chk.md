## REGISTRY — P3.1 — CHK v1

ID RANGES        API-CHK-001 · QR-CHK-001
ENTITIES / TABLES bound: ENT-CHK-001 CHK_ACTIVE_CHECK · lookups reused: OVERALL_STATUS, CHECK_STATUS, FINDING_OUTCOME, CHECK_FAILURE_REASON (CHK, service code enums), FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON (Document Access enums, by value), SERVICE_CODE, DOCUMENT_TYPE, CONNECTION_TYPE (consumed) · new: none
INTEGRATION      XM-CHK-001, XM-CHK-002, XM-CHK-003, XM-CHK-004, XM-CHK-005 — one block each in CROSS-MOD · requires REG:DELIVERED for every edge
CATALOG          3 codes (CHK-400-CHECK-ID-INVALID, CHK-404-ACTIVE-CHECK-NOT-FOUND, CHK-500) · 5 in-process rejection codes (CHK-400-START-INCOMPLETE, CHK-422-SERVICE-NOT-AVAILABLE, CHK-422-CONNECTION-NOT-ACTIVATED, CHK-404-CHECK-NOT-FOUND, CHK-409-CHECK-NOT-AWAITING-DOCUMENTS) · ar messages PENDING ADR-CHK-018
API DOCUMENT     api-spec-chk.yaml · operations 1 = API blocks 1 · error responses 3 = catalog rows 3
ALIGN            verdict as stamped by the orchestrator
ADRs             ADR-CHK-017 (ACCEPTED), ADR-CHK-018 (ACCEPTED)
CONTRACT         Honours: CON-CHK-004, CON-CHK-005 (in-process procedures) · Declares: CON-CHK-006 … CON-CHK-011 (result port, implemented by RPT) · CON-CHK-001 … CON-CHK-003 are lookup promises carried by the enums of CORE
TRACEABILITY     REQ covered by ≥1 API/DBF: 82/82 · orphan REQ: none
