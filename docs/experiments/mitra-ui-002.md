# MITRA-UI-002 — authenticated management inventory

Date: 2026-10-07 local (UTC−03); collection began 2026-10-08T01:00:56Z.
Question: what does this exact firmware expose, and does it offer a shell?

## Access and authentication

Laptop cris-MS-1454 reached the router from 192.168.15.3 on enp6s0, with the
expected neighbor ac:c6:62:8d:99:78. Owner-supplied admin credentials were passed
through transient stdin with echo disabled, never embedded in commands or
tracked files.

Menu, header, status, and About were readable without credentials. Seven
protected destinations redirected with HTTP 302 to sophia_login.asp, then
sophia_login.html and its inner login.html form.

The active LOGIN button invokes clicklogin(), which sends
`base64(username + ':' + MD5(page-issued SID + ':' + password))` in the query
of a POST to sophia_index.asp. It clears the password field before submission.
An inactive helper uses a different transformation; two initial HTTP form
submissions using that transformation did not authenticate. Two read-only
Basic-auth checks also did not establish access. No credential guessing occurred.

Mero Browser executed the real challenge flow successfully. Dedicated headless
Chromium on the main machine used a temporary local-only SOCKS SSH tunnel to
the laptop (127.0.0.1:1088; CDP 127.0.0.1:9228). The owner's Firefox session was
separate and was not controlled. Native DOM focus and verified field population
resolved frame-coordinate issues.

Login URLs, challenge responses, session keys, and cookies remain sensitive.
This client protocol does not establish the server's credential-storage scheme.

## Independently confirmed identity

about-power-box.html exactly matched the owner's report: MitraStar
DSL-100HN-T1-NV, software BR_SA_113WUK0b15, hardware string tmp_hardware1.0,
serial ACC6628D9978, LAN/WAN MAC entries AC:C6:62:8D:99:78. These are UI values,
not physical chip or PCB identifications.

## Observed state and capabilities

| Surface | Observation |
| --- | --- |
| Status | No active DSL/PPP data; displayed WAN IPv4 values zero. Wired management works. |
| Statistics | LAN 4 carries traffic; LAN 1–3 counters zero; displayed Ethernet errors/discards zero at capture time. |
| DHCP | Enabled, 192.168.15.1/24; pool .2–.253; lease 720 minutes; specific DNS disabled. |
| LAN settings | DHCP, forwarding, DMZ, UPnP, DDNS sections. No setting or mapping saved. |
| Firewall | Default Policy Reject; WAN ping Reject. Directional rules support TCP, UDP, TCP/UDP, ICMP, ICMPv6. No rule entries visible in the captured view. |
| Wi-Fi | Network, SSID broadcast, WPS enabled; WPA/WPA2; 802.11g/n; Automatic channel; 20 MHz. No password value extracted. |
| Internet | PPPoE account form and generic Vivo defaults; not proof of the configured account. |
| WAN mode | Router/Bridge choices. Bridge warning mentions disabling telephone services and denying software update. Selected mode not separately recorded. |
| Games | SSH and Telnet Server forwarding presets; these do not enable a router shell. |
| Account | Password-change fields; no visible shell enable control. |
| Logs | Module/severity filters and pagination; no entries visible with the default filter. Does not prove logging is disabled. |
| Utilities | Ping, TraceRoute, DNS lookup. No diagnostic run. Configuration is a section heading, not an observed export control. |
| Resets | Reboot and factory reset; neither invoked. No firmware upload or backup/export control found in inspected pages. |

Default Reject is consistent with earlier TCP timeouts, but does not establish
which services exist behind the policy. LAN ping replies do not contradict the
separate WAN-ping Reject setting.

## Paths observed in UI source

Paths below are under /cgi-bin/html_sophia/ unless shown absolute. Presence in
source does not establish dormant functions work. Mutation handlers were not
submitted except the normal login.

| Feature | Observed request surface |
| --- | --- |
| Statistics | device-management-statistics.cgi |
| Logs | device-management-system-logs.html; POST handler .asp; /cgi-bin/gvt_viewsyslog.cgi, /cgi-bin/gvt_logpagenum.cgi |
| Diagnostics | POST device-management-utilities-internet.asp; results frame device-management-utilities-internet.cgi; other result/net-performance functions present but not invoked |
| Account | device-management-account-settings.asp |
| Firewall | settings-firewall.asp; /cgi-bin/TR181FirewallRule.cgi |
| DHCP/forwarding | settings-local-network-dhcp.asp and .cgi; /cgi-bin/dhcp_client_list.cgi, /cgi-bin/GVT_portForwarding_rule.cgi |
| Session protection | /cgi-bin/sessionkey.cgi; games also references /cgi-bin/secondkey.cgi; values omitted |
| Reset | POST device-management-resets.asp; not invoked |

No native command execution, shell, kernel version, SoC, ABI, writable executable
storage, or bootloader access was obtained.

## Evidence and changes

Private ignored directory: .local/captures/20261008T010056Z-mitra-ui-002/.

- frames.json and pages.json: initial responses and About.
- login-resources.json, login-form.json, login-inner.json: pre-login source.
- authenticated-pages*.json: unsuccessful checks; helpers omitted login POST
  URLs, cookie values, credential digests and passwords.
- browser-pages.jsonl: nine rendered management snapshots; session-key/SID
  assignments and matching hidden values redacted. Original HTML hashes retained
  separately from stored-content hashes.
- extra-pages.jsonl: reset/Wi-Fi observations without password values.

Redacted browser-pages.jsonl SHA-256:
`8bd0c05125237fd1ce35328d934d78f6d80ec34a0f54f1f874ceb372b4b4f027`.

No configuration, firewall rule, firmware, bootloader, or diagnostic operation
was changed. Login changed temporary authentication state. Browser/tunnel cleanup
is recorded in progress.md.

## Result and next step

Identity is independently confirmed and the protected UI is accessible through
direct Ethernet. No visible route to a Linux shell was found. MT7505/MIPS/Linux
reports remain unverified for this unit; forwarding presets do not prove local
listeners.

Next: MITRA-ACCESS-003, review the observed diagnostic/session handlers and
exact-firmware research for an understood access route. A normal bounded
diagnostic can be a separate experiment when useful. If network evidence yields
no usable route, request unpowered board photos and verify UART before connecting.
