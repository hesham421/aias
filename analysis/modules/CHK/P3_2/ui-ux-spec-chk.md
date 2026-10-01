# UI/UX SPEC — Check Engine (CHK)
══════════════════════════════════════════════════════════════════
Module : CHK   Version : v1   Profile : aias   Stage : P3.2 (Part A — UX design)
Inputs : srs-chk.md (SCR-REQ 0) · prd-chk.md · api-spec-chk.yaml · registry-srs-chk.md · registry-exec-be-chk.md
Counts : SCR 0 · UXD 0   ADRs : ADR-CHK-019
══════════════════════════════════════════════════════════════════

## Screens

No `SCR-*` block. The SRS of CHK declares no screen entry (PART B not applicable; module-registry
AUTO-DECISION; [KB:raw-idea.md §15 A1]), and this stage mints one `SCR-*` per SRS screen entry and
nothing more (§A.1). The employee frontend of amendment A1 is owned by Host Integration (INT), which
holds all five screens (SCR-REQ-INT-001 … SCR-REQ-INT-005) and their fields, compositions and states.

## Cross-module display dependencies

No `UXD-*`. A UXD is keyed by the module that owns the screen that displays foreign data (§A.5). CHK
owns no screen, so it has no UXD to mint. If one of INT's screens renders the Active Check read by
API-CHK-001, that screen's module (INT) mints the UXD (ADR-CHK-019).

## Frontend surface of CHK (consumed, not displayed by CHK)

What the frontend can read from CHK, stated as design intent for Part B. The fields come from the SRS
(ENT-CHK-001) and the shapes from api-spec-chk.yaml:

| Field (ENT-CHK-001) | Label en | Label ar | Read-only | Source |
|---|---|---|---|---|
| checkId | Check | PENDING ADR-CHK-018 | yes | API-CHK-001 response |
| checkStatus (CHECK_STATUS: AWAITING_DOCUMENTS · RUNNING) | Status (Awaiting documents · Running) | PENDING ADR-CHK-018 | yes | API-CHK-001 response |
| deadlineAt | Deadline | PENDING ADR-CHK-018 | yes | API-CHK-001 response |

- Meaning of the deadline (REQ-CHK-076, REQ-CHK-077): the end of the upload window while AWAITING_DOCUMENTS,
  and the end of the Check timeout (counted from RUNNING) while RUNNING.
- A Check that has ended (COMPLETED or FAILED) has no Active Check (REQ-CHK-078). The read then answers "not
  found", and a consumer shows the ended Check from the Report Store record that INT reads. It does not
  show a refusal.
- Permissions: none. There is no permission model; caller authentication is deferred (raw-idea A2).
- No input, no submit: CHK takes nothing from the employee through the frontend. Starting a Check and
  confirming uploads are INT's endpoints, which call CHK in-process (ADR-CHK-013, ADR-CHK-017).

Labels are copied from the SRS (A3 field labels, A6 CHECK_STATUS labels). Arabic labels are PENDING ADR-CHK-018,
because the SRS gives no Arabic text and none is translated here.

## RECONCILIATION — CHK v1

```
RECONCILIATION — CHK v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → 0 flows (flow-diagram-chk.md); nothing excluded
B2 no RULE-* contradicts a flow/spec outcome                           → 0 SCR blocks; the consumed surface agrees with RULE-CHK-009 (unfinished statuses only)
B3 every field/permission on a screen exists in the SRS               → 0 screens; the three consumed fields are ENT-CHK-001's, no permission added
B4 every screen entry of the SRS has exactly one SCR-* block          → 0 SRS screen entries · 0 SCR-* blocks
RESULT  reconciled 0 · reworked 0 · ADRs ADR-CHK-019
```
