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
