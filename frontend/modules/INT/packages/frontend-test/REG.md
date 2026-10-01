<!-- source: PHASE:INT-UXD / SUB:REG -->
<!-- context: INT-UXD-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-020, AC-INT-024, REQ-INT-016, REQ-INT-020, SCR-INT-003, SCR-INT-004, UXD-INT-005, UXD-INT-008 -->
<!-- SUB:REG:START traces=REQ-INT-016,REQ-INT-020,AC-INT-020,AC-INT-024,UXD-INT-005,UXD-INT-008,SCR-INT-003,SCR-INT-004 -->
### REG — Service Registry

<!-- TC:TC-INT-079:START traces=UXD-INT-005,REQ-INT-016,AC-INT-020,SCR-INT-003 -->
### TC-INT-079 — The document type choices render the Service Registry's required types
Derived from : UXD-INT-005 (REQ-INT-016, AC-INT-020)
Exercises    : SCR-INT-003 `/checks/622/documents` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-003
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: Check 622 runs `manual-service` and is AWAITING_DOCUMENTS; INT's read API-INT-008 answers requiredDocumentTypes TRANSCRIPT and ID_CARD for the Check's version; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 622; open the document type control.
Expected     : The choices are the values API-INT-008 returned: TRANSCRIPT and ID_CARD.
Test data    : checkId 622 (AC-INT-020)
<!-- TC:TC-INT-079:END -->

<!-- TC:TC-INT-080:START traces=UXD-INT-005,REQ-INT-016,AC-INT-020,SCR-INT-003 -->
### TC-INT-080 — No choice is offered when the Service Registry read fails
Derived from : UXD-INT-005 (REQ-INT-016, AC-INT-020)
Exercises    : SCR-INT-003 `/checks/622/documents` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-003
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: Check 622 is AWAITING_DOCUMENTS; INT's read API-INT-008 answers HTTP 500 (INT-500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the document upload of Check 622.
Expected     : No document type choice is offered, "Upload" is disabled and the screen's error state is shown; no blank screen.
Test data    : checkId 622; answer: HTTP 500
<!-- TC:TC-INT-080:END -->

<!-- TC:TC-INT-081:START traces=UXD-INT-008,REQ-INT-020,AC-INT-024,SCR-INT-004 -->
### TC-INT-081 — The required types with no upload render from the Service Registry
Derived from : UXD-INT-008 (REQ-INT-020, AC-INT-024)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F3-SCR-INT-004
Scenario     : INTEGRATION · data class VALID · language ALL
Preconditions: INT's read API-INT-008 answers requiredDocumentTypes TRANSCRIPT and ID_CARD for Check 626's version; Document Access lists one TRANSCRIPT; launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626.
Expected     : ID_CARD — the required type API-INT-008 returned with no uploaded document — is listed as having no upload; TRANSCRIPT is not.
Test data    : checkId 626 (AC-INT-024)
<!-- TC:TC-INT-081:END -->

<!-- TC:TC-INT-082:START traces=UXD-INT-008,REQ-INT-020,AC-INT-024,SCR-INT-004 -->
### TC-INT-082 — The confirmation shows its error state when the Service Registry read fails
Derived from : UXD-INT-008 (REQ-INT-020, AC-INT-024)
Exercises    : SCR-INT-004 `/checks/626/upload-confirmation` (+ launch query string)
Rule / code  : —
Package      : F2-SCR-INT-004
Scenario     : INTEGRATION · data class EDGE · language ALL
Preconditions: INT's read API-INT-008 answers HTTP 500 (INT-500); launched with a complete launch context (serviceCode, requestNumber, employeeId); the mock server serves api-spec-int.yaml (ADR-INT-021 (2), ADR-INT-022)
Host data    : none
Steps        : 1. Open the upload confirmation of Check 626.
Expected     : The screen's error state with a retry; "Confirm uploads" is disabled; no blank screen.
Test data    : checkId 626; answer: HTTP 500
<!-- TC:TC-INT-082:END -->
<!-- SUB:REG:END -->
