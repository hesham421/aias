# FLOW DIAGRAM — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Stage : P3.2 (Part A)
Inputs : srs-reg.md · prd-reg.md · api-spec-reg.yaml · registry-srs-reg.md · registry-exec-be-reg.md
Decisions : ADR-REG-007, ADR-REG-011, ADR-REG-012 (analysis/decisions/REG/)
══════════════════════════════════════════════════════════════════

## Flows

None. REG v1 has no screen, so it has no navigation path.

- The SRS declares no screen entry for REG (PART B "Not applicable", SCR-REQ 0): the full administration UI is out of scope (raw idea §2, AIAS-2) [KB:raw-idea.md §2].
- The Service Administrator maintains the package folders and the activation configuration directly, outside the service; a new service package goes live at the next start (ADR-REG-007). No REG story (US-REG-001 … US-REG-013) implies a screen (PRD resolved decision 3).
- The employee frontend of amendment A1 (React + TypeScript) is owned by INT, which holds all five screens and their SCR-REQs [KB:raw-idea.md §15 A1]. Any navigation through a Check, its report, a manual upload or an Employee Decision is an INT flow.
- A flow with no SRS-backed screen would invent navigation (engine A.2); none is drawn (ADR-REG-012).

## What the frontend reaches in REG

REG's only frontend-facing surface is its read API, consumed like any host (A1). It is not a flow: no REG screen is entered, left or navigated.

| Read | API | Traces | Used by |
|---|---|---|---|
| List services | API-REG-001 | REQ-REG-013 | any INT screen that needs the available services (cited there as an INT `UXD-*`) |
| Read one service | API-REG-002 | REQ-REG-014, REQ-REG-015 | INT's Document upload (SCR-REQ-INT-003, REQ-INT-016 — the required document types as the upload's document type choices; cited there as an INT `UXD-*`) |
| Read the load report | API-REG-003 | REQ-REG-008 | no v1 screen — the Service Administrator reads it directly (no administration UI) |

## Reconciliation

```
RECONCILIATION — REG v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → no flow drawn; no US-REG-* implies a screen
B2 no RULE-* contradicts a flow/spec outcome                           → none (no flow, no screen)
B3 every field/permission on a screen exists in the SRS               → none (no screen)
B4 every screen entry of the SRS has exactly one SCR-* block          → SRS screen entries 0 = SCR blocks 0
RESULT  reconciled 0 · reworked 0 · ADRs ADR-REG-012
```
