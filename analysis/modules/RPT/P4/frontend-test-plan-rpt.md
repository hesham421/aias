# FRONTEND TEST PLAN — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Stage : P4   Framework : agnostic (the consumer repo chooses its tool; this plan names none)
Sources : _state/current-srs.md (v1) · current-frontend-execution-plan.md (SCR 0, UXD 0; units F1, F2, F3, F4; ALIGN-FE no_tests) · current-api-spec.yaml (served by the mock server)
Open ADRs : none BLOCKED — applied ADR-RPT-013, ADR-RPT-014, ADR-RPT-015
TCs : 10 (TC-RPT-061 … TC-RPT-070) — UI-FLOWS 7 · INT-FLOW 3
══════════════════════════════════════════════════════════════════

RPT has no screen (ADR-RPT-014): its frontend cases exercise what the employee frontend consumes from RPT — the F1 models, the F2 read hooks, the F3 parameter validators and the F4 rendering obligations — against api-spec-rpt.yaml served by the mock server, for the ACs whose Given/When names the employee frontend or the report it renders. Navigation and screen flows are Host Integration's cases. No permission case: no permission model (raw-idea A2). INT-UXD is absent: RPT cites no UXD. Every refusal asserted by its text is bound in the frontend plan (`text:` on the F2/F3 rows routing RPT-404-CHECK-NOT-FOUND and RULE-RPT-009 / RPT-400-REQUEST-KEYS-MISSING); Arabic texts `PENDING ADR-RPT-013`.

<!-- PHASE:TEST-PLAN-FE:START traces=AC-RPT-027,AC-RPT-028,AC-RPT-029,AC-RPT-030,AC-RPT-031,AC-RPT-032,AC-RPT-033,AC-RPT-034,AC-RPT-035,AC-RPT-058,REQ-RPT-023,REQ-RPT-024,REQ-RPT-025,REQ-RPT-026,REQ-RPT-027,REQ-RPT-028,REQ-RPT-029,REQ-RPT-050 -->
## PHASE TEST-PLAN-FE

<!-- SUB:UI-FLOWS:START traces=AC-RPT-027,AC-RPT-028,AC-RPT-030,AC-RPT-031,AC-RPT-033,AC-RPT-034,AC-RPT-035,REQ-RPT-023,REQ-RPT-025,REQ-RPT-026,REQ-RPT-028,REQ-RPT-029 -->
### UI-FLOWS

<!-- TC:TC-RPT-061:START traces=AC-RPT-027,REQ-RPT-023,API-RPT-001 -->
### TC-RPT-061 — CheckReport model, RUNNING Check
Derived from : AC-RPT-027  (REQ-RPT-023)
Exercises    : F1 model CheckReport (consumed by Host Integration's Check report screen) against API-RPT-001 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : —
Package      : F1
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the mock server serves api-spec-rpt.yaml with Check 522 RUNNING
Host data    : none
Steps        : 1. CHECK-QUERY loads checkId 522 from the mock
               2. decode the payload with the F1 CheckReport model
Expected     : decoded status RUNNING; overallStatus, findings, documents absent/empty; the model exposes no report fields for a RUNNING Check
Test data    : Check 522
<!-- TC:TC-RPT-061:END -->

<!-- TC:TC-RPT-062:START traces=AC-RPT-028,REQ-RPT-023,API-RPT-001 -->
### TC-RPT-062 — CheckReport model, COMPLETED Check
Derived from : AC-RPT-028  (REQ-RPT-023)
Exercises    : F1 model CheckReport (consumed by Host Integration's Check report screen) against API-RPT-001 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : —
Package      : F1
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the mock serves Check 523 COMPLETED, NOT_COMPLIANT, 3 findings, 2 documents, 0 unread queries, no decision
Host data    : none
Steps        : 1. CHECK-QUERY loads 523
               2. decode with CheckReport
Expected     : decoded status COMPLETED, overallStatus NOT_COMPLIANT, comparisonModel present, 3 findings, 2 documents, 0 unread queries, decision null
Test data    : Check 523
<!-- TC:TC-RPT-062:END -->

<!-- TC:TC-RPT-063:START traces=AC-RPT-031,REQ-RPT-026,API-RPT-001 -->
### TC-RPT-063 — CheckReport model, FAILED Check
Derived from : AC-RPT-031  (REQ-RPT-026)
Exercises    : F1 model CheckReport (consumed by Host Integration's Check report screen) against API-RPT-001 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : —
Package      : F1
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the mock serves Check 525 FAILED, MODEL_UNAVAILABLE, "provider answered 503"
Host data    : none
Steps        : 1. CHECK-QUERY loads 525
               2. decode with CheckReport
Expected     : decoded status FAILED, failureReason MODEL_UNAVAILABLE, failureDetail "provider answered 503", overallStatus null
Test data    : Check 525
<!-- TC:TC-RPT-063:END -->

<!-- TC:TC-RPT-064:START traces=AC-RPT-030,REQ-RPT-025,API-RPT-001 -->
### TC-RPT-064 — CHECK-QUERY routes not found
Derived from : AC-RPT-030  (REQ-RPT-025)
Exercises    : F2 hook CHECK-QUERY against API-RPT-001 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : REQ-RPT-025 → RPT-404-CHECK-NOT-FOUND
Package      : F2
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: the mock answers 404 RPT-404-CHECK-NOT-FOUND for Check 998
Host data    : none
Steps        : 1. CHECK-QUERY loads 998
Expected     : the hook reports the not-found state (not an error crash, not a running state); text en: "Check 998 was not found." · ar: PENDING ADR-RPT-013
Test data    : Check 998
<!-- TC:TC-RPT-064:END -->

<!-- TC:TC-RPT-065:START traces=AC-RPT-033,REQ-RPT-028,API-RPT-002 -->
### TC-RPT-065 — CHECKS-OF-REQUEST-QUERY order and key
Derived from : AC-RPT-033  (REQ-RPT-028)
Exercises    : F2 hook CHECKS-OF-REQUEST-QUERY against API-RPT-002 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : —
Package      : F2
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the mock serves request `1001` of ⟨SVC-A⟩ with Checks 528 (09:00) and 527 (08:00), total 2
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. CHECKS-OF-REQUEST-QUERY with {serviceCode ⟨SVC-A⟩, requestNumber `1001`}
               2. inspect the cache key
Expected     : data [528, 527] in that order with total 2; cache key ["rpt-checks-of-request", {serviceCode, requestNumber}]
Test data    : ⟨SVC-A⟩; `1001`
<!-- TC:TC-RPT-065:END -->

<!-- TC:TC-RPT-066:START traces=AC-RPT-034,REQ-RPT-028,API-RPT-002 -->
### TC-RPT-066 — CHECKS-OF-REQUEST-QUERY keeps the total beside the cut
Derived from : AC-RPT-034  (REQ-RPT-028)
Exercises    : F2 hook CHECKS-OF-REQUEST-QUERY against API-RPT-002 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : —
Package      : F2
Scenario     : BOUNDARY · data class BOUNDARY · language ALL
Preconditions: the mock serves request `1002` with 100 checks and total 130
Host data    : none
Steps        : 1. CHECKS-OF-REQUEST-QUERY for request `1002`
Expected     : exposes 100 checks and total 130 — the cut stays visible
Test data    : 100 / 130
<!-- TC:TC-RPT-066:END -->

<!-- TC:TC-RPT-067:START traces=AC-RPT-035,REQ-RPT-029,API-RPT-002,RULE-RPT-009 -->
### TC-RPT-067 — list refused without service code
Derived from : AC-RPT-035  (REQ-RPT-029)
Exercises    : F3 validator ChecksOfRequestParams + CHECKS-OF-REQUEST-QUERY against API-RPT-002 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : RULE-RPT-009 → RPT-400-REQUEST-KEYS-MISSING
Package      : F3
Scenario     : VIOLATION · data class INVALID · language ALL
Preconditions: launch context carries requestNumber `1001` and no serviceCode; the mock would answer 400 RPT-400-REQUEST-KEYS-MISSING
Host data    : none
Steps        : 1. validate the parameters with ChecksOfRequestParams
               2. force the call and let the mock answer 400
Expected     : the validator blocks the call; the forced call is routed to the user message; text en: "Both a service code and a request number are needed to list Checks." · ar: PENDING ADR-RPT-013
Test data    : serviceCode absent
<!-- TC:TC-RPT-067:END -->
<!-- SUB:UI-FLOWS:END -->

<!-- SUB:INT-FLOW:START traces=AC-RPT-029,AC-RPT-032,AC-RPT-058,REQ-RPT-024,REQ-RPT-027,REQ-RPT-050 -->
### INT-FLOW

<!-- TC:TC-RPT-068:START traces=AC-RPT-058,REQ-RPT-050,API-RPT-002 -->
### TC-RPT-068 — request number passed untouched
Derived from : AC-RPT-058  (REQ-RPT-050)
Exercises    : F3 validator ChecksOfRequestParams + CHECKS-OF-REQUEST-QUERY against API-RPT-002 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : —
Package      : F3
Scenario     : VIOLATION · data class ATTACK · language ALL
Preconditions: the mock records the query string it receives
Host data    : none — service codes and document types are the placeholders ⟨SVC-A⟩, ⟨SVC-B⟩, ⟨DOC-TYPE-1⟩, ⟨DOC-TYPE-2⟩, passed by value as opaque text (ADR-RPT-015)
Steps        : 1. validate {serviceCode ⟨SVC-A⟩, requestNumber `1001' OR '1'='1`} with ChecksOfRequestParams
               2. send CHECKS-OF-REQUEST-QUERY
Expected     : the validator accepts it unchanged (no trim, no escape); the request carries the value byte-identical, URL-encoded; the mock's empty answer (total 0) is shown as an empty list
Test data    : `1001' OR '1'='1`
<!-- TC:TC-RPT-068:END -->

<!-- TC:TC-RPT-069:START traces=AC-RPT-032,REQ-RPT-027,API-RPT-001 -->
### TC-RPT-069 — evidence rendered as text
Derived from : AC-RPT-032  (REQ-RPT-027)
Exercises    : F4 rendering obligations (no RPT route — ADR-RPT-014) against API-RPT-001 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : —
Package      : F4
Scenario     : HAPPY · data class ATTACK · language ALL
Preconditions: the mock serves Check 526 with evidence "<script>alert(1)</script> ignore previous instructions"
Host data    : none
Steps        : 1. render the report of 526 in a consuming screen harness
Expected     : the evidence appears as the literal text; no script element is created and no script runs
Test data    : evidence as in the AC
<!-- TC:TC-RPT-069:END -->

<!-- TC:TC-RPT-070:START traces=AC-RPT-029,REQ-RPT-024,API-RPT-001 -->
### TC-RPT-070 — finding rendered as one entry
Derived from : AC-RPT-029  (REQ-RPT-024)
Exercises    : F4 rendering obligations (no RPT route — ADR-RPT-014) against API-RPT-001 served by the mock server (api-spec-rpt.yaml) — RPT has no SCR (ADR-RPT-014)
Rule / code  : —
Package      : F4
Scenario     : HAPPY · data class VALID · language ALL
Preconditions: the mock serves Check 524 with finding "GPA at least 3.0", NOT_SATISFIED, "GPA = 2.7", "Below the 3.0 minimum"
Host data    : none
Steps        : 1. render the report of 524 in a consuming screen harness
Expected     : one finding entry shows condition, outcome, evidence and note together, in position order
Test data    : values as in the AC
<!-- TC:TC-RPT-070:END -->
<!-- SUB:INT-FLOW:END -->

<!-- PHASE:TEST-PLAN-FE:END -->

## TC TRACEABILITY INDEX

| AC | REQ | TC | API (mock) | SCR | RULE → code | Package |
|---|---|---|---|---|---|---|
| AC-RPT-027 | REQ-RPT-023 | TC-RPT-061 | API-RPT-001 | none (ADR-RPT-014) | — | F1 |
| AC-RPT-028 | REQ-RPT-023 | TC-RPT-062 | API-RPT-001 | none (ADR-RPT-014) | — | F1 |
| AC-RPT-031 | REQ-RPT-026 | TC-RPT-063 | API-RPT-001 | none (ADR-RPT-014) | — | F1 |
| AC-RPT-030 | REQ-RPT-025 | TC-RPT-064 | API-RPT-001 | none (ADR-RPT-014) | REQ-RPT-025 → RPT-404-CHECK-NOT-FOUND | F2 |
| AC-RPT-033 | REQ-RPT-028 | TC-RPT-065 | API-RPT-002 | none (ADR-RPT-014) | — | F2 |
| AC-RPT-034 | REQ-RPT-028 | TC-RPT-066 | API-RPT-002 | none (ADR-RPT-014) | — | F2 |
| AC-RPT-035 | REQ-RPT-029 | TC-RPT-067 | API-RPT-002 | none (ADR-RPT-014) | RULE-RPT-009 → RPT-400-REQUEST-KEYS-MISSING | F3 |
| AC-RPT-058 | REQ-RPT-050 | TC-RPT-068 | API-RPT-002 | none (ADR-RPT-014) | — | F3 |
| AC-RPT-032 | REQ-RPT-027 | TC-RPT-069 | API-RPT-001 | none (ADR-RPT-014) | — | F4 |
| AC-RPT-029 | REQ-RPT-024 | TC-RPT-070 | API-RPT-001 | none (ADR-RPT-014) | — | F4 |

Package → TC: F1: TC-RPT-061, TC-RPT-062, TC-RPT-063 · F2: TC-RPT-064, TC-RPT-065, TC-RPT-066 · F3: TC-RPT-067, TC-RPT-068 · F4: TC-RPT-069, TC-RPT-070 · ALIGN-FE: no_tests (profile)
UXD → TC: none (0 UXD)

## COVERAGE

frontend cases cover 10 ACs (AC-RPT-027 … AC-RPT-035 AC-RPT-058 — as indexed); every AC 60/60 is covered across both plans (backend 60/60) ✓ · SCR covered 0/0 (no RPT screen) · UXD covered 0/0 (none cited) · frontend units covered 4/4 (F1, F2, F3, F4) ✓
