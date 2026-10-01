<!-- source: PHASE:ALIGN-FE -->
<!-- traces: API-DOC-001, REQ-DOC-017, REQ-DOC-018, REQ-DOC-043, REQ-DOC-056 -->
<!-- PHASE:ALIGN-FE:START traces=REQ-DOC-017,REQ-DOC-018,REQ-DOC-043,REQ-DOC-056,API-DOC-001 -->
## PHASE ALIGN-FE — ALIGN-FE

R5 — Security (frontend half): no permission model — screens open per the SRS (caller authentication deferred, raw-idea A2; REQ-DOC-017, REQ-DOC-018 name no role check).

```
ALIGN — DOC v1
row           backing check   assertion
SCREENS       orphans         every SCR is referenced by a plan block — examined nothing (0 SCR)
COMPOSITION   screen-composition  every SCR names where its secondary detail sits and that it saves once — examined nothing (0 SCR)
CONTAINER     composition-rule    every SCR names its container, and a child collection sits where that container puts it — examined nothing (0 SCR)
READS         ux-reads-spec   every read a screen binds is an operation of api-spec-doc.yaml, and the plan names the document its mock server serves — examined nothing (0 screen SUBs; the mock line names api-spec-doc.yaml)
UXD           orphans         every UXD is cited by a plan block — this is where a UX decision closes — examined nothing (0 UXD)
TRACES        traces          every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD (UXD/SCR clauses examined nothing — 0 of each)
API           traces          every API this plan cites is an operation of api-spec-doc.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface    every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree  every UXD and SCR defined here is in the stage registry, and nothing else is — examined nothing (0 UXD, 0 SCR)
LANGUAGES     languages       labels and messages in en + ar
MARKERS       markers         the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist      every ADR this plan cites exists on disk in analysis/decisions/DOC/
COVERAGE      (the report)    C9.3, C9.4, C9.6, C9.7, C9.8, C9.15, C9.17, C9.22, C9.24 examined nothing (DOC has no screen)
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
| List the Uploaded Documents of a Check | API-DOC-001 | none in DOC — consumed by INT's Document upload and Upload confirmation screens through DOC-FRONTEND-ENTRY | — (DOC has no route; the route is INT's) | ✓ bound (ADR-DOC-013) |
<!-- PHASE:ALIGN-FE:END -->
