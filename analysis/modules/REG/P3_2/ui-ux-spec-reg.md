# UI/UX SPEC — Service Registry (REG)
══════════════════════════════════════════════════════════════════
Module : REG   Version : v1   Profile : aias   Stage : P3.2 (Part A)
Inputs : srs-reg.md · prd-reg.md · api-spec-reg.yaml · registry-srs-reg.md · registry-exec-be-reg.md
Counts : SCR 0 · UXD 0
Decisions : ADR-REG-012 (new, ACCEPTED); applied ADR-REG-007, ADR-REG-011
══════════════════════════════════════════════════════════════════

## Screens

None. REG v1 owns no screen, so this spec carries no screen block, no `Composition` line and no cross-module display dependency.

| Source | Statement |
|---|---|
| SRS PART B | "Not applicable: REG has no screen in this version. The full administration UI is out of scope (scope exceptions, AIAS-2) … The employee frontend's screens belong to INT/RPT." |
| SRS Access summary | Service Administrator: screens none (no administration UI); Employee / Host System: none in REG |
| Registry P1 | Screens: none — no SCR-REQ in this version |
| Raw idea §2, §15 A1 | A full administration UI stays out of scope; the employee frontend is a web frontend embedded in the host screen, owned by INT |
| Raw idea §15 A2 | Caller authentication and the security phases are deferred — no permission is shown or checked on any screen |

## Cross-module display dependencies

None minted here. A `UXD-*` is keyed by the module that owns the **screen** (engine A.5): when an INT screen renders a REG field (a service's fetch mode or required document types, from API-REG-001 or API-REG-002), INT mints and records that UXD in its own spec and registry.

## Fields REG's read API exposes to a consuming screen

For reference only — no REG screen renders them. Shapes are read in `api-spec-reg.yaml` (`ServiceSummary`, `LoadResult`), not restated beyond the labels the SRS gives.

| Field | Operation | Label (en) | Label (ar) |
|---|---|---|---|
| serviceCode | API-REG-001, API-REG-002, API-REG-003 | Service code | PENDING ADR-REG-011 |
| available | API-REG-001, API-REG-002 | Available (false when the service is withdrawn — ADR-REG-016) | PENDING ADR-REG-011 |
| versionNumber | API-REG-001, API-REG-002, API-REG-003 | Version | PENDING ADR-REG-011 |
| fetchMode | API-REG-001, API-REG-002 | Fetch mode | PENDING ADR-REG-011 |
| requiredDocumentTypes | API-REG-001, API-REG-002 | Document type | PENDING ADR-REG-011 |
| approvalEnabled | API-REG-001, API-REG-002 | Approval API enabled | PENDING ADR-REG-011 |
| subjectKind, subjectName, outcome, reason, loadRunAt | API-REG-003 | Subject kind, Subject, Outcome, Reason, Load run | PENDING ADR-REG-011 |

Service codes in every response are canonical lower case; a read matches the code trimmed and case-insensitively (REQ-REG-064, ADR-REG-017). `subjectKind` includes PACKAGE_DIRECTORY (ADR-REG-018).

No response carries SQL text or a connection setting (REQ-REG-013, AC-REG-014); the frontend never asks for one.

## States

Not applicable — there is no REG screen to hold an empty, loading, error or offline state. The error routing of the three reads (for a consuming screen) is Part B's (frontend-execution-plan-reg.md, F2).
