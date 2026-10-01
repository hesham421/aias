## REGISTRY — P2 — RPT v1

### Tables
| Table | ENT | Kind | DBF range |
|---|---|---|---|
| RPT_CHECK_RUN | ENT-RPT-001 | transactional | DBF-RPT-001, DBF-RPT-002, DBF-RPT-003, DBF-RPT-004, DBF-RPT-005, DBF-RPT-006, DBF-RPT-007, DBF-RPT-008, DBF-RPT-009, DBF-RPT-010, DBF-RPT-011, DBF-RPT-012, DBF-RPT-013, DBF-RPT-014, DBF-RPT-015, DBF-RPT-016, DBF-RPT-017, DBF-RPT-018, DBF-RPT-019, DBF-RPT-020 |
| RPT_FINDING | ENT-RPT-002 | transactional | DBF-RPT-021, DBF-RPT-022, DBF-RPT-023, DBF-RPT-024, DBF-RPT-025, DBF-RPT-026, DBF-RPT-027, DBF-RPT-028, DBF-RPT-029 |
| RPT_CHECK_DOCUMENT | ENT-RPT-003 | transactional | DBF-RPT-030, DBF-RPT-031, DBF-RPT-032, DBF-RPT-033, DBF-RPT-034, DBF-RPT-035, DBF-RPT-036, DBF-RPT-037, DBF-RPT-038, DBF-RPT-039 |
| RPT_UNREAD_QUERY | ENT-RPT-004 | transactional | DBF-RPT-040, DBF-RPT-041, DBF-RPT-042, DBF-RPT-043, DBF-RPT-044, DBF-RPT-045, DBF-RPT-046 |

### XM index
None — `records: []` (SRS A8 `consumes: []`; the RPT → CHK edge is the platform edge).

### Lookups
| Key | Seeded values | Owner |
|---|---|---|
| EMPLOYEE_DECISION | 2 (service code enum; CHECK on RPT_CHECK_RUN.EMPLOYEE_DECISION) | RPT |
| CHECK_STATUS, OVERALL_STATUS, FINDING_OUTCOME, CHECK_FAILURE_REASON | 0 here (CHECK constraints on RPT columns) | CHK |
| FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON | 0 here (CHECK constraints on RPT columns) | DOC |
| SERVICE_CODE, DOCUMENT_TYPE | 0 here (open lists stored as values) | REG |

### Sequences
last DBF: DBF-RPT-046 · last XM: none

### Decisions
ADR-RPT-011 (ACCEPTED). BLOCKED: none.

### Event
"P2 completed: RPT v1 — 4 tables, 46 DBF, 0 XM"

### Cascade
none by hand — `gov.py graph` derives the edges targeting RPT and raises their resolution events.
