## REGISTRY — P3.2 — INT v1

```
REGISTRY — P3.2 — INT v1
ID RANGES     UXD-INT-001..UXD-INT-008 · SCR-INT-001..SCR-INT-005
ALIGN         verdict as stamped by the orchestrator · findings fixed: see the analyze report
ADRs          decisions/INT/ADR-INT-018, ADR-INT-021 (ACCEPTED, non-breaking; ADR-INT-021 supersedes ADR-INT-018 (1), (2), (3), (7)) · ADR-INT-020 (P1 revision) · ADR-INT-025, ADR-INT-026 (gate round 1 — DOC upload refusals, same-type uploads, background-read failure) · applied ADR-INT-001, ADR-INT-006, ADR-INT-011, ADR-INT-013, ADR-INT-017
TRACEABILITY  REQ covered by ≥1 SCR/F-block: 51/66 · the other 15 (REQ-INT-002 … REQ-INT-005, REQ-INT-007, REQ-INT-008, REQ-INT-029 … REQ-INT-033, REQ-INT-039, REQ-INT-058 … REQ-INT-060) are backend behaviour with no screen element, bound in the api-surface block to API-INT-001 … API-INT-004 or held by the backend CORE · orphan REQ: none
```

### Screens
| SCR | Name | SCR-REQ | Owning ENT | Permissions |
|---|---|---|---|---|
| SCR-INT-001 | Checks of a request | SCR-REQ-INT-001 | ENT-RPT-001 (Report Store, read) | none — no permission model (raw-idea A2) |
| SCR-INT-002 | Check report | SCR-REQ-INT-002 | ENT-RPT-001 (Report Store, read) | none |
| SCR-INT-003 | Document upload | SCR-REQ-INT-003 | ENT-RPT-001 (Report Store, read) | none |
| SCR-INT-004 | Upload confirmation | SCR-REQ-INT-004 | ENT-RPT-001 (Report Store, read) | none |
| SCR-INT-005 | Employee decision | SCR-REQ-INT-005 | ENT-RPT-001, ENT-REG-002 | none |

### UXD index
| UXD | Screen | Field(s) | Owner module · API used |
|---|---|---|---|
| UXD-INT-001 | SCR-INT-001 | Checks of a request (checkId, status, overallStatus, startedAt, endedAt, employeeDecision), total | RPT · API-INT-006 |
| UXD-INT-002 | SCR-INT-002 | header, failure, recorded decision | RPT · API-INT-005 |
| UXD-INT-003 | SCR-INT-002 | findings (condition, outcome, evidence, note) | RPT · API-INT-005 |
| UXD-INT-004 | SCR-INT-002 | documents (read status, reason, detail), service queries not read | RPT · API-INT-005 |
| UXD-INT-005 | SCR-INT-003 | document type choices (requiredDocumentTypes of the Check's version) | REG · API-INT-008 |
| UXD-INT-006 | SCR-INT-003 | uploaded documents | DOC · API-INT-007 |
| UXD-INT-007 | SCR-INT-004 | uploaded documents | DOC · API-INT-007 |
| UXD-INT-008 | SCR-INT-004 | required document types with no upload | REG · API-INT-008 |

### API coverage
| Document | Used | Unused |
|---|---|---|
| api-spec-int.yaml | API-INT-001 … API-INT-008 (8/8) | — |

The frontend binds no operation of another module's document (ADR-INT-021). API-INT-007's backend adapter waits on Document Access's in-process listing operation (ADR-INT-020 (4)); the frontend is built against the document.

### Event
P3.2 completed: INT v1 — 5 SCR · 8 UXD · 20 F-SUB (F1–F4 × 5 screens) · 2 ADR (ADR-INT-018, ADR-INT-021)
