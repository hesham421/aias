## REGISTRY — P2 — CHK v1

### Tables
| Table | ENT | Kind | DBF range |
|---|---|---|---|
| CHK_ACTIVE_CHECK | ENT-CHK-001 | transactional | DBF-CHK-001, DBF-CHK-002, DBF-CHK-003, DBF-CHK-004, DBF-CHK-005, DBF-CHK-006 |

### XM index
| XM | Type | Target | State | Contract |
|---|---|---|---|---|
| XM-CHK-001 | SOFT-READ | REG · ENT-REG-001 | CONTRACTED | CON-REG-001 |
| XM-CHK-002 | SOFT-READ | REG · ENT-REG-002 | CONTRACTED | CON-REG-002 |
| XM-CHK-003 | SOFT-READ | REG · ENT-REG-003 | CONTRACTED | CON-REG-003 |
| XM-CHK-004 | SOFT-READ | REG · ENT-REG-004 | CONTRACTED | CON-REG-004 |
| XM-CHK-005 | SOFT-READ | REG · ENT-REG-005 | CONTRACTED | CON-REG-005 |

### Lookups
| Key | Seeded values | Owner |
|---|---|---|
| OVERALL_STATUS | 3 (service code enum; CHECK on RPT's column) | CHK |
| CHECK_STATUS | 4 (service code enum; CHECK on CHK_ACTIVE_CHECK — 2 values — and on RPT's column) | CHK |
| FINDING_OUTCOME | 3 (service code enum; CHECK on RPT's column) | CHK |
| CHECK_FAILURE_REASON | 7 (service code enum; CHECK on RPT's column) | CHK |
| SERVICE_CODE, DOCUMENT_TYPE, CONNECTION_TYPE | 0 here | REG |
| FETCH_MODE, DOCUMENT_READ_STATUS, UNREADABLE_REASON | 0 here | DOC |

### Sequences
last DBF: DBF-CHK-006 · last XM: XM-CHK-005

### Decisions
ADR-CHK-016 (ACCEPTED). BLOCKED: none.

### Event
"P2 completed: CHK v1 — 1 tables, 6 DBF, 5 XM"

### Cascade
none by hand — `gov.py graph` derives the edges targeting CHK and raises their resolution events.
