<!-- source: PHASE:TEST-PLAN-FE / SUB:UI-FLOWS -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-RPT-027, AC-RPT-028, AC-RPT-030, AC-RPT-031, AC-RPT-033, AC-RPT-034, AC-RPT-035, API-RPT-001, API-RPT-002, REQ-RPT-023, REQ-RPT-025, REQ-RPT-026, REQ-RPT-028, REQ-RPT-029, RULE-RPT-009 -->
<!-- SUB:UI-FLOWS:START traces=AC-RPT-027,AC-RPT-028,AC-RPT-030,AC-RPT-031,AC-RPT-033,AC-RPT-034,AC-RPT-035,REQ-RPT-023,REQ-RPT-025,REQ-RPT-026,REQ-RPT-028,REQ-RPT-029 -->
### UI-FLOWS

<!-- TC:TC-RPT-065:START traces=AC-RPT-027,REQ-RPT-023,API-RPT-001 -->
### TC-RPT-065 — CheckReport model, RUNNING Check
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
<!-- TC:TC-RPT-065:END -->

<!-- TC:TC-RPT-066:START traces=AC-RPT-028,REQ-RPT-023,API-RPT-001 -->
### TC-RPT-066 — CheckReport model, COMPLETED Check
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
<!-- TC:TC-RPT-066:END -->

<!-- TC:TC-RPT-067:START traces=AC-RPT-031,REQ-RPT-026,API-RPT-001 -->
### TC-RPT-067 — CheckReport model, FAILED Check
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
<!-- TC:TC-RPT-067:END -->

<!-- TC:TC-RPT-068:START traces=AC-RPT-030,REQ-RPT-025,API-RPT-001 -->
### TC-RPT-068 — CHECK-QUERY routes not found
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
<!-- TC:TC-RPT-068:END -->

<!-- TC:TC-RPT-069:START traces=AC-RPT-033,REQ-RPT-028,API-RPT-002 -->
### TC-RPT-069 — CHECKS-OF-REQUEST-QUERY order and key
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
<!-- TC:TC-RPT-069:END -->

<!-- TC:TC-RPT-070:START traces=AC-RPT-034,REQ-RPT-028,API-RPT-002 -->
### TC-RPT-070 — CHECKS-OF-REQUEST-QUERY keeps the total beside the cut
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
<!-- TC:TC-RPT-070:END -->

<!-- TC:TC-RPT-071:START traces=AC-RPT-035,REQ-RPT-029,API-RPT-002,RULE-RPT-009 -->
### TC-RPT-071 — list refused without service code
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
<!-- TC:TC-RPT-071:END -->
<!-- SUB:UI-FLOWS:END -->
