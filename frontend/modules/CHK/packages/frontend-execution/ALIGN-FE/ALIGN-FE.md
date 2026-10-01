<!-- source: PHASE:ALIGN-FE -->
<!-- traces: API-CHK-001, REQ-CHK-076, REQ-CHK-077, REQ-CHK-080 -->
<!-- PHASE:ALIGN-FE:START traces=API-CHK-001,REQ-CHK-076,REQ-CHK-077,REQ-CHK-080 -->
## PHASE ALIGN-FE — ALIGN-FE

```
ALIGN — CHK v1
row           backing check       assertion
SCREENS       orphans             every SCR is referenced by a plan block — examined nothing (0 SCR)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once — examined nothing (0 SCR)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it — examined nothing (0 SCR)
READS         ux-reads-spec       every read a screen binds is an operation of api-spec-chk.yaml, and the plan names the document its mock server serves
UXD           orphans             every UXD is cited by a plan block — examined nothing (0 UXD)
TRACES        traces              every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces              every API this plan cites is an operation of api-spec-chk.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface        every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree      every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages           labels and messages in en + ar
MARKERS       markers             the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist          every ADR this plan cites exists on disk in analysis/decisions/CHK/
COVERAGE      (the report)        as stamped by the orchestrator from the analyze report
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
| Read the Active Check of a Check | API-CHK-001 | none in CHK — read by the F2 hook for a screen of INT | — (no CHK route) | ✗ by the template's rule (empty route); accepted by ADR-CHK-019, because CHK has no screen |

R5 — Security (frontend half): no permission model — screens open per the SRS (REQ-CHK-076, REQ-CHK-077,
REQ-CHK-080; caller authentication deferred, raw-idea A2).
<!-- PHASE:ALIGN-FE:END -->
