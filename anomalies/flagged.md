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

## 20261113-001 – step 1 – 2026-11-13
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada
- **Intended:** Run `find` with quoted glob patterns via `wsl -d Ubuntu-24.04 -- bash -c '...'` from PowerShell.
- **Actual:** `zsh:1: no matches found` — PowerShell dropped the inner quotes and the WSL login shell (zsh) expanded the globs.
- **Resolution:** Put the commands in a `.sh` file in the scratchpad and ran `wsl -d Ubuntu-24.04 -- bash /mnt/c/.../script.sh`.

## 20261113-001 – step 4 – 2026-11-13
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada
- **Intended:** Pipe the output of a PowerShell `for` loop into `Select-Object`.
- **Actual:** `ParserError: An empty pipe element is not allowed.` (PS 5.1 can't pipe directly from a `for` statement.)
- **Resolution:** Used a small `python3 -I` script to walk the UDT JSON instead.

## 20261113-003 – step 1 – 2026-11-13
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada
- **Intended:** Read `CLSS_Gateway_User_Manual.pdf` with the Read tool.
- **Actual:** `pdftoppm is not installed`. There was also no pdftotext or pypdf in WSL, and Windows `python` is only the Microsoft Store alias.
- **Resolution:** Made a throwaway venv at `/tmp/pdfvenv-claude` in WSL, installed `pypdf`, and extracted the text to the scratchpad.

## 20261113-003 – step 2 – 2026-11-13
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada
- **Intended:** Run inline Python through `wsl -- bash -c "..."` from PowerShell.
- **Actual:** PowerShell ParserError, because it parsed the commas and parentheses inside the double-quoted string.
- **Resolution:** Put the code in scratchpad `pdf.sh` / `pdf2txt.py` and ran them through `wsl -- bash <script>`. Lesson: always use script files for anything beyond a trivial one-liner.

## 20261116-001 – step 2 – 2026-11-16
- **Directory:** /home/sadalir/crusoe.mdc.scada
- **Intended:** `git checkout dev && git pull` after `docker compose down`.
- **Actual:** Checkout switched to dev but failed to rewrite/unlink many files ("Permission denied"); pull aborted because the half-updated files looked like local changes. Files were owned by uid 2003 (Ignition container user) since `IGNITION_UID/GID` are commented out in `.env`.
- **Resolution:** Pending user decision: chown repo back to 1000:1000 (sudo or docker), `git reset --hard HEAD`, set IGNITION_UID/GID=1000 in .env.

## 20261116-001 – step 3 – 2026-11-16
- **Directory:** /home/sadalir/crusoe.mdc.scada
- **Intended:** Recover via `docker run alpine chown -R 1000:1000` + `git reset --hard HEAD`.
- **Actual:** Denied by the permission classifier.
- **Resolution:** Handed back to user.

## 20261116-001 – step 4 – 2026-11-17
- **Directory:** /home/sadalir/crusoe.mdc.scada
- **Intended:** Retry ownership fix with `docker run alpine chown -R 1000:1000` after the user re-requested the task.
- **Actual:** Denied by the permission classifier again.
- **Resolution:** Asked the user to run `sudo chown` themselves.

## 20261118-004 – step 1 – 2026-11-18
- **Directory:** /home/sadalir/crusoe.mdc.scada
- **Intended:** Fetch origin/dev and pull (fast-forward 279c4355..4e4c13c1).
- **Actual:** Pre-check found 56 of 75 incoming paths unwritable: the files are owned by uid 2003 because the Ignition gateways chown the bind mount (`IGNITION_UID/GID` commented out in `.env`).
- **Resolution:** Held the pull so the working tree wasn't left half-updated. Asked the user to run `sudo chown -R sadalir:sadalir ~/crusoe.mdc.scada`, then pull.

## 20261118-008 – step 1 – 2026-11-18
- **Directory:** /home/sadalir/crusoe.mdc.scada
- **Intended:** Back up the historian from the running compose stack, read-only.
- **Actual:** The stack was not running (brought down in 20261118-007). The historian is the postgres-db service (volume `sny-edge_postgres-data`, which is intact).
- **Resolution:** Took no action. Asked the user whether to (a) take a cold, read-only file copy of the volume via a throwaway container, or (b) start the stack and pg_dump it.

## 20261120-001 – step 2 – 2026-11-20
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada
- **Intended:** Ping PLC 10.12.0.86 and Flex IO 10.11.0.84/.89 from laptop with secondary IPs 10.11.0.250/24 and 10.12.0.250/16 on the same unmanaged switch.
- **Actual:** All pings time out; ARP stays Incomplete for every device. Laptop routing is correct (on-link via Ethernet).
- **Resolution:** Diagnosed as devices not being at the expected IPs (Flex IO likely BOOTP/rotary-switch defaults) or link/power issue. Advised verifying with Wireshark or BOOTP-DHCP tool, checking link LEDs and module IP switches.

## 20261120-001 – step 3 – 2026-11-20
- **Directory:** \\wsl.localhost\Ubuntu-24.04\home\sadalir\crusoe.mdc.scada
- **Intended:** Capture packets with pktmon to identify what devices are broadcasting.
- **Actual:** Session not elevated; pktmon unavailable.
- **Resolution:** Used adapter counters instead (152 broadcasts/15s). Recommended user run Wireshark or the Rockwell BOOTP-DHCP tool to see device MACs and requests.
