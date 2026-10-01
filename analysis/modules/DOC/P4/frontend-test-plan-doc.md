# FRONTEND TEST PLAN — Document Access (DOC)
══════════════════════════════════════════════════════════════════
Module : DOC   Version : v1   Profile : aias   Track : frontend   Framework : agnostic (profile.stack.testing.frontend)
Sources: _state/current-srs.md (P1 v1 — AC 62) · _state/current-registry-srs.md · P3_2/frontend-execution-plan-doc.md (packages F1, F2, F3, F4; 0 SCR, 0 UXD) · P3_2/registry-exec-fe-doc.md · _state/current-api-spec.yaml (api-spec-doc.yaml — API-DOC-001)
TCs    : 5 module · 0 integration · range TC-DOC-070 … TC-DOC-074 (the sequence continues from the backend plan's TC-DOC-069)
Open ADRs : 0 BLOCKED — applied: ADR-DOC-003, ADR-DOC-011, ADR-DOC-012, ADR-DOC-013, ADR-DOC-014
══════════════════════════════════════════════════════════════════

Framework note: framework-agnostic — every block below is the whole contract; the consumer repository chooses its tool. DOC has no screen (ADR-DOC-003, ADR-DOC-013): its frontend packages are the module-level model (F1), the shared uploaded-documents query and facade (F2), the validators of that read (F3) and the module entry INT's screens import (F4). Each case runs against the mock server of `api-spec-doc.yaml` and drives DOC-FRONTEND-ENTRY; none navigates a screen, since DOC owns none (the screens, routes and their UI flows are INT's and are tested in INT's plan). The cases are derived from the ACs that API-DOC-001 makes observable (ADR-DOC-014). No case asserts a refusal by its text. Grouping: TEST-PLAN-FE holds 5 TCs (≤ 8) → no SUB (UI-FLOWS / INT-FLOW apply above the threshold). INT-UXD is absent: the plan cites no UXD.

<!-- PHASE:TEST-PLAN-FE:START traces=AC-DOC-019,AC-DOC-020,AC-DOC-025,AC-DOC-046,AC-DOC-057,REQ-DOC-017,REQ-DOC-018,REQ-DOC-023,REQ-DOC-043,REQ-DOC-054 -->
## PHASE TEST-PLAN-FE

<!-- TC:TC-DOC-070:START traces=AC-DOC-019,REQ-DOC-017,API-DOC-001 -->
### TC-DOC-070 — Uploaded Document summary model binds the API-DOC-001 response
Derived from : AC-DOC-019  (REQ-DOC-017)
Exercises    : DOC-FRONTEND-ENTRY → UPLOADED-DOCUMENTS-FACADE reading API-DOC-001 GET /api/v1/uploaded-documents?checkId=501 from the mock of api-spec-doc.yaml (DOC has no SCR — ADR-DOC-013)
Rule / code  : —
Package      : F1
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Check 501 runs `manual-service` version 1 (fetch mode `manual`, required document types TRANSCRIPT and ID_CARD); the mock answers checkId = 501 with 1 item {uploadedDocumentId `<id>`, documentType TRANSCRIPT, fileName `transcript.pdf`, fileSize 81920, oversized false, createdAt `<createdAt>`} — the state after AC-DOC-019's handover.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Read the uploaded documents of Check 501 through the facade exported by DOC-FRONTEND-ENTRY. 2. Inspect the returned model.
Expected     : 1 summary with documentType TRANSCRIPT, fileName `transcript.pdf`, fileSize 81920 and oversized false; the model exposes no content member.
Test data    : Check 501, file `transcript.pdf`, 81920 bytes (AC-DOC-019); `<id>`, `<createdAt>` are placeholders.
<!-- TC:TC-DOC-070:END -->

<!-- TC:TC-DOC-071:START traces=AC-DOC-020,REQ-DOC-018,API-DOC-001 -->
### TC-DOC-071 — Uploaded-documents query keeps one cache entry per Check
Derived from : AC-DOC-020  (REQ-DOC-018)
Exercises    : DOC-FRONTEND-ENTRY → UPLOADED-DOCUMENTS-QUERY on API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId} from the mock of api-spec-doc.yaml (DOC has no SCR — ADR-DOC-013)
Rule / code  : RULE-DOC-008 → —
Package      : F2
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: Checks 501 and 502 run `manual-service` version 1; the mock answers checkId = 501 with 1 item of documentType TRANSCRIPT and checkId = 502 with 1 item of documentType ID_CARD.
Host data    : DOCUMENT_TYPE TRANSCRIPT, ID_CARD — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Read the uploaded documents of Check 501 through the facade. 2. Read the uploaded documents of Check 502 through the facade. 3. Inspect the query cache.
Expected     : Check 501's list carries 1 item, documentType TRANSCRIPT; Check 502's list carries 1 item, documentType ID_CARD; 2 cache entries exist, keyed [uploaded-documents, {checkId: 501}] and [uploaded-documents, {checkId: 502}]; Check 501's list never contains Check 502's item.
Test data    : Checks 501 and 502 (AC-DOC-020).
<!-- TC:TC-DOC-071:END -->

<!-- TC:TC-DOC-072:START traces=AC-DOC-025,REQ-DOC-023,API-DOC-001 -->
### TC-DOC-072 — Facade lists every upload of a Check in upload order, the earlier one unchanged
Derived from : AC-DOC-025  (REQ-DOC-023)
Exercises    : DOC-FRONTEND-ENTRY → UPLOADED-DOCUMENTS-FACADE reading API-DOC-001 GET /api/v1/uploaded-documents?checkId=501 from the mock of api-spec-doc.yaml (DOC has no SCR — ADR-DOC-013)
Rule / code  : RULE-DOC-004 → —
Package      : F2
Scenario     : STATE · data class VALID · language ALL
Preconditions: Check 501 runs `manual-service` version 1; the mock answers checkId = 501 with 2 items of documentType TRANSCRIPT in upload order — `transcript.pdf` (createdAt `<t1>`) then `transcript-v2.pdf` (createdAt `<t2>`, later than `<t1>`).
Host data    : DOCUMENT_TYPE TRANSCRIPT — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Read the uploaded documents of Check 501 through the facade. 2. Inspect the list order and the first item.
Expected     : 2 items in upload order; the first is `transcript.pdf` with its original fileSize; the facade offers no edit or replace operation.
Test data    : files `transcript.pdf`, `transcript-v2.pdf` (AC-DOC-025); `<t1>`, `<t2>` are placeholders.
<!-- TC:TC-DOC-072:END -->

<!-- TC:TC-DOC-073:START traces=AC-DOC-046,REQ-DOC-043,RULE-DOC-005,API-DOC-001 -->
### TC-DOC-073 — Response validator accepts an oversized summary that carries no content
Derived from : AC-DOC-046  (REQ-DOC-043)
Exercises    : UPLOADED-DOCUMENTS-VALIDATORS on the API-DOC-001 GET /api/v1/uploaded-documents?checkId=501 response from the mock of api-spec-doc.yaml (DOC has no SCR — ADR-DOC-013)
Rule / code  : RULE-DOC-005 → —
Package      : F3
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: The maximum file size is 10 MB; Check 501 runs `manual-service` version 1; the mock answers checkId = 501 with 1 item of documentType TRANSCRIPT, fileSize 15728640 (15 MB), oversized true and no content member — the state after AC-DOC-046's handover.
Host data    : DOCUMENT_TYPE TRANSCRIPT — present (required document types of the version) · SERVICE_CODE manual-service — present — created by the REG service package load at start-up (folder `services/<serviceCode>/`, REQ-REG-005; REG publishes no HTTP add-value call — ADR-DOC-014), confirmed by GET /api/v1/services/{serviceCode} (API-REG-002) before the case runs
Steps        : 1. Read the uploaded documents of Check 501 through the facade. 2. Inspect the validated list.
Expected     : The validator accepts the item; the list carries 1 item with oversized true and fileSize 15728640; no content member is present; no error state is raised.
Test data    : maximum file size 10 MB, upload 15 MB (AC-DOC-046).
<!-- TC:TC-DOC-073:END -->

<!-- TC:TC-DOC-074:START traces=AC-DOC-057,REQ-DOC-054,API-DOC-001 -->
### TC-DOC-074 — Module entry registers no route, and an ended Check's list is the empty state
Derived from : AC-DOC-057  (REQ-DOC-054)
Exercises    : DOC-FRONTEND-ENTRY (0 routes) and its facade reading API-DOC-001 GET /api/v1/uploaded-documents?checkId={checkId} from the mock of api-spec-doc.yaml (DOC has no SCR — ADR-DOC-013)
Rule / code  : —
Package      : F4
Scenario     : STATE · data class VALID · language ALL
Preconditions: Checks 501 and 502 run `manual-service` version 1; the mock first answers checkId = 501 with 2 items and checkId = 502 with 1 item, then — after the Check Engine reports Check 501 ended — answers checkId = 501 with an empty array and checkId = 502 with 1 item.
Host data    : none
Steps        : 1. Import DOC-FRONTEND-ENTRY and count the routes it registers. 2. Read the uploaded documents of Check 501 through its facade. 3. Switch the mock to the post-end state and read Check 501 again. 4. Read Check 502.
Expected     : 0 routes registered; step 2 lists 2 items; step 3 lists 0 items and the facade reports its empty state, not an error; step 4 lists 1 item.
Test data    : Checks 501 and 502 (AC-DOC-057).
<!-- TC:TC-DOC-074:END -->

<!-- PHASE:TEST-PLAN-FE:END -->

## TC TRACEABILITY INDEX

| TC | AC | REQ | SCR | API | RULE / code | UXD | Package |
|---|---|---|---|---|---|---|---|
| TC-DOC-070 | AC-DOC-019 | REQ-DOC-017 | — (no DOC screen) | API-DOC-001 | — | — | F1 |
| TC-DOC-071 | AC-DOC-020 | REQ-DOC-018 | — (no DOC screen) | API-DOC-001 | RULE-DOC-008 | — | F2 |
| TC-DOC-072 | AC-DOC-025 | REQ-DOC-023 | — (no DOC screen) | API-DOC-001 | RULE-DOC-004 | — | F2 |
| TC-DOC-073 | AC-DOC-046 | REQ-DOC-043 | — (no DOC screen) | API-DOC-001 | RULE-DOC-005 | — | F3 |
| TC-DOC-074 | AC-DOC-057 | REQ-DOC-054 | — (no DOC screen) | API-DOC-001 | — | — | F4 |

Package → TC: F1 → TC-DOC-070 · F2 → TC-DOC-071, TC-DOC-072 · F3 → TC-DOC-073 · F4 → TC-DOC-074 · ALIGN-FE → — (`no_tests` in the profile)

## COVERAGE

AC covered by this track 5/62 — the five ACs API-DOC-001 makes observable; all 62 are covered across both plans (backend 62/62) ✓ · REQ covered 5 · SCR covered 0/0 (DOC has no screen) · UXD covered 0/0 (none cited) — INT-UXD absent · packages with acceptance 4/4 (F1–F4) ✓
