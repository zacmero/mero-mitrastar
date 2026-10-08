# MITRA-PASSIVE-010 — Clean idle capture, IPv6 router advertisement discovery, and single action attribution

Date/time: 2026-10-08T03:25:26Z (lab timezone: 2026-10-08 00:25:26 -0300)
Device: MitraStar DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`
Questions:
1. What autonomous traffic does the router originate during an undisturbed 30-second idle period without port scans or active browser sessions?
2. How does an ordinary status request (`GET /cgi-bin/html_sophia/about-power-box.html`) trace at packet level when attributed by direction?

Host and interfaces: Laptop `cris-MS-1454`, Wi-Fi `wlp4s0` at `192.168.1.80/24` (control route), Ethernet `enp6s0` at `192.168.15.3/24` (device-facing route, 100 Mb/s full duplex).
Control route: SSH key `/home/zacmero/.ssh/mero_stb_isolated_lab` to `cris@192.168.1.80`.
Target IP and observed MAC: `192.168.15.1`, `ac:c6:62:8d:99:78`.
Preconditions:
- No background scans, browser sessions, or proxy tunnels running during the idle capture.
- Neighbor MAC verified before capture.
- Sudo credentials provided via private stdin to `sudo -S`.

## Procedure

1. Ran an undisturbed 30-second capture on `enp6s0` with no concurrent traffic generation.
2. Parsed packet headers and protocol fields to attribute traffic by direction and type.
3. Started a 10-second capture on `enp6s0` filtered to `tcp port 80`.
4. After a 2-second capture initialization pause, executed a single source-bound HTTP GET to `http://192.168.15.1/cgi-bin/html_sophia/about-power-box.html` from `192.168.15.3`.
5. Extracted HTTP request/response flow metadata using native parsing.
6. Verified post-experiment host SSH and router HTTP responsiveness.

## Observations

1. **Idle capture (`idle-30s.pcap`, 13 packets, 0 kernel drops):**
   - **ARP (4 packets):** Periodic ARP keepalives between router `192.168.15.1` and laptop `192.168.15.3`.
   - **IPv4 DNS (2 packets):** Laptop sent UDP query on port 53; router responded with `rcode=5` (`REFUSED`).
   - **IPv6 Neighbor Discovery (4 packets):** Neighbor Solicitations and Advertisements between laptop link-local and router link-local `fe80::aec6:62ff:fe8d:9978`.
   - **IPv6 DNS (2 packets):** Laptop sent UDP IPv6 query (`connectivity-check.ubuntu.com`) to `fe80::aec6:62ff:fe8d:9978:53`; router responded with `rcode=5` (`REFUSED`).
   - **Autonomous Router Advertisement (1 packet):** Router originated an ICMPv6 Router Advertisement from `fe80::aec6:62ff:fe8d:9978` to `ff02::1` (all-nodes multicast) with `cur_hop=64`, `lifetime=180`, Option 25 (RDNSS) advertising `fe80::aec6:62ff:fe8d:9978` (lifetime 40s), Option 1 (Source link-layer MAC `ac:c6:62:8d:99:78`), and Option 5 (MTU). No prefix information option was present; the capture does not isolate why.
   - **Absence of autonomous WAN traffic:** No provisioning, TR-069, NTP, or firmware check traffic was directed to the LAN interface.

2. **Action capture (`action-about-http.pcap`, 79 packets, 0 kernel drops):**
   - Single TCP flow: Laptop `192.168.15.3:50082` -> Router `192.168.15.1:80`.
   - Request: `GET /cgi-bin/html_sophia/about-power-box.html HTTP/1.1`.
   - Response: `HTTP/1.0 200 OK`, 12,708 bytes HTML body.
   - Flow closed cleanly; zero extraneous packets observed during the window.

## Artifacts

Stored privately under `.local/captures/mitra-passive-010/`:
- `idle-30s.pcap` (1,278 bytes, SHA-256 `e51c8135c3a083adfafe27ad100fae1dd8fe7fd24c7052395484b01f25de1e69`).
- `action-about-http.pcap` (19,660 bytes, SHA-256 `366d0054ba51e03659127df22f39f11a0a5960ba42203ca17669d543b9c333f1`).
- `capture-metadata.json` (1,684 bytes, SHA-256 `2400cd104f9627dddbdcae701a54850c3fc890e01bb2d45667dc62ca791603c3`).

## Result and limits

- Identified router's autonomous IPv6 behavior: it advertises a default-router lifetime and itself as an RDNSS address, answering the observed DNS queries with REFUSED. No SLAAC prefix was advertised and working recursive resolution was not demonstrated.
- No autonomous WAN/provisioning request was observed on this port during the 30-second interval. This does not establish a general absence or reveal the DSL/WAN path.
- LAN promiscuous capture cannot observe traffic destined exclusively for the internal DSL modem / WAN uplink.

## Changes and cleanup

- Captures terminated cleanly after their defined timeout windows.
- Zero kernel packet drops recorded.
- Host SSH and router HTTP 200 responsiveness verified.

## Next decision

Proceed to Stage 5: investigate official firmware image / GPL sources for `DSL-100HN-T1-NV` and build `BR_SA_113WUK0b15` to enable offline inspection of Boa binaries and backend CGI handlers.
