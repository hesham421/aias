<!-- source: PHASE:CROSS-MOD / XM:XM-CHK-001 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-CHK-005, REQ-CHK-007 -->
<!-- XM:XM-CHK-001:START traces=REQ-CHK-005,REQ-CHK-007 -->
### XM-CHK-001 — Service availability at Check start
target     : REG · ENT-REG-001
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-001
requires   : REG:DELIVERED
do         :
  adapter   : `RegServiceAdapter implements ServiceAvailability` (CHK port) wraps the injected REG in-process interface `ServiceRegistry` and calls `isServiceAvailable(serviceCode)` (CON-REG-013) — false for an unknown or withdrawn code, i.e. ENT-REG-001.serviceCode not held or ENT-REG-001.available false → RULE-CHK-001 (data source ENT-REG-001.serviceCode, ENT-REG-001.available) raises CHK-422-SERVICE-NOT-AVAILABLE before anything is created. Service codes are passed exactly as received and never hardcoded. No FK, nothing kept.
  config    : none — the REG interface is a Spring bean of the same deployable, injected by type.
tests      : AC-CHK-005, AC-CHK-006, AC-CHK-008
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-CHK-001:END -->
