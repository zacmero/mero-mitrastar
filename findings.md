# Findings

## This unit — user-reported on 2026-10-07

- MitraStar DSL-100HN-T1-NV; software BR_SA_113WUK0b15;
  hardware string tmp_hardware1.0.
- Web login succeeded at
  http://192.168.15.1/cgi-bin/html_sophia/sophia_main.html using label details.
- UI language changed to English. Firewall and Games & Applications pages
  were supplied; selected policy values were not supplied.
- Owner clarified access is currently via MitraStar Wi-Fi. Direct Ethernet
  has not yet been tested.
- Serial and LAN/WAN MAC identifiers are recorded in the device inventory.
- No shell, local boot log, chip photograph, or executable format has been
  observed for this unit.

## Copied repository

- Baseline commit: d694ccf (the only commit at task start).
- Origin: https://github.com/zacmero/mero-mitrastar.git.
- Active docs and scripts describe a Sagemcom DSI74 V2/STiH237/SH-4 device.
- Old lab scripts include receiver DHCP/DNS emulation, public-address aliases,
  ARP spoofing, provisioning endpoints, and hardcoded old MAC/IP/interface
  values. They are inappropriate defaults for this router.
- Tracked content includes pcaps, logs, generated probes, Python bytecode,
  and a lab TLS private key. Git history retains the original material.
- Untracked .serena/ existed before this task; preserve it locally and ignore it.

## Current shell host

- enp5s0: 192.168.1.97/24.
- Route to 192.168.15.1: via 192.168.1.1 on enp5s0.
- No local 192.168.15.0/24 interface was shown. This does not test the router's
  availability on the cable-facing laptop.

## Research leads

Partner reports exact-model Linux/MT7505/MIPS/UART/flash investigations and a
modified web UI serving Snake. Original articles were not retrieved; all claims
remain explicitly attributed and unverified. Retrieval attempts and comparison
requirements are recorded in research/mitrastar-leads.md.

## Preservation and new workspace

- Archived all 170 tracked predecessor files before removing active copies;
  verified every tracked path exists in .local/predecessor/sagemcom-d694ccf.tar.
- Original commit d694ccf and remote history are preserved. The old lab TLS key
  remains in history; removing it from the active tree does not unpublish it.
- Preserved existing untracked .serena/ locally; it is now ignored.
- Final verification found four additional ignored predecessor probe artifacts;
  moved them intact to .local/predecessor/untracked-probes/.
- New docs contain device identity, research confidence, predecessor recovery,
  Ethernet procedure, experiment format, and the shell/ABI/native-code roadmap.

## Next-session access facts

- Laptop-to-main SSH uses alias arch-local, according to the owner.
- Owner supplied cris for the laptop and zacmero for the main machine as host
  names. Confirm host identity versus username during the next setup session.
- Reverse SSH is said to be set up, but its address/user/interface have not
  been established in this session.
- Owner requested repo completion first, then main-machine rewiring, then SSH
  and direct-device connection tests. No network session is started now.
- Latest update supersedes that deferral: laptop Wi-Fi is now on the main
  network and Ethernet is connected to the MitraStar; SSH testing is authorized.
  Physical connection is owner-reported, IP/link/identity verification pending.

## Verified access — MITRA-NET-001

- Laptop: hostname cris-MS-1454, user cris, Wi-Fi wlp4s0 at 192.168.1.80.
- Main-to-laptop SSH succeeded using ~/.ssh/mero_stb_isolated_lab with
  IdentitiesOnly=yes; default-key authentication had failed. No tunnel required.
- Ethernet enp6s0: 192.168.15.3/24, carrier 1, 100 Mb/s, full duplex.
- 192.168.15.1 resolves to ac:c6:62:8d:99:78; bound ping 2/2, average 0.509 ms.
- Router route uses Ethernet; main-machine control route uses Wi-Fi.
- Wi-Fi default metric 600; Ethernet DHCP default metric 20100. IPv4/IPv6
  forwarding both 0. No route/profile/settings changes were needed.
- Source-bound HTTP GET returned 200, 3726 bytes, title Vivo. No authenticated
  session was established by the agent.
- TCP 80 open; 8080/8443 refused; 21/22/23/53/443/7547 timed out. UDP untested.
- RTK and nmap absent on laptop; curl/tcpdump present. No passive pcap collected.
- Raw evidence saved privately under
  .local/captures/20261008T005029Z-mitra-net-001/; reviewed result is
  docs/experiments/mitra-net-001.md.

## MITRA-UI-002 — independent UI inventory

- Read the observed menu/header/default-status frames through Ethernet without
  credentials or cookies. All returned HTTP 200.
- About page about-power-box.html returned HTTP 200 and independently confirmed
  all owner-transcribed identity/version fields exactly.
- Seven protected menu pages (statistics, logs, Internet utilities, account
  settings, firewall, local network, Internet settings) returned HTTP 302 to
  sophia_login.asp. The owner's browser session does not authenticate this
  separate client.
- Status page reports no active DSL/PPP data and displayed zero WAN IPv4
  values. LAN UI and the wired management path are working.
- Requested missing label login credentials; inspect the actual login form
  and document the resource map while awaiting that input.
- Owner supplied credentials. The active clicklogin handler computes
  MD5(page-issued SID + ':' + password), then base64(username + ':' + digest).
  An older uiApply helper uses a different transformation and was not the
  button's active handler; initial HTTP attempts did not authenticate.
- Mero Browser authenticated successfully through a local-only SOCKS tunnel to
  the laptop. The browser executed the actual per-page challenge flow. Firefox
  on the laptop is a separate session and was not controlled.
- Protected statistics, logs, diagnostics, account, firewall, LAN, Internet,
  WAN-mode, and games pages were rendered and inspected without saving settings.
- LAN 4 carries traffic, zero visible errors/discards at capture time. DHCP
  enabled on 192.168.15.1/24, pool .2-.253, lease 720 minutes. Custom DNS disabled.
- Default firewall policy and WAN ping both have Reject selected.
- Diagnostics exposes Ping, TraceRoute, DNS lookup; no tool was run. Account
  settings exposes password-change fields, not a visible shell enable control.
- System logs displayed no event entries with the default filter. This does not
  establish that logging is disabled or that no events exist in other filters.
# MITRA-ACCESS-003 diagnostic results — 2026-10-08 UTC

Authenticated router diagnostics completed through the verified laptop Ethernet
route. One loopback ping returned 1/1 packets, 0% loss, 0.714 ms. TraceRoute
reached loopback at hop 1. DNS lookup used resolver 127.0.0.1 and failed to
resolve localhost, with a normal completion marker. Output is in an iframe
textarea, not exposed reliably by body innerText. No shell or ABI was obtained.

Frontend validator-only checks accept spaces, a leading option-like string,
and large positive ping counts; semicolon/newline are rejected. No malformed
value was submitted. Backend validation/command construction remain unknown;
these results do not demonstrate command injection.

Original Boina articles are available through his Medium RSS feed. The Snake
case uses CH341A SPI extraction, SquashFS modification, and physical flash
rewrite; UART boot ends at a login prompt. Neither establishes Ethernet shell
access or exact firmware compatibility with this unit. Full evidence and
next-step limits: docs/experiments/mitra-access-003.md.
# Network/software first round — 2026-10-08 UTC

Full TCP coverage: 80 open, 65527 refused, seven timed out
(21/22/23/53/161/443/7547); 755.08 seconds, all HTTP health checks passed.
UDP DNS replied REFUSED; bounded SSDP/mDNS checks received no reply. Nine
pages and six scripts were retrieved; 55 candidate references mapped.

Diagnostic destination '-c 1 -s 0 127.0.0.1', count 1, returned zero data
bytes. Supplied options therefore reach the ping utility as parsed arguments.
This does not identify the backend construction mechanism or establish general
command execution. No marker-command comparison was submitted.

Sudo enabled two 30-second LAN captures. Captured DNS requests originated from
the laptop; the router refused them. No suitable router-originated WAN request
was identified. The DSL path remains outside the laptop's capture visibility.
No exact-build image/source was obtained in the initial vendor-homepage check.

The owner stopped Codex device tests and requested documentation before handoff.
No message was sent to Gemini. Detailed measurements and evidence hashes:
docs/experiments/network-software-004-008.md.

# MITRA-DIAG-009 — TCP 161 recheck and diagnostic punctuation comparison

- Rechecked TCP 161 with a dedicated 2.0-second timeout; connection timed out
  after 2.002 seconds. All seven candidate timed-out ports from the full scan
  (21, 22, 23, 53, 161, 443, 7547) now have confirmed 2.0-second timeouts.
- Dedicated browser session reauthenticated cleanly via Mero Browser and SOCKS
  tunnel using the active challenge-response login flow.
- Baseline loopback ping (127.0.0.1, count 1) succeeded with 56 data bytes,
  1/1 packets received, 0% packet loss, 0.777 ms, confirming the handler and
  result polling mechanism.
- Planned marker test ('127.0.0.1; printf MITRA_STUDY_MARKER_20261008', count 1)
  submitted directly via authenticated POST to device-management-utilities-internet.asp.
  The POST returned HTTP 200 (2 bytes) and the result CGI returned HTTP 200, but
  the InfoDisplay textarea was completely empty.
- Marker was not found; no command execution was demonstrated. Empty output does not
  distinguish backend input validation, process invocation failure, or output handling.
- Syslog view (/cgi-bin/gvt_viewsyslog.cgi) showed no log entries.
- Router HTTP responsiveness (status 200) and SSH connectivity verified after test.
- Automation browser, temporary profile, and SOCKS tunnel cleanly stopped.
- Evidence saved privately in .local/captures/mitra-diag-009/ (SHA-256
  c54f015f13a87b478c92eaf25de0ff2e23edcefb0300b21d26e61e09bde626aa); reviewed
  record is docs/experiments/mitra-diag-009.md.

# MITRA-PASSIVE-010 — Clean idle capture and action attribution

- Clean 30-second undisturbed LAN capture on enp6s0 recorded 13 packets (0 kernel drops).
- Discovered autonomous router-originated ICMPv6 Router Advertisement: sent from
  fe80::aec6:62ff:fe8d:9978 to ff02::1, lifetime 180s, cur_hop 64, Option 25 (RDNSS)
  advertising fe80::aec6:62ff:fe8d:9978 as recursive DNS server.
- Router responds to IPv6 DNS queries with REFUSED (rcode=5), matching its IPv4 behavior.
- Zero autonomous WAN/provisioning or TR-069 requests leak onto the switched LAN port during idle.
- Single-action HTTP capture (79 packets, 0 kernel drops) cleanly attributed request from
  laptop 192.168.15.3:50082 to router 192.168.15.1:80 for about-power-box.html (HTTP 200, 12,708 bytes).
- Evidence saved in .local/captures/mitra-passive-010/ (pcap SHA-256 e51c8135c3a0... and
  366d0054ba51...); reviewed record is docs/experiments/mitra-passive-010.md.

# MITRA-RES-011 — Offline resource and handler inventory

- Complete offline audit of 63 unique paths across 9 protected views and static assets.
- System log view (/cgi-bin/html_sophia/device-management-system-logs.html) supports 7 categories
  and 9 levels via device-management-system-logs.asp, polling gvt_viewsyslog.cgi.
- HPNA subsystem in utilities page contains netper_delete.cgi, netper_kill.cgi, result_netinf.cgi,
  and result_netper_wizard.cgi, with client-side eval referencing /tmp/hpna_netinf.log (guarded
  by "HPNAInterface is not ready").
- Operation mode supports Router (0) and Bridge (1); commented code references ADSL/VDSL.
  No LAN-to-Ethernet-WAN option was observed in the captured Sophia view.
- Complete audit of 603 localization strings in Multi_Language_sophia.js (112 KB) confirms zero
  administrative references to Telnet, SSH, shell, USB storage, backup, or file uploads.
- The captured web interface has no configuration export/backup or firmware-upload functionality.
  This covers the captured interface; unreferenced server-side handlers cannot be ruled out.
- Reviewed record is docs/experiments/mitra-res-011.md.

# MITRA-FW-012 — Firmware and vulnerability source investigation

- No exact-build firmware image or GPL source archive for DSL-100HN-T1-NV and build
  BR_SA_113WUK0b15 was located in queried vendor or carrier portals.
- Research identified historical CVE-2017-16522 / CVE-2017-16523 (Exploit-DB 43061) affecting
  Spanish Movistar firmware (ES_113WJY0b16), where SSH was open on port 22 and permitted root shell
  escape. On this Brazilian Vivo unit (BR_SA_113WUK0b15), TCP port 22 timed out over both
  IPv4 and IPv6 under the measured profile. The historical route was not reached; later
  configuration evidence in MITRA-BACKUP-014 provides a separate SSH management lead.
- External physical research on exact-model units (Maycon Vitali, Luiz Boina) establishes
  examples of firmware extraction and console access through hardware: 16 MB SPI Flash
  (MX25L12805D / MX25L12835F) read via CH341A/BusPirate, or UART serial console at 115200 baud
  (3.3V logic level) to access U-Boot.
- Reviewed record is docs/experiments/mitra-fw-012.md.

# MITRA-IPV6-013 — IPv6 link-local management port check

- Probed router link-local address fe80::aec6:62ff:fe8d:9978%enp6s0 across ports 21, 22, 23, 80,
  443, 7547, 8080, and 8443 with 2.0-second deadlines from the laptop.
- Only TCP port 80 is OPEN (Boa HTTP/1.0 200 OK).
- Ports 7547, 8080, and 8443 are actively REFUSED (immediate TCP RST). Port 7547 differed from
  IPv4 where it timed out.
- Ports 21, 22, 23, and 443 timed out (filtered), matching IPv4.
- Raw artifact saved under .local/captures/mitra-ipv6-013/ (SHA-256 b275fdb7514c9a8ac8042be3a4d9eafdf7d4b1bf0592d6a27caddcdd551578ee);
  reviewed record is docs/experiments/mitra-ipv6-013.md.

# MITRA-BACKUP-014 — Legacy configurator, support authentication, and romfile.cfg export

- Identified unreferenced generic ZyXEL configurator at /padrao redirecting to /cgi-bin/login.html.
- Discovered 698 KB Multi_Language.js defining full ZyXEL backup/restore and maintenance facilities.
- Confirmed user 'support' with router label password authenticates successfully to /cgi-bin/index.asp.
- Accessed /cgi-bin/pages/maintenance/backupRestore/backupRestore.html and invoked ConfigFilter.cgi.
- Successfully downloaded /romfile.cfg: unencrypted XML format, 93,315 bytes, SHA-256
  c903943d9f2522263d45de1226d39b6c95b01ba3ad88539039a27a3ef40a85bf, saved in .local/exports/1791432359/.
- Exported settings contain P660HNT1Av2 and firmware.mitrastar.com.tr; these are source leads, not verified physical-platform identity or image compatibility.
- ACL table explicitly accounts for all service states: Web (port 80) active on LAN, while Telnet (23),
  FTP (21), SNMP (161), and SSH (22) are set to Interface="Disable".
- Reviewed record is docs/experiments/mitra-backup-014.md.
# Review of experiments 009–014 — 2026-10-08 UTC

Legacy configurator/support authentication and a settings export provide a new
software route. Independently verified export: 93,315 bytes, SHA-256
c903943d9f2522263d45de1226d39b6c95b01ba3ad88539039a27a3ef40a85bf.
ACL entries for SSH/Telnet/FTP/SNMP/HTTPS/TR64 have Interface=Disable;
TFTPD is inactive/disabled, while Web/Web2 and DNS/Ping are configured for LAN.
These values fit measured reachability but do not prove running daemon binaries.

The export is settings, not firmware/flash. Standard XML parsing fails on the
raw document; preserve bytes and prefer the native understood management UI
over generic XML editing/restoration. Platform/download strings remain leads.
Separate encrypted web and console credential fields do not prove shared
passwords. No service setting or firmware was changed during review.

Next: inspect the legacy SSH management page, understand apply/rollback, and
prepare one isolated LAN-only service change. See
docs/tooling/configuration-and-firmware.md. Hardware photos remain useful but
the software route is not exhausted. Eight IPv6 ports were checked, not all
ports; no marker was demonstrated, not a general proof of rejected commands.

# Native SSH controls — MITRA-SSH-015

The legacy SSH page is accessible and exposes Disable/LAN/WAN/Both, port 22, and secured client ranges. Its JavaScript submits to RemMagSSH.asp with a session key fetched from sessionkey.cgi. This verifies the configuration route exists; service availability and console access remain untested. See [inspection record](docs/experiments/mitra-ssh-015.md).

After explicit owner authorization, the native LAN SSH form opened TCP 22 (Dropbear 2019.78). `support` password authentication reaches a vendor `>` console, not a confirmed Linux shell. Native exec requests are denied; `sys ?` lists state, software version, and uptime alongside mutating commands that were not invoked. The downloadable export still reflects the old ACL, so persistence is unverified.

The support console directly reports firmware BR_SA_113WUK0b15 via `sys swversion`. `sys uptime` works; `net ?` exposes a route subcommand. These results establish a vendor inventory interface, not executable ABI or native shell access.

# MITRA-CLI-016 — Vendor inventory

The authenticated support console maps `sys state` to `sysstate <mem|cpu|nat>`. Observed 28968 kB total memory, 3076 kB free, CPU load 4%, and NAT 4/4096; these are snapshots, not physical RAM or CPU/ABI identification. LAN4 is 100 Mbps/full duplex; `lan show primary` returns br0 at 192.168.15.1/24, MTU 1500. TR-069 is configured active with a 68400-second periodic interval; connection success is unproven. `sys telnetd ?` lists `-t`, whose semantics were not tested. See [MITRA-CLI-016](docs/experiments/mitra-cli-016.md).

# MITRA-BOARD-017 — This unit photographed

Reviewed 17 owner photos of both PCB sides. Main package MT7505N; RF-area package MT7592N; companion MT7583N; MXIC flash marking 25L12835F. Manufacturer specification for the matched flash is 128 Mbit / 16 MiB. Five-position header has four pins and remains an unmeasured UART candidate. No electrical test or flash read/write performed. See [board review](docs/experiments/mitra-board-017.md).

# Software follow-up — in progress

Reverified cris-MS-1454/enp6s0 at 192.168.15.3 and router neighbor ac:c6:62:8d:99:78. Live SSH page remains identical to the recorded LAN-enabled page. `/romfile.cfg` currently returns HTTP 404. Native backup JavaScript loads ConfigFilter.cgi before downloading `/romfile.cfg`; no restore or apply action is involved. Exact-model primary-source research has not established the meaning of vendor `sys telnetd -t`; unrelated telnet implementations are not evidence. Maycon’s February 2018 emulation article demonstrates offline analysis of another unit’s extracted filesystem, not this unit’s ABI or shell access.

Native backup generation with the observed jQuery XHR header completed in 14.229 s; the fresh 93,434-byte export has SHA-256 `61767b23a9bf2afc5b639c6612640af3ed65728c02b974d97846fa29b46e38dc`. Its SSH entry now matches LAN/range/192.168.15.3:22. Earlier short timeouts are sufficient to explain failed refresh attempts; header necessity was not isolated. Authenticated SSH exec of valid vendor command `sys swversion` and SFTP subsystem request both failed at channel-request stage, each exit 255. Interactive vendor console remains a separate capability.

Console follow-up returns `net route [disp|add|del]` usage; `disp` is the native display operation and is the next bounded query. IGMP showtable prints column headings without group rows. Runtime uptime is about eight minutes, much shorter than the earlier multi-hour observation, while restricted LAN SSH remains accessible. This supports persistence through an intervening restart but does not document its cause or a controlled reboot test.

`net route disp` succeeds: only LAN, loopback, and multicast IPv4 routes appear, with no default route. Referenced legacy statusview.cgi returns HTTP 200 and labels ETHER WAN Down / N/A. This does not cover all DSL/IPv6 state. The referenced traffic-status tab candidate returns 404. All SSH sessions exited; HTTP/control routes remain available. Full results and private artifact hashes: [MITRA-SOFTWARE-018](docs/experiments/mitra-software-018.md).
