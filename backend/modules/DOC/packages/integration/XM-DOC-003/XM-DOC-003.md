<!-- source: PHASE:CROSS-MOD / XM:XM-DOC-003 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-DOC-021, REQ-DOC-035 -->
<!-- XM:XM-DOC-003:START traces=REQ-DOC-021,REQ-DOC-035 -->
### XM-DOC-003 — Required document types of the version
target     : REG · ENT-REG-004
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-004
requires   : REG:DELIVERED
do         :
  adapter   : `RegVersionAdapter` maps the version's requiredDocumentTypes (ENT-REG-004.documentType values, from `getServicePackageVersion`, CON-REG-009) into an unmodifiable `Set<String>`; used by RULE-DOC-002 (data source ENT-DOC-001.documentType, ENT-REG-004.documentType) at handover and by `fetchDocuments` step 6 (MISSING outcomes). Values compared exactly as stored (case-sensitive), never hardcoded.
  config    : none — in-process bean.
tests      : AC-DOC-023, AC-DOC-037
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-DOC-003:END -->
