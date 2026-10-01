# FLOW DIAGRAM — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module : CHK   Version : v1   Profile : aias   Stage : P3.2 (Part A — UX design)
Inputs : srs-chk.md (SCR-REQ 0) · prd-chk.md (18 stories) · api-spec-chk.yaml (1 operation) · registry-srs-chk.md · registry-exec-be-chk.md
Flows  : 0   Screens : 0   ADRs : ADR-CHK-019
══════════════════════════════════════════════════════════════════

## Navigation paths

No FLOW block. A flow is identified by its starting `SCR-*`, and the SRS of CHK declares no screen
(PART B "Not applicable: CHK has no screen of its own"; registry-srs "Screens: None"). Writing a flow
here would invent navigation (§A.2), so ADR-CHK-019 records that none is written.

The employee's navigation for a Check runs entirely through the screens of Host Integration (INT), which
owns the embedded frontend of raw-idea amendment A1: Checks of a request → Check report → Document upload /
Upload confirmation / Employee decision (SCR-REQ-INT-001 … SCR-REQ-INT-005). Those flows are INT's to draw.

## What the frontend reads from CHK

CHK has exactly one HTTP operation, and the frontend consumes it outside of any CHK screen:

| Consumed operation | What it gives the frontend | Where it is shown | Stories |
|---|---|---|---|
| API-CHK-001 — read the Active Check of a Check (api-spec-chk.yaml) | the status (AWAITING_DOCUMENTS or RUNNING) and the deadline of an unfinished Check; 404 once the Check has ended | on a screen of INT, if INT decides to show it (ADR-CHK-019) | US-CHK-013, US-CHK-014 |

The Check life cycle the consumed operation reflects (SRS A7). Its states are shown here only so that the
read can be understood. This is not a screen sequence:

```
start (manual)      → AWAITING_DOCUMENTS ─ uploads confirmed (REQ-CHK-057) → RUNNING
start (path | blob) → RUNNING
AWAITING_DOCUMENTS / RUNNING → Check ended (COMPLETED or FAILED) → the Active Check is deleted → API-CHK-001 answers 404
```

## RECONCILIATION — CHK v1

```
RECONCILIATION — CHK v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → no flow written: no US-* is used in a flow; no screen invented (ADR-CHK-019)
B2 no RULE-* contradicts a flow/spec outcome                           → nothing to contradict: 0 flows
B3 every field/permission on a screen exists in the SRS               → 0 screens; no field or permission placed
B4 every screen entry of the SRS has exactly one SCR-* block          → the SRS has 0 screen entries; 0 SCR-* blocks
RESULT  reconciled 0 · reworked 0 · ADRs ADR-CHK-019
```
