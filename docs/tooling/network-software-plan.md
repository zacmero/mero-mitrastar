# Network and software investigation sequence

Owner authorized this sequence on 2026-10-07, after the diagnostic baseline.
Goal: exhaust evidence-led network/software access before moving to hardware.
No shell, arbitrary command execution, or exact ABI is established yet.

## Common procedure

1. Recheck laptop SSH, device-facing source address, route, and neighbor MAC.
2. State one question and bounded test parameters before each experiment.
3. Record timestamps, results, private artifact hashes, and negative findings.
4. Verify router responsiveness and SSH control connectivity after the test.
5. Stop on unexpected loss of responsiveness. Do not infer absence from a timeout.

Do not use credential guessing, persistent service activation, firewall changes,
firmware writes, resets, or bootloader changes in this sequence. Those operations
need a separate explicit instruction. Do not replay predecessor IPs or endpoints.

## 1. MITRA-SERVICES-004 — service discovery

- TCP-connect scan of ports 1–65535 on the verified router only. Cap starts at
  100 connections/second and 64 pending connects, with a 0.5-second timeout.
  Classify open/refused/timeout separately; recheck candidate ports with a longer
  timeout before drawing conclusions. Monitor HTTP responsiveness between batches.
- Read banners only from open ports; use protocol-specific read-only requests
  when the protocol is identified. Do not run generic exploit or brute-force scans.
- Small UDP checks: DNS, SSDP discovery, and mDNS queries. Use non-mutating
  protocol messages with deadlines. Inspect DHCP information already present on
  the laptop. SNMP inspection needs an observed enabled service and credentials;
  do not guess community strings.
- Exit: full TCP coverage with limitations, bounded UDP results, service map,
  and a justified next protocol test for every responding service.

## 2. MITRA-WEB-005 — authenticated resource map

- Authenticate using the observed login flow. Inventory references in pages the
  UI actually exposes, including scripts, frames, forms, and handlers.
- Fetch referenced static resources and inspect them offline for maintenance,
  export, remote-management, firmware-check, and diagnostic controls.
- Separate referenced paths from successfully retrieved handlers. Do not submit
  unknown forms or GET endpoints that might invoke an action.
- Keep raw pages and credentials private. Publish redacted path/capability maps.
- Exit: observed resource map, relevant client-side code, and explicit gaps.

## 3. MITRA-DIAG-006 — diagnostic backend behavior

- Retain the completed Ping/TraceRoute/DNS baseline from MITRA-ACCESS-003.
- Compare a small set of harmless, local input variations, one at a time, with
  bounded count and deadline. Verify the exact field values before submission.
- Determine which checks occur server-side and whether arguments are passed as
  literal destinations or parsed as options. Do not submit large counts or
  long-running destinations. Frontend acceptance does not establish injection.
- Prefer inspecting handler source from an exact-build firmware if available.
- Exit: characterized validation/argument behavior, or a precise unresolved gap;
  claim execution only with direct router-side evidence.

## 4. MITRA-PASSIVE-007 — outbound activity and interception feasibility

- Capture a bounded idle interval and individual ordinary UI actions on enp6s0.
  Verify capture privileges without changing the host's privilege configuration.
- Record which traffic is visible, names/paths contacted, action timing, and any
  blind spots. Avoid capturing unrelated household traffic.
- If a router-originated request is observed, identify how its response is used
  before proposing an isolated service emulator with a harmless marker response.
- Do not assume the old receiver's provisioning or browser runtime exists here.
- Exit: request/response evidence or a documented visibility limitation, followed
  by a concrete topology proposal if WAN observation is needed.

## Solving the WAN visibility limitation

A laptop plugged into a LAN port is not automatically on the router's WAN path.
Its capture normally sees traffic delivered to that port, not all switched LAN
traffic or the router's DSL uplink. Promiscuous mode alone does not fix this.

Choose a method after reading this unit's WAN-mode controls and physical ports:

1. If a supported Ethernet WAN mode exists, place the lab laptop or a dedicated
   gateway upstream of that WAN port on an isolated segment. Capture on that
   gateway, TAP, or mirrored switch port. Enabling a different WAN mode is a
   separate configuration change and is not currently authorized.
2. If this unit's uplink is DSL only, an ordinary Ethernet TAP cannot capture the
   DSL link. Use a controlled compatible DSL/DSLAM environment or an appropriate
   ISP-side capture point if available; do not improvise with LAN bridging.
3. If a genuine router shell becomes available, capture on its actual WAN/PPP
   interface using an available tool, or inspect read-only connection/log data.
   Account for captures containing ISP credentials and personal traffic.
4. Existing supported packet-capture/export controls could provide a software
   alternative. Verify they exist and understand their effect before use.

DNS substitution only helps if we control the resolver the router itself uses
and the response is accepted. HTTPS validation, pinned destinations, signed
payloads, or a non-rendering protocol may prevent the GVT technique from applying.
Never override household DNS or redirect ISP traffic as an exploratory shortcut.

## 5. MITRA-FW-008 — exact firmware analysis

- Seek an official firmware image, GPL source release, or understood read-only
  export for this model and build. Record origin, version, and hash before analysis.
- Inspect filesystems, Boa handlers, diagnostic command construction, startup
  scripts, enabled management services, and ELF ABI offline.
- Distinguish another unit's image from this build and this physical device.
  Do not copy Boina's published offsets or flash-chip assumptions.
- Exit: relevant exact-build implementation evidence, or a documented source gap.
  Flash writing remains outside this sequence.

## Status

| Stage | Status | Experiment |
| --- | --- | --- |
| Services | Full IPv4 scan, UDP checks, timeout rechecks, and eight IPv6 ports measured; only HTTP reached among TCP ports tested | [MITRA-SERVICES-004](../experiments/network-software-004-008.md), [MITRA-DIAG-009](../experiments/mitra-diag-009.md), [MITRA-IPV6-013](../experiments/mitra-ipv6-013.md) |
| Web resources | 63 Sophia paths audited; legacy configurator at `/padrao` discovered; `romfile.cfg` exported | [MITRA-WEB-005](../experiments/network-software-004-008.md), [MITRA-RES-011](../experiments/mitra-res-011.md), [MITRA-BACKUP-014](../experiments/mitra-backup-014.md) |
| Diagnostic backend | Supplied ping options parsed; command punctuation (`;`) yielded empty output (no execution demonstrated) | [MITRA-DIAG-006](../experiments/network-software-004-008.md), [MITRA-DIAG-009](../experiments/mitra-diag-009.md) |
| Outbound capture | Bounded samples complete; IPv6 RA/RDNSS observed; no suitable provisioning request in the observed intervals | [MITRA-PASSIVE-007](../experiments/network-software-004-008.md), [MITRA-PASSIVE-010](../experiments/mitra-passive-010.md) |
| Exact firmware | No exact-build image/source acquired; settings export is not firmware | [MITRA-FW-008](../experiments/network-software-004-008.md), [MITRA-FW-012](../experiments/mitra-fw-012.md), [MITRA-BACKUP-014](../experiments/mitra-backup-014.md) |
| Configuration route | Legacy support interface and disabled service entries discovered; SSH controls/apply behavior not yet inspected | [Configuration/firmware decision](configuration-and-firmware.md) |

The Sophia map did not describe the complete interface: a legacy configurator
and working configuration export were subsequently found. Prioritize reading
the legacy SSH management view and preparing a single LAN-only change with
rollback. No service activation or firmware write was submitted. Board
photographs remain useful; software possibilities have not been exhausted.
