# ADR-CHK-008 — Starting a Check needs an available service code, a request number and the employee's identity; CHK creates the Check run record through its result port and returns its identifier at once; an unavailable service is refused with no Check created
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: CHK        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 55cb93ff67f4
traces      : POL-CHK-001, POL-CHK-002

## Decision
The host sends the service code, the request number and the employee's identity (raw idea §5 step 1) and polls the status afterwards (§5, §8), so the Check needs an identifier before the pipeline runs, while the Check run record is RPT's (ADR-REG-001). Recommended: CHK checks the service code against REG (CON-REG-013); an unknown or withdrawn code — or a service whose connection is not activated (CON-REG-007) — is refused at once and no Check is created. Otherwise CHK creates the Check run record through its result port, which RPT implements and which returns the Check's identifier (`NUMBER(19)`, profile conventions.identifiers), returns that identifier to its caller and runs the pipeline in the background. The request number and the employee's identity are kept exactly as the host sent them, as strings, and are never foreign keys; the request number reaches the host only as the bound parameter of the service's queries (G4). The employee's identity is not authenticated in this version (A2).

Status: recommended — pending owner confirmation at prd-approval. Sources: [KB:raw-idea.md §5, §8]; profile conventions.identifiers, conventions.lookups; CON-REG-007, CON-REG-013; ADR-REG-001, ADR-REG-002.

Source      : governance-shared/analysis/modules/CHK/_state/briefs/pass-1.md (P0 operator run)
