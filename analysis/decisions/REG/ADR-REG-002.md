# ADR-REG-002 — Keep the owner's dependency graph and carry CHK's writes to RPT through a result port that CHK declares and RPT implements
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: REG        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: af0fd36aca95
traces      : —

## Decision
The owner's graph has RPT depends_on [CHK] (agreeing with domain-profile §6), while the OQ-1 answer makes CHK write its results through RPT's interface. A direct CHK -> RPT call would add the edge CHK -> RPT and close a cycle. Options: A) flip the edge (CHK depends_on RPT; RPT depends on nothing but REG) — contradicts both the owner's graph and §6; B) keep the graph and invert the write: CHK declares the port it writes results to, RPT implements it, wired in-process (profile conventions.module_interface: in_process). Recommended B: it honours the owner's graph and §6 unchanged and keeps the graph acyclic; RPT still owns the records (ADR-REG-001).

Reviewer challenge: a port declared by CHK for data RPT owns blurs ownership. Answer: the port carries CHK's results as values; the stored records and their schema stay RPT's. Status: recommended — pending owner confirmation at prd-approval. Sources: domain-profile §6; owner platform-dependency input.

Source      : governance-shared/analysis/modules/REG/_state/briefs/pass-1.md (P0 operator run)
