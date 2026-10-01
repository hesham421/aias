<!-- source: PHASE:CROSS-MOD / XM:XM-INT-001 -->
<!-- context: CROSS-MOD-HEADER.md — phase-level preamble -->
<!-- traces: REQ-INT-025, REQ-INT-028, REQ-INT-030, REQ-INT-064 -->
<!-- XM:XM-INT-001:START traces=REQ-INT-025,REQ-INT-028,REQ-INT-030,REQ-INT-064 -->
### XM-INT-001 — Approval definition and required document types of the Check's service package version
target     : REG · ENT-REG-002
type       : SOFT-READ
contract   : contract-reg.md#CON-REG-002
requires   : REG:DELIVERED
do         :
  adapter   : `RegApprovalDefinitionAdapter implements ApprovalDefinitionPort` (INT port) wraps the injected REG in-process interface `ApprovalApiRegistry` — the separate interface REG gives only to the Employee Decision operation — and calls `getApprovalApi(serviceCode, versionNumber)` (CON-REG-012) with the service code and version number of the Check; maps ENT-REG-002.approvalEnabled (DBF-INT-009) to `ApprovalDefinition.enabled` and splits ENT-REG-002.approvalApi (DBF-INT-010, e.g. `POST /requests/{requestId}/approve`) at its first space into `method` and `pathTemplate`. REG's not-found for the Check's own version (CON-REG-002 promises it never disappears) → `INT-500`, logged with the Check identifier. Nothing is kept beyond the call; no FK. The adapter is injected only into `DecisionService` (AIAS-4) and never exposed to a model. Second adapter (ADR-INT-020): `RegVersionDocumentsAdapter implements VersionDocumentsPort` wraps the injected REG in-process interface `ServiceRegistry` and calls `getServicePackageVersion(serviceCode, versionNumber)` (CON-REG-009) with the Check's service code and version number; maps the version's `requiredDocumentTypes` (ENT-REG-004 values of ENT-REG-002) into an unmodifiable list in the version's order; REG's `VersionNotFoundException` for the Check's own version (CON-REG-002 promises it never disappears) → `INT-500`, logged with the Check identifier. Injected only into `RequiredDocumentTypesService`; it never receives the approval interface.
  config    : none — the REG interface is a Spring bean of the same deployable, injected by type.
tests      : AC-INT-029, AC-INT-032, AC-INT-034, AC-INT-073
if_not_met : skip-block; record in execution-state.json → deferred_xm; continue
<!-- XM:XM-INT-001:END -->
