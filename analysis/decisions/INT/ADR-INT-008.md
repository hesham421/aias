# ADR-INT-008 — INT reads the approval API definition of a Check's service package version from REG in-process (CON-REG-012); the platform block keeps the owner's INT depends_on [CHK, RPT, DOC], and the INT → REG read is declared at entity level in the SRS
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: INT        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T15:00:00+00:00
Dialogue-key: bb567d40c34b
traces      : POL-INT-009

## Decision
REG's contract reserves the approval API definition for INT's Employee Decision operation (CON-REG-012; ADR-REG-008) and leaves it out of the package CHK reads (CON-REG-007), so INT must read REG to know whether a version enables the Approval API. The owner's platform graph, kept identical in every module's platform summary, lists INT depends_on [CHK, RPT, DOC]. Options: A) change INT's platform row — diverges from the four existing platform summaries; B) keep the owner's platform row and declare the finer INT → REG (ENT-REG-002) SOFT-READ in the SRS `module-dependencies` block, which `gov.py graph` folds in. The edge targets tier 0 from tier 4, so the tier rule holds and the build order is unchanged (REG is already upstream of CHK, DOC and RPT). Recommended B. Status: recommended — confirmed at prd-approval. Sources: CON-REG-012, CON-REG-007; ADR-REG-008; RPT platform summary (owner graph); factory `xm.graph`.

Source      : governance-shared/analysis/modules/INT/_state/briefs/pass-1.md (P0 operator run)
