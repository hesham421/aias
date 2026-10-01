<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND TEST PLAN — Host Integration (INT) — employee frontend
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias   Stage : P4 (test-gen)   Framework : agnostic (profile.stack.testing.frontend)
Sources: _state/current-srs.md (AC-INT-001 … AC-INT-079) · current-registry-srs.md · current-frontend-execution-plan.md (SCR-INT-001 … SCR-INT-005, F1–F4 SUBs, UXD-INT-001 … UXD-INT-008, §3.0 message binding) · current-api-spec.yaml (api-spec-int.yaml) (served by the mock server) — all v1
Open ADRs: ADR-INT-017 (Arabic messages PENDING) · ADR-INT-018 / ADR-INT-021 (frontend binding) · ADR-INT-019 / ADR-INT-022 (derivation choices) · ADR-INT-020 (INT reads) · ADR-INT-023 (DOC listing gap closed) · ADR-INT-025 (DOC upload refusals) · ADR-INT-026 (background-read failure, same-type uploads) — 0 BLOCKED
══════════════════════════════════════════════════════════════════

Framework note: the plan is framework-neutral — each TC block below is the whole contract; the consumer repository
chooses its tool and turns each TC into a test. Screens and routes are the frontend plan's (F4); every refusal
text a case asserts is bound in the frontend plan's §3.0 message binding (`text:`). Every case runs against the
mock server serving api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022). The launch query
string is `?serviceCode=…&requestNumber=…&employeeId=…` (ADR-INT-018 (4)). 24 frontend ACs plus 6 frontend cases of
backend ACs (3 on SCR-INT-005 — ADR-INT-019 (2); 3 on SCR-INT-003 — ADR-INT-025, ADR-INT-026); 16 integration cases for 8 UXD.





## TC TRACEABILITY INDEX

| TC | AC / UXD | REQ | SCR | RULE / code | Package |
|---|---|---|---|---|---|
| TC-INT-045 | AC-INT-020 | REQ-INT-016 | SCR-INT-003 | — | F3-SCR-INT-003 |
| TC-INT-046 | AC-INT-021 | REQ-INT-017 | SCR-INT-003 | — | F4-SCR-INT-003 |
| TC-INT-047 | AC-INT-024 | REQ-INT-020 | SCR-INT-004 | — | F4-SCR-INT-004 |
| TC-INT-048 | AC-INT-027 | REQ-INT-023 | SCR-INT-005 | — | F3-SCR-INT-005 |
| TC-INT-049 | AC-INT-045 | REQ-INT-040 | SCR-INT-001 | — | F4-SCR-INT-001 |
| TC-INT-050 | AC-INT-046 | REQ-INT-041 | SCR-INT-001 | RULE-INT-004 → — (frontend check, no catalog code) | F3-SCR-INT-001 |
| TC-INT-051 | AC-INT-047 | REQ-INT-042 | SCR-INT-001 | — | F4-SCR-INT-001 |
| TC-INT-052 | AC-INT-048 | REQ-INT-043 | SCR-INT-001 | — | F4-SCR-INT-001 |
| TC-INT-053 | AC-INT-050 | REQ-INT-045 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-054 | AC-INT-051 | REQ-INT-046 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-055 | AC-INT-052 | REQ-INT-047 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-056 | AC-INT-053 | REQ-INT-048 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-057 | AC-INT-054 | REQ-INT-049 | SCR-INT-002 | — | F3-SCR-INT-002 |
| TC-INT-058 | AC-INT-055 | REQ-INT-049 | SCR-INT-002 | — | F3-SCR-INT-002 |
| TC-INT-059 | AC-INT-056 | REQ-INT-050 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-060 | AC-INT-057 | REQ-INT-051 | SCR-INT-002 | — | F3-SCR-INT-002 |
| TC-INT-061 | AC-INT-058 | REQ-INT-052 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-062 | AC-INT-059 | REQ-INT-053 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-063 | AC-INT-060 | REQ-INT-054 | SCR-INT-002 | — | F4-SCR-INT-002 |
| TC-INT-064 | AC-INT-063 | REQ-INT-057 | SCR-INT-001 | — | F2-SCR-INT-001 |
| TC-INT-065 | AC-INT-041 | REQ-INT-036 | SCR-INT-005 | INT-502-APPROVAL-API-FAILED | F2-SCR-INT-005 |
| TC-INT-066 | AC-INT-049 | REQ-INT-044 | SCR-INT-001 | — | F2-SCR-INT-001 |
| TC-INT-067 | AC-INT-061 | REQ-INT-055 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-068 | AC-INT-062 | REQ-INT-056 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-069 | AC-INT-026 | REQ-INT-022 | SCR-INT-005 | — | F1-SCR-INT-005 |
| TC-INT-070 | AC-INT-043 | REQ-INT-038 | SCR-INT-005 | — | F4-SCR-INT-005 |
| TC-INT-071 | UXD-INT-001 (AC-INT-047) | REQ-INT-042 | SCR-INT-001 | — | F1-SCR-INT-001 |
| TC-INT-072 | UXD-INT-001 (AC-INT-045) | REQ-INT-040 | SCR-INT-001 | — | F2-SCR-INT-001 |
| TC-INT-073 | UXD-INT-002 (AC-INT-050) | REQ-INT-045 | SCR-INT-002 | — | F1-SCR-INT-002 |
| TC-INT-074 | UXD-INT-002 (AC-INT-050) | REQ-INT-045 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-075 | UXD-INT-003 (AC-INT-051) | REQ-INT-046 | SCR-INT-002 | — | F1-SCR-INT-002 |
| TC-INT-076 | UXD-INT-003 (AC-INT-051) | REQ-INT-046 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-077 | UXD-INT-004 (AC-INT-052) | REQ-INT-047 | SCR-INT-002 | — | F1-SCR-INT-002 |
| TC-INT-078 | UXD-INT-004 (AC-INT-053) | REQ-INT-048 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-079 | UXD-INT-005 (AC-INT-020) | REQ-INT-016 | SCR-INT-003 | — | F3-SCR-INT-003 |
| TC-INT-080 | UXD-INT-005 (AC-INT-020) | REQ-INT-016 | SCR-INT-003 | — | F2-SCR-INT-003 |
| TC-INT-081 | UXD-INT-008 (AC-INT-024) | REQ-INT-020 | SCR-INT-004 | — | F3-SCR-INT-004 |
| TC-INT-082 | UXD-INT-008 (AC-INT-024) | REQ-INT-020 | SCR-INT-004 | — | F2-SCR-INT-004 |
| TC-INT-083 | UXD-INT-006 (AC-INT-021) | REQ-INT-017 | SCR-INT-003 | — | F1-SCR-INT-003 |
| TC-INT-084 | UXD-INT-006 (AC-INT-021) | REQ-INT-017 | SCR-INT-003 | — | F2-SCR-INT-003 |
| TC-INT-085 | UXD-INT-007 (AC-INT-024) | REQ-INT-020 | SCR-INT-004 | — | F1-SCR-INT-004 |
| TC-INT-086 | UXD-INT-007 (AC-INT-024) | REQ-INT-020 | SCR-INT-004 | — | F2-SCR-INT-004 |
| TC-INT-101 | AC-INT-078 | REQ-INT-066 | SCR-INT-002 | — | F2-SCR-INT-002 |
| TC-INT-102 | AC-INT-075, AC-INT-076 | REQ-INT-006 | SCR-INT-003 | PASS-THROUGH → DOC-409-CHECK-ENDED · DOC-422-UPLOAD-LIMIT-REACHED | F3-SCR-INT-003 |
| TC-INT-103 | AC-INT-077 | REQ-INT-065 | SCR-INT-003 | — | F2-SCR-INT-003 |

Package → TC: F1-SCR-INT-001: TC-INT-071 · F1-SCR-INT-002: TC-INT-073, TC-INT-075, TC-INT-077 · F1-SCR-INT-003: TC-INT-083 · F1-SCR-INT-004: TC-INT-085 · F1-SCR-INT-005: TC-INT-069 · F2-SCR-INT-001: TC-INT-064, TC-INT-066, TC-INT-072 · F2-SCR-INT-002: TC-INT-067, TC-INT-068, TC-INT-074, TC-INT-076, TC-INT-078, TC-INT-101 · F2-SCR-INT-003: TC-INT-080, TC-INT-084, TC-INT-103 · F2-SCR-INT-004: TC-INT-082, TC-INT-086 · F2-SCR-INT-005: TC-INT-065 · F3-SCR-INT-001: TC-INT-050 · F3-SCR-INT-002: TC-INT-057, TC-INT-058, TC-INT-060 · F3-SCR-INT-003: TC-INT-045, TC-INT-079, TC-INT-102 · F3-SCR-INT-004: TC-INT-081 · F3-SCR-INT-005: TC-INT-048 · F4-SCR-INT-001: TC-INT-049, TC-INT-051, TC-INT-052 · F4-SCR-INT-002: TC-INT-053, TC-INT-054, TC-INT-055, TC-INT-056, TC-INT-059, TC-INT-061, TC-INT-062, TC-INT-063 · F4-SCR-INT-003: TC-INT-046 · F4-SCR-INT-004: TC-INT-047 · F4-SCR-INT-005: TC-INT-070

## COVERAGE

AC covered (frontend track) 30 — AC-INT-020, AC-INT-021, AC-INT-024, AC-INT-026, AC-INT-027, AC-INT-041, AC-INT-043, AC-INT-045, AC-INT-046, AC-INT-047, AC-INT-048, AC-INT-049, AC-INT-050, AC-INT-051, AC-INT-052, AC-INT-053, AC-INT-054, AC-INT-055, AC-INT-056, AC-INT-057, AC-INT-058, AC-INT-059, AC-INT-060, AC-INT-061, AC-INT-062, AC-INT-063, AC-INT-075, AC-INT-076, AC-INT-077, AC-INT-078; of these AC-INT-026, AC-INT-041, AC-INT-043, AC-INT-075, AC-INT-076, AC-INT-077 are also covered in `backend-test-plan-int.md` (ADR-INT-019 (2), ADR-INT-025, ADR-INT-026) · the remaining 49 ACs (incl. AC-INT-067 … AC-INT-074 of INT's reads and AC-INT-079) are covered there → module AC coverage 79/79, no gap ✗.
SCR covered 5/5 (SCR-INT-001, SCR-INT-002, SCR-INT-003, SCR-INT-004, SCR-INT-005) · UXD covered 8/8 (UXD-INT-001 … UXD-INT-008, 2 cases each) · frontend plan units with acceptance 20/20 (F1–F4 × 5 screens; ALIGN-FE is `no_tests`).
TC count 45 for 24 frontend ACs + 6 twins + 8 UXD (guard ~2×: 29 AC-derived cases for 24 ACs; TC-INT-101 … TC-INT-103 — ADR-INT-025, ADR-INT-026).
