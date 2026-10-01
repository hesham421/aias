# ADR-RPT-005 — RPT offers INT three in-process reads — one Check's status and report, the Checks of a service code and request number newest first, and the decision agreement of a service by package version — with no viewer restriction in this version
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: RPT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T14:00:00+00:00
Dialogue-key: c298c6915f90
traces      : POL-RPT-011, POL-RPT-012, POL-RPT-017

## Decision
Raw idea §8 `GET /checks/{id}` returns the status and the report; A1 gives the employee "the checks of a request"; §9 says the decision beside the result measures accuracy; domain-profile §6 makes INT read the report and record the decision from RPT. Options for the list key: A) request number only — two services could share a host request number and mix reports; B) service code + request number. Recommended B. For accuracy: A) leave it to ad-hoc SQL; B) a read that returns, for one service code, per service package version, the count of decided Checks for each Overall Status × Employee Decision. Recommended B — it makes the §9 measure usable without an administration UI. These reads are offered in-process to INT (profile `module_interface: in_process`); whether RPT also serves them over HTTP is chosen at P3.1. Who may view stored reports is deferred with caller authentication (D4, A2): no viewer restriction is added and no requirement may add one in this version. Status: recommended — confirmed at prd-approval; viewer restriction deferred by owner. Sources: [KB:raw-idea.md §8, §9, §15 A1, A2]; domain-profile §6, §8 D4, D7.

Source      : governance-shared/analysis/modules/RPT/_state/briefs/pass-1.md (P0 operator run)
