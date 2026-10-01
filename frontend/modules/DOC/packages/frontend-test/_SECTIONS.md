<!-- source: content outside every PHASE block (leading / between / trailing sections) -->
# FRONTEND TEST PLAN — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module : DOC   Version : v1   Profile : aias   Track : frontend   Framework : agnostic (profile.stack.testing.frontend)
Sources: _state/current-srs.md (P1 v1 — AC 62) · _state/current-registry-srs.md · P3_2/frontend-execution-plan-doc.md (packages F1, F2, F3, F4; 0 SCR, 0 UXD) · P3_2/registry-exec-fe-doc.md · _state/current-api-spec.yaml (api-spec-doc.yaml — API-DOC-001)
TCs    : 5 module · 0 integration · range TC-DOC-078 … TC-DOC-082 (the sequence continues from the backend plan's TC-DOC-077)
Open ADRs : 0 BLOCKED — applied: ADR-DOC-003, ADR-DOC-011, ADR-DOC-012, ADR-DOC-013, ADR-DOC-014
══════════════════════════════════════════════════════════════════

Framework note: framework-agnostic — every block below is the whole contract; the consumer repository chooses its tool. DOC has no screen (ADR-DOC-003, ADR-DOC-013): its frontend packages are the module-level model (F1), the shared uploaded-documents query and facade (F2), the validators of that read (F3) and the module entry INT's screens import (F4). Each case runs against the mock server of `api-spec-doc.yaml` and drives DOC-FRONTEND-ENTRY; none navigates a screen, since DOC owns none (the screens, routes and their UI flows are INT's and are tested in INT's plan). The cases are derived from the ACs that API-DOC-001 makes observable (ADR-DOC-014). No case asserts a refusal by its text. Grouping: TEST-PLAN-FE holds 5 TCs (≤ 8) → no SUB (UI-FLOWS / INT-FLOW apply above the threshold). INT-UXD is absent: the plan cites no UXD.



## TC TRACEABILITY INDEX

| TC | AC | REQ | SCR | API | RULE / code | UXD | Package |
|---|---|---|---|---|---|---|---|
| TC-DOC-078 | AC-DOC-019 | REQ-DOC-017 | — (no DOC screen) | API-DOC-001 | — | — | F1 |
| TC-DOC-079 | AC-DOC-020 | REQ-DOC-018 | — (no DOC screen) | API-DOC-001 | RULE-DOC-008 | — | F2 |
| TC-DOC-080 | AC-DOC-025 | REQ-DOC-023 | — (no DOC screen) | API-DOC-001 | RULE-DOC-004 | — | F2 |
| TC-DOC-081 | AC-DOC-046 | REQ-DOC-043 | — (no DOC screen) | API-DOC-001 | RULE-DOC-005 | — | F3 |
| TC-DOC-082 | AC-DOC-057 | REQ-DOC-054 | — (no DOC screen) | API-DOC-001 | — | — | F4 |

Package → TC: F1 → TC-DOC-078 · F2 → TC-DOC-079, TC-DOC-080 · F3 → TC-DOC-081 · F4 → TC-DOC-082 · ALIGN-FE → — (`no_tests` in the profile)

## COVERAGE

AC covered by this track 5/62 — the five ACs API-DOC-001 makes observable; all 62 are covered across both plans (backend 62/62) ✓ · REQ covered 5 · SCR covered 0/0 (DOC has no screen) · UXD covered 0/0 (none cited) — INT-UXD absent · packages with acceptance 4/4 (F1–F4) ✓
