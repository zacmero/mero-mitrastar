# Canonical workspace update

Use `/home/zacmero/projects/mero-mitrastar` on `master`. The temporary
`mero-mitrastar-docs` worktree was removed; older references to it below are
historical. All 17 board photos are inside `board_pictures/`: original HEICs
in ignored `originals/`, metadata-stripped JPEGs in `reviewed/`, and a hash
manifest/index. Read [MITRA-BOARD-017](docs/experiments/mitra-board-017.md).
MT7505N and MXIC 25L12835F markings are now observed on this unit. Header pinout,
voltage, flash contents, runtime ABI, and Linux shell remain unverified.

# MitraStar study handoff to Gemini

## Latest update — read before the original instructions

Gemini completed experiments 009–014. The semicolon comparison yielded no
marker, eight IPv6 ports were checked, and the legacy configurator was found at
`/padrao`. User `support` authenticated and exported `romfile.cfg`. The export
contains disabled SSH/Telnet/FTP/SNMP/HTTPS/TR64 access entries; it is not a
firmware image or proof of running daemons. Do not repeat the original marker
test merely because the older instructions below call it pending.

The next lead is the legacy SSH management page and an understood LAN-only
configuration change, not mandatory firmware rewriting. Read
[MITRA-BACKUP-014](docs/experiments/mitra-backup-014.md) and the
[configuration/firmware decision](docs/tooling/configuration-and-firmware.md).
Inspect controls first; no service activation, restore, or flash write is
authorized by the documentation-publication request.

The original session-restoration instructions below describe the earlier
handoff. Recheck local listeners and current sessions before launching helpers;
the other agent may have its own active session. Its ongoing analysis branch is
`romfile-analysis`; this reviewed documentation is published separately on master.
Private artifacts remain in the original workspace's `.local` directories.

## Original handoff — retained as dated context

Prepared 2026-10-08 UTC. Workspace: `/home/zacmero/projects/mero-mitrastar`.
The owner will give Gemini this file directly. No task was sent through Herdr.
Codex has stopped device testing and will interpret subsequent results.

## Mission and authorization

Study the owner's MitraStar through Ethernet and software before deciding
whether hardware access is necessary. Obtain genuine router-side execution,
measure the runtime/ABI, and eventually run a tiny compatible program in RAM.
The owner authorized the five-stage network/software sequence and bounded sudo
captures. Ownership and this authorization are established in the conversation.

Read [AGENTS.md](AGENTS.md) and follow its RTK and evidence rules. All
main-machine shell commands must start with `rtk`; `rtk proxy` preserves raw
output. The laptop has no RTK or nmap, so native commands go inside the remote
payload of an RTK-prefixed SSH command. The cloud SSH helper is not the
procedure for this local laptop.

Persistent router changes, firmware/bootloader writes, resets, mode changes,
service activation, and household-network redirection need separate explicit
instructions. Continue the authorized diagnostic/read-only study without
asking again for ownership or permission already supplied. Do not guess
credentials, invoke unknown state-changing handlers, or run disruptive tests.

You share this workspace with the owner and other agents. Preserve their
edits, current documentation, and private evidence. Own subsequent experiments
and their summaries; update findings/progress/plan as results arrive. Explain
measurements like a teacher: question, observation, implication, and limit.

## Read these first

1. [First-round results](docs/experiments/network-software-004-008.md): exact
   measurements, interpretations, evidence hashes, and unresolved questions.
2. [Five-stage plan](docs/tooling/network-software-plan.md): scope, bounds, exit
   criteria, and WAN visibility alternatives.
3. [Diagnostic baseline](docs/experiments/mitra-access-003.md) and
   [authenticated UI inventory](docs/experiments/mitra-ui-002.md).
4. [Device inventory](docs/hardware/device.md), [roadmap](ROADMAP.md),
   [research sources](research/mitrastar-leads.md), and [Ethernet runbook](docs/tooling/ethernet-lab.md).
5. [Predecessor provenance](docs/reference/predecessor.md) before borrowing any
   Sagemcom/GVT method. Its hardware, IPs, endpoints, and firmware are not defaults
   for this router.

## Device and access route

| Item | Last verified value |
| --- | --- |
| Router | MitraStar DSL-100HN-T1-NV |
| Software / UI hardware string | BR_SA_113WUK0b15 / tmp_hardware1.0 |
| Router LAN IP / MAC | 192.168.15.1 / ac:c6:62:8d:99:78 |
| Management URL | http://192.168.15.1/cgi-bin/html_sophia/sophia_main.html |
| Router login user / language | admin / English |
| Main machine user / last LAN IP | zacmero / 192.168.1.97 |
| Laptop hostname / login | cris-MS-1454 / cris |
| Laptop Wi-Fi control path | wlp4s0, 192.168.1.80/24 |
| Laptop device-facing Ethernet | enp6s0, 192.168.15.3/24, 100 Mb/s full duplex |
| Main-to-laptop key | /home/zacmero/.ssh/mero_stb_isolated_lab |
| Owner's laptop-to-main alias | arch-local; it is not the reverse SSH destination |

Do not use mero-oz, ozagent, or unrelated SSH entries. Recheck current topology
before target traffic; addresses can change. Stop and identify the device if
the router neighbor MAC differs. Preserve the Wi-Fi SSH control route.

```bash
rtk proxy ssh -i /home/zacmero/.ssh/mero_stb_isolated_lab \
  -o IdentitiesOnly=yes -o BatchMode=yes -o ConnectTimeout=10 \
  -o StrictHostKeyChecking=yes cris@192.168.1.80 \
  'hostname; ip -4 addr show enp6s0; ip route get 192.168.15.1; ip neigh show 192.168.15.1 dev enp6s0'
```

## Private credential and artifact availability

In this same workspace, `.local/handoff/credentials.json` is an ignored private
handoff record, mode 0600 inside a mode-0700 directory. It contains
`router_username`, `router_password`, and `laptop_sudo_password`, already supplied
by the owner. Read it inside a process and pass passwords through stdin. Do not
print it, include values in command arguments/environment variables, or copy it
into tracked files or prompts. Do not change sudoers or save passwords in SSH
configuration. This record is not pushed to GitHub.

The SSH private key, local helpers, raw captures, and credential record exist
only on the current machine. A fresh clone does not contain them. This handoff
is ready for the Gemini agent in the same Herdr workspace; elsewhere, those
prerequisites must be supplied securely by the owner.

Private evidence directories:

- `.local/captures/20261008T010056Z-mitra-ui-002/`
- `.local/captures/mitra-access-003/`
- `.local/captures/mitra-services-004/`
- `.local/captures/mitra-web-005/`

The first-round report lists the important SHA-256 values. Raw HTTP/pcap may
contain session tokens, authentication data, and personal details. Keep it
private; publish reviewed summaries and hashes only.

## What is already established

| Stage | Result | Remaining limit |
| --- | --- | --- |
| Full TCP scan | All 65535 ports; only 80 open; 65527 refused; seven timeouts | Timeouts do not prove absence; TCP 161 needs longer recheck |
| UDP checks | DNS replied REFUSED; no reply to bounded SSDP/mDNS queries | External DNS and all discovery behavior remain unknown |
| Web resources | Nine pages, six scripts, 55 candidate references | Client-side references are not server-side implementation |
| Diagnostics | Four one-packet loopback variants completed | General command handling and shell access remain unproven |
| Passive capture | Two 30-second LAN captures, zero kernel drops | No suitable router-originated WAN request identified |
| Firmware | Initial official-homepage check; primary author articles retained | No exact-build image/source acquired |

TCP timeouts: 21, 22, 23, 53, 161, 443, 7547. Two-second rechecks confirmed
21/22/23/53/443/7547; TCP 8443 refused immediately. Do not repeat the full scan
without new evidence. Its settings were 100 starts/s maximum, 64 pending
connects, 0.5-second timeout, and HTTP health checks between 2048-port batches.

The most useful result: destination `-c 1 -s 0 127.0.0.1`, count 1, produced
**0 data bytes**, while normal loopback produced 56. Supplied option text
therefore affects ping arguments. This could reflect shell-string construction
or other tokenization; it does not distinguish them. No command separator or
constant-text marker test has been submitted. Do not describe a shell as found.

The referenced USB page returned 404. Client source contains network-performance
functions and result/start/stop/delete references. None of those operations
were launched. The visible operation-mode choices are Router and Bridge;
they do not establish Ethernet WAN support. No new visible firmware-upload,
backup, or shell control was found in the inspected views.

## Restore the browser session when needed

At handoff, the dedicated browser, daemon, SOCKS tunnel, and temporary profile
are **closed/removed**. No scan or capture remains running. The owner's Firefox
was not closed or logged out. Use Mero Browser rather than bypassing its wrapper.
Read `/home/zacmero/projects/mero-browser/SKILL.md` first.

Start each long-running command in its own managed terminal/session:

```bash
rtk proxy ssh -i /home/zacmero/.ssh/mero_stb_isolated_lab \
  -o IdentitiesOnly=yes -o BatchMode=yes -o StrictHostKeyChecking=yes \
  -o ExitOnForwardFailure=yes -D 127.0.0.1:1088 -N cris@192.168.1.80
```

```bash
rtk proxy env MERO_BROWSER_CDP_PORT=9228 \
  MERO_BROWSER_PROFILE_DIR=/home/zacmero/projects/mero-mitrastar/.local/browser-profile \
  /home/zacmero/projects/mero-browser/bin/mero-browser-headless-chromium \
  --proxy-server=socks5://127.0.0.1:1088 about:blank
```

The installed wrapper accepts Python snippets on stdin, not `-c`:

```bash
rtk proxy env BH_RECORD=0 BU_CDP_URL=http://127.0.0.1:9228 BU_NAME=mitrastar-ui \
  /home/zacmero/projects/mero-browser/bin/mero-browser <<'PY'
new_tab('http://192.168.15.1/cgi-bin/html_sophia/sophia_login.html')
cdp('Network.enable')
cdp('Network.setCacheDisabled', cacheDisabled=True)
PY
```

Local `.local/scripts/mitra_login_native.py` accepts JSON with `username` and
`password` on stdin. It performs native field focus/input and clicks the real
login control. Supply its stdin from the private record inside Python, without
printing values. The active login uses a page-issued SID in the MD5 input;
the older inactive password-only helper is unsuitable.

Observed active token formula: `base64(username + ':' + MD5(SID + ':' + password))`,
submitted by the real form to `sophia_index.asp?<token>`. Preserve the current
form/session context; do not use a copied SID or log the token.

Do not treat an index/blank page as confirmed authentication. Wait for a real
protected frame and verify its actual path and controls. `basefrm` holds the
view and `menufrm` provides parent functions required by diagnostics. Sessions
expire; get a fresh page/session key when necessary. Cause of earlier login
failures was not isolated, even though fresh login with cache disabled worked.

## Diagnostic protocol and exact next comparison

Use the observed authenticated handler, not guessed CGI paths:

```text
POST /cgi-bin/html_sophia/device-management-utilities-internet.asp
sessionKey=<current authenticated page key>
wanPVCFlag=<current page value; observed 0>
PINGACT=1
PingformSaveFlag=1
pingIPAddr=<destination under test>
pingNUM=1
```

Read `/cgi-bin/html_sophia/device-management-utilities-internet.cgi`. Output
lives in the `InfoDisplay` textarea; body innerText can miss it. The key was
available as page global `gblsessionKey` or hidden `session_gblsessionKey`.
Keep it out of printed request metadata. Parse response HTML in a detached
document if needed; do not dump full responses into agent output.

Next question: does the backend reject command punctuation independently of
the frontend? The frontend rejects semicolon/newline but accepts spaces and
leading options. One **planned, not yet run** bounded comparison is:

```text
pingIPAddr=127.0.0.1; printf MITRA_STUDY_MARKER_20261008
pingNUM=1
```

Send it once through the same authenticated POST, leaving other fields as
above. It asks for a single local ping and a constant text marker if a command
separator is interpreted. It requests no persistent setting/file/program change.
Use a deadline, record pre-test output and the actual post-test response, and
check responsiveness afterward. Do not infer execution from HTTP 200, an
echoed request, or an unchanged/stale textarea. Stop to interpret rejection,
inconclusive output, or a returned marker before choosing another case.

If direct router-side execution is demonstrated, the next evidence is a
bounded read-only inventory: `id`, `uname -a`, `/proc/cpuinfo`, `/proc/meminfo`,
`/proc/mtd`, and `mount`. Do not dump environment variables, credentials, or
unbounded logs. Distinguish execution through a diagnostic handler from an
interactive shell. Establish transfer/storage/ABI before compiling or running
any custom binary. No reverse connection, service activation, or flash write
is required to answer the marker question.

## Remaining experiments in order

1. Verify topology/authentication and read existing artifacts. Recheck TCP 161
   with a two-second deadline. No SNMP community guessing.
2. Finish the bounded diagnostic comparison above and interpret it precisely.
3. Inspect source-referenced read-only views/handlers offline first. Prioritize
   logs, result formats, service/configuration information, and any understood
   export path. Do not launch hidden performance tests or unknown actions.
4. Capture a fresh idle interval without the completed port scan, then one
   ordinary status/diagnostic action at a time. Attribute requests by direction.
5. Seek official legacy support/GPL or an understood exact-build export;
   hash/version-check and analyze offline. Inspect Boa/diagnostic handlers,
   startup/service configuration, and ELF requirements if an image is obtained.

For captures, sudo authentication is available in the private record. Pass it
to `sudo -S` through stdin, never as a command argument. Use enp6s0 and a
30–60-second deadline; save stdout pcap bytes to ignored local storage. The
earlier timeout exit 124 meant the planned capture deadline, not capture
failure. Verify packet counts/drops and that the process stopped.

Local helper inventory: `mitra_services_004.py`, `mitra_udp_004.py`,
`mitra_login_native.py`, `pcap_http_metadata.py`, and `check_secret_hygiene.py`
under `.local/scripts/`. They are available here but not tracked. The older
`mitra_ui_inventory.py` HTTP login approach did not establish protected access;
prefer the actual browser flow. Read helper code before reusing it.

## GVT precedent and WAN visibility

The old Sagemcom TV receiver fetched `/portal.svg` and executed supplied
ECMAScript in its own Ekioh runtime. That was device-side script execution.
MitraStar management-page JavaScript normally runs on the laptop; a custom
page alone does not establish router CPU execution.

Luiz Boina's verified Snake article used CH341A SPI extraction, modified
SquashFS/Boa portal files, rebuilt the filesystem, and wrote flash physically.
It is a firmware-modification precedent, not an Ethernet shell recipe. His
UART report reached a login prompt. Do not assume those units' chip markings,
image offsets, firmware, or credentials apply here.

To reproduce an interception technique, first identify a request the router
itself makes and how it consumes the response. Current LAN samples do not
show a suitable request. Promiscuous mode cannot reveal the router's DSL uplink.
Possible later visibility routes are a supported controlled Ethernet WAN
topology, a compatible controlled DSL/DSLAM path, router-side capture after
execution access, or an existing supported capture/export facility. None is
established or configured here. Router/Bridge mode alone is insufficient.
DNS substitution also requires control of the router's own resolver and an
accepted response; HTTPS or signed payloads may change feasibility.

## Finish each experiment

Use the [experiment format](docs/experiments/README.md). Record exact scope,
timestamps, output, private artifact hashes, interpretation, unresolved limits,
and cleanup. Verify SSH control and router responsiveness. Close only the
browser/tunnel/processes created for the experiment; do not log out or close
the owner's Firefox. Remove temporary browser profiles after closing Chromium.

The owner has authorized committing/pushing this handoff and first-round
documentation. Later experimental changes need their own commit/push instruction.
Raw evidence and the private credential record must remain ignored. Do not
rewrite predecessor history. Report the next smallest justified test instead
of claiming all software/network possibilities have been exhausted.

# Latest SSH route — MITRA-SSH-015

Branches merged into master at `2d70fd1`. Owner subsequently authorized native restricted LAN SSH. Live page shows LAN / port 22 / client range 192.168.15.3 only; Dropbear 2019.78 responds. `support` credentials yield a vendor console; remote exec requests are denied. Read [MITRA-SSH-015](docs/experiments/mitra-ssh-015.md) before continuing. Do not invoke console save, reset, or reboot to test persistence without a corresponding owner instruction. That round downloaded an old ACL snapshot; MITRA-SOFTWARE-018 below resolves freshness. Board photos are now archived in MITRA-BOARD-017.

# Latest vendor console inventory — MITRA-CLI-016

Read [MITRA-CLI-016](docs/experiments/mitra-cli-016.md). `sys state mem`, `sys state cpu`, and `sys state nat` work. The telnetd branch lists `-t` without semantics; it was not invoked. TR-069/Wi-Fi display output contains secrets, so capture privately before review. Native shell and ABI remain unknown. All sessions closed; no router changes beyond the earlier authorized SSH setting.

# Latest software follow-up — MITRA-SOFTWARE-018

Read [MITRA-SOFTWARE-018](docs/experiments/mitra-software-018.md). Native backup generation completed in 14.229 seconds with the observed XHR headers; then `/romfile.cfg` returned a fresh 93,434-byte export matching restricted LAN SSH. Allow a bounded 20-second generation request; a direct download alone does not refresh the file. Raw artifact stays ignored. This is settings, not firmware.

Authenticated SSH exec of the valid vendor command `sys swversion` and SFTP subsystem requests were rejected. Interactive support console remains usable. `net route disp` displays IPv4 routes and currently has no default route. `sys uptime` reports about eight minutes despite retained LAN SSH; consistent with persistence through an unobserved restart, not a controlled reboot test. No router setting was changed in this round.

The referenced status view labels ETHER WAN Down/N/A, consistent with the IPv4 route snapshot. Next software questions: inspect remaining referenced read-only status/log views; obtain matching firmware/handler implementation before interpreting `sys telnetd -t`. Avoid another undirected port scan. Hardware measurements remain the independent path to UART/flash evidence. Do not invoke service-start flags, reset, reboot, or flash operations as part of a read-only round.

# Latest status/implementation round — MITRA-SOFTWARE-019

Read [MITRA-SOFTWARE-019](docs/experiments/mitra-software-019.md). Static tab JSON lives under /pages/...; dynamic display pages live under /cgi-bin/pages/.... The traffic-tab 404 in 018 used the wrong static path and is corrected. Working legacy syslog has no message rows; WAN frames have no populated connection and zero aggregate packets; NAT counts do not prove Internet access. Current native port is LAN2, 100 Mbps/full duplex; LAN4 is the earlier session's observation.

The referenced firewareUpgrade.html (vendor spelling) exposes a native multipart upload form for this build. Both Upgrade_Managed/upgradesManaged fields render 0, but no upgrade check, upload, acceptance test, or firmware acquisition occurred. Do not treat the form as a recovery guarantee. Referenced remote-management tabs expose no Telnet/FTP activation form; General is a master control, not a Telnet switch.

Public EN751221 SDK was inspected as comparison source. Its console set and Dropbear 0.52 differ from our device; it does not establish telnet flag semantics or explain our server's rejected requests. Next concrete access evidence: UART pin/voltage measurements and passive serial output after compatibility is established, or verified matching firmware/source for offline handler analysis. No unexplained service flag or guessed upload is part of the read-only route.
