# ADR-RPT-010 — The report retention period is a whole number of days greater than zero and the purge schedule a cron expression (default daily at 02:00) of the platform configuration; the purge deletes each expired Check run in its own transaction, skips one that fails and logs the count and cut-off
Status      : ACCEPTED
Stage       : P1        Module: RPT        Version: v1
Context     : ADR-RPT-004 fixed the retention behaviour. Left open: the value's form, the default schedule, how a purge treats a partial failure and what trace it leaves.
Decision    : (1) Report retention period: whole days, greater than zero; absent or invalid → the purge deletes nothing and logs "Report purge skipped: no valid report retention period is configured." (REQ-RPT-044). (2) Purge schedule: cron expression, default `0 0 2 * * *` (daily 02:00 server time). (3) Cut-off = run time minus the period; every Check run COMPLETED or FAILED with endedAt before the cut-off is deleted with its Findings, Check Documents and Unread Queries in one transaction per Check run; a failed deletion keeps that Check run whole and the purge continues (REQ-RPT-052). (4) The run logs the number deleted and the cut-off (REQ-RPT-046).
Alternatives rejected: One transaction for the whole purge — one bad row would stop every deletion; soft delete — contradicts D4 and the profile.
Consequences: P2 adds no table for the configuration; P3.1 places the purge as a scheduled service procedure with no HTTP API.
traces      : US-RPT-012, REQ-RPT-043, REQ-RPT-044, REQ-RPT-046, REQ-RPT-052
