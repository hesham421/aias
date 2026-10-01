## REGISTRY — P2 — INT v1

### Tables
None — INT creates no table (ADR-INT-015). Read bindings (owner tables, not created here):
| Bound table (owner) | ENT | DBF range |
|---|---|---|
| RPT_CHECK_RUN (RPT) | ENT-RPT-001 | DBF-INT-001, DBF-INT-002, DBF-INT-003, DBF-INT-004, DBF-INT-005, DBF-INT-006, DBF-INT-007, DBF-INT-008 |
| REG_SVC_PKG_VER (REG) | ENT-REG-002 | DBF-INT-009, DBF-INT-010 |

### XM index
| XM | Type | Target | State | Contract |
|---|---|---|---|---|
| XM-INT-001 | SOFT-READ | REG · ENT-REG-002 | CONTRACTED | CON-REG-002 |

### Lookups
| Key | Seeded values | Owner |
|---|---|---|
| — | 0 — INT owns no lookup | — |

### Sequences
last DBF: DBF-INT-010 · last XM: XM-INT-001

### Decisions
ADR-INT-015, ADR-INT-016 (ACCEPTED). BLOCKED: none.

### Event
"P2 completed: INT v1 — 0 tables, 10 DBF, 1 XM"

### Cascade
none by hand — `gov.py graph` derives the edges targeting INT and raises their resolution events.
