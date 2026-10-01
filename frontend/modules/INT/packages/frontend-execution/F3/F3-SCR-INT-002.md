<!-- source: PHASE:F3 / SUB:F3-SCR-INT-002 -->
<!-- context: F3-HEADER.md — phase-level preamble -->
<!-- traces: AC-INT-050, AC-INT-051, AC-INT-052, AC-INT-053, AC-INT-054, AC-INT-055, AC-INT-056, AC-INT-057, AC-INT-058, AC-INT-059, AC-INT-060, AC-INT-061, AC-INT-062, AC-INT-067, AC-INT-068, AC-INT-078, API-INT-005, REQ-INT-045, REQ-INT-046, REQ-INT-047, REQ-INT-048, REQ-INT-049, REQ-INT-050, REQ-INT-051, REQ-INT-052, REQ-INT-053, REQ-INT-054, REQ-INT-055, REQ-INT-056, REQ-INT-061, REQ-INT-066, SCR-INT-002, UXD-INT-002, UXD-INT-003, UXD-INT-004 -->
<!-- SUB:F3-SCR-INT-002:START traces=REQ-INT-045,REQ-INT-046,REQ-INT-047,REQ-INT-048,REQ-INT-049,REQ-INT-050,REQ-INT-051,REQ-INT-052,REQ-INT-053,REQ-INT-054,REQ-INT-055,REQ-INT-056,REQ-INT-061,AC-INT-050,AC-INT-051,AC-INT-052,AC-INT-053,AC-INT-054,AC-INT-055,AC-INT-056,AC-INT-057,AC-INT-058,AC-INT-059,AC-INT-060,AC-INT-061,AC-INT-062,AC-INT-067,AC-INT-068,API-INT-005,UXD-INT-002,UXD-INT-003,UXD-INT-004,SCR-INT-002,REQ-INT-066,AC-INT-078 -->
### F3 — SCR-INT-002 Check report
- `presentOverallStatus(report)` — returns the stored `overallStatus`, except `NOT_VERIFIED_MISSING` when it is COMPLIANT and any `documents[].readStatus` is MISSING (REQ-INT-049), shown as "Not verified — a required document is missing" (AC-INT-055; ar PENDING ADR-INT-017). Never derives a status of its own (ADR-INT-011 (2)); returns nothing for a FAILED Check (REQ-INT-050).
- `reportSchema` — the zod mirror of `CheckReportResponse` (API-INT-005) used to parse the response; an unparseable response → the error state, never a partial report.
- Plain-text rule — every report text is rendered as a text node: no HTML injection API, no markdown, no auto-linking (REQ-INT-051).
- No form, no submit.
<!-- SUB:F3-SCR-INT-002:END -->
