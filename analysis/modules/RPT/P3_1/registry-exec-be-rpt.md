## REGISTRY — P3.1 — RPT v1

ID RANGES        API-RPT-001, API-RPT-002, API-RPT-003 · QR-RPT-001, QR-RPT-002, QR-RPT-003, QR-RPT-004, QR-RPT-005, QR-RPT-006, QR-RPT-007
ENTITIES / TABLES bound: ENT-RPT-001 RPT_CHECK_RUN · ENT-RPT-002 RPT_FINDING · ENT-RPT-003 RPT_CHECK_DOCUMENT · ENT-RPT-004 RPT_UNREAD_QUERY · lookups reused: CHECK_STATUS, OVERALL_STATUS, FINDING_OUTCOME, CHECK_FAILURE_REASON (Check Engine enums, by value), FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON (Document Access enums, by value), SERVICE_CODE, DOCUMENT_TYPE (strings) · new: EMPLOYEE_DECISION
INTEGRATION      none — 0 XM (RPT implements the Check Engine's result port; no dependency on Check Engine data)
CATALOG          5 codes (RPT-400-CHECK-ID-INVALID, RPT-404-CHECK-NOT-FOUND, RPT-400-REQUEST-KEYS-MISSING, RPT-400-SERVICE-CODE-MISSING, RPT-500) · 16 in-process rejection codes · ar messages PENDING ADR-RPT-013
API DOCUMENT     api-spec-rpt.yaml · operations 3 = API blocks 3 · error responses 5 = catalog rows 5
ALIGN            verdict as stamped by the orchestrator
ADRs             ADR-RPT-012 (ACCEPTED), ADR-RPT-013 (ACCEPTED)
CONTRACT         Honours: CON-RPT-001, CON-RPT-002, CON-RPT-003, CON-RPT-004, CON-RPT-005, CON-RPT-006 · Implements the Check result port: CON-CHK-006, CON-CHK-007, CON-CHK-008, CON-CHK-009, CON-CHK-010, CON-CHK-011
TRACEABILITY     REQ covered by ≥1 API/DBF: 53/53 · orphan REQ: none
