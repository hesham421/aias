<!-- source: PHASE:CROSS-MOD / XM:XM-DOC-002 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-DOC-004, REQ-DOC-012, REQ-DOC-051 -->
<!-- XM:XM-DOC-002:START traces=REQ-DOC-004,REQ-DOC-012,REQ-DOC-051 -->
### XM-DOC-002 — Document source query of the version (SQL text, connection name)
target     : REG · ENT-REG-003
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-003
requires   : REG:DELIVERED
do         :
  adapter   : `RegVersionAdapter` (same adapter as XM-DOC-001) selects, from the queries of the version returned by `getServicePackageVersion` (CON-REG-009), the query whose queryName equals documentSourceQueryName and maps ENT-REG-003.sqlText (unaltered) and ENT-REG-003.connectionName into `DocumentSourceQuery {sqlText, connectionName, inputName}`; the SQL text is passed to `DocumentSourceQueryPort` exactly as stored and the request number is bound to `:{inputName}` (CON-REG-003 promise; REQ-DOC-051).
  config    : none — in-process bean.
tests      : AC-DOC-004, AC-DOC-014, AC-DOC-054
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-DOC-002:END -->
