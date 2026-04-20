---
name: bloatware-log
description: View, search, and summarise the bloatware operations log at ~/.claude/adb-ops/bloatware-log.jsonl. Use when the user asks "what bloatware did I remove", "show my debloat history", "what did I disable on this phone", or wants to audit past package operations before restoring something.
---

# adb-ops: bloatware-log

Read-only browser over the append-only bloatware log.

## Procedure

1. Read `~/.claude/adb-ops/bloatware-log.jsonl` line-by-line. If missing, tell the user no operations have been logged yet.

2. Depending on the user's request:
   - **List all**: pretty-print as a table (timestamp, device, action, package, result).
   - **Filter by device**: match on serial.
   - **Filter by package**: substring match on package name.
   - **Filter by date range**: e.g. "last 30 days", "since March".
   - **Summary**: counts by action, counts by device, top 10 most-disabled packages.

3. Highlight any `failure` rows so the user can retry them.

4. Never modify the log from this skill — restoration goes through `remove-bloatware`'s restore mode so the new entry is appended correctly.
