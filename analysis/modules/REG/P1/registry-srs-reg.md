## REGISTRY — P1 — REG v1

### Entities
| ENT | Name | Kind | Ownership | Status |
|---|---|---|---|---|
| ENT-REG-001 | Service Package | config | SHARED(owner) | REGISTERED |
| ENT-REG-002 | Service Package Version | config | SHARED(owner) | REGISTERED |
| ENT-REG-003 | Service Query | config | SHARED(owner) | REGISTERED |
| ENT-REG-004 | Required Document | config | SHARED(owner) | REGISTERED |
| ENT-REG-005 | Connection | config | SHARED(owner) | REGISTERED |
| ENT-REG-006 | Load Result | transactional | PRIVATE | REGISTERED |

### Consumed
None — the `module-dependencies` block is `consumes: []`.

### Lookups owned
| Key | ENT | Values |
|---|---|---|
| SERVICE_CODE | ENT-REG-001 | 1 |
| CONNECTION_TYPE | ENT-REG-005 | 2 |
| DOCUMENT_TYPE | ENT-REG-004 | 2 |
| LOAD_OUTCOME | ENT-REG-006 | 7 |
| LOAD_SUBJECT | ENT-REG-006 | 2 |

### Lookups consumed
| Key | Owner |
|---|---|
| FETCH_MODE | DOC (profile closed enum, no runtime read — ADR-REG-005) |

### Screens
None — no SCR-REQ in this version (administration UI out of scope).

### Requirements
REQ count 62 · AC count 64 · RULE count 20 · last sequence per atom (REQ: 62, AC: 64, ENT: 6, RULE: 20, SCR-REQ: 0)

| REQ | AC |
|---|---|
| REQ-REG-001 | AC-REG-001 |
| REQ-REG-002 | AC-REG-002 |
| REQ-REG-003 | AC-REG-003 |
| REQ-REG-004 | AC-REG-004 |
| REQ-REG-005 | AC-REG-005 |
| REQ-REG-006 | AC-REG-006 |
| REQ-REG-007 | AC-REG-007 |
| REQ-REG-008 | AC-REG-008 |
| REQ-REG-009 | AC-REG-009 |
| REQ-REG-010 | AC-REG-010 |
| REQ-REG-011 | AC-REG-011 |
| REQ-REG-012 | AC-REG-012, AC-REG-013 |
| REQ-REG-013 | AC-REG-014 |
| REQ-REG-014 | AC-REG-015 |
| REQ-REG-015 | AC-REG-016 |
| REQ-REG-016 | AC-REG-017 |
| REQ-REG-017 | AC-REG-018 |
| REQ-REG-018 | AC-REG-019 |
| REQ-REG-019 | AC-REG-020 |
| REQ-REG-020 | AC-REG-021 |
| REQ-REG-021 | AC-REG-022 |
| REQ-REG-022 | AC-REG-023 |
| REQ-REG-023 | AC-REG-024 |
| REQ-REG-024 | AC-REG-025 |
| REQ-REG-025 | AC-REG-026 |
| REQ-REG-026 | AC-REG-027 |
| REQ-REG-027 | AC-REG-028 |
| REQ-REG-028 | AC-REG-029 |
| REQ-REG-029 | AC-REG-030 |
| REQ-REG-030 | AC-REG-031 |
| REQ-REG-031 | AC-REG-032 |
| REQ-REG-032 | AC-REG-033, AC-REG-034 |
| REQ-REG-033 | AC-REG-035 |
| REQ-REG-034 | AC-REG-036 |
| REQ-REG-035 | AC-REG-037 |
| REQ-REG-036 | AC-REG-038 |
| REQ-REG-037 | AC-REG-039 |
| REQ-REG-038 | AC-REG-040 |
| REQ-REG-039 | AC-REG-041 |
| REQ-REG-040 | AC-REG-042 |
| REQ-REG-041 | AC-REG-043 |
| REQ-REG-042 | AC-REG-044 |
| REQ-REG-043 | AC-REG-045 |
| REQ-REG-044 | AC-REG-046 |
| REQ-REG-045 | AC-REG-047 |
| REQ-REG-046 | AC-REG-048 |
| REQ-REG-047 | AC-REG-049 |
| REQ-REG-048 | AC-REG-050 |
| REQ-REG-049 | AC-REG-051 |
| REQ-REG-050 | AC-REG-052 |
| REQ-REG-051 | AC-REG-053 |
| REQ-REG-052 | AC-REG-054 |
| REQ-REG-053 | AC-REG-055 |
| REQ-REG-054 | AC-REG-056 |
| REQ-REG-055 | AC-REG-057 |
| REQ-REG-056 | AC-REG-058 |
| REQ-REG-057 | AC-REG-059 |
| REQ-REG-058 | AC-REG-060 |
| REQ-REG-059 | AC-REG-061 |
| REQ-REG-060 | AC-REG-062 |
| REQ-REG-061 | AC-REG-063 |
| REQ-REG-062 | AC-REG-064 |

| RULE | Traces |
|---|---|
| RULE-REG-001 | REQ-REG-003 |
| RULE-REG-002 | REQ-REG-004 |
| RULE-REG-003 | REQ-REG-021 |
| RULE-REG-004 | REQ-REG-022 |
| RULE-REG-005 | REQ-REG-031 |
| RULE-REG-006 | REQ-REG-032 |
| RULE-REG-007 | REQ-REG-033 |
| RULE-REG-008 | REQ-REG-038 |
| RULE-REG-009 | REQ-REG-039 |
| RULE-REG-010 | REQ-REG-040 |
| RULE-REG-011 | REQ-REG-043 |
| RULE-REG-012 | REQ-REG-034, REQ-REG-041 |
| RULE-REG-013 | REQ-REG-047 |
| RULE-REG-014 | REQ-REG-052 |
| RULE-REG-015 | REQ-REG-055 |
| RULE-REG-016 | REQ-REG-012, REQ-REG-015 |
| RULE-REG-017 | REQ-REG-053 |
| RULE-REG-018 | REQ-REG-028 |
| RULE-REG-019 | REQ-REG-035 |
| RULE-REG-020 | REQ-REG-062 |

### Decisions
ADR-REG-007, ADR-REG-008, ADR-REG-009 (new, ACCEPTED); applied ADR-REG-001 … ADR-REG-006. BLOCKED: none.

### Event
"P1 completed: REG v1 — REQ 62 · AC 64 · ENT 6 · RULE 20 · SCR-REQ 0 · ADR 3"
