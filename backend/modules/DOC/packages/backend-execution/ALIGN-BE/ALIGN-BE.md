<!-- source: PHASE:ALIGN-BE -->
<!-- traces: ENT-DOC-001, REQ-DOC-034 -->
<!-- PHASE:ALIGN-BE:START traces=REQ-DOC-034,ENT-DOC-001 -->
## PHASE ALIGN-BE — ALIGN-BE

```
ALIGN — DOC v1
row               backing check        assertion
TRACEABILITY      traces               every PHASE/SUB/atom block carries traces=, and every API traces to its REQ and its DBF
COVERED           orphans              every REQ is covered by ≥1 API or DBF
BINDING (§2A)     value-agreement      every DBF names the same physical column here as the db-script declares for it
MANIFEST (§4)     count-agrees         every total this plan states equals the rows it heads
WRITERS           required-writer      every required column is written by an endpoint, or the row states why not
QRC (§5)          orphans              every catalogued query is reached by ≥1 API
API (R3)          code-format          every catalog code is an instance of the declared format and carries a status the platform can emit
API DOCUMENT      api-spec-agree       every API block is one operation of api-spec-doc.yaml and every operation one block, agreeing on method and path
ERROR RESPONSES   api-spec-errors      every catalog row is answered by an operation of api-spec-doc.yaml with its status and code
DOCUMENT VALID    api-spec-valid       api-spec-doc.yaml validates against OPENAPI 3.1.0 and reaches every required item
RULE INPUTS       data-source          every RULE enforced at runtime names where the data it READS comes from, or is deferred
CROSS-MODULE      registry-agree       every registered XM is placed here, and every XM minted here is back-registered
INTEGRATION       xm-block-complete    every edge is one complete block of the last phase, and nothing else names its target
FOREIGN IDS       xref-resolve         every id of another module cited here is defined in that module's own registry
SECURITY (R5)     operation-resolves   every declared entity operation resolves to an API, and every marked matrix cell names its API and its permission
DEMAND (SRS)      operation-resolves   every operation an SRS screen names is built by an API, or the plan states why it is not — examined nothing (0 SCR-REQ)
DECISIONS         refs-exist           every ADR this plan cites exists on disk in analysis/decisions/DOC/
PATHS             paths-resolve        every path the generated manifest and execution state emit resolves to something that exists
COVERAGE          (the report)         as stamped by the orchestrator from the analyze report
```
```yaml name=self-check
findings: 0
clean: true
examined_nothing:
- C7.19
- C7.20
- C7.23
- C7.24
- C7.28
```
R5 — Security (backend half): no permission model — endpoints are open per the SRS (caller authentication deferred, raw-idea A2; no REQ of the SRS names a role check).
<!-- PHASE:ALIGN-BE:END -->
