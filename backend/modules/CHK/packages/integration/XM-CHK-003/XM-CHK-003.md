<!-- source: PHASE:CROSS-MOD / XM:XM-CHK-003 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-CHK-011, REQ-CHK-012, REQ-CHK-015 -->
<!-- XM:XM-CHK-003:START traces=REQ-CHK-011,REQ-CHK-012,REQ-CHK-015 -->
### XM-CHK-003 — Service queries of the version (name, connection name, SQL text)
target     : REG · ENT-REG-003
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-003
requires   : REG:DELIVERED
do         :
  adapter   : `RegPackageAdapter` (same adapter as XM-CHK-002) maps each query of the package (CON-REG-007 / CON-REG-009) — ENT-REG-003.queryName, ENT-REG-003.connectionName, ENT-REG-003.sqlText (unaltered) — into `ServiceQuery {queryName, connectionName, sqlText}`; the SQL text reaches `ServiceQueryPort` exactly as stored and the request number is bound to `:{inputName}` (CON-REG-003 promise; RULE-CHK-003 data source ENT-REG-002.inputName, ENT-REG-003.sqlText); the query whose ENT-REG-003.queryName equals ENT-REG-002.documentSourceQueryName is skipped (RULE-CHK-005).
  config    : none — in-process bean.
tests      : AC-CHK-012, AC-CHK-013, AC-CHK-016
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-CHK-003:END -->
