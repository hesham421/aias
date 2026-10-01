<!-- source: PHASE:CROSS-MOD / XM:XM-DOC-001 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-DOC-002, REQ-DOC-003, REQ-DOC-020 -->
<!-- XM:XM-DOC-001:START traces=REQ-DOC-002,REQ-DOC-003,REQ-DOC-020 -->
### XM-DOC-001 — Service package version of a Check (fetch mode, document source)
target     : REG · ENT-REG-002
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-002
requires   : REG:DELIVERED
do         :
  adapter   : `RegVersionAdapter implements VersionLookup` (DOC port) wraps the injected REG in-process interface `ServiceRegistry` and calls `getServicePackageVersion(serviceCode, versionNumber)` (CON-REG-009) → maps fetchMode (ENT-REG-002.fetchMode → `FetchMode`), documentSourceQueryName, documentTypeColumn, documentPathColumn, documentContentColumn and inputName into DOC's immutable `VersionDocumentSettings`; REG's `VersionNotFoundException` → DOC's `ServiceVersionNotFoundException` (DOC-404-SERVICE-VERSION-NOT-FOUND). Used by RULE-DOC-001 (data source ENT-DOC-001.serviceCode, ENT-DOC-001.versionNumber, ENT-REG-002.fetchMode) and by `fetchDocuments` step 1. No FK, no copy kept beyond the call.
  config    : none — the REG interface is a Spring bean of the same deployable, injected by type.
tests      : AC-DOC-002, AC-DOC-003, AC-DOC-022
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-DOC-001:END -->
