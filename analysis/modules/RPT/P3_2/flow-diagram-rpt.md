# FLOW DIAGRAM — Report Store (RPT)
══════════════════════════════════════════════════════════════════
Module : RPT   Version : v1   Profile : aias   Stage : P3.2 (Part A)
Inputs : srs-rpt.md (SCR-REQ 0) · prd-rpt.md · api-spec-rpt.yaml · registry-srs-rpt.md · registry-exec-be-rpt.md
Decisions : ADR-RPT-014 (no RPT screen; frontend surface = what the frontend consumes from RPT's API)
══════════════════════════════════════════════════════════════════

## Flows

None. A flow starts at a screen the SRS declares; the RPT SRS declares no screen entry (PART B "not applicable" — [KB:raw-idea.md §15 A1]) and the engine forbids a flow with no SRS-backed screen (A.2). No navigation is invented here (ADR-RPT-014).

The employee follows RPT's data inside Host Integration's flows — the Checks of a request, then a Check report, then (while COMPLETED and undecided) the employee decision — on screens Host Integration owns. Those flows read RPT through two operations of api-spec-rpt.yaml:

| Where the reading happens (INT screen, by name) | RPT read | Stories served |
|---|---|---|
| Checks of a request | API-RPT-002 `GET /api/v1/checks` (serviceCode, requestNumber) | US-RPT-009 |
| Check report (status while it runs, whole report once ended) | API-RPT-001 `GET /api/v1/checks/{checkId}` | US-RPT-002, US-RPT-008, US-RPT-010 (the decision shown beside the result) |

API-RPT-003 `GET /api/v1/decision-agreement` (US-RPT-011) has no frontend flow: the service administrator reads it directly and an administration UI is out of scope ([KB:raw-idea.md §2]).

## Reconciliation self-check (A.6)

```
RECONCILIATION — RPT v1
B1 every US-* used in a flow has an SRS counterpart   → no flow drafted; nothing to check
B2 no RULE-* contradicts a flow/spec outcome           → no flow/spec outcome; RULE-RPT-009 and RULE-RPT-015 bound in the plan's error routing
B3 every field/permission on a screen exists in the SRS → no screen; the plan's F1 models cite API-RPT-001 / API-RPT-002 schemas only
B4 every screen entry of the SRS has exactly one SCR-* → 0 screen entries · 0 SCR
RESULT  reconciled 0 · reworked 0 · ADRs ADR-RPT-014
```
