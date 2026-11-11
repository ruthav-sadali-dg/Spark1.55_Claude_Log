# Global Instructions

## Session Logging

Every session is documented in a central log repo so the record can later be turned
into guides, SOPs, and troubleshooting docs. Follow these rules in every directory.

**Log repo:** `/home/sadalir/Spark1.55_Claude_Log` in WSL Ubuntu-24.04
(remote: `git@github.com:ruthav-sadali-dg/Spark1.55_Claude_Log.git`).
All log paths below are relative to the log repo, never to the current working directory.

### Accessing the log repo
- **From WSL (Linux shell):** path `~/Spark1.55_Claude_Log`; run git as
  `git -C ~/Spark1.55_Claude_Log ...`.
- **From Windows (PowerShell):** read/write files at
  `\\wsl.localhost\Ubuntu-24.04\home\sadalir\Spark1.55_Claude_Log\...`. Git is not on the
  Windows PATH, so run git through WSL:
  `wsl -d Ubuntu-24.04 -- git -C /home/sadalir/Spark1.55_Claude_Log ...`.
  Get timestamps with `wsl -d Ubuntu-24.04 -- date -Iseconds` (or `Get-Date -Format o`).

### Session start
- Before the first command, generate a session ID: `YYYYMMDD-NNN`, where NNN is the
  next unused number for today (check existing files in `sessions/`).
- Create `sessions/{session_id}.jsonl` with a header line:
  `{"type":"session_start","session_id":"...","timestamp":"...","cwd":"...","task":"<user's request in one sentence>"}`
- If the user gives a new, unrelated task later in the same conversation, start a new
  session ID for it.

### Every command (shell commands, file edits, file writes, and other actions that change state)
Append one JSON object per line (JSON Lines):
- **Before** the command, an intent entry:
  `{"type":"intent","step":N,"timestamp":"...","tool":"...","command":"...","purpose":"why this step is being done","expected":"what should happen"}`
- **After** the command, a result entry:
  `{"type":"result","step":N,"timestamp":"...","status":"success|failure","output":"actual output (trimmed to the relevant part)","deviation":true|false,"deviation_notes":"how the result differed from expected, if it did"}`
- `deviation` is `true` when the result differs from `expected`: errors, unexpected
  output, a needed workaround, or a changed plan.
- Read-only lookups (reading files, searching) may be grouped into a single entry
  instead of logged one by one.
- Writes to the log repo itself are not logged as steps.

### Deviations
When any step has `deviation: true`, also append to `anomalies/flagged.md`:
```
## {session_id} – step {N} – {date}
- **Directory:** {cwd}
- **Intended:** ...
- **Actual:** ...
- **Resolution:** what was done about it
```

### Wrap-up (after finishing each task the user asked for)
1. Write or update `summaries/{YYYY-MM-DD}_summary.md` with a plain-English
   summary under a `## {session_id}: {short title}` heading: the goal, the steps taken,
   problems and fixes, and the final outcome. Write it so it could be reused as an SOP
   or troubleshooting guide draft.
2. Commit and push **only the log repo**, never the project being worked on:
   - WSL: `git -C ~/Spark1.55_Claude_Log add . && git -C ~/Spark1.55_Claude_Log commit -m "session {session_id}" && git -C ~/Spark1.55_Claude_Log push`
   - Windows: `wsl -d Ubuntu-24.04 -- bash -c 'cd ~/Spark1.55_Claude_Log && git add . && git commit -m "session {session_id}" && git push'`

### Safety
- Never log secrets: replace passwords, tokens, API keys, connection strings, and
  credential file contents with `[REDACTED]` before writing them to any log file.
- Logging must never change the project being worked on. Do not run `git add`,
  `git commit`, or `git push` in the project repo unless the user asks.
- If logging fails (for example, the push is rejected), mention it to the user and keep
  working on the task.
