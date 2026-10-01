<!-- source: PHASE:CROSS-MOD — the phase around its blocks -->
<!-- traces: REQ-RPT-001 -->
## PHASE CROSS-MOD — CROSS-MODULE

No edge: RPT consumes no entity of another module (SRS A8 `consumes: []`, db-script `records: []`). The Check result port RPT implements is the Check Engine's declared port (ADR-RPT-001) — an inbound call, not a dependency on Check Engine data. RPT's `CheckResultStore` implements its six operations as contract-chk.md promises them: `createCheckRun` (CON-CHK-006), `markRunning` (CON-CHK-007), `completeCheck` (CON-CHK-008), `failCheck` (CON-CHK-009), `getCheck` (CON-CHK-010), `listUnfinishedChecks` (CON-CHK-011); the codes it stores are those of CON-CHK-001 … CON-CHK-003 and of Document Access CON-DOC-001, CON-DOC-002, enforced by the CHECK constraints of db-script-rpt.md; inbound edges from Host Integration reach RPT through contract-rpt.md.

Result-port operations implemented by `CheckResultStore` (PORTS) and served by `CheckRunCommandService` / `CheckRunQueryService` (SVC-API), each signed exactly as contract-chk.md declares it:

**`createCheckRun(serviceCode, versionNumber, fetchMode, requestNumber, employeeId, status, startedAt)` → checkId** — SVC-API `createCheckRun`.
  - Honours: CON-CHK-006

**`markRunning(checkId, runningSince)`** — SVC-API `markRunning`.
  - Honours: CON-CHK-007

**`completeCheck(checkId, overallStatus, findings, documentOutcomes, unreadQueries, metadata)`** — SVC-API `completeCheck`; closed value types per PORTS (REQ-RPT-053, ADR-RPT-018).
  - Honours: CON-CHK-008

**`failCheck(checkId, failureReason, detail, endedAt)`** — SVC-API `failCheck`; closed value types per PORTS (REQ-RPT-053, ADR-RPT-018).
  - Honours: CON-CHK-009

**`getCheck(checkId)` → {checkId, status, serviceCode, versionNumber, fetchMode, requestNumber, employeeId, startedAt}** — SVC-API `getCheck`.
  - Honours: CON-CHK-010

**`listUnfinishedChecks()` → list of {checkId, status, startedAt}** — SVC-API `listUnfinishedChecks`.
  - Honours: CON-CHK-011
