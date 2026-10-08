# MITRA-SOFTWARE-019 — Legacy status/log views and SDK comparison

Date/time: 2026-10-08, 16:08–16:17 UTC.
Device: DSL-100HN-T1-NV / BR_SA_113WUK0b15.
Question: what remaining referenced inventory views work, and does public
related SDK code explain this build's vendor-console or SSH restrictions?

## Connection and procedure

Reverified cris-MS-1454, enp6s0 at 192.168.15.3/24, Ethernet route to
192.168.15.1, and neighbor ac:c6:62:8d:99:78. Wi-Fi SSH control through
cris@192.168.1.80 stayed available. Used the existing legacy support login
and previously authorized restricted LAN SSH. HTTP requests had eight-second
socket timeouts; console session was bounded and ended with exit.

Read page source and follow its referenced display paths using GET. No browser
JavaScript was executed. Apart from the normal login, no POST was submitted.
No refresh interval, log filter/clear, remote-management setting, service,
firmware upload/check/upgrade, reset, reboot, or save action was submitted.
Raw pages may contain session/account/device data and remain ignored.

## Corrected path resolution

`tabFW.html?tabJson=../pages/systemMonitoring/.../tab.json` is a generic wrapper.
Its source resolves tab data under **/pages/**, not **/cgi-bin/pages/**.
Both `/pages/systemMonitoring/log/tab.json` and
`/pages/systemMonitoring/trafficStatus/tab.json` returned HTTP 200 and referenced
working display pages. Thus the wrong-path 404 in MITRA-SOFTWARE-018 does not
mean traffic tabs are missing. The candidate Sophia `default-status.html` also
returned 404; the observed Sophia main frame instead references sophia_side.html.

## Direct display results

| Read-only view | Observed result | Limit |
| --- | --- | --- |
| `log/viewlog.html` and `/cgi-bin/ViewSyslog.cgi` | HTTP 200; syslog response has headings and blank row, no message entries | Current/default display only; not proof logging is absent |
| `trafficStatus/wan.html`, `wan_frame1.html`, `wan_frame2.html` | HTTP 200; aggregate sent/received packet values zero, no populated connection row | No active WAN connection shown; not a complete DSL/IPv6 diagnosis |
| `trafficStatus/lan.html`, `/cgi-bin/lan_frame.cgi` | HTTP 200; nonzero counters under LAN2, others zero | Snapshot; no errors/drops shown in this response |
| `trafficStatus/nat.html`, `/cgi-bin/traffic_nat.cgi` | HTTP 200; laptop listed with 20 open sessions | Per-device count, not destination/connection table or proof of Internet traffic |
| `maintenance/system/system.html` | HTTP 200; settings form | Read source only, no submission |

The LAN2 snapshot shows sent/received bytes 577055/208104 and data-packet
counts 2101/2313. Labels come from the page's translation IDs. Rechecked the
console with exact commands:

```text
lan portstatus
sys state nat
sys uptime
exit
```

Native portstatus now agrees: **LAN2 up, 100 Mbps, full duplex**; LAN1/3/4 down.
The earlier session's LAN4 measurement remains historical, not the current port
state. No cable movement was performed or independently observed by this agent.
Console NAT usage is 30/4096 at the later sample; it need not equal the earlier
web count of 20, whose timing and metric differ. Uptime is about 24 minutes.

## Native firmware-upload surface

The menu-referenced path retains the vendor spelling:
`/cgi-bin/pages/maintenance/firewareUpgrade/firewareUpgrade.html`.
It returned HTTP 200, displays BR_SA_113WUK0b15, and includes a multipart POST
upload form whose action is that same path. The source calls uiDoUpdate, sets
preparepost/upgradeflag, checks an ACS-managed restriction, and then submits.
A hidden referenced iframe, Fireware_UpgradesManaged.html, returned HTTP 200
with upgradesManaged=0; its POST is conditional on preparepost=1, never set here.
The main form also rendered Upgrade_Managed=0.

This establishes a **native upload interface in this build**, expanding the
Sophia-only inventory. It does not prove backend acceptance, integrity rules,
image format, image compatibility, or provide a downloadable firmware backup.
No upload or automatic upgrade check was invoked. A settings export is still
not the kernel/root filesystem/bootloader image required for custom firmware.

## Referenced service controls

`/pages/maintenance/remotemgmt/tab.json` returned HTTP 200 and lists General,
WWW, SNMP, DNS, ICMP, SSH. No Telnet or FTP tab appears. RemMagGeneral.html
returned HTTP 200 with a master enabled/disabled control, currently Enabled;
it is not a per-Telnet activation form. No apply was submitted.

tabFW remaps the SSH translation key to MLG_Tab_subTitle_NO_SFTP, but the
retrieved translation renders simply SSH. The identifier is an implementation
lead, not a user-visible no-SFTP statement or proof of the backend policy.
The actual SFTP channel rejection remains the observation from experiment 018.

## Public implementation comparison

Bounded searches: MitraStar/sys/telnetd/-t; exact model plus firmware/bin/download;
MitraStar GPL source; missing-subcommand text plus telnetd; MT7505 source/GitHub.
No exact-build firmware image or matching vendor handler was acquired.

[Public EN751221 SDK repository](https://github.com/cjdelisle/EN751221-Linux26)
contains a Linux 2.6.36 tree with an explicit MT7505 identification macro in
[tc3162.h](https://github.com/cjdelisle/EN751221-Linux26/blob/master/tclinux_phoenix/linux-2.6.36/arch/mips/include/asm/tc3162/tc3162.h).
This is related-platform source, not a confirmed source release for our board/build.
Direct GitHub tree inspection returned 52,543 apps entries without truncation;
root tree SHA aea3a43562e8d3dc0335624202fde08d713a18c2.

- [tcci.c](https://github.com/cjdelisle/EN751221-Linux26/blob/master/tclinux_phoenix/apps/private/tcci/tcci.c)
  contains a different command/function set. The inspected file does not contain
  telnetd, exitOnIdle, tecal, swversion, or the observed missing-subcommand text.
  It therefore does not establish our console handler or flag behavior.
- [SDK Dropbear svr-chansession.c](https://github.com/cjdelisle/EN751221-Linux26/blob/master/tclinux_phoenix/apps/public/dropbear-0.52/svr-chansession.c)
  contains exec/subsystem request handlers. It is version 0.52, whereas our
  device banner reports 2019.78; it cannot explain our exact request rejection.
- [SDK utelnetd.c](https://github.com/cjdelisle/EN751221-Linux26/blob/master/tclinux_phoenix/apps/public/utelnetd-0.1.2/utelnetd.c)
  uses a different option parser. Its daemon options are not evidence for the
  vendor CLI's sys telnetd -t branch.
- [Official MitraStar Germany/o2 page](https://www.mitrastar.com/Germany_o2/)
  lists GPL material for other products, not this exact Vivo model/build on the
  inspected page. No corresponding source archive was acquired here.

The code-search connector first suggested a nonexistent vendor_cmd.c path;
its parallel search failed during remote repository import. Direct GitHub API
and raw source retrieval succeeded as a fallback. No SDK executable was run,
compiled, or transferred to the router. No full firmware-compatible source or
shell access is claimed from this comparison.

## Private evidence

All artifact paths below are inside the canonical repo and ignored. HTTP status,
duration and body hashes also appear in each capture directory's metadata.json.

| Artifact | SHA-256 |
| --- | --- |
| `.local/software-019/1791475734/2-firewareUpgrade.html` | `7d3aaf5e47f036bfef5949d8b946aa9d0678a53cc99130998647691268545ce2` |
| `.local/software-019/1791475876/4-Fireware_UpgradesManaged.html` | `db746ca45e80c2798ebb077e2898cf37d776bc441459d540fc8d08278ac3bd29` |
| `.local/software-019/1791475848/0-tab.json` | `718bb591708e3bc8d7136d8724d388d211780a1fb4b178f0a34d3b4d4d87e444` |
| `.local/software-019/1791475848/1-tab.json` | `c23fc0625cffddb99e0f20e6d318a707148c7d58be48db53667a6193b1909148` |
| `.local/software-019/1791475909/0-ViewSyslog.cgi` | `89e2b8c4d1ac03995f4590e38caaa8ebc0c6b7219ec272274895e07cce2b3c34` |
| `.local/software-019/1791475909/1-lan_frame.cgi` | `4b5bacd11d048bcf556b2e73d590995ce88feec6e344c290546afc040477dcec` |
| `.local/software-019/1791475909/2-traffic_nat.cgi` | `f3d266f3de7909102765bbaa46ddf675d95a82a6316f2d62d8399d3f873344dc` |
| `.local/software-019/1791475909/4-wan_frame1.html` | `3b0439474ef8bac2a28422e18bc1e1081d944ade4cbb1d8cab4e12509f256c6a` |
| `.local/software-019/1791475909/3-wan_frame2.html` | `627e9a32ed924a04b4f11d8ae6f8be454b1c39b16d7a57c16689ad304e80fcdb` |
| `.local/software-019/1791476118/0-tab.json` | `12299aa885b0743ee6e1521af863c97b4aecaee6b9fd2d423be068b45758ff52` |
| `.local/software-019/1791476137/0-RemMagGeneral.html` | `cf1f371f12b29f15debfdbdc0c1133a1aba65f32e6ac8ce65e8901d1b8c44734` |
| `.local/software-019/1791476170/0-Multi_Language.js` | `04ef950ce6d3252eb323d020338ccfbc4f2dbf6556b0287323d58932cd9b97c2` |
| `.local/ssh-015/1791475972/console.json` | `9ff53e6435542be84e4d350e5f89954e550dcb92fd09daa261c035bdb58e83e7` |
| `.local/research/sdk-019/apps-tree.json` | `50e1ba77e29baac35f1553f26d5ee9b72dc276fc87c78a2423f72305db89f164` |
| `.local/research/sdk-019/tcci.c` | `767d708f6b06e47a330997ae74f05d4ec8f561817fcf2297413b260e2e6cab7a` |
| `.local/research/sdk-019/svr-chansession.c` | `5560d5217834e36b1b7d7101774a98384cbb4f032c0381e1df8717dc1d43eea5` |
| `.local/research/sdk-019/utelnetd.c` | `469750c116515569a1190bd17f3fdd4ce92ac1fca994de7b2656952cdd30e25c` |

## Result and next experiment

More native inventory and an upload interface are now confirmed. No new shell,
filesystem transfer, firmware image, or arbitrary native execution was obtained.
A broad search or additional port scan is less useful than acquiring matching
implementation evidence. Software work remains open; this round does not prove
that every network route has been exhausted.

Next access step: identify and measure the photographed UART candidate, then
capture serial output once electrical compatibility is established. In parallel
as a research route, seek this build's firmware/source or a verified original
flash read to inspect the actual console, login and SSH handlers offline.
Do not activate unexplained telnet flags or upload guessed firmware to substitute
for that missing evidence. Hardware connections and writes are separate steps;
none occurred in this software round.
