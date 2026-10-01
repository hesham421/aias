# FLOW DIAGRAM — Host Integration (INT) — employee frontend
══════════════════════════════════════════════════════════════════
Module : INT   Version : v1   Profile : aias   Stage : P3.2 (Part A)
Inputs : srs-int.md (SCR-REQ-INT-001 … SCR-REQ-INT-005) · prd-int.md (US-INT-001 … US-INT-012) · api-spec-int.yaml (API-INT-001 … API-INT-008)
Screens: SCR-INT-001 … SCR-INT-005 (one per SRS screen entry)   ADRs : ADR-INT-006, ADR-INT-011, ADR-INT-018, ADR-INT-020, ADR-INT-021
══════════════════════════════════════════════════════════════════

The frontend is embedded in the host screen and opened for ONE request: the Host System passes the
service code, the request number and the employee identity at launch (ADR-INT-011 (1), ADR-INT-018 (4)).
Every route keeps that launch context; without all three values no Check is shown (RULE-INT-004).
No role check exists in this version (raw-idea A2).

```
                 Host System screen (launch: serviceCode · requestNumber · employeeId)
                                   │
                                   ▼
                    SCR-INT-001  Checks of a request ──── "Start a Check" (API-INT-001)
                                   │ select a Check (checkId)
                                   ▼
                    SCR-INT-002  Check report  ◄─── read again every 5 s while AWAITING_DOCUMENTS / RUNNING
                     │   (AWAITING_DOCUMENTS)        │ (AWAITING_DOCUMENTS)        │ (COMPLETED, no Employee Decision)
                     ▼                               ▼                             ▼
          SCR-INT-003  Document upload ──► SCR-INT-004  Upload confirmation    SCR-INT-005  Employee decision
                     │  "Upload" (API-INT-002)        │ "Confirm uploads" (API-INT-003)   │ "Record decision" (API-INT-004)
                     └──────────► back to SCR-INT-002 ◄┘──────────────────────────────────┘
```

FLOW — Open the Checks of a request                   traces=US-INT-008,REQ-INT-040,REQ-INT-041,REQ-INT-042,REQ-INT-043,SCR-INT-001
Screens   : SCR-INT-001
Sequence  : Host System screen (launch context) → SCR-INT-001 (Checks newest first, total when more exist) → exit to the host screen
Trigger   : the employee opens the embedded frontend from the host screen of one request
Priority  : HIGH (US-INT-008)

FLOW — Launch without its request                     traces=US-INT-008,REQ-INT-041,SCR-INT-001
Screens   : SCR-INT-001
Sequence  : Host System screen (service code, request number or employee identity absent) → SCR-INT-001 shows no Check and the RULE-INT-004 message → exit
Trigger   : the frontend is opened without its full launch context
Priority  : HIGH (US-INT-008)

FLOW — Start a Check and follow it                    traces=US-INT-001,US-INT-010,REQ-INT-044,REQ-INT-001,REQ-INT-055,REQ-INT-056,SCR-INT-001,SCR-INT-002
Screens   : SCR-INT-001, SCR-INT-002
Sequence  : SCR-INT-001 → "Start a Check" (accepted at once, the new Check first in the list) → select it → SCR-INT-002 (read again every 5 seconds while AWAITING_DOCUMENTS or RUNNING) → report or failure shown when COMPLETED or FAILED → exit
Trigger   : the employee wants a new Check of the request on the host screen
Priority  : HIGH (US-INT-001) · MEDIUM (US-INT-010)

FLOW — Verify a report                                traces=US-INT-009,REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,SCR-INT-001,SCR-INT-002
Screens   : SCR-INT-001, SCR-INT-002
Sequence  : SCR-INT-001 → select a Check → SCR-INT-002 (header, Overall Status as stored with the MISSING safeguard, each Finding beside its Evidence, documents read / missing / unreadable, service queries not read, failure, recorded Employee Decision) → back to SCR-INT-001
Trigger   : the employee opens the latest or an earlier Check of the request
Priority  : HIGH (US-INT-009)

FLOW — Upload the documents of a manual Check         traces=US-INT-003,US-INT-004,REQ-INT-053,REQ-INT-009,REQ-INT-016,REQ-INT-017,REQ-INT-018,REQ-INT-019,REQ-INT-020,SCR-INT-002,SCR-INT-003,SCR-INT-004
Screens   : SCR-INT-002, SCR-INT-003, SCR-INT-004
Sequence  : SCR-INT-002 (AWAITING_DOCUMENTS) → "Upload documents" → SCR-INT-003 ("Upload", one file at a time, repeated; never confirms) → "Confirm uploads" → SCR-INT-004 (uploaded documents and required types with no upload) → "Confirm uploads" (accepted) → SCR-INT-002 (RUNNING, read again every 5 seconds)
Trigger   : the opened Check of a `manual` service waits for documents
Priority  : HIGH (US-INT-003, US-INT-004)

FLOW — Record the Employee Decision                   traces=US-INT-005,US-INT-006,US-INT-007,REQ-INT-054,REQ-INT-021,REQ-INT-023,REQ-INT-025,REQ-INT-036,REQ-INT-037,REQ-INT-038,SCR-INT-002,SCR-INT-005
Screens   : SCR-INT-002, SCR-INT-005
Sequence  : SCR-INT-002 (COMPLETED, no Employee Decision) → "Record decision" → SCR-INT-005 (APPROVED / REJECTED; the deciding employee is the launch identity) → "Record decision" → recorded → SCR-INT-002 shows the decision · on an Approval API failure or timeout: the refusal is shown on SCR-INT-005, nothing is recorded, the employee may submit again
Trigger   : the employee has verified a completed report
Priority  : HIGH (US-INT-005, US-INT-006, US-INT-007)

FLOW — A refusal shown in the owner's words           traces=US-INT-002,REQ-INT-006,REQ-INT-007,REQ-INT-008,SCR-INT-001,SCR-INT-003,SCR-INT-004,SCR-INT-005
Screens   : SCR-INT-001, SCR-INT-003, SCR-INT-004, SCR-INT-005
Sequence  : any submit (start, upload, confirm, decide) → refused → the same screen shows the refusal's detail as the server sent it, keeps its form state → the employee corrects or leaves
Trigger   : the Check Engine, Document Access, the Report Store or INT refuses a request
Priority  : — (US-INT-002)

No flow for US-INT-011 (the same REST API for a host's own display) and US-INT-012 (no second copy of a
request): neither is a navigation path. US-INT-011 is honoured by every read and write of the frontend being
an operation of INT's published API document (REQ-INT-057 — ADR-INT-020, ADR-INT-021); US-INT-012 has no screen.

```
RECONCILIATION — INT v1
B1 every US-* used in a flow has an SRS counterpart (REQ/AC/screen)   → 9 of 12 US used, each with its SCR-REQ; US-INT-011, US-INT-012 have no screen (no flow, no ADR needed — not navigation)
B2 no RULE-* contradicts a flow/spec outcome                           → none: RULE-INT-001 (upload only while AWAITING_DOCUMENTS) — the upload is offered only then; RULE-INT-002/003 — the decision is offered only on a COMPLETED, undecided Check; RULE-INT-004 — no Check without the launch context
B3 every field/permission on a screen exists in the SRS               → no extra field; added: the required-document-types read on SCR-INT-004 (REQ-INT-020, REQ-INT-064 — ADR-INT-021 (1)); no permission (no role check — raw-idea A2)
B4 every screen entry of the SRS has exactly one SCR-* block          → 5 SCR-REQ → SCR-INT-001 … SCR-INT-005
RESULT  reconciled 5 · reworked 1 (SCR-INT-004 reads) · ADRs ADR-INT-018, ADR-INT-021
```
