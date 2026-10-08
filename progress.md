# Progress

## 2026-10-07 — recognize and reset

- Read the copied repository inventory, baseline commit, device overview,
  direct-Ethernet methodology, and old lab runner/relay defaults.
- Inspected local interface addresses and the route to the management IP.
- Requested the cable-facing machine's SSH identity and interface.
- Created persistent planning and evidence notes.
- Documented source retrieval limitations without promoting partner claims to
  local facts.
- Archived all 170 predecessor files and verified archive completeness before
  retiring them from the active tree. Preserved Git history and local agent state.
- Added the MitraStar device inventory, roadmap, Ethernet runbook, research
  status, predecessor recovery/methods, experiment format, and ignore rules.
- Owner clarified Wi-Fi access and requested deferring SSH/device tests until
  after repo completion and main-machine rewiring.
- Next: final local documentation, archive, and Git hygiene checks.
- First final check found three untracked bytecode files and an old USB pcap;
  preserved them under .local/predecessor/untracked-probes rather than deleting
  them. Rechecking the resulting workspace.
- Recorded cris / zacmero as owner-supplied host labels; login/destination
  mapping is deferred to the next SSH session.
- Local verification passed: documentation links/formatting, Git diff whitespace,
  ignore rules, absence of old binary/capture/key files from the active tree,
  and byte-for-byte archive comparison for all 170 tracked predecessor files.
  Four additional ignored artifacts were preserved separately. Archive mode 0600.
- Repo correction phase complete locally. Changes have not been committed/pushed.
- Owner then authorized SSH testing now: laptop Wi-Fi is on the main network,
  Ethernet is newly connected to the MitraStar. Beginning host/access discovery.
- Confirmed cris is the laptop login user. The default-key SSH attempt failed;
  the existing dedicated mero_stb_isolated_lab key succeeded. Laptop hostname
  cris-MS-1454, destination 192.168.1.80; no tunnel required.
- Verified device-facing enp6s0/192.168.15.3, expected router MAC, 100 Mb/s full
  duplex, two ping replies, separate Wi-Fi control route, and disabled forwarding.
- Collected source-bound unauthenticated HTTP response and nine TCP connect
  checks. Saved raw evidence privately and documented MITRA-NET-001.
- Updated identity, README, roadmap, and Ethernet instructions with the observed
  topology and the absence of RTK/nmap on the laptop. No network settings changed.
- Final consistency check passed: 13 active files, valid local documentation
  links, clean whitespace, no predecessor generated/secret files in the active
  tree, and matching network artifact hashes. All requested work and authorized
  connection checks are complete. Git changes remain local and uncommitted;
  shell access remains unverified.

## MITRA-UI-002 — requested management inventory

- Owner reports the reset was committed/pushed; Git working tree is clean.
- Existing unauthenticated response is a frameset. It loads sophia_menu.html,
  sophia_header.html, and gvt_info.html by default. Begin with these observed
  resources through the laptop's Ethernet link.
- Rechecked the Ethernet route and neighbor identity before read-only requests.
- Retrieved About and seven protected menu destinations. About independently
  matched every reported identity field; protected destinations redirected to
  the login page. No credentials/cookies were sent and no forms were submitted.
- Requested the router login credentials, absent from the conversation. Raw
  responses are local ignored files with mode 0600.
- Owner supplied login credentials; used transient stdin with terminal echo
  disabled, never embedded credentials in commands or tracked files.
- Corrected an HTTP helper import; no login had occurred before that error.
  Its initial password transformation was wrong because it came from an inactive
  UI helper. Two form submissions and two read-only Basic-auth checks did not
  establish access. No password guessing occurred.
- Switched to Mero Browser, dedicated headless Chromium and a local-only SOCKS
  SSH tunnel. Followed the active challenge-based login handler. Native DOM
  focus and verified field population resolved iframe coordinate issues;
  authenticated statistics and nine protected pages were then inspected.
- Continuing with reset-page inventory and Wi-Fi state only; no device settings
  or diagnostic tests are submitted.
- Authenticated inventory complete. Confirmed DHCP details, Reject firewall/WAN
  ping selections, Wi-Fi security/radio state, empty default log view, diagnostics
  choices, and reboot/reset controls. SSH/Telnet strings are application
  forwarding presets, not local shell controls.
- Wrote MITRA-UI-002 and updated README, inventory, and roadmap. Two combined
  documentation patches failed context checks without applying; resolved by
  applying the new record and known updates separately.
- Redacted session-key/SID assignments in retained rendered snapshots, preserving
  original and stored-content hashes. Credentials remain absent from tracked
  files; no diagnostic or settings operation was submitted.
- Closed the dedicated investigation Chromium and daemon, stopped only the
  matching local SOCKS SSH tunnel, removed its temporary browser profile, and
  removed the transient helper from the laptop. Ports 1088/9228 have no listeners.
- Final checks passed: documentation links/formatting, Git whitespace, nine
  authenticated rendered snapshots, artifact SHA-256, and credential-leak scan
  of all 14 public workspace files. No matches for the supplied password or
  its simple digest/authentication encoding. User's Firefox was not controlled.
- MITRA-UI-002 complete. Next is diagnostic/session handler and exact-firmware
  research toward shell access. Changes are local and uncommitted.

## MITRA-ACCESS-003 — diagnostic handlers

- Owner authorized the diagnostic route and asked to retain the Snake firmware
  precedent. It remains recorded in research/mitrastar-leads.md; original URLs
  requested for direct verification.
- Began extracting actual diagnostic validation and request-construction code.
- Web search response decoder still fails; do not treat reported Snake details
  as independently retrieved evidence.
# MITRA-ACCESS-003 — 2026-10-08 UTC

- Pulled source-index commit 076d8fb, preserving local UI documentation.
- Reauthenticated the dedicated browser. The helper's statistics-only success
  assertion failed because login restored the diagnostics page; inspected the
  actual authenticated page before continuing.
- Initial laptop SSH banner timeouts resolved; authenticated SSH subsequently
  confirmed cris-MS-1454/enp6s0/192.168.15.3 and the expected router neighbor MAC.
- Completed one loopback ping, one loopback traceroute, and one localhost DNS
  lookup. No malformed payload, configuration change, or flash operation ran.
- Corrected result extraction to read the iframe textarea. Duplicate TEST IDs
  and an input replacement check stopped early attempts before submission;
  verified the visible controls and exact destinations before running tests.
- Retrieved Boina's primary articles through Medium RSS; confirmed the Snake
  method requires SPI firmware read/write and does not supply a network shell.
- Wrote MITRA-ACCESS-003 and updated README/roadmap/plan/findings. Shell access
  remains open. Local evidence stays ignored; repository changes are uncommitted.
- Closed the dedicated browser/daemon/tunnel and removed its temporary profile.
  Artifact hashes, result assertions, local links, and git diff checks passed.
# Network/software first round — 2026-10-08 UTC

- Recorded all five stages, limits, exit criteria, and WAN visibility alternatives.
- Completed TCP ports 1–65535 with rate/concurrency caps and passing health
  checks; performed bounded UDP discovery and longer selected-port rechecks.
- Retrieved nine pages and six scripts; mapped 55 candidate references without
  launching unknown/performance/configuration operations. USB view returned 404.
- Restored authenticated access with a fresh login and disabled cache; index
  arrival alone had not verified a protected page. Session expiry later required
  another fresh login. Recorded the limitation without asserting a cause.
- Ran four single-packet local Ping variants. Zero-byte output confirmed supplied
  option parsing. Early native input verification failures caused no submission.
- Used owner-supplied sudo authentication via hidden stdin for two bounded LAN
  captures. No credentials were placed in command arguments or tracked files.
- Performed an initial official firmware-source check; no exact-build image found.
- Owner stopped Codex tests and requested a Gemini handoff, then requested
  documentation first. Identified the idle Gemini tab but sent no message.
  A credential handoff helper was cancelled before receiving any credentials;
  no credential FIFO was created. Removed the unused helper and handoff draft.
- Wrote first-round report with artifact hashes; updated roadmap, README,
  findings, plan, and progress. No marker-command comparison, shell, persistent
  router change, commit, or push occurred during this follow-up round.
- Verified local links, seven artifact hashes, scan totals, diagnostic output,
  and absence of the unused credential FIFO. Both supplied-secret scans passed
  across 17 public workspace files. Git diff whitespace checks passed.
- Closed the dedicated automation browser/daemon/SOCKS tunnel and removed its
  temporary profile. The owner's Firefox session was not closed or logged out.
# Complete handoff preparation — 2026-10-08 UTC

- Owner requested HANDOFF.md and commit/push of the reviewed first round.
- Wrote the self-contained handoff: topology, SSH/browser restoration, confirmed
  measurements, exact next diagnostic comparison, remaining five-stage work,
  interpretation criteria, WAN visibility, recovery boundaries, and cleanup.
- Stored owner-supplied credentials in ignored .local/handoff/credentials.json
  with mode 0600 in a mode-0700 directory, using hidden stdin. Values are absent
  from tracked documents and command arguments. This is local-only handoff data.
- Added README entry and linked first-round documentation. The owner will
  instruct Gemini directly; no agent message or additional device test was sent.
# MITRA-DIAG-009 — TCP 161 and diagnostic comparison — 2026-10-08 UTC

- Verified host topology and router neighbor MAC over Wi-Fi SSH.
- Rechecked TCP port 161 with a 2.0-second deadline; connection timed out at 2.002s.
- Started local SOCKS SSH tunnel on port 1088 and headless Chromium on port 9228.
- Authenticated browser session using active challenge-response flow with
  credentials from ignored local handoff record.
- Executed baseline loopback ping via authenticated POST to confirm handler.
- Submitted planned bounded comparison ('127.0.0.1; printf MITRA_STUDY_MARKER_20261008')
  with session key. POST and result CGI returned HTTP 200, but InfoDisplay textarea
  was empty; marker was not found and command execution was not demonstrated.
- Inspected syslog endpoint; no entries observed.
- Cleanly terminated dedicated Chromium, removed temporary browser profile, and
  stopped SOCKS tunnel. Verified ports 1088 and 9228 closed.
- Verified router HTTP 200 responsiveness and laptop SSH connectivity.
- Wrote docs/experiments/mitra-diag-009.md and saved private evidence under
  .local/captures/mitra-diag-009/ with SHA-256 hash.
# MITRA-PASSIVE-010 — Passive captures and action attribution — 2026-10-08 UTC

- Collected undisturbed 30-second LAN capture on enp6s0 using bounded sudo tcpdump.
- Parsed packet flow: identified autonomous ICMPv6 Router Advertisement with RDNSS
  pointing to fe80::aec6:62ff:fe8d:9978; confirmed router acts as IPv6 DNS forwarder.
- Verified that no autonomous WAN traffic leaks onto the LAN Ethernet link during idle.
- Captured single HTTP GET transaction for about-power-box.html with 79 packets,
  attributing request/response flow.
- Saved raw pcaps and metadata under .local/captures/mitra-passive-010/; wrote
  docs/experiments/mitra-passive-010.md with artifact hashes.

# MITRA-RES-011 — Offline resource map and handler inventory — 2026-10-08 UTC

- Completed systematic offline audit of 63 unique paths across 9 protected views.
- Verified absence of backup/export and firmware upload controls in the UI.
- Audited 603 localization strings in Multi_Language_sophia.js; confirmed zero
  shell/Telnet/SSH/USB configuration strings.
- Wrote docs/experiments/mitra-res-011.md.

# MITRA-FW-012 — Firmware and vulnerability source research — 2026-10-08 UTC

- Searched vendor and carrier sources; no exact-build firmware or GPL release located.
- Reviewed CVE-2017-16522 (Exploit-DB 43061), reported for Spanish Movistar firmware
  with reachable SSH. Port 22 timed out on this unit over IPv4 and IPv6; applicability
  to this build is unproven. MITRA-BACKUP-014 later identified an SSH configuration lead.
- Correlated external physical research demonstrating SPI flash extraction and UART
  console interaction; those examples do not establish that hardware is our only route.
- Wrote docs/experiments/mitra-fw-012.md.

# MITRA-IPV6-013 — IPv6 link-local management port check — 2026-10-08 UTC

- Probed router link-local address fe80::aec6:62ff:fe8d:9978%enp6s0 across ports 21, 22,
  23, 80, 443, 7547, 8080, and 8443 with 2.0s timeouts.
- Confirmed only TCP port 80 is OPEN (Boa HTTP/1.0 200 OK).
- Discovered port 7547 is actively REFUSED (TCP RST), unlike IPv4 where it timed out.
  Ports 8080 and 8443 also REFUSED; ports 21, 22, 23, and 443 timed out (filtered).
- Saved artifact in .local/captures/mitra-ipv6-013/; wrote docs/experiments/mitra-ipv6-013.md.

# MITRA-BACKUP-014 — Legacy configurator and romfile.cfg export — 2026-10-08 UTC

- Probed /padrao and discovered unreferenced generic ZyXEL configurator at /cgi-bin/login.html.
- Identified 698 KB Multi_Language.js with full maintenance and backup functionality.
- Authenticated user 'support' with label password via /cgi-bin/index.asp challenge-response.
- Invoked ConfigFilter.cgi and successfully exported /romfile.cfg (93,315 bytes, SHA-256 c903943d...).
- Inspected vendor configuration fields: found P660HNT1Av2 as a source lead and the internal ACL table explicitly
  disabling Telnet (23), FTP (21), SNMP (161), and SSH (22) on LAN.
- Saved export in .local/exports/1791432359/; wrote docs/experiments/mitra-backup-014.md.
# Review and publication — 2026-10-08 UTC

- Read experiments 009–014 and the updated status documents. Verified all six
  referenced artifact hashes and independently inspected allowlisted export
  ACL/account metadata without printing credential values.
- Corrected scope and inference: IPv6 covered eight ports; empty marker output
  demonstrates no execution; Sophia absence claims exclude the legacy interface;
  service configuration is not proof of running daemons; settings are not firmware.
- Recorded native-configuration-first route and firmware prerequisites. No
  live device test, service activation, restore, or firmware write was performed.
- The other agent switched the shared checkout to romfile-analysis and committed
  its work. Created a separate master worktree at mero-mitrastar-docs and imported
  only reviewed documentation from 53b46e0. Its raw research/romfile.cfg is not
  included in this documentation publication; the other branch was left intact.
- Updated handoff and roadmap to the legacy SSH configuration lead. The owner
  requested committing/pushing these reviewed discoveries on the current docs route.

# Branch reconciliation — 2026-10-08

- Merged `romfile-analysis` into `master`, retaining reviewed experiment corrections.
- Reconciled configuration analysis: daemon presence, boot hooks, parser integrity, console credentials, and restore recovery remain unverified.
- Removed the raw export from the merged tree; earlier branch history still contains it. Original private evidence remains available locally.
- Verified laptop `cris-MS-1454`, Ethernet `enp6s0` at `192.168.15.3/24`, route to `192.168.15.1`, and router neighbor `ac:c6:62:8d:99:78` before read-only SSH-page inspection.

# MITRA-SSH-015 — 2026-10-08

- Completed authenticated read-only inspection of native SSH page, submission script, and session-key endpoint.
- Confirmed port 22, Disable selected, LAN option, and four client IP range pairs.
- Prepared restricted LAN proposal and Disable rollback; requested explicit policy-change authorization under AGENTS.md. No settings submitted.
- Record: [MITRA-SSH-015](docs/experiments/mitra-ssh-015.md).

- Owner authorized restricted LAN SSH. Native form applied successfully; live readback shows LAN, port 22, range restricted to laptop. Dropbear 2019.78 banner observed.
- `support` authenticated and reached an interactive vendor console; `id` rejected, `?` and `sys ?` list vendor commands. Exec requests denied; Linux shell not established.
- Downloaded export still contains pre-change ACL; export freshness and reboot persistence remain unresolved. No reset, reboot, save, or firmware write performed.

- Console `sys swversion` directly confirms BR_SA_113WUK0b15; `sys uptime` and `net ?` queried successfully. Sessions closed normally; authorized LAN SSH remains enabled.

# MITRA-CLI-016 — 2026-10-08

- Reverified laptop/interface/router identity and completed five bounded support-console sessions using help and display commands.
- Discovered native `sys state mem/cpu/nat`; collected LAN, TR-069, Wi-Fi, and DMZ status.
- Preserved private captures and recorded SHA-256 values; credential-bearing output is excluded from public summaries.
- No settings or services changed; no reset, reboot, save, or firmware write. All console sessions ended.
- Remaining: determine telnetd flag/console handler implementation; Linux shell, ABI, and SSH persistence remain unverified.

# Workspace consolidation and MITRA-BOARD-017

- Owner requested one organized project. Verified the temporary docs worktree was clean at the same commit as canonical master, contained no remaining photos/local evidence, and removed it without force.
- Canonical checkout is now solely /home/zacmero/projects/mero-mitrastar.
- Organized all 17 originals under board_pictures/originals, verified unchanged hashes, and generated metadata-stripped JPEG review copies with a manifest/index.
- Visually identified MT7505N, MT7592N, MT7583N and MXIC 25L12835F markings; checked flash density against the manufacturer datasheet. Documented the populated header as a candidate, not a verified pinout.
- No device/network test or persistent router change performed.

# Resumed software investigation

Owner requested continued software tests after board identification. Starting with topology, export freshness, and SSH/console implementation evidence. Existing raw evidence and originals remain in ignored local storage; no additional worktree created.

Completed MITRA-SOFTWARE-018: native backup generation at a 20-second bound completed in 14.229 seconds; new 93,434-byte export matches restricted LAN SSH. Authenticated SSH exec of valid vendor command and SFTP subsystem requests were rejected. Interactive console identified `net route disp`; its IPv4 table has no default route. Referenced statusview.cgi labels ETHER WAN Down/N/A. Traffic-status tab candidate returned 404. Uptime is about eight minutes with retained SSH, consistent with an unobserved restart, not a controlled persistence test. Primary searches acquired no exact-build image or telnetd flag semantics. Captures remain private, sessions exited, no settings changed. Documentation and artifact hashes updated in one repo.

Local inspection errors: two stale documentation paths did not exist and were replaced with the actual inventory; a multi-file tail invocation failed and was replaced by direct reads. No router effect. Initial romfile HTTP 404 was resolved by native generation; traffic-status-tab 404 remains a scoped negative result.

# MITRA-SOFTWARE-019 started

Owner directed proceeding after 018. Reverified cris-MS-1454/enp6s0 192.168.15.3 and ac:c6:62:8d:99:78. Continuing referenced status/log views and implementation research with existing native login, without changing router state.

External code-search connector failures: proposed vendor_cmd.c path was missing and parallel import failed with a server-side clone/config-lock error. Switched to direct GitHub tree API inspection. No clone into a second workspace and no device effect.

MITRA-SOFTWARE-019 complete. Corrected static /pages/... tab resolution and fetched working legacy logs and WAN/LAN/NAT display handlers. Current native LAN2 100 Mbps/full duplex matches counters; earlier LAN4 is historical. No log messages or populated WAN row displayed; aggregate WAN packets zero; laptop web session count 20 and later console usage 30/4096 are different snapshots/metrics. Menu-referenced firmware upload form and managed-status iframe return 200; both managed fields render 0, backend acceptance not tested. Remote-management tabs lack Telnet/FTP; master General enabled. Related EN751221 SDK source differs from this console and Dropbear build; no matching handler/image acquired. Sessions exited and HTTP/control connectivity remained usable. Reviewed summaries/hashes only; private captures remain ignored.
