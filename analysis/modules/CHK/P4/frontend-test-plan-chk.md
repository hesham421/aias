# FRONTEND TEST PLAN — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module : CHK   Version : v1   Profile : aias   Track : frontend   Plan : test
Sources: _state/current-srs.md (v1) · current-registry-srs.md (v1) · current-frontend-execution-plan.md (v1 — 0 SCR, 0 UXD; F1–F3 bound to API-CHK-001, F4 empty) · current-api-spec.yaml (v1)
TCs    : 6 — TC-CHK-101 … TC-CHK-106 (the sequence continues from the backend plan)
Open ADRs : 0 BLOCKED — ADR-CHK-019 (no CHK screen), ADR-CHK-020 (derivation)
══════════════════════════════════════════════════════════════════

Framework: `profile.stack.testing.frontend` is agnostic. CHK has no screen (ADR-CHK-019), so no TC navigates a CHK
route. Five TCs drive the F2 hook ACTIVE-CHECK-QUERY (with the F1 types and F3 validators) against the mock server
of api-spec-chk.yaml; one structural F4 TC proves CHK registers no route and sends no write call (ADR-CHK-021). The six TCs are under the TEST-PLAN-FE threshold (> 8), so there is no UI-FLOWS / INT-FLOW
SUB. The INT-UXD phase is absent because CHK cites no UXD. No refusal text is asserted: the plan routes
CHK-404-ACTIVE-CHECK-NOT-FOUND to the ended state with no text, and the other codes to the generic error.


<!-- PHASE:TEST-PLAN-FE:START traces=AC-CHK-079,AC-CHK-080,AC-CHK-081,AC-CHK-083,AC-CHK-085,REQ-CHK-076,REQ-CHK-077,REQ-CHK-078,REQ-CHK-080,REQ-CHK-082 -->
## PHASE TEST-PLAN-FE

<!-- TC:TC-CHK-101:START traces=AC-CHK-079,REQ-CHK-076,API-CHK-001 -->
### TC-CHK-101 — F1 — the Active Check of a waiting Check is typed as ActiveCheckView
Derived from : AC-CHK-079  (REQ-CHK-076)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}, served by the mock of api-spec-chk.yaml, through the F2 hook ACTIVE-CHECK-QUERY — no CHK route (ADR-CHK-019)
Rule / code  : —
Package      : F1
Scenario     : HAPPY · data class VALID · language en
Preconditions: The mock serves api-spec-chk.yaml with an example for checkId 502: checkStatus AWAITING_DOCUMENTS, deadlineAt 11:00 (the upload window of 60 minutes from creation at 10:00).
Host data    : none
Steps        : 1. Read checkId 502 through ACTIVE-CHECK-QUERY.
Expected     : The hook returns state unfinished with a view of exactly checkId 502, checkStatus AWAITING_DOCUMENTS (a member of the two-value union) and deadlineAt 11:00 as received; the status label en is "Awaiting documents" (label ar PENDING ADR-CHK-018).
Test data    : checkId 502, 10:00, 11:00, 60 minutes (from the AC)
<!-- TC:TC-CHK-101:END -->

<!-- TC:TC-CHK-102:START traces=AC-CHK-080,REQ-CHK-077,API-CHK-001 -->
### TC-CHK-102 — F2 — after the uploads are confirmed the read shows RUNNING with the timeout deadline
Derived from : AC-CHK-080  (REQ-CHK-077)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}, served by the mock of api-spec-chk.yaml, through the F2 hook ACTIVE-CHECK-QUERY — no CHK route (ADR-CHK-019)
Rule / code  : —
Package      : F2
Scenario     : STATE · data class VALID · language en
Preconditions: The mock first answers checkId 502 with AWAITING_DOCUMENTS, then — after the confirmation at 10:20:00 with a Check timeout of 300 seconds — with RUNNING and deadlineAt 10:25:00; a consumer has cached the key ["active-check", 502].
Host data    : none
Steps        : 1. Read checkId 502. 2. Invalidate the key ["active-check", 502] as a consumer's confirm-uploads mutation does on success. 3. Let the hook refetch.
Expected     : The second read returns state unfinished with checkStatus RUNNING and deadlineAt 10:25:00; exactly one GET of API-CHK-001 per read (the cache key holds checkId only).
Test data    : checkId 502, 10:20:00, 300 seconds, 10:25:00 (from the AC)
<!-- TC:TC-CHK-102:END -->

<!-- TC:TC-CHK-103:START traces=AC-CHK-081,REQ-CHK-078,API-CHK-001 -->
### TC-CHK-103 — F2 — a completed Check reads as ended, with no refusal shown
Derived from : AC-CHK-081  (REQ-CHK-078)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}, served by the mock of api-spec-chk.yaml, through the F2 hook ACTIVE-CHECK-QUERY — no CHK route (ADR-CHK-019)
Rule / code  : — (CHK-404-ACTIVE-CHECK-NOT-FOUND, PLATFORM-STD — routed to the ended state, no text)
Package      : F2
Scenario     : STATE · data class EDGE · language en
Preconditions: Check 501 has completed with Overall Status COMPLIANT; the mock answers GET for 501 with 404 and ProblemDetail code CHK-404-ACTIVE-CHECK-NOT-FOUND.
Host data    : none
Steps        : 1. Read checkId 501 through ACTIVE-CHECK-QUERY.
Expected     : The hook returns state ended (not state error); no refusal text and no error state is produced for the consumer.
Test data    : Check 501 (from the AC)
<!-- TC:TC-CHK-103:END -->

<!-- TC:TC-CHK-104:START traces=AC-CHK-083,REQ-CHK-080,API-CHK-001 -->
### TC-CHK-104 — F2 — past the deadline the waiting Check reads ended while the running one stays unfinished
Derived from : AC-CHK-083  (REQ-CHK-080)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}, served by the mock of api-spec-chk.yaml, through the F2 hook ACTIVE-CHECK-QUERY — no CHK route (ADR-CHK-019)
Rule / code  : —
Package      : F2
Scenario     : STATE · data class BOUNDARY · language en
Preconditions: At 11:01 the server's deadline check has ended Check 502 (AWAITING_DOCUMENTS, deadline 11:00); Check 507 is RUNNING with deadline 11:02. The mock answers 502 with 404 CHK-404-ACTIVE-CHECK-NOT-FOUND and 507 with RUNNING, deadlineAt 11:02.
Host data    : none
Steps        : 1. Read checkId 502 at 11:01. 2. Read checkId 507 at 11:01.
Expected     : 502 returns state ended; 507 returns state unfinished with checkStatus RUNNING and deadlineAt 11:02. The frontend computes no ending itself: a past deadline is shown as received until the server ends the Check.
Test data    : Checks 502 and 507, 11:00, 11:02, 11:01 (from the AC)
<!-- TC:TC-CHK-104:END -->

<!-- TC:TC-CHK-105:START traces=AC-CHK-085,REQ-CHK-082,API-CHK-001 -->
### TC-CHK-105 — F3 — the view validator keeps the three Active Check fields and nothing of the request
Derived from : AC-CHK-085  (REQ-CHK-082)
Exercises    : API-CHK-001 GET /api/v1/active-checks/{checkId}, served by the mock of api-spec-chk.yaml, through the F2 hook ACTIVE-CHECK-QUERY — no CHK route (ADR-CHK-019)
Rule / code  : RULE-CHK-009
Package      : F3
Scenario     : VIOLATION · data class INVALID · language en
Preconditions: Check 501 of request 1001 is RUNNING; the mock answers 501 with checkId 501, checkStatus RUNNING, deadlineAt, plus an extra member requestNumber "1001"; a second mock answer for 501 carries checkStatus COMPLETED.
Host data    : none
Steps        : 1. Read checkId 501 with the first answer. 2. Read checkId 501 with the second answer.
Expected     : First read: the view has exactly checkId 501, checkStatus RUNNING and deadlineAt; 0 values of request 1001 reach it. Second read: the body does not parse (RULE-CHK-009 allows only AWAITING_DOCUMENTS or RUNNING), so the hook returns state error and shows no half-read view.
Test data    : Check 501, request 1001 (from the AC); the extra member and COMPLETED are non-conforming mock answers (ADR-CHK-020)
<!-- TC:TC-CHK-105:END -->

<!-- TC:TC-CHK-106:START traces=AC-CHK-081,REQ-CHK-078,API-CHK-001 -->
### TC-CHK-106 — F4 — CHK's frontend module registers no route and issues no write call; the read goes only through the F2 hook
Derived from : AC-CHK-081  (REQ-CHK-078)
Exercises    : F4 — no CHK screen, no route (ADR-CHK-019, ADR-CHK-021); API-CHK-001 GET /api/v1/active-checks/{checkId} is CHK's only operation, read through ACTIVE-CHECK-QUERY
Rule / code  : —
Package      : F4
Scenario     : PERMISSION · data class EDGE · language en
Preconditions: The frontend build with CHK's client contract (F1–F3); the mock serves api-spec-chk.yaml and answers checkId 501 first with RUNNING, then — after Check 501 completes — with 404 CHK-404-ACTIVE-CHECK-NOT-FOUND.
Host data    : none
Steps        : 1. List the routes, lazy chunks and components CHK's frontend module registers. 2. List every request CHK's client contract can send. 3. Read checkId 501 through ACTIVE-CHECK-QUERY, switch the mock to the post-completion answer, and read it again.
Expected     : 0 routes, 0 lazy chunks and 0 components belong to CHK; the only request CHK's client sends is GET of API-CHK-001 (0 POST, PUT, PATCH or DELETE), issued only by ACTIVE-CHECK-QUERY; step 3 returns state unfinished with checkStatus RUNNING, then state ended with no refusal text.
Test data    : Check 501 (from the AC); none beyond the build
<!-- TC:TC-CHK-106:END -->
<!-- PHASE:TEST-PLAN-FE:END -->

## TC TRACEABILITY INDEX

| AC | TC |
|---|---|
| AC-CHK-079 | TC-CHK-101 |
| AC-CHK-080 | TC-CHK-102 |
| AC-CHK-081 | TC-CHK-103, TC-CHK-106 |
| AC-CHK-083 | TC-CHK-104 |
| AC-CHK-085 | TC-CHK-105 |

| REQ | TC |
|---|---|
| REQ-CHK-076 | TC-CHK-101 |
| REQ-CHK-077 | TC-CHK-102 |
| REQ-CHK-078 | TC-CHK-103, TC-CHK-106 |
| REQ-CHK-080 | TC-CHK-104 |
| REQ-CHK-082 | TC-CHK-105 |

| API | TC |
|---|---|
| API-CHK-001 | TC-CHK-101, TC-CHK-102, TC-CHK-103, TC-CHK-104, TC-CHK-105, TC-CHK-106 |

| Rule / code | TC |
|---|---|
| RULE-CHK-009 | TC-CHK-105 |
| — (CHK-404-ACTIVE-CHECK-NOT-FOUND, PLATFORM-STD — routed to the ended state, no text) | TC-CHK-103 |

| Package | TC |
|---|---|
| F1 | TC-CHK-101 |
| F2 | TC-CHK-102, TC-CHK-103, TC-CHK-104 |
| F3 | TC-CHK-105 |
| F4 | TC-CHK-106 |

| SCR | TC |
|---|---|
| none — CHK has no screen (ADR-CHK-019) | — |

| UXD | TC |
|---|---|
| none | — |

## COVERAGE

- AC covered by frontend TCs: 5 (AC-CHK-079, AC-CHK-080, AC-CHK-081, AC-CHK-083, AC-CHK-085 — the ACs API-CHK-001 makes observable; 6 TCs). Module AC coverage is 85/85 with the backend plan, no gap ✗.
- SCR covered: 0/0. UXD covered: 0/0, so the INT-UXD phase is absent.
- Packages with acceptance: F1, F2, F3, F4. ALIGN-FE is `no_tests`. F4 holds no screen and no route (ADR-CHK-019); its acceptance is the structural TC proving exactly that: no CHK route and no write call, with API-CHK-001 read only through the F2 hook (ADR-CHK-021).
