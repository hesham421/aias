<!-- source: PHASE:TEST-PLAN-FE / SUB:INT-FLOW -->
<!-- context: TEST-PLAN-FE-HEADER.md — phase-level preamble -->
<!-- traces: AC-RPT-029, AC-RPT-032, AC-RPT-058, API-RPT-001, API-RPT-002, REQ-RPT-024, REQ-RPT-027, REQ-RPT-050 -->
<!-- SUB:INT-FLOW:START traces=AC-RPT-029,AC-RPT-032,AC-RPT-058,REQ-RPT-024,REQ-RPT-027,REQ-RPT-050 -->
### INT-FLOW

<!-- TC:TC-RPT-072:START traces=AC-RPT-058,REQ-RPT-050,API-RPT-002 -->
### TC-RPT-072 — request number passed untouched
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
<!-- TC:TC-RPT-072:END -->

<!-- TC:TC-RPT-073:START traces=AC-RPT-032,REQ-RPT-027,API-RPT-001 -->
### TC-RPT-073 — evidence rendered as text
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
<!-- TC:TC-RPT-073:END -->

<!-- TC:TC-RPT-074:START traces=AC-RPT-029,REQ-RPT-024,API-RPT-001 -->
### TC-RPT-074 — finding rendered as one entry
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
<!-- TC:TC-RPT-074:END -->
<!-- SUB:INT-FLOW:END -->
