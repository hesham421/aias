<!-- source: PHASE:ALIGN-FE -->
<!-- traces: API-RPT-001, API-RPT-002, REQ-RPT-023, REQ-RPT-028 -->
<!-- PHASE:ALIGN-FE:START traces=REQ-RPT-023,REQ-RPT-028,API-RPT-001,API-RPT-002 -->
## PHASE ALIGN-FE — ALIGN-FE

```
ALIGN — RPT v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block — examined nothing (0 SCR, ADR-RPT-014)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once — examined nothing (0 SCR)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it — examined nothing (0 SCR)
READS         ux-reads-spec   every read a screen binds is an operation of api-spec-rpt.yaml, and the plan names the document its mock server serves
UXD           orphans         every UXD is cited by a plan block — examined nothing (0 UXD)
TRACES        traces          every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces          every API this plan cites is an operation of api-spec-rpt.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface    every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree  every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages       labels and messages in en + ar
MARKERS       markers         the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in analysis/decisions/RPT/
COVERAGE      (the report)    as stamped by the orchestrator from the analyze report
```
```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C9.15
- C9.17
- C9.22
- C9.24
- C9.3
- C9.4
- C9.6
- C9.7
- C9.8
```

Operations coverage:

| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| Read a Check and its report | API-RPT-001 | none in RPT — read by Host Integration's Check report screen | Host Integration's route | consumed (CHECK-QUERY) — no RPT route by design (ADR-RPT-014) |
| List the Checks of a request | API-RPT-002 | none in RPT — read by Host Integration's Checks of a request screen | Host Integration's route | consumed (CHECKS-OF-REQUEST-QUERY) — no RPT route by design (ADR-RPT-014) |
| Read the decision agreement of a service | API-RPT-003 | none | none | unused by the employee frontend — administration UI out of scope (ADR-RPT-014) |
<!-- PHASE:ALIGN-FE:END -->
