<!-- source: PHASE:CROSS-MOD / XM:XM-CHK-004 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-CHK-019, REQ-CHK-020, REQ-CHK-021, REQ-CHK-022 -->
<!-- XM:XM-CHK-004:START traces=REQ-CHK-019,REQ-CHK-020,REQ-CHK-021,REQ-CHK-022 -->
### XM-CHK-004 — Required document types of the version
target     : REG · ENT-REG-004
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-004
requires   : REG:DELIVERED
do         :
  adapter   : `RegPackageAdapter` maps the package's requiredDocumentTypes (ENT-REG-004.documentType values, CON-REG-007 / CON-REG-009) into an unmodifiable `Set<String>`; RULE-CHK-004 (data source ENT-REG-004.documentType) gives one finding per value, compared exactly as stored (case-sensitive) with the documentType of each Document Access outcome; never hardcoded.
  config    : none — in-process bean.
tests      : AC-CHK-020, AC-CHK-021, AC-CHK-022, AC-CHK-023
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-CHK-004:END -->
