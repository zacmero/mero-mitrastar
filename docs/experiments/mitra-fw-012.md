# MITRA-FW-012 — Exact firmware source search, vulnerability analysis, and hardware boundary

Date/time: 2026-10-08T03:30:00Z (lab timezone: 2026-10-08 00:30:00 -0300)
Device: MitraStar DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`
Scope: Stage 5 methodical investigation of firmware distribution, public GPL availability, historical vulnerability reports, and feasibility of remote software execution.

## Questions

1. Does an official vendor GPL archive or carrier firmware image exist publicly for `DSL-100HN-T1-NV` and build `BR_SA_113WUK0b15`?
2. Does historical CVE research for this router model (e.g., CVE-2017-16522 / CVE-2017-16523) provide an applicable network attack vector against the observed service configuration?
3. Have all five software/network investigation stages been exhausted, formally justifying the hardware access branch (UART/SPI)?

## Method

1. Agent-reported vendor/carrier queries for GPL sources and firmware. This report does not retain a URL inventory or query log sufficient to independently establish search coverage; a global absence claim is not justified.
2. Technical evaluation of known CVEs against the port and service profile established in MITRA-SERVICES-004 and MITRA-DIAG-009.
3. Synthesis of findings across all five stages of the network/software sequence ([network-software-plan.md](../tooling/network-software-plan.md)).

## Observations

### 1. Firmware Availability and GPL Source Status
- No exact-build firmware image or GPL source archive for model `DSL-100HN-T1-NV` and build `BR_SA_113WUK0b15` was located in queried vendor or carrier portals.
- Carrier updates for this deployment have been reported to use TR-069 provisioning; alternative distribution channels or unindexed mirrors remain unverified.
- Offline analysis of client-side assets (MITRA-RES-011) confirmed no HTTP firmware upload or configuration restore controls in the captured UI.

### 2. Vulnerability Research Analysis (CVE-2017-16522 / CVE-2017-16523)
- The 2017 advisory authored by j0lama ([Exploit-DB 43061](https://www.exploit-db.com/exploits/43061)) describes authenticated SSH privilege escalation on related model DSL-100HN-T1 with firmware `ES_113WJY0b16`, and GPT-2541GNAC with another build. It does not establish applicability to our NV unit/build.
- That attack vector required an active SSH daemon listening on TCP port 22, allowing execution of shells via `ssh 1234@<ip> /bin/sh`.
- On our Brazilian Vivo unit (`BR_SA_113WUK0b15`), TCP port 22 timed out from the tested LAN client over IPv4 and link-local IPv6. This is consistent with the disabled SSH access entry subsequently exported in MITRA-BACKUP-014; it does not prove an absent daemon, fixed implementation, or permanent lack of an SSH route.

### 3. Synthesis of the Software and Network Investigation

| Stage | Scope | Measured Result | Boundary |
| --- | --- | --- | --- |
| 1. Services | Ports 1–65535 (IPv4), eight ports (IPv6), UDP checks | Only TCP 80 reached among tested ports. Management ports timed out; UDP DNS replied REFUSED. | Disabled access entries are now known; service runtime and enabling behavior remain unverified. |
| 2. Web Resources | 63 paths, forms, CGIs in captured UI | No backup export, firmware upgrade, or hidden management shell endpoints observed. | Captured UI lacks export/upgrade controls; unreferenced server-side handlers remain unmapped. |
| 3. Diagnostics | Ping/TraceRoute/DNS handlers | Ping options parsed (`-s 0` alters payload); command punctuation (`;`) yielded empty output (no execution demonstrated). | Empty output does not distinguish validation rejection, process failure, or output handling. TraceRoute/DNS argument handling remains untested. |
| 4. Outbound Traffic | 30s idle and action pcaps | IPv6 RA with RDNSS; no suitable provisioning request observed in that interval. | Switched LAN capture does not observe the DSL/WAN path. |
| 5. Firmware Analysis | Reported source search and CVE comparison | No exact-build image/source acquired; SSH historical behavior untested on this build. | Search coverage and exact implementation remain unresolved. |

## Result and Limits

- Negative for remote network software execution or firmware acquisition via tested network interfaces.
- The historical SSH route was not reachable under the measured service-access profile; its applicability to this build was not tested.
- Open software questions remain (TraceRoute/DNS argument validation variations, deeper firmware searches), but physical board access provides direct evidence of hardware components and serial interfaces.


## Next Decision

MITRA-BACKUP-014 subsequently found a legacy support interface and service-access
configuration. Inspect that route before concluding software access is blocked;
see [the configuration/firmware decision](../tooling/configuration-and-firmware.md).
Board photographs remain useful independent evidence:
1. Safely disconnect power from the MitraStar unit.
2. Disassemble chassis and capture high-resolution PCB photographs of both component sides.
3. Identify SoC, RAM, and SPI Flash chip markings (expected Macronix 16 MB SPI flash based on external research).
4. Identify UART candidates and measure ground/actual logic levels before choosing and attaching a compatible adapter. Do not assume this board's voltage or pinout from another unit.
