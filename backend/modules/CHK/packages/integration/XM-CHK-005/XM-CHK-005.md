<!-- source: PHASE:CROSS-MOD / XM:XM-CHK-005 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-CHK-006, REQ-CHK-013, REQ-CHK-014 -->
<!-- XM:XM-CHK-005:START traces=REQ-CHK-006,REQ-CHK-013,REQ-CHK-014 -->
### XM-CHK-005 — Connection settings of each service query
target     : REG · ENT-REG-005
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-005
requires   : REG:DELIVERED
do         :
  adapter   : `RegConnectionAdapter implements ConnectionLookup` (CHK port) calls `getConnection(connectionName)` (CON-REG-011) on the injected `ServiceRegistry` and maps ENT-REG-005.connectionType (`mcp` | `jdbc`), endpoint, queryTool, dialect, credentialReference and ENT-REG-005.readOnly into `ConnectionSettings`; RULE-CHK-002 (data source ENT-REG-003.connectionName, ENT-REG-005.connectionType, ENT-REG-005.readOnly) refuses a non-`mcp` or non-read-only connection and the query is recorded as not read. REG's not-found at start → CHK-422-CONNECTION-NOT-ACTIVATED (REQ-CHK-006). The credential is resolved from the environment's secret store by its reference, never from REG.
  config    : none — in-process bean.
tests      : AC-CHK-007, AC-CHK-014, AC-CHK-015
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-CHK-005:END -->
