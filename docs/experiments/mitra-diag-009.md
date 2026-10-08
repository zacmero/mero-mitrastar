# MITRA-DIAG-009 — TCP 161 recheck and diagnostic command-punctuation comparison

Date/time: 2026-10-08T03:11:35Z (lab timezone: 2026-10-08 00:11:35 -0300)
Device: MitraStar DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`
Question:
1. Does TCP 161 respond within an extended 2.0-second connection deadline?
2. Does the backend diagnostic handler execute command punctuation (`127.0.0.1; printf MITRA_STUDY_MARKER_20261008`) or reject it independently of the frontend?

Host and interfaces: Laptop `cris-MS-1454`, Wi-Fi `wlp4s0` at `192.168.1.80/24` (control route), Ethernet `enp6s0` at `192.168.15.3/24` (device-facing route, 100 Mb/s full duplex).
Control route: SSH key `/home/zacmero/.ssh/mero_stb_isolated_lab` to `cris@192.168.1.80`.
Target IP and observed MAC: `192.168.15.1`, `ac:c6:62:8d:99:78`.
Preconditions:
- Topology and MAC verified before traffic.
- Authenticated browser session established via Mero Browser through local SOCKS tunnel (`127.0.0.1:1088`).
- Router responsiveness verified before testing.

## Procedure

1. Rechecked TCP port 161 with a dedicated 2.0-second connection deadline from source address `192.168.15.3` on `enp6s0`.
2. Started dedicated headless Chromium on port 9228 with profile `.local/browser-profile` via the SOCKS tunnel.
3. Authenticated into the management interface using the active challenge-response flow (`clicklogin()`).
4. Navigated `basefrm` to `/cgi-bin/html_sophia/device-management-utilities-internet.html` and retrieved the active session key.
5. Executed positive baseline check: submitted `pingIPAddr=127.0.0.1`, `pingNUM=1`, `PINGACT=1`, `PingformSaveFlag=1`, `wanPVCFlag=0` to `/cgi-bin/html_sophia/device-management-utilities-internet.asp` and polled `/cgi-bin/html_sophia/device-management-utilities-internet.cgi?1`.
6. Executed planned comparison: submitted `pingIPAddr=127.0.0.1; printf MITRA_STUDY_MARKER_20261008`, `pingNUM=1`, `PINGACT=1`, `PingformSaveFlag=1`, `wanPVCFlag=0` to `/cgi-bin/html_sophia/device-management-utilities-internet.asp` and polled `/cgi-bin/html_sophia/device-management-utilities-internet.cgi?1`.
7. Inspected `/cgi-bin/gvt_viewsyslog.cgi` for potential server log entries.
8. Verified post-test HTTP responsiveness (`GET /cgi-bin/html_sophia/sophia_main.html`) and SSH link.
9. Stopped automation processes, removed temporary browser profile, and verified port closure.

## Observations

1. TCP 161 recheck timed out after 2.002 seconds. With this check, all seven ports that timed out in the full scan (21, 22, 23, 53, 161, 443, 7547) have confirmed 2.0-second timeouts.
2. Baseline loopback ping (`127.0.0.1`):
   - POST returned HTTP 200.
   - Result CGI returned HTTP 200.
   - `InfoDisplay` textarea contained standard output:
     ```text
     PING 127.0.0.1 (127.0.0.1): 56 data bytes
     64 bytes from 127.0.0.1: seq=0 ttl=64 time=0.777 ms

     --- 127.0.0.1 ping statistics ---
     1 packets transmitted, 1 packets received, 0% packet loss
     round-trip min/avg/max = 0.777/0.777/0.777 ms
     --PING Test Fin--
     ```
3. Command-punctuation comparison (`127.0.0.1; printf MITRA_STUDY_MARKER_20261008`):
   - POST returned HTTP 200 (body length 2 bytes).
   - Result CGI returned HTTP 200.
   - `InfoDisplay` textarea was completely empty (`""`).
   - Marker `MITRA_STUDY_MARKER_20261008` was **not found**.
   - No ping statistics, no ping header, no error text, and no completion footer (`--PING Test Fin--`) were generated.
4. Syslog view (`/cgi-bin/gvt_viewsyslog.cgi`) contained no log messages.

## Artifacts

Raw artifact stored under `.local/captures/mitra-diag-009/`:
- `diagnostic-punctuation-comparison.json` (1983 bytes, SHA-256 `c54f015f13a87b478c92eaf25de0ff2e23edcefb0300b21d26e61e09bde626aa`). Session tokens and passwords are redacted.

## Result and limits

- Negative for demonstrated command execution via semicolon command separator. Empty diagnostic output means no execution was demonstrated.
- This result does not prove the backend explicitly rejected the semicolon: backend validation rejecting the string, process invocation failure, or output redirection/handling issues could each explain the empty textarea.
- General command execution and shell access remain unproven.


## Changes and cleanup

- Terminated headless Chromium (task-72) and SOCKS SSH tunnel (task-66).
- Removed temporary profile directory `.local/browser-profile`.
- Confirmed ports 1088 and 9228 have no active listeners.
- Verified router HTTP responsiveness (status 200) and laptop SSH connectivity over Wi-Fi.

## Next decision

Proceed to offline inspection of source-referenced read-only views and handlers (Stage 3 in the handoff sequence), focusing on log handling, result formats, configuration metadata, and investigating exact-build firmware/GPL sources to determine backend CGI implementation directly.
