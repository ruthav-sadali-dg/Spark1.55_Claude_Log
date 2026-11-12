# Flagged Anomalies

Steps where the actual result deviated from the stated intent.

## 20261111-001 – step 1 – 2026-11-11
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada (Claude Code on Windows)
- **Intended:** Clone the reference and target repos with `git clone` from PowerShell.
- **Actual:** `git : The term 'git' is not recognized`. Git is installed only inside WSL, not on the Windows PATH.
- **Resolution:** Ran git through WSL (`wsl -d Ubuntu-24.04 -- git ...`). The global CLAUDE.md now tells Windows sessions to do the same. To fix permanently, install Git for Windows (`winget install Git.Git`).

## 20261111-001 – step 2 – 2026-11-11
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada
- **Intended:** Run a multi-command `bash -lc "..."` one-liner through `wsl` from PowerShell to read files and check git config.
- **Actual:** Output came back mostly empty. PowerShell 5.1 mangled the nested quotes and `$` variables before bash received them.
- **Resolution:** Wrote the commands to a `.sh` file and ran `wsl -d Ubuntu-24.04 -- bash /path/script.sh`, or read files directly through the `\\wsl.localhost\...` path. **Tip:** avoid complex inline bash from PowerShell; use a script file.

## 20261112-001 – step 1 – 2026-11-12
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada (Claude Code on Windows)
- **Intended:** Read the FAT Tracker .xlsx with Python + openpyxl.
- **Actual:** Windows `python` is only the Microsoft Store alias (not installed). In WSL, `openpyxl` is missing. Also, `|` characters in an argument passed through `wsl -- python3 ...` were run as shell pipes by zsh.
- **Resolution:** Parsed the xlsx with the standard library only (`zipfile` + `xml.etree`; an .xlsx is a zip of XML). Passed search terms comma-separated instead of with `|`. To fix permanently: `sudo apt install python3-openpyxl` in WSL.

## 20261112-001 – step 3 – 2026-11-12
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada
- **Intended:** View drawing PDF E-340-01366 pages directly.
- **Actual:** `pdftoppm is not installed` (poppler-utils missing).
- **Resolution:** Created a temp venv (`python3 -m venv /tmp/pdfvenv && /tmp/pdfvenv/bin/pip install pypdf pymupdf`) and used PyMuPDF to extract text and render each page to PNG. To fix permanently: `sudo apt install poppler-utils`.
