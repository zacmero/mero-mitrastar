# MitraStar investigation roadmap

## Objective and current boundary

Obtain a genuine router shell, identify the running binary ABI, and execute a
tiny program from verified writable temporary storage. Firmware modification
is a later branch with separate recovery requirements.

Confirmed by the owner's UI transcription: model DSL-100HN-T1-NV, firmware
BR_SA_113WUK0b15, management login and English UI. Direct Ethernet and reverse
SSH are now verified. A bounded TCP baseline and unauthenticated HTTP response
were collected. Linux details and shell access are pending.
See [device inventory](docs/hardware/device.md) and
[research status](research/mitrastar-leads.md).

## Phase 0 — recognition and repository reset

- Record this unit's identity and the original partner leads.
- Preserve the copied project at commit `d694ccf` and in a local archive.
- Replace Sagemcom docs/scripts/assets with MitraStar-specific guidance.
- Ignore raw evidence, credentials, build output, bytecode, and agent caches.

Exit: a clean active workspace with traceable predecessor methods and no old
target defaults. This phase is implemented; local verification is recorded in
`progress.md`.

## Phase 1 — MITRA-NET-001: Ethernet baseline

Completed: reverse SSH, 100 Mb/s full-duplex link, correct neighbor MAC, bound
ping, HTTP response, and short TCP baseline. See
[the result](docs/experiments/mitra-net-001.md). No passive pcap was needed for
this connectivity result; it remains a separate follow-up option.

1. Verify reverse SSH to `cris@192.168.1.80` and inventory the interfaces and
   routes again. `arch-local` is the known laptop-to-main alias.
2. Connect laptop Ethernet to a MitraStar LAN port; preserve Wi-Fi initially.
3. Check link, address, route, and neighbor MAC against `ac:c6:62:8d:99:78`.
4. Fetch a read-only management response; optionally capture a bounded session.
5. Check a short list of TCP ports on the verified target.

Exit: reproducible access to `192.168.15.1` over the cable, documented control
connectivity, and a bounded service baseline. Follow
[the Ethernet runbook](docs/tooling/ethernet-lab.md).

## Phase 2 — MITRA-UI-002: firmware and management surface

Completed: identity independently confirmed; authenticated statistics, logs,
diagnostics, account, firewall, LAN, PPPoE, WAN-mode, games, reset, and Wi-Fi
pages inspected. See [MITRA-UI-002](docs/experiments/mitra-ui-002.md). No visible
shell control or backup/firmware-upload control was found. Next is Phase 3.

Record read-only pages for identity, status, diagnostics, management services,
logs, and backup/export capabilities. Inspect resources the UI actually loads
to identify page paths and request structure. Export configuration only through
an understood backup operation; keep the export private and hash it. Do not
restore/import configuration or submit firewall/port-forwarding changes.

Compare the observed firmware and endpoints with independently retrieved
exact-model sources. Document existing Telnet/SSH/diagnostic controls if exposed;
do not assume the partner's firmware has identical access paths.

Exit: a map of observed management capabilities and a justified shell-access
candidate, or a documented absence of a usable network route.

## Phase 3 — MITRA-ACCESS-003: obtain a shell

The owner authorized the methodical [network/software sequence](docs/tooling/network-software-plan.md):
service discovery, authenticated resource mapping, diagnostic argument handling,
outbound capture/interception feasibility, and exact firmware analysis. Follow
that sequence before deciding whether the hardware branch is necessary.

[The first round](docs/experiments/network-software-004-008.md) completed full
TCP coverage, initial UDP checks, authenticated reference mapping, four bounded
Ping variants, and two LAN captures. Follow-up experiments [MITRA-DIAG-009](docs/experiments/mitra-diag-009.md),
[MITRA-PASSIVE-010](docs/experiments/mitra-passive-010.md), and [MITRA-RES-011](docs/experiments/mitra-res-011.md)
added these measurements and discoveries:

- TCP 161 confirmed timeout at 2.0s;
- Command punctuation (';') yielded empty output (no execution demonstrated);
- Undisturbed 30s capture identified autonomous IPv6 Router Advertisements with RDNSS;
- 63 UI paths and 603 localization strings audited in Sophia interface;
- Stage 5 ([MITRA-FW-012](docs/experiments/mitra-fw-012.md)) recorded a firmware-source gap and historical CVE comparison; exact-build implementation remains unavailable;
- [MITRA-IPV6-013](docs/experiments/mitra-ipv6-013.md) found port 80 open among eight tested link-local IPv6 ports, port 7547 refused, and ports 21, 22, 23, and 443 timed out;
- [MITRA-BACKUP-014](docs/experiments/mitra-backup-014.md) discovered `/padrao`, authenticated `support`, exported settings, and found SSH/Telnet/FTP/SNMP/HTTPS/TR64 interfaces configured as disabled.

The software route remains open. Prioritize [MITRA-SSH-015](docs/tooling/configuration-and-firmware.md):
read the legacy SSH management view and prepare a single LAN-only change with
rollback before any apply. No service change or firmware write has been submitted.
The settings export is not a firmware backup; running daemons and privileges
remain unconfirmed. Board photographs can independently document hardware.
Diagnostic baseline completed; see [the result](docs/experiments/mitra-access-003.md).
Ping and TraceRoute reached loopback; DNS lookup completed with an unresolved
local name. Frontend validation was inspected without submitting malformed
values. Server-side argument handling is unknown and no shell was obtained.
The verified Snake precedent used physical SPI read/write, not network access.

Use an observed management service and owner-provided credentials when it
offers a shell. An open port or successful web login does not establish shell
authorization, privilege level, or executable access. Record the prompt and
minimal read-only identity output.

If network evidence does not yield an understood route, request motherboard
photos of both sides with power disconnected. Compare this PCB with verified
exact-model references. Identify ground and logic levels before a UART
connection; use a compatible TTL adapter, not RS-232. Do not connect its power
lead to the router. Treat 115200 baud as a reported starting candidate.

Read UART boot output first. A Linux login prompt may still require credentials;
bootloader access alone does not imply a running Linux shell. SPI reading is
conditional on identifying the chip, electrical setup, and backup method.

Exit: a genuine shell or a precisely documented access barrier and evidence
needed to resolve it. Hardware photos are requested only when this branch is
needed.

## Phase 4 — MITRA-ABI-004: runtime inventory

After shell access, collect:

```bash
uname -a
id
cat /proc/cpuinfo
cat /proc/meminfo
cat /proc/mtd
mount
df -h
```

These are **router-shell commands**, not host terminal commands. RTK may not
exist in vendor firmware; invoke host commands through `rtk` and send these as
remote command payloads, or enter them directly at the device console.

Record available BusyBox applets, storage capacity, filesystem permissions, and
mount flags. Verify that the chosen temporary location is writable and allows
execution before using it. Copy an existing ELF binary via an observed transfer
method; inspect it offline with host `file`/`readelf` through `rtk proxy`.

Exit: known byte order, ISA/ABI, libc/interpreter, kernel requirements, transfer
method, privilege level, and temporary executable storage. Do not choose a
cross-compiler from the word “MIPS” alone.

## Phase 5 — MITRA-EXEC-005: tiny native program in RAM

Build the smallest C program compatible with the measured ABI. Initially print
a unique marker and exit successfully. Transfer it to the verified temporary
location, compare hashes if supported, run it, and capture stdout/exit status.
Remove it after verification and record any limitations.

Exit: the router CPU executes our native program. A page served by the router
and executed by a laptop browser does not satisfy this milestone.

A second program can report uptime or answer a bounded network request once
the execution baseline works. LED control comes later, only after discovering
actual controls and their side effects.

## Phase 6 — optional persistence and recovery

Consider persistence or modified firmware only after obtaining repeated,
matching flash reads/backups, a verified partition map, image layout/checksums,
and a recovery path for this exact unit. Establish how original firmware and
settings can be restored before a write. A partner's successful Snake firmware
does not establish compatibility with this board/version.

Flash writes, factory resets, bootloader changes, and other persistent device
changes require an explicit instruction for that operation. No such operation
is part of the repository-reset session.

MITRA-SSH-015 has now reached the authenticated support vendor console through the native LAN SSH setting. Next: documented read-only console inventory, then determine whether a supported Linux-shell route exists. Configuration export freshness and persistence remain open. Owner plans powered-off board photographs tomorrow for the archive.

MITRA-CLI-016 completes an initial read-only console map. Native memory, CPU-load, NAT-usage, LAN, Wi-Fi, and TR-069 queries work. Next is understanding exact-build command handlers, especially the unexplained telnetd flag, while preparing the independent board-photo/UART route. A Linux shell remains the next programming prerequisite.

Board photographs are now archived and reviewed in [MITRA-BOARD-017](docs/experiments/mitra-board-017.md). Next hardware step: unpowered ground continuity, then suitable logic-voltage/activity measurement to identify the candidate serial pins. Maintain the software handler research route. Use only the canonical mero-mitrastar checkout; the temporary docs worktree is removed.
