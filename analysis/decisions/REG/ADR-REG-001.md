# ADR-REG-001 — RPT owns the Check run record, the findings and the Check Document record; CHK writes through RPT's interface; DOC reads documents but owns no stored rows
Status      : RESOLVED-IN-DIALOGUE
Stage       : P0        Module: REG        Version: v1
Lane        : analysis-dialogue · operator (single-operator converging dialogue)
Decided     : 2026-10-01T12:00:00+00:00
Dialogue-key: 5b4b0d026090
traces      : —

## Decision
Closes project-registry OQ-1 and OQ-2. Owner statement (2026-10-01): the raw idea §14 puts "Runs, findings, documents" under RPT, so RPT owns the Check run record (the raw idea's CHECK_RUN), the findings (CHECK_FINDING) and the Check Document record (CHECK_DOCUMENT). CHK runs the pipeline and writes its results through RPT's interface. DOC fetches and reads documents but does not own their stored rows. The term "Check" stays CHK vocabulary (domain-profile §7.1); ownership of the stored record is RPT's.

Accepted in dialogue: owner input, no counter-proposal. Sources: [KB:raw-idea.md §9, §14]; domain-profile §4 rows 3-4; project-registry §8.

Source      : governance-shared/analysis/modules/REG/_state/briefs/pass-1.md (P0 operator run)
