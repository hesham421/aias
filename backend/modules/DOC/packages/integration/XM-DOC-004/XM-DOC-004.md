<!-- source: PHASE:CROSS-MOD / XM:XM-DOC-004 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-DOC-012, REQ-DOC-014, REQ-DOC-052 -->
<!-- XM:XM-DOC-004:START traces=REQ-DOC-012,REQ-DOC-014,REQ-DOC-052 -->
### XM-DOC-004 — Connection settings of the document source query
target     : REG · ENT-REG-005
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-005
requires   : REG:DELIVERED
do         :
  adapter   : `RegConnectionAdapter implements ConnectionLookup` (DOC port) calls `getConnection(connectionName)` (CON-REG-011) on the injected `ServiceRegistry` and maps connectionType (ENT-REG-005.connectionType → `mcp` | `jdbc`), endpoint, queryTool, dialect, credentialReference and the read-only declaration (ENT-REG-005.readOnly, true for every registered connection per CON-REG-005) into `ConnectionSettings`. RULE-DOC-006 reads ENT-REG-002.fetchMode, ENT-REG-003.connectionName and ENT-REG-005.connectionType; RULE-DOC-007 reads ENT-REG-003.connectionName and ENT-REG-005.readOnly. REG's not-found → every required document type UNREADABLE / SOURCE_QUERY_FAILED (ADR-DOC-007). The credential itself is resolved from the environment's secret store by its reference, never from REG.
  config    : none — in-process bean.
tests      : AC-DOC-014, AC-DOC-016, AC-DOC-055
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-DOC-004:END -->
