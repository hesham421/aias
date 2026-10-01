# ADR-RPT-016 — The RPT PRD records its prd-approval of 2026-10-01 in its own Status and APPROVAL block
Status      : ACCEPTED
Stage       : P0.5      Module: RPT        Version: v1
Context     : The gate record `_state/approvals/prd-approval.json` (2026-10-01T13:17:57Z) states: "Hesham Ezzat (owner) — standing instruction 'do all with recommended, don't ask me'; PRD + RPT ADRs confirmed 2026-10-01". The SRS (A1, header) and the backend execution plan already build on "PRD approved 2026-10-01 (gate prd-approval)", but prd-rpt.md still read "Status: DRAFT — awaiting prd-approval" and "Approved by : —", so the PRD read in isolation contradicted every downstream artifact (gate-analysis finding G6). The same correction was made for REG by ADR-REG-013.
Decision    : prd-rpt.md's header Status reads "APPROVED — prd-approval 2026-10-01 (Hesham Ezzat, owner …)" and its APPROVAL block names the approver, the date 2026-10-01 and the gate record. No story, policy coverage or RESOLVED DECISIONS row changes; the story-level Status lines stay as the PRD template defines them.
Alternatives rejected: leaving the PRD DRAFT and relying on the gate record alone — an artifact must carry the approval it is relied on for; re-running P0.5 from scratch — nothing in the PRD's content changed.
Consequences: The PRD, SRS and plans agree on the approval state. Non-breaking.
traces      : US-RPT-001, US-RPT-003, US-RPT-005, US-RPT-008, US-RPT-010
