<!-- source: PHASE:CROSS-MOD / XM:XM-CHK-002 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-CHK-007, REQ-CHK-008, REQ-CHK-015, REQ-CHK-027, REQ-CHK-034, REQ-CHK-056, REQ-CHK-057 -->
<!-- XM:XM-CHK-002:START traces=REQ-CHK-007,REQ-CHK-008,REQ-CHK-015,REQ-CHK-027,REQ-CHK-034,REQ-CHK-056,REQ-CHK-057 -->
### XM-CHK-002 — Service package version of a Check (version, service knowledge, input name, fetch mode, document source query name)
target     : REG · ENT-REG-002
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-002
requires   : REG:DELIVERED
do         :
  adapter   : `RegPackageAdapter implements PackageLookup` (CHK port) calls, on the injected `ServiceRegistry`, `getCurrentServicePackage(serviceCode)` (CON-REG-007) at Check start and `getServicePackageVersion(serviceCode, versionNumber)` (CON-REG-009) when a `manual` Check resumes (RULE-CHK-007, data source ENT-REG-001.serviceCode, ENT-REG-002.versionNumber); maps ENT-REG-002.versionNumber, ENT-REG-002.serviceKnowledge (whole, unaltered — RULE-CHK-006 data source), ENT-REG-002.inputName (RULE-CHK-003 data source), ENT-REG-002.fetchMode and ENT-REG-002.documentSourceQueryName (RULE-CHK-005 data source) into CHK's immutable `CheckPackage`. REG's "connection not activated" → CHK-422-CONNECTION-NOT-ACTIVATED (REQ-CHK-006); "service not available" → CHK-422-SERVICE-NOT-AVAILABLE; version not found on resume → FAILED / INTERNAL_ERROR with the RULE-CHK-007 message. The approval API operation (CON-REG-012) is never called by CHK. The pair serviceCode + versionNumber travels by value to the result port; no FK.
  config    : none — in-process bean.
tests      : AC-CHK-007, AC-CHK-008, AC-CHK-009, AC-CHK-016, AC-CHK-028, AC-CHK-035, AC-CHK-058, AC-CHK-059
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-CHK-002:END -->
