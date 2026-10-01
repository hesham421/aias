<!-- source: PHASE:TEST-PLAN-BE / SUB:MODEL-EVAL -->
<!-- context: TEST-PLAN-BE-HEADER.md — phase-level preamble -->
<!-- traces: AC-RPT-057, REQ-RPT-049 -->
<!-- SUB:MODEL-EVAL:START traces=AC-RPT-057,REQ-RPT-049 -->
### MODEL-EVAL

<!-- TC:TC-RPT-057:START traces=AC-RPT-057,REQ-RPT-049 -->
### TC-RPT-057 — No model call
Derived from : AC-RPT-057  (REQ-RPT-049)
Exercises    : every RPT operation with model traffic monitored
Rule / code  : —
Package      : PORTS
Scenario     : HAPPY · data class EDGE · language ALL
Preconditions: a comparison model is configured for the service
Host data    : none
Steps        : 1. create, complete, read, decide and purge a Check run
               2. count requests to any model endpoint from RPT; run the architecture rule (no RPT class imports Spring AI)
Expected     : 0 model requests from RPT; rule passes — repeated on every model change with the fixed known-result request set
Test data    : —
<!-- TC:TC-RPT-057:END -->
<!-- SUB:MODEL-EVAL:END -->
