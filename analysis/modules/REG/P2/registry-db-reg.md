## REGISTRY — P2 — REG v1

### Tables
| Table | ENT | Kind | DBF range |
|---|---|---|---|
| REG_SVC_PKG | ENT-REG-001 | config | DBF-REG-001, DBF-REG-002, DBF-REG-003, DBF-REG-004, DBF-REG-005 |
| REG_SVC_PKG_VER | ENT-REG-002 | config | DBF-REG-006, DBF-REG-007, DBF-REG-008, DBF-REG-009, DBF-REG-010, DBF-REG-011, DBF-REG-012, DBF-REG-013, DBF-REG-014, DBF-REG-015, DBF-REG-016, DBF-REG-017, DBF-REG-018, DBF-REG-019, DBF-REG-020 |
| REG_SVC_QUERY | ENT-REG-003 | config | DBF-REG-021, DBF-REG-022, DBF-REG-023, DBF-REG-024, DBF-REG-025 |
| REG_REQ_DOC | ENT-REG-004 | config | DBF-REG-026, DBF-REG-027, DBF-REG-028 |
| REG_CONNECTION | ENT-REG-005 | config | DBF-REG-029, DBF-REG-030, DBF-REG-031, DBF-REG-032, DBF-REG-033, DBF-REG-034, DBF-REG-035, DBF-REG-036, DBF-REG-037, DBF-REG-038, DBF-REG-039 |
| REG_LOAD_RESULT | ENT-REG-006 | transactional | DBF-REG-040, DBF-REG-041, DBF-REG-042, DBF-REG-043, DBF-REG-044, DBF-REG-045, DBF-REG-046, DBF-REG-047, DBF-REG-048, DBF-REG-049 |

### XM index
None — REG consumes nothing (`records: []`).

### Lookups
| Key | Seeded values | Owner |
|---|---|---|
| SERVICE_CODE | 1 (via pilot package load) | REG |
| CONNECTION_TYPE | 2 (CHECK) | REG |
| DOCUMENT_TYPE | 2 (via pilot package load) | REG |
| LOAD_OUTCOME | 7 (CHECK) | REG |
| LOAD_SUBJECT | 3 (CHECK) | REG |
| FETCH_MODE | 3 (CHECK; profile closed enum) | DOC (consumed by value, ADR-REG-005) |

### Sequences
last DBF: DBF-REG-049 · last XM: none

### Decisions
ADR-REG-010 (ACCEPTED); applied ADR-REG-015, ADR-REG-017, ADR-REG-018 (gate-analysis REVISE — 2 CHECK constraints added / widened, no new DBF). BLOCKED: none.

### Event
"P2 completed: REG v1 — 6 tables, 49 DBF, 0 XM"
"P2 revised (gate-analysis REVISE 2026-10-01): REG v1 — 6 tables, 49 DBF, 0 XM; CHK_REG_SVC_PKG_SERVICE_CODE added, CHK_REG_LOAD_RESULT_SUBJECT_KIND widened"

### Cascade
none by hand — `gov.py graph` derives the edges targeting REG.
