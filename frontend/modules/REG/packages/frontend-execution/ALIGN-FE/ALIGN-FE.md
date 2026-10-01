<!-- source: PHASE:ALIGN-FE -->
<!-- traces: API-REG-001, API-REG-002, API-REG-003, REQ-REG-008, REQ-REG-013, REQ-REG-014, REQ-REG-015, REQ-REG-017 -->
<!-- PHASE:ALIGN-FE:START traces=REQ-REG-008,REQ-REG-013,REQ-REG-014,REQ-REG-015,REQ-REG-017,API-REG-001,API-REG-002,API-REG-003 -->
## PHASE ALIGN-FE — ALIGN-FE

```
ALIGN — REG v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block — examined nothing (0 SCR)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once — examined nothing (0 SCR)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it — examined nothing (0 SCR)
READS         ux-reads-spec   every read a screen binds is an operation of api-spec-reg.yaml, and the plan names the document its mock server serves — examined nothing (0 screen SUB)
UXD           orphans         every UXD is cited by a plan block — this is where a UX decision closes — examined nothing (0 UXD)
TRACES        traces          every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces          every API this plan cites is an operation of api-spec-reg.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface    every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree  every UXD and SCR defined here is in the stage registry, and nothing else is — examined nothing (0 SCR, 0 UXD)
LANGUAGES     languages       labels and messages in en + ar
MARKERS       markers         the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in analysis/decisions/REG/
COVERAGE      (the report)    examined nothing: C9.3, C9.4, C9.6, C9.7, C9.8, C9.15, C9.17, C9.22, C9.24 — as stamped by the orchestrator from the analyze report
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

### Operations coverage
| Operation | API | SCR action | Route | Status |
|---|---|---|---|---|
| List services | API-REG-001 | none — no REG screen; read query SERVICES-QUERY for consuming INT screens | — | n/a — no REG screen (ADR-REG-012) |
| Read one service | API-REG-002 | none — no REG screen; read query SERVICE-QUERY reused by INT's SCR-REQ-INT-003 | — | n/a — no REG screen (ADR-REG-012) |
| Read the load report | API-REG-003 | none — no v1 consumer (no administration UI) | — | n/a — no REG screen (ADR-REG-012) |
<!-- PHASE:ALIGN-FE:END -->
