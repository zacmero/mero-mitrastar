# MITRA-IPV6-013 — IPv6 link-local service probe and comparison with IPv4

Date/time: 2026-10-08T03:50:16Z (lab timezone: 2026-10-08 00:50:16 -0300)
Device: MitraStar DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`
Question: Does the router's link-local IPv6 interface expose different listening services or firewall policies across candidate management ports (21, 22, 23, 80, 443, 7547, 8080, 8443) compared to the IPv4 interface?

Host and interfaces: Laptop `cris-MS-1454`, Wi-Fi `wlp4s0` at `192.168.1.80/24` (control route), Ethernet `enp6s0` at `192.168.15.3/24`, link-local `fe80::430:6b22:7c40:59/64`.
Control route: SSH key `/home/zacmero/.ssh/mero_stb_isolated_lab` to `cris@192.168.1.80`.
Target: `fe80::aec6:62ff:fe8d:9978%enp6s0`, MAC `ac:c6:62:8d:99:78`.
Preconditions:
- Neighbor entry confirmed `REACHABLE`.
- ICMPv6 ping verified: 2 packets transmitted, 2 received, 0% loss, avg round-trip 0.532 ms.

## Procedure

1. Sent two ICMPv6 echo requests to `fe80::aec6:62ff:fe8d:9978%enp6s0` from laptop `enp6s0`.
2. Executed non-blocking TCP socket connect checks from `cris-MS-1454` with a 2.0-second deadline across ports 21, 22, 23, 80, 443, 7547, 8080, and 8443.
3. Fetched HTTP response headers from `http://[fe80::aec6:62ff:fe8d:9978%enp6s0]/` via curl over IPv6.
4. Recorded response times, states, and saved raw JSON output to local storage.

## Observations

1. **Port States:**
   - **TCP 80:** `OPEN` (connect duration 1.1 ms). HTTP GET returned `HTTP/1.0 200 OK` (Boa web server, identical security headers as IPv4).
   - **TCP 7547:** `REFUSED` (connect duration 0.8 ms). Connection refused with TCP RST immediately. (On IPv4, port 7547 timed out after 2.0 seconds).
   - **TCP 8080:** `REFUSED` (connect duration 0.5 ms). Immediate TCP RST.
   - **TCP 8443:** `REFUSED` (connect duration 0.5 ms). Immediate TCP RST.
   - **TCP 21 (FTP):** `TIMEOUT` (2.006s). Dropped / filtered.
   - **TCP 22 (SSH):** `TIMEOUT` (2.002s). Dropped / filtered.
   - **TCP 23 (Telnet):** `TIMEOUT` (2.002s). Dropped / filtered.
   - **TCP 443 (HTTPS):** `TIMEOUT` (2.002s). Dropped / filtered.

2. **IPv4 vs IPv6 Comparison:**
   - Port 80 is the only open port among the eight IPv6 ports tested. Unlike the IPv4 scan, this was not full port coverage.
   - Port 7547 behavior differs: IPv4 dropped packets (timed out), whereas IPv6 actively refused connections with RST.
   - Ports 21, 22, 23, and 443 remain filtered/timed out over both protocol families.

## Artifacts

Stored privately under `.local/captures/mitra-ipv6-013/`:
- `ipv6-port-check.json` (673 bytes, SHA-256 `b275fdb7514c9a8ac8042be3a4d9eafdf7d4b1bf0592d6a27caddcdd551578ee`).

## Result and Limits

- Port 80 is the only TCP service reached among the eight tested IPv6 ports.
- SSH (port 22) and Telnet (port 23) timed out over IPv6 link-local, consistent with IPv4 and with filtering; timeout alone does not identify the cause.
- This measurement establishes link-local scope behavior on `enp6s0`; it does not measure global unicast IPv6 behavior when a WAN prefix is delegated.

## Changes and Cleanup

- No persistent settings or network configurations modified.
- Sockets closed immediately after connect/timeout.
- SSH control and router HTTP responsiveness verified.

## Next Decision

The subsequent [legacy configurator discovery](mitra-backup-014.md) provides
service-access configuration to inspect before deciding the software route is
blocked. Board photographs remain an independent hardware documentation step.
