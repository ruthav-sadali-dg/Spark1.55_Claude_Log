# Spark1.55_Claude_Log

A running record of the work Claude Code does across every project and directory.

## Goal

Capture what was done, why it was done, and what actually happened, so the record can be
turned into:

- **Guides and SOPs:** repeatable step-by-step procedures taken from real sessions
- **Troubleshooting docs:** what went wrong, how it showed up, and how it was fixed
- **General work docs:** a searchable history of changes, decisions, and setups

## How it works

A global `CLAUDE.md` tells Claude Code to log every session here, in any directory. It is
installed in both environments Claude Code runs from:

- Windows: `C:\Users\SadaliR\.claude\CLAUDE.md`
- WSL (Ubuntu-24.04): `~/.claude/CLAUDE.md`

A versioned copy lives in [`config/CLAUDE.md`](config/CLAUDE.md). This repo is cloned at
`~/Spark1.55_Claude_Log` in WSL; Windows sessions reach it through
`\\wsl.localhost\Ubuntu-24.04\home\sadalir\Spark1.55_Claude_Log` and run git through `wsl`.

| Folder | Contents |
|---|---|
| `sessions/` | One `{YYYYMMDD-NNN}.jsonl` file per session. Each step has an *intent* entry (command, purpose, expected result) and a *result* entry (status, output, deviation). |
| `summaries/` | `{YYYY-MM-DD}_summary.md`: plain-English summaries of each day's sessions, written to be reused as SOP or guide drafts. |
| `anomalies/flagged.md` | Every step where the actual result differed from what was intended, with the resolution. This is the starting point for troubleshooting docs. |
| `config/` | Versioned copies of the Claude Code configuration that drives this logging. |

## Notes

- Logs are committed and pushed only to this repo; project repos are never touched.
- Secrets (passwords, tokens, keys, connection strings) are replaced with `[REDACTED]`.
- If the global `CLAUDE.md` changes, update `config/CLAUDE.md` (and the other environment's copy) to match.
