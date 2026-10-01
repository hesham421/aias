## REGISTRY — P2 — DOC v1

### Tables
| Table | ENT | Kind | DBF range |
|---|---|---|---|
| DOC_UPLOADED_DOC | ENT-DOC-001 | transactional | DBF-DOC-001, DBF-DOC-002, DBF-DOC-003, DBF-DOC-004, DBF-DOC-005, DBF-DOC-006, DBF-DOC-007, DBF-DOC-008, DBF-DOC-009, DBF-DOC-010, DBF-DOC-011 |
| DOC_ENDED_CHECK | ENT-DOC-002 | transactional | DBF-DOC-012, DBF-DOC-013, DBF-DOC-014, DBF-DOC-015 |

### XM index
| XM | Type | Target | State | Contract |
|---|---|---|---|---|
| XM-DOC-001 | SOFT-READ | REG · ENT-REG-002 | CONTRACTED | CON-REG-002 |
| XM-DOC-002 | SOFT-READ | REG · ENT-REG-003 | CONTRACTED | CON-REG-003 |
| XM-DOC-003 | SOFT-READ | REG · ENT-REG-004 | CONTRACTED | CON-REG-004 |
| XM-DOC-004 | SOFT-READ | REG · ENT-REG-005 | CONTRACTED | CON-REG-005 |

### Lookups
| Key | Seeded values | Owner |
|---|---|---|
| FETCH_MODE | 3 (CHECK in REG_SVC_PKG_VER; service code enum) | DOC |
| DOCUMENT_READ_STATUS | 3 (service code enum; CHECK on RPT's column) | DOC |
| UNREADABLE_REASON | 8 (service code enum; CHECK on RPT's column) | DOC |
| DOCUMENT_TYPE | 0 here (REG data) | REG |
| SERVICE_CODE | 0 here (REG data) | REG |

### Sequences
last DBF: DBF-DOC-015 · last XM: XM-DOC-004

### Decisions
ADR-DOC-010 (ACCEPTED); applied ADR-DOC-015, ADR-DOC-016. BLOCKED: none.

### Event
"P2 completed: DOC v1 — 2 tables, 15 DBF, 4 XM (analysis-gate revise: DOC_ENDED_CHECK)"

### Cascade
none by hand — `gov.py graph` derives the edges targeting DOC and raises their resolution events.
