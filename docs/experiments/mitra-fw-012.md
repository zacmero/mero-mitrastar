# MITRA-FW-012 — Exact firmware source search, vulnerability analysis, and hardware boundary

Date/time: 2026-10-08T03:30:00Z (lab timezone: 2026-10-08 00:30:00 -0300)
Device: MitraStar DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`
Scope: Stage 5 methodical investigation of firmware distribution, public GPL availability, historical vulnerability reports, and feasibility of remote software execution.

## Questions

1. Does an official vendor GPL archive or carrier firmware image exist publicly for `DSL-100HN-T1-NV` and build `BR_SA_113WUK0b15`?
2. Does historical CVE research for this router model (e.g., CVE-2017-16522 / CVE-2017-16523) provide an applicable network attack vector against the observed service configuration?
3. Have all five software/network investigation stages been exhausted, formally justifying the hardware access branch (UART/SPI)?

## Method

1. Systematic audit of vendor (MitraStar/Arcadyan/Sercomm) and carrier (Telefônica/Vivo Brasil/GVT) repositories for GPL source distributions and published firmware files.
2. Technical evaluation of known CVEs against the port and service profile established in MITRA-SERVICES-004 and MITRA-DIAG-009.
3. Synthesis of findings across all five stages of the network/software sequence ([network-software-plan.md](../tooling/network-software-plan.md)).

## Observations

### 1. Firmware Availability and GPL Source Status
- No exact-build firmware image or GPL source archive for model `DSL-100HN-T1-NV` and build `BR_SA_113WUK0b15` was located in queried vendor or carrier portals.
- Carrier updates for this deployment have been reported to use TR-069 provisioning; alternative distribution channels or unindexed mirrors remain unverified.
- Offline analysis of client-side assets (MITRA-RES-011) confirmed no HTTP firmware upload or configuration restore controls in the captured UI.

### 2. Vulnerability Research Analysis (CVE-2017-16522 / CVE-2017-16523)
- Research by Eduardo Novella ([Exploit-DB 43061](https://www.exploit-db.com/exploits/43061)) identified an authenticated command execution and privilege escalation issue on MitraStar DSL-100HN-T1-NV running Spanish Movistar firmware `ES_113WJY0b16`.
- That attack vector required an active SSH daemon listening on TCP port 22, allowing execution of shells via `ssh 1234@<ip> /bin/sh`.
- On our Brazilian Vivo unit (`BR_SA_113WUK0b15`), TCP port 22 is closed/filtered from LAN over both IPv4 and IPv6 (confirmed by timeouts in MITRA-SERVICES-004, MITRA-DIAG-009, and MITRA-IPV6-013). This report serves as a comparison rather than proof of applicability to this build.

### 3. Synthesis of the Software and Network Investigation

| Stage | Scope | Measured Result | Boundary |
| --- | --- | --- | --- |
| 1. Services | Ports 1–65535 (IPv4), key ports (IPv6), UDP checks | Only TCP 80 open (IPv4 & IPv6). Ports 21, 22, 23, 443 confirmed timed out. UDP DNS replies REFUSED. IPv6 port 7547 refused (RST). | No secondary network daemon accessible over LAN. |
| 2. Web Resources | 63 paths, forms, CGIs in captured UI | No backup export, firmware upgrade, or hidden management shell endpoints observed. | Captured UI lacks export/upgrade controls; unreferenced server-side handlers remain unmapped. |
| 3. Diagnostics | Ping/TraceRoute/DNS handlers | Ping options parsed (`-s 0` alters payload); command punctuation (`;`) yielded empty output (no execution demonstrated). | Empty output does not distinguish validation rejection, process failure, or output handling. TraceRoute/DNS argument handling remains untested. |
| 4. Outbound Traffic | 30s idle and action pcaps | Discovered autonomous IPv6 RA with RDNSS. Zero autonomous WAN traffic leaks onto the LAN link. | Switched LAN capture cannot observe the DSL modem / WAN uplink. |
| 5. Firmware Analysis | Queried sources and CVE comparisons | No exact-build firmware or GPL located in queried repositories. CVE-2017-16522 SSH route blocked by closed port 22. | Unindexed firmware repositories or carrier dumps may exist; remaining software questions include TraceRoute/DNS parsing. |

## Result and Limits

- Negative for remote network software execution or firmware acquisition via tested network interfaces.
- Known network attack vectors (including published SSH exploits on other firmware builds) are blocked by the firewall/service profile on this unit.
- Open software questions remain (TraceRoute/DNS argument validation variations, deeper firmware searches), but physical board access provides direct evidence of hardware components and serial interfaces.


## Next Decision

Transition from Phase 3 software investigation to Phase 3 hardware investigation per [ROADMAP.md](../../ROADMAP.md):
1. Safely disconnect power from the MitraStar unit.
2. Disassemble chassis and capture high-resolution PCB photographs of both component sides.
3. Identify SoC, RAM, and SPI Flash chip markings (expected Macronix 16 MB SPI flash based on external research).
4. Identify UART serial pin header candidates and verify electrical GND and logic levels (3.3V) with a multimeter before attaching a USB-to-TTL serial adapter.
