# DB SCRIPT — Host Integration (INT)
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Dialect : oracle19c   Schema prefix : none (INT creates no object)
Date   : 2026-10-01   Inputs : srs-int.md, registry-srs-int.md, contract-int.md, contract-chk.md, contract-doc.md, contract-reg.md, contract-rpt.md
Counts : 0 tables · 10 DBF (read bindings — ADR-INT-015) · 2 XM · 0 sequences · 0 seed rows
Identifier transformation : logical camelCase field → physical UPPER_SNAKE_CASE column (a capital letter starts a new word, joined by `_`), e.g. checkRunId → CHECK_RUN_ID — the same transformation the owners' scripts declare, so every bound column below is spelled as its owner creates it. Every name ≤ 128 bytes (oracle19c), no reserved word.
PK generation : identity (profile `pk_generation: identity`) — not exercised: INT creates no table.
══════════════════════════════════════════════════════════════════

## 1. Entry check
srs ✓ · registry-srs ✓ · contract ✓ (contract-int.md, CON-INT-001 … CON-INT-004) · contracts ✓ (contract-chk.md, CON-CHK-001 … CON-CHK-011; contract-doc.md, CON-DOC-001 … CON-DOC-005; contract-reg.md, CON-REG-001 … CON-REG-013; contract-rpt.md, CON-RPT-001 … CON-RPT-006).
Extracted: 0 entities → 0 tables (SRS A3 declares none — ADR-INT-007, ADR-INT-013) · 0 intra-module FKs · 2 XM candidates (SRS A8: ENT-RPT-001, ENT-REG-002, both SOFT-READ) · 0 lookups owned (SRS A6 `lookups: []`).

## 2. DB field traceability matrix

Every row is a READ BINDING (ADR-INT-015): the column belongs to the owner's table, is created and written only by the owner's script and code, and reaches INT only as a value returned by the owner's in-process operation (XM-INT-001 → CON-RPT-003 / CON-RPT-006; XM-INT-002 → CON-REG-012). INT creates, alters and writes none of them and never queries the owner's table.

```yaml name=dbf-matrix
rows:
  - {id: DBF-INT-001, table: RPT_CHECK_RUN, column: CHECK_RUN_ID, type: "NUMBER(19)", entity_field: ENT-RPT-001.checkRunId, traces: [REQ-INT-005, REQ-INT-006, REQ-INT-009, REQ-INT-012, REQ-INT-017, REQ-INT-018, REQ-INT-020, REQ-INT-021, REQ-INT-032, REQ-INT-039, REQ-INT-042, REQ-INT-043, REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, REQ-INT-049, REQ-INT-050, REQ-INT-051, REQ-INT-052, ENT-RPT-001]}
  - {id: DBF-INT-002, table: RPT_CHECK_RUN, column: CHECK_STATUS, type: "VARCHAR2(30 CHAR)", entity_field: ENT-RPT-001.checkStatus, traces: [REQ-INT-001, REQ-INT-002, REQ-INT-011, REQ-INT-019, REQ-INT-035, REQ-INT-050, REQ-INT-053, REQ-INT-054, REQ-INT-055, REQ-INT-056, ENT-RPT-001]}
  - {id: DBF-INT-003, table: RPT_CHECK_RUN, column: SERVICE_CODE, type: "VARCHAR2(100 CHAR)", entity_field: ENT-RPT-001.serviceCode, traces: [REQ-INT-010, REQ-INT-016, REQ-INT-030, REQ-INT-040, REQ-INT-041, REQ-INT-044, ENT-RPT-001]}
  - {id: DBF-INT-004, table: RPT_CHECK_RUN, column: VERSION_NUMBER, type: "NUMBER(10)", entity_field: ENT-RPT-001.versionNumber, traces: [REQ-INT-010, REQ-INT-030, REQ-INT-045, ENT-RPT-001]}
  - {id: DBF-INT-005, table: RPT_CHECK_RUN, column: REQUEST_NUMBER, type: "VARCHAR2(100 CHAR)", entity_field: ENT-RPT-001.requestNumber, traces: [REQ-INT-003, REQ-INT-031, REQ-INT-039, REQ-INT-040, REQ-INT-041, REQ-INT-044, ENT-RPT-001]}
  - {id: DBF-INT-006, table: RPT_CHECK_RUN, column: EMPLOYEE_ID, type: "VARCHAR2(100 CHAR)", entity_field: ENT-RPT-001.employeeId, traces: [REQ-INT-003, REQ-INT-004, REQ-INT-023, REQ-INT-041, REQ-INT-044, ENT-RPT-001]}
  - {id: DBF-INT-007, table: RPT_CHECK_RUN, column: EMPLOYEE_DECISION, type: "VARCHAR2(30 CHAR)", entity_field: ENT-RPT-001.employeeDecision, traces: [REQ-INT-021, REQ-INT-022, REQ-INT-024, REQ-INT-026, REQ-INT-027, REQ-INT-034, REQ-INT-035, REQ-INT-042, REQ-INT-052, REQ-INT-054, ENT-RPT-001]}
  - {id: DBF-INT-008, table: RPT_CHECK_RUN, column: DECIDED_BY, type: "VARCHAR2(100 CHAR)", entity_field: ENT-RPT-001.decidedBy, traces: [REQ-INT-021, REQ-INT-023, REQ-INT-032, REQ-INT-034, ENT-RPT-001]}
  - {id: DBF-INT-009, table: REG_SVC_PKG_VER, column: APPROVAL_ENABLED, type: "NUMBER(1)", entity_field: ENT-REG-002.approvalEnabled, traces: [REQ-INT-025, REQ-INT-028, REQ-INT-029, REQ-INT-030, ENT-REG-002]}
  - {id: DBF-INT-010, table: REG_SVC_PKG_VER, column: APPROVAL_API, type: "VARCHAR2(500 CHAR)", entity_field: ENT-REG-002.approvalApi, traces: [REQ-INT-025, REQ-INT-031, REQ-INT-033, REQ-INT-036, REQ-INT-037, REQ-INT-038, ENT-REG-002]}
```
Total: 10 DBF ids across 0 tables created here (2 owner tables bound: RPT_CHECK_RUN — RPT; REG_SVC_PKG_VER — REG).

RULE data-source bindings (C6.9): RULE-INT-001 reads ENT-RPT-001.checkStatus (DBF-INT-002) · RULE-INT-002 reads ENT-RPT-001.employeeDecision (DBF-INT-007) and ENT-RPT-001.decidedBy (DBF-INT-008) · RULE-INT-003 reads ENT-RPT-001.checkStatus (DBF-INT-002) and ENT-RPT-001.employeeDecision (DBF-INT-007) · RULE-INT-004 reads ENT-RPT-001.serviceCode (DBF-INT-003), ENT-RPT-001.requestNumber (DBF-INT-005) and ENT-RPT-001.employeeId (DBF-INT-006). For RULE-INT-002 and RULE-INT-004 the values are those of the incoming request and the launch context, checked against the fields they will be stored in.

## 3. XM register

```yaml name=xm-register
records:
  - {id: XM-INT-001, type: SOFT-READ, target_module: RPT, target_entity: ENT-RPT-001, traces: [REQ-INT-010, REQ-INT-011, REQ-INT-012, REQ-INT-021, REQ-INT-026, REQ-INT-030, REQ-INT-031, REQ-INT-035], state: CONTRACTED, contract_ref: "CON-RPT-001", column: "in-process RPT interface (CON-RPT-003 readCheck before an upload and before a decision; CON-RPT-006 recordDecision) — reads ENT-RPT-001.checkStatus, serviceCode, versionNumber, requestNumber, employeeDecision by checkId; hands over the decision; no column, no FK"}
  - {id: XM-INT-002, type: SOFT-READ, target_module: REG, target_entity: ENT-REG-002, traces: [REQ-INT-025, REQ-INT-028, REQ-INT-030], state: CONTRACTED, contract_ref: "CON-REG-002", column: "in-process REG interface (CON-REG-012 getApprovalApi) — reads ENT-REG-002.approvalEnabled and ENT-REG-002.approvalApi by serviceCode + versionNumber of the Check, only on the Employee Decision path; no column, no FK"}
```

Both are SOFT-READ over in-process interfaces: INT holds no column that references another module's table (it holds no column at all). The Check identifier is a value in INT's requests and answers, never a stored reference. INT's calls to the Check Engine (CON-CHK-004, CON-CHK-005) and to Document Access (CON-DOC-003) read no entity of theirs, so they are the platform edges INT → CHK and INT → DOC, not XM records (SRS A8).

## 4. FULL_DATABASE_SCRIPT

```sql
-- ════════════════════════════════════════════════════════════════
-- Host Integration (INT) v1 — oracle19c
-- INT owns no entity (ADR-INT-007, ADR-INT-013) and creates no database object (ADR-INT-015).
-- This script is intentionally a no-op: running it changes nothing.
-- BLOCK 1  SEQUENCES            : none
-- BLOCK 2  PARENT TABLES        : none
-- BLOCK 3  CHILD TABLES         : none
-- BLOCK 4  COMMENTS             : none
-- BLOCK 5  CONSTRAINTS          : none
-- BLOCK 6  TRIGGERS             : none
-- BLOCK 7  INDEXES              : none
-- BLOCK 8  LOOKUP SEED DATA     : none (SRS A6 lookups: [])
-- BLOCK 9  VIEWS                : none
-- BLOCK 10 FUNCTIONS/PROCEDURES : none
--
-- XM-INT-001 SOFT-READ — Host Integration's upload and decision handling reads the Check Run
-- (status, service code, version number, request number, decision) from RPT through CON-RPT-003
-- and hands the decision over through CON-RPT-006, without an FK. Rationale: SRS A8; INT keeps
-- no copy (REQ-INT-059). Risk: a change to the Check Run read or the decision operation requires
-- impact assessment on REQ-INT-010, REQ-INT-011, REQ-INT-021, REQ-INT-035.
--
-- XM-INT-002 SOFT-READ — Host Integration's decision handling reads the approval flag and the
-- approval API of the Check's service package version from REG through CON-REG-012, without an FK.
-- Rationale: SRS A8; ADR-INT-008. Risk: a change to the approval API definition requires impact
-- assessment on REQ-INT-025, REQ-INT-028, REQ-INT-030, REQ-INT-031.
-- ════════════════════════════════════════════════════════════════
```

## 5. Decisions applied
| DEFAULT / ADR | What | Override / status |
|---|---|---|
| ADR-INT-007 | INT keeps no records | Confirmed at prd-approval |
| ADR-INT-008 | INT reads REG's approval API in-process; entity-level edge | Confirmed at prd-approval |
| ADR-INT-013 | No ENT-INT; rules read the owners' fields; no lookup owned | ACCEPTED (P1) |
| ADR-INT-015 | No table; the DBF matrix holds read bindings to the owners' columns; two SOFT-READ XMs | ACCEPTED (this stage) — non-breaking |
| ADR-RPT-011 | Owner column types copied verbatim (RPT_CHECK_RUN) | RPT — ACCEPTED |

## 6. Registry content
See registry-db-int.md.
