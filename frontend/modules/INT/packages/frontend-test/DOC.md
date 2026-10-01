<!-- source: PHASE:INT-UXD / SUB:DOC -->
<!-- context: INT-UXD-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-021, AC-INT-024, REQ-INT-017, REQ-INT-020, SCR-INT-003, SCR-INT-004, UXD-INT-006, UXD-INT-007 -->
<!-- SUB:DOC:START traces=REQ-INT-017,REQ-INT-020,AC-INT-021,AC-INT-024,UXD-INT-006,UXD-INT-007,SCR-INT-003,SCR-INT-004 -->
### DOC — Document Access

<!-- TC:TC-INT-083:START traces=UXD-INT-006,REQ-INT-017,AC-INT-021,SCR-INT-003 -->
### TC-INT-083 — The uploaded documents render Document Access's values
Derived from : UXD-INT-006 (REQ-INT-017, AC-INT-021)
Exercises    : SCR-INT-003 `/checks/623/documents` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-003
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-007 answers one TRANSCRIPT `t.pdf` of 300 KB for Check 623; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 623.
Expected     : The entry renders the values API-INT-007 returned: TRANSCRIPT, "t.pdf", 300 KB.
Test data    : checkId 623 (AC-INT-021)
<!-- TC:TC-INT-083:END -->

<!-- TC:TC-INT-084:START traces=UXD-INT-006,REQ-INT-017,AC-INT-021,SCR-INT-003 -->
### TC-INT-084 — The uploaded list shows its error state and the form stays usable
Derived from : UXD-INT-006 (REQ-INT-017, AC-INT-021)
Exercises    : SCR-INT-003 `/checks/623/documents` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-003
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: Check 623 is AWAITING_DOCUMENTS; INT's read API-INT-007 answers HTTP 500 (INT-500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 623.
Expected     : The uploaded documents list shows its error state with a retry; the upload form is still offered; no blank screen.
Test data    : checkId 623; answer: HTTP 500
<!-- TC:TC-INT-084:END -->

<!-- TC:TC-INT-085:START traces=UXD-INT-007,REQ-INT-020,AC-INT-024,SCR-INT-004 -->
### TC-INT-085 — The confirmation lists Document Access's uploaded documents
Derived from : UXD-INT-007 (REQ-INT-020, AC-INT-024)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F1-SCR-INT-004
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-007 answers one TRANSCRIPT for Check 626; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626.
Expected     : The uploaded documents list renders the TRANSCRIPT entry API-INT-007 returned (document type, file name, size).
Test data    : checkId 626 (AC-INT-024)
<!-- TC:TC-INT-085:END -->

<!-- TC:TC-INT-086:START traces=UXD-INT-007,REQ-INT-020,AC-INT-024,SCR-INT-004 -->
### TC-INT-086 — The confirmation shows its error state when the Document Access read fails
Derived from : UXD-INT-007 (REQ-INT-020, AC-INT-024)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-004
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: INT's read API-INT-007 answers HTTP 500 (INT-500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626.
Expected     : The screen's error state with a retry; "Confirm uploads" is disabled; no blank screen.
Test data    : checkId 626; answer: HTTP 500
<!-- TC:TC-INT-086:END -->
<!-- SUB:DOC:END -->
