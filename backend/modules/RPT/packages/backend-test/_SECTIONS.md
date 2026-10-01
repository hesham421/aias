<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# BACKEND TEST PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Stage : P4   Framework : agnostic (the consumer repo chooses its tool; this plan names none)
Sources : _state/current-srs.md (v1, AC 64) · current-registry-srs.md · current-registry-db.md (XM 0) · current-backend-execution-plan.md (units PORTS, SVC-API; CORE, DATA-DOM, ALIGN-BE no_tests; CROSS-MOD 0 edges) · current-api-spec.yaml (API-RPT-001 … API-RPT-003) · dependency-graph (no RPT XM edge)
Open ADRs : none BLOCKED — applied ADR-RPT-006, ADR-RPT-012, ADR-RPT-013, ADR-RPT-015, ADR-RPT-018, ADR-RPT-020
TCs : 64 (TC-RPT-001 … TC-RPT-064) — one per AC; RULE-SCENARIOS 34 · API-SCENARIOS 29 · MODEL-EVAL 1
══════════════════════════════════════════════════════════════════

Every TC derives from one AC. In-process operations (the Check result port, the decision procedure, the purge — ADR-RPT-006) are named on the `Exercises` line; HTTP cases cite the API id and read the shape in api-spec-rpt.yaml. Errors over HTTP are ProblemDetail (RFC 9457) → {type, title, status, detail, code}; in-process refusals are typed exceptions carrying the same code and message (ADR-RPT-013). Arabic messages are `PENDING ADR-RPT-013`. Values of the open REG lists (service code, document type) are placeholders carried by value (ADR-RPT-015). INT-XM is absent: RPT declares no XM edge (registry-db XM 0).



## TC TRACEABILITY INDEX

| AC | REQ | TC | API / operation | RULE → code | Package |
|---|---|---|---|---|---|
| AC-RPT-001 | REQ-RPT-001 | TC-RPT-001 | in-process | — | PORTS |
| AC-RPT-002 | REQ-RPT-002 | TC-RPT-002 | API-RPT-001 | — | PORTS |
| AC-RPT-003 | REQ-RPT-003 | TC-RPT-003 | in-process | RULE-RPT-001 → RPT-400-CHECK-RUN-INCOMPLETE | PORTS |
| AC-RPT-004 | REQ-RPT-004 | TC-RPT-004 | in-process | RULE-RPT-002 → RPT-422-INITIAL-STATUS-MISMATCH | PORTS |
| AC-RPT-005 | REQ-RPT-004 | TC-RPT-005 | in-process | RULE-RPT-002 → RPT-422-INITIAL-STATUS-MISMATCH | PORTS |
| AC-RPT-006 | REQ-RPT-005 | TC-RPT-006 | in-process | RULE-RPT-003 (allowed transition AWAITING_DOCUMENTS → RUNNING) | PORTS |
| AC-RPT-007 | REQ-RPT-005 | TC-RPT-007 | in-process | RULE-RPT-003 (allowed transition RUNNING → RUNNING) | PORTS |
| AC-RPT-008 | REQ-RPT-006 | TC-RPT-008 | in-process | RULE-RPT-003 → RPT-409-CHECK-ENDED | PORTS |
| AC-RPT-009 | REQ-RPT-006 | TC-RPT-009 | in-process | RULE-RPT-003 → RPT-409-CHECK-NOT-RUNNING | PORTS |
| AC-RPT-010 | REQ-RPT-007 | TC-RPT-010 | in-process | REQ-RPT-007 → RPT-404-CHECK-NOT-FOUND | PORTS |
| AC-RPT-011 | REQ-RPT-008 | TC-RPT-011 | API-RPT-001 | — | PORTS |
| AC-RPT-012 | REQ-RPT-009 | TC-RPT-012 | in-process | RULE-RPT-006 → RPT-422-UNKNOWN-CODE (refused before any write) | PORTS |
| AC-RPT-013 | REQ-RPT-010 | TC-RPT-013 | API-RPT-001 | — | PORTS |
| AC-RPT-014 | REQ-RPT-011 | TC-RPT-014 | API-RPT-001 | — | PORTS |
| AC-RPT-015 | REQ-RPT-012 | TC-RPT-015 | API-RPT-001 | — | PORTS |
| AC-RPT-016 | REQ-RPT-013 | TC-RPT-016 | in-process | RULE-RPT-004 → RPT-422-METADATA-MISMATCH | PORTS |
| AC-RPT-017 | REQ-RPT-014 | TC-RPT-017 | in-process | RULE-RPT-005 → RPT-422-COMPLIANT-NOT-VERIFIED | PORTS |
| AC-RPT-018 | REQ-RPT-015 | TC-RPT-018 | in-process | RULE-RPT-006 → RPT-422-UNKNOWN-CODE | PORTS |
| AC-RPT-019 | REQ-RPT-016 | TC-RPT-019 | in-process | RULE-RPT-007 → RPT-422-FINDING-INCOMPLETE | PORTS |
| AC-RPT-020 | REQ-RPT-017 | TC-RPT-020 | in-process | RULE-RPT-008 → RPT-422-DOCUMENT-REASON-MISMATCH | PORTS |
| AC-RPT-021 | REQ-RPT-018 | TC-RPT-021 | API-RPT-001 | RULE-RPT-003 (allowed transition RUNNING → FAILED) | PORTS |
| AC-RPT-022 | REQ-RPT-019 | TC-RPT-022 | API-RPT-001 | — | SVC-API |
| AC-RPT-023 | REQ-RPT-020 | TC-RPT-023 | in-process | RULE-RPT-003 → RPT-409-CHECK-ENDED | PORTS |
| AC-RPT-024 | REQ-RPT-021 | TC-RPT-024 | in-process | — | PORTS |
| AC-RPT-025 | REQ-RPT-022 | TC-RPT-025 | in-process | — | PORTS |
| AC-RPT-026 | REQ-RPT-022 | TC-RPT-026 | in-process | — | PORTS |
| AC-RPT-027 | REQ-RPT-023 | TC-RPT-027 | API-RPT-001 | — | SVC-API |
| AC-RPT-028 | REQ-RPT-023 | TC-RPT-028 | API-RPT-001 | — | SVC-API |
| AC-RPT-029 | REQ-RPT-024 | TC-RPT-029 | API-RPT-001 | — | SVC-API |
| AC-RPT-030 | REQ-RPT-025 | TC-RPT-030 | API-RPT-001 | REQ-RPT-025 → RPT-404-CHECK-NOT-FOUND | SVC-API |
| AC-RPT-031 | REQ-RPT-026 | TC-RPT-031 | API-RPT-001 | — | SVC-API |
| AC-RPT-032 | REQ-RPT-027 | TC-RPT-032 | API-RPT-001 | — | SVC-API |
| AC-RPT-033 | REQ-RPT-028 | TC-RPT-033 | API-RPT-002 | — | SVC-API |
| AC-RPT-034 | REQ-RPT-028 | TC-RPT-034 | API-RPT-002 | — | SVC-API |
| AC-RPT-035 | REQ-RPT-029 | TC-RPT-035 | API-RPT-002 | RULE-RPT-009 → RPT-400-REQUEST-KEYS-MISSING | SVC-API |
| AC-RPT-036 | REQ-RPT-030 | TC-RPT-036 | API-RPT-001 | — | PORTS |
| AC-RPT-037 | REQ-RPT-031 | TC-RPT-037 | API-RPT-002 | — | SVC-API |
| AC-RPT-038 | REQ-RPT-032 | TC-RPT-038 | API-RPT-001 | — | SVC-API |
| AC-RPT-039 | REQ-RPT-033 | TC-RPT-039 | in-process | RULE-RPT-011 → RPT-409-DECISION-ALREADY-RECORDED | SVC-API |
| AC-RPT-040 | REQ-RPT-034 | TC-RPT-040 | in-process | RULE-RPT-012 → RPT-409-CHECK-NOT-COMPLETED | SVC-API |
| AC-RPT-041 | REQ-RPT-035 | TC-RPT-041 | in-process | RULE-RPT-013 → RPT-400-DECISION-INCOMPLETE | SVC-API |
| AC-RPT-042 | REQ-RPT-035 | TC-RPT-042 | in-process | RULE-RPT-013 → RPT-400-DECISION-INCOMPLETE | SVC-API |
| AC-RPT-043 | REQ-RPT-036 | TC-RPT-043 | API-RPT-001 | — | SVC-API |
| AC-RPT-044 | REQ-RPT-037 | TC-RPT-044 | in-process | — | SVC-API |
| AC-RPT-045 | REQ-RPT-038 | TC-RPT-045 | in-process | REQ-RPT-038 → RPT-404-CHECK-NOT-FOUND | SVC-API |
| AC-RPT-046 | REQ-RPT-039 | TC-RPT-046 | in-process | RULE-RPT-014 → RPT-422-APPROVAL-FLAG-ON-REJECTION | SVC-API |
| AC-RPT-047 | REQ-RPT-040 | TC-RPT-047 | API-RPT-003 | — | SVC-API |
| AC-RPT-048 | REQ-RPT-040 | TC-RPT-048 | API-RPT-003 | — | SVC-API |
| AC-RPT-049 | REQ-RPT-041 | TC-RPT-049 | API-RPT-003 | RULE-RPT-015 → RPT-400-SERVICE-CODE-MISSING | SVC-API |
| AC-RPT-050 | REQ-RPT-042 | TC-RPT-050 | in-process | — | SVC-API |
| AC-RPT-051 | REQ-RPT-043 | TC-RPT-051 | API-RPT-001 | — | SVC-API |
| AC-RPT-052 | REQ-RPT-044 | TC-RPT-052 | in-process | — | SVC-API |
| AC-RPT-053 | REQ-RPT-045 | TC-RPT-053 | in-process | — | SVC-API |
| AC-RPT-054 | REQ-RPT-046 | TC-RPT-054 | in-process | — | SVC-API |
| AC-RPT-055 | REQ-RPT-047 | TC-RPT-055 | in-process | — | PORTS |
| AC-RPT-056 | REQ-RPT-048 | TC-RPT-056 | API-RPT-001 | — | SVC-API |
| AC-RPT-057 | REQ-RPT-049 | TC-RPT-057 | in-process | — | PORTS |
| AC-RPT-058 | REQ-RPT-050 | TC-RPT-058 | API-RPT-002 | — | SVC-API |
| AC-RPT-059 | REQ-RPT-051 | TC-RPT-059 | in-process | RULE-RPT-010 → RPT-400-FAILURE-INCOMPLETE | PORTS |
| AC-RPT-060 | REQ-RPT-052 | TC-RPT-060 | in-process | — | SVC-API |
| AC-RPT-061 | REQ-RPT-053 | TC-RPT-061 | in-process | — (structural, ADR-RPT-018) | PORTS |
| AC-RPT-062 | REQ-RPT-009 | TC-RPT-062 | in-process | REQ-RPT-009 → RPT-500-REPORT-NOT-STORED | PORTS |
| AC-RPT-063 | REQ-RPT-018 | TC-RPT-063 | API-RPT-001 | RULE-RPT-003 (allowed transition AWAITING_DOCUMENTS → FAILED) | PORTS |
| AC-RPT-064 | REQ-RPT-054 | TC-RPT-064 | in-process | — (ADR-RPT-020) | SVC-API |

Package → TC: PORTS: 32 (TC-RPT-001 …) · SVC-API: 32 (TC-RPT-022 …) · CORE, DATA-DOM, ALIGN-BE: no_tests (profile) · CROSS-MOD: 0 edges, no unit
XM → TC: none (0 XM)

## COVERAGE

AC covered 64/64 ✓ · REQ covered 54/54 ✓ · API covered 3/3 (API-RPT-001, API-RPT-002, API-RPT-003) ✓ · XM edges covered 0/0 (none declared) · retention purge: TC-RPT-050 … TC-RPT-054, TC-RPT-060, TC-RPT-064 · report stored whole: TC-RPT-012, TC-RPT-062 · failCheck from both start states: TC-RPT-021, TC-RPT-063 · one-time final decision: TC-RPT-039 · COMPLIANT only when every finding SATISFIED: TC-RPT-017 · closed-list CHECK constraints: TC-RPT-004, TC-RPT-018, TC-RPT-020, TC-RPT-046
