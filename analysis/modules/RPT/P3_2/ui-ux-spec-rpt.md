# UI/UX SPEC — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Stage : P3.2 (Part A)
Inputs : srs-rpt.md (SCR-REQ 0) · prd-rpt.md · api-spec-rpt.yaml
Counts : SCR 0 · UXD 0
Decisions : ADR-RPT-014
══════════════════════════════════════════════════════════════════

## Screens

None. The RPT SRS declares no screen entry (PART B "not applicable"), so no `SCR-RPT-*` block is written (A.1: one SCR per SRS screen entry). All five employee screens of the embedded frontend ([KB:raw-idea.md §15 A1]) and their screen requirements belong to Host Integration; RPT's frontend surface is the data those screens consume from RPT's API, stated in frontend-execution-plan-rpt.md (F1–F3) — ADR-RPT-014.

## Cross-module display dependencies

None. A `UXD-*` is minted when a screen owned by this module displays another module's data; RPT owns no screen. The reverse dependency — Host Integration's screens rendering Report Store fields — is minted by the displaying module, never here (A.5).

## Design intent RPT hands to the consuming screens (not a screen block)

The SRS states these about RPT's data wherever it is shown; Part B (F4) turns them into rendering obligations:

| Intent | Source |
|---|---|
| A Check's status is shown while it runs; the report only once it is COMPLETED; a FAILED Check shows its failure reason and detail and no Overall Status | REQ-RPT-023, AC-RPT-027, AC-RPT-028, REQ-RPT-026, AC-RPT-031 |
| Each finding is shown as one entry: condition, outcome, evidence and note together | REQ-RPT-024, AC-RPT-029 |
| Each Check Document is shown as one entry with its document type, source mode and read status; READ, MISSING and UNREADABLE are visibly told apart (distinct label per status, never colour alone); an UNREADABLE document shows its unreadable reason and detail beside the status, a MISSING one its detail; a missing or unreadable required document is never presented as satisfied — it stands beside the finding it caused (NOT_SATISFIED / UNDETERMINED), so no COMPLIANT reading can hide it | REQ-RPT-023, AC-RPT-028, REQ-RPT-017, AC-RPT-020, REQ-RPT-014, ADR-RPT-017 |
| Findings, documents and unread queries keep report order (position) | REQ-RPT-010, AC-RPT-013 |
| Stored texts are shown as text, never interpreted | REQ-RPT-027, AC-RPT-032 |
| The Checks of a request are listed newest first, at most 100, with the total shown so a cut is visible | REQ-RPT-028, REQ-RPT-031, AC-RPT-034, AC-RPT-037 |
| A Check that is not found (never created or purged) is told apart from a running one | REQ-RPT-025, AC-RPT-030 |
| The Employee Decision block is shown only once a decision is recorded (`decision` null before — shown as "no decision yet", never as a default APPROVED or REJECTED); once recorded, employeeDecision, decidedBy and decidedAt are shown together, with whether it was executed through the Approval API (approvalApiExecuted) as its own distinct text label; the block stands beside the Overall Status and findings and never replaces them, and nothing in it implies the Report Store approved on its own | REQ-RPT-032, AC-RPT-038, REQ-RPT-036, AC-RPT-043, REQ-RPT-037, AC-RPT-044 |

Labels (en) are the SRS field labels of ENT-RPT-001 … ENT-RPT-004; Arabic labels and messages are `PENDING ADR-RPT-013`. No permission model: screens open per the SRS (raw-idea A2).
