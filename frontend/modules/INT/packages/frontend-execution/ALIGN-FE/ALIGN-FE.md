<!-- source: PHASE:ALIGN-FE -->
<!-- traces: REQ-INT-049, REQ-INT-051, REQ-INT-057, SCR-INT-001, SCR-INT-002, SCR-INT-003, SCR-INT-004, SCR-INT-005 -->
<!-- PHASE:ALIGN-FE:START traces=REQ-INT-049,REQ-INT-051,REQ-INT-057,SCR-INT-001,SCR-INT-002,SCR-INT-003,SCR-INT-004,SCR-INT-005 -->
## PHASE ALIGN-FE — ALIGN-FE

RF5 — Security (frontend half): no permission model — screens open per the SRS (REQ-INT-004; caller
authentication deferred, raw-idea A2). AIAS-11: every Finding shows its Evidence beside it (REQ-INT-046 — F4
SCR-INT-002) and a report with a MISSING document is never presented as COMPLIANT (REQ-INT-049 — F3 presenter);
an UNREADABLE document is shown with its reason beside the stored Overall Status (ADR-INT-011 (2), ADR-INT-018 (5) — kept by ADR-INT-021 (6)).

```
ALIGN — INT v1
row           backing check       mark   assertion
SCREENS       orphans             ✓      every SCR is referenced by a plan block
COMPOSITION   screen-composition  ✓      every SCR names where its secondary detail sits and that it saves once
CONTAINER     composition-rule    ✓      every SCR names its container, and a child collection sits where that container puts it
READS         ux-reads-spec       ✓      every read a screen binds is an operation of api-spec-int.yaml, and the plan names the document its mock server serves
UXD           orphans             ✓      every UXD is cited by a plan block — this is where a UX decision closes
TRACES        traces              ✓      every PHASE/SUB carries traces=, every UXD traces to its REQ/AC, every SCR to its REQ/UXD
API           traces              ✓      every API this plan cites is an operation of api-spec-int.yaml — never a line of the backend plan's prose
FOREIGN       xref-surface        ✓      every reference to another module's surface resolves in that module's own artifacts
REGISTRY      registry-agree      ✓      every UXD and SCR defined here is in the stage registry, and nothing else is
LANGUAGES     languages           ✓      labels and messages in en + ar (profile require_all false; labels en/ar in the ui-ux-spec, messages ar PENDING ADR-INT-017)
MARKERS       markers             ✓      the parser reports no structural or semantic error for this track and plan
DECISIONS     refs-exist          ✓      every ADR this plan cites exists on disk in analysis/decisions/INT/
COVERAGE      (the report)        none   the clauses the analyze report lists as having examined nothing — none of this stage's
```
```yaml name=self-check
findings: 0
clean: true
```
<!-- PHASE:ALIGN-FE:END -->
