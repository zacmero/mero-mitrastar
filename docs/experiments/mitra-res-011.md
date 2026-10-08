# MITRA-RES-011 — Offline referenced resource map and handler inventory

Date/time: 2026-10-08T03:26:00Z (lab timezone: 2026-10-08 00:26:00 -0300)
Device: MitraStar DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`
Scope: Comprehensive offline analysis of all 63 unique paths, forms, CGIs, and static scripts captured in `mitra-web-005` without active device probing.

## Method

Analyzed captured authenticated HTML documents, embedded scripts, form structures, and static assets (`Multi_Language_sophia.js`, jQuery components) stored in `.local/captures/mitra-web-005/`.

## Findings by Functional Area

### 1. System Logs
- **Page:** `/cgi-bin/html_sophia/device-management-system-logs.html`
- **Form:** `gvt_syslog` posting to `/cgi-bin/html_sophia/device-management-system-logs.asp`.
- **Parameters:** `ApplyFlag=1`, `gvt_logcategory` (0: All, 1: Internet, 2: LAN, 3: VoIP, 4: VoD, 5: FirewallLog, 6: Others), `gvt_loglevel` (0: All, 1: debug, 2: info, 3: notice, 4: warn, 5: err, 6: crit, 7: alert, 8: emerg).
- **Log Fetching:** `$('#gvt_syslogframe').load('/cgi-bin/gvt_viewsyslog.cgi')`.
- **Observation:** `gvt_viewsyslog.cgi` returns segmented `<ul>` lists (`part_1` to `part_6`), which were completely empty under default configuration.

### 2. Diagnostics and Performance Testing
- **Page:** `/cgi-bin/html_sophia/device-management-utilities-internet.html`
- **Form:** `DiagGeneral` posting to `/cgi-bin/html_sophia/device-management-utilities-internet.asp`.
- **Actions:** `PINGACT` values: `1` (Ping IPv4), `2` (Ping IPv6), `4` (TraceRoute IPv4), `5` (DNS lookup).
- **Output:** Loaded into iframe `showBoard` from `/cgi-bin/html_sophia/device-management-utilities-internet.cgi?1` inside textarea `InfoDisplay`.
- **HPNA Subsystem:** References `netper_delete.cgi`, `netper_kill.cgi`, `result_netinf.cgi`, and `result_netper_wizard.cgi`. Contains code attempting to assign `location.assign("/tmp/hpna_netinf.log")`, but functions abort with alert `"HPNAInterface is not ready"` when HPNA hardware is not present.

### 3. System Resets and Maintenance Controls
- **Page:** `/cgi-bin/html_sophia/device-management-resets.html`
- **Form:** `tools_System_Restore` posting to `/cgi-bin/html_sophia/device-management-resets.asp`.
- **Supported Operations:**
  - `restoreFlag: 1`, `RestartBtn: "RESTART"` (Software Reboot).
  - `restoreFlag: 6`, `RestartBtn: "RESTART"` (Factory Reset).
- **Scope:** No configuration backup, export, or firmware upgrade control was found in the captured Sophia views. This conclusion does not cover unreferenced interfaces. MITRA-BACKUP-014 subsequently found a working export in the legacy configurator.

### 4. Operation Mode and WAN Routing
- **Page:** `/cgi-bin/html_sophia/settings-wan-mode.html`
- **Form:** `form_op_mode` posting to `/cgi-bin/html_sophia/settings-op-mode.asp`.
- **Options:** `op_mode` value `0` (Router) and `1` (Bridge).
- **Observations:** Commented-out client-side code contains a disabled `wan_mode` selection (Auto/ADSL/VDSL). The captured view exposes no LAN-to-Ethernet-WAN option. Other interfaces, implementation capabilities, and hardware behavior remain unverified.

### 5. Local Network, DHCP, and Port Forwarding
- **Page:** `/cgi-bin/html_sophia/settings-local-network.html`
- **Data CGIs:**
  - `/cgi-bin/dhcp_client_list.cgi`: Returns active DHCP client table.
  - `/cgi-bin/GVT_portForwarding_rule.cgi`: Returns configured port forwarding rules.
  - `/cgi-bin/html_sophia/settings-local-network-dhcp.cgi`: Returns static IP reservations.
  - `/cgi-bin/sophia_dhcp_SelectIndex.cgi`: Populates reservation select indices.
- **Forms:**
  - `form_DHCP` -> `/cgi-bin/html_sophia/settings-local-network-dhcp.asp`
  - `PortForwarding_Form` -> `/cgi-bin/html_sophia/settings-local-network.asp`
  - `dmz` -> `/cgi-bin/html_sophia/settings-local-network-dmz.asp`
  - `RN_DDNSform` -> `/cgi-bin/html_sophia/settings-local-network-ddns.asp`

### 6. Client Language Asset Audit (`Multi_Language_sophia.js`)
- File size: 112,760 bytes; defines 603 `MLG_` localization variables.
- Audit for administrative/management keywords:
  - `telnet`, `ssh`, `shell`: 0 occurrences.
  - `backup`, `upload`, `download`: 0 occurrences.
  - `usb`: 0 occurrences (consistent with `settings-usb.html` returning HTTP 404).
  - `firmware`, `upgrade`, `update`: Present only in physical LED description strings (e.g., LED blinking behavior during automatic ISP firmware upgrade).

## Implication for Investigation

1. **No Software Backup or Firmware Upload Vector in Captured UI:** The captured web interface provides no standard administrative export (configuration backup file) or firmware upload facility. The resource map covers the captured interface and its referenced assets; it cannot establish that no other unreferenced server-side maintenance handlers exist.
2. **No Management Shell Toggle:** No UI toggle exists in the captured templates to enable Telnet or SSH.
3. **Session Key Protocol:** Every form submission requires `sessionKey`, retrieved dynamically via `/cgi-bin/sessionkey.cgi` or embedded page globals.


## Next Step

MITRA-BACKUP-014 supersedes any global absence claim: a legacy interface and
configuration export were discovered. Inspect its remote-management views and
retain the Sophia map as evidence about that particular interface. Exact-build
firmware/source analysis remains a separate open task.
