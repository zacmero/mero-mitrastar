# MITRA-CLI-016 — Read-only vendor console inventory

Date: 2026-10-08 UTC. Device: MitraStar DSL-100HN-T1-NV, build
`BR_SA_113WUK0b15`. Goal: map the authenticated management console and collect
runtime information without changing settings or starting another service.

## Connection and scope

Reverified SSH control host `cris-MS-1454`, router-facing `enp6s0` at
`192.168.15.3/24`, route to `192.168.15.1`, and neighbor MAC
`ac:c6:62:8d:99:78`. Connected interactively as `support` using the previously
authorized restricted LAN SSH policy. The owner directed continuation of
vendor-console exploration and reported `sys telnetd` returning missing
subcommand usage.

The console helper used SSH PTY sessions, a pinned host key, and the supplied
owner credential. Only help and informational commands were submitted.
Sessions ended with `exit`; raw console captures remain private and ignored.
Status commands can print Wi-Fi and TR-069 credentials, so raw output must
not be copied into public notes. No password values, Wi-Fi identifier, or
provider authentication values are included below.

## Command map observed

| Console branch | Observed help / result | Interpretation and limit |
| --- | --- | --- |
| `sys telnetd ?` | Lists `-t` | Flag semantics unknown; flag not invoked |
| `sys state` | `sysstate <mem\|cpu\|nat>`; `sysstate help` | Native read-only system inventory route |
| `sys state ?` | Invalid option | This branch does not use the top-level question-mark convention |
| `lan help` | `show`, `portstatus`, IP/DHCP configuration and delete operations | Used display operations only |
| `net route ?` | No result text | Does not establish route-table availability |
| `nat natp ?` | `add`, `delete`, `dmz`, `show` | Configuration operations not invoked |
| `nat natp show` | Requires `dmz` subcommand | Not a full NAT/rules inventory |
| `tr69 help` | `display`, `help`, `set` fields, debug and save controls | Used `display`; no setters, debug, or save |
| `wlan --help` | Separate `config` and `show` operations | Used selected `show` operations |
| `voip profile ?` | Provider/SIP/RTP fields, `display`, `save` | Help only; no profile changes |
| `voip fxs ?` | Flash timing fields, `display`, `save` | Help only |
| `igmp ?` | `config`, `proxy`, `snooping`, `showtable` | Help only |
| `portmirror ?` | `on/off` and LAN port mask | No mirroring change |

A listed command is not proof of implementation, privilege level, or absence
of additional commands. Some branches validate a question mark; others
forward it to a utility that rejects it. Help requests do not establish
service activation behavior.

## Direct observations

| Query | Result |
| --- | --- |
| `lan portstatus ?` | LAN4 up, 100 Mbps/full duplex; LAN1–3 down |
| `lan show primary` | `br0`, IPv4 `192.168.15.1/24`, matching link-local IPv6/MAC, MTU 1500 |
| `sys state mem` | `MemTotal: 28968 kB`; `MemTotalFree: 3076 kB` |
| `sys state cpu` | `CPULoad: 4%` |
| `sys state nat` | `Total: 4096`; `Used: 4` |
| `tr69 display` | Active Yes; periodic inform enabled; interval 68400 seconds (19 hours); request port 7547; request path `/tr69` |
| `wlan show status` | Enabled |
| `wlan show channel` | 6 (Auto) |
| `wlan show protocol` | 802.11g+n |
| `wlan show bw` | 20 MHz |
| `wlan show wps` | WPS setup enabled, WPS 2.0, push-button method; secret fields omitted |
| `wlan show encryption` | TKIPAES |
| `nat natp show dmz` | DMZ host disabled, printed twice; family/interface interpretation unverified |

Memory and CPU values are snapshots, not physical chip capacity or CPU-model
identification. `br0` output describes the internal LAN bridge; no bridge
between the laptop's Wi-Fi and Ethernet was created. Its MTU 1500 concerns a
different interface/context from the earlier IPv6 RA's advertised MTU 1492.
The WPS setup flag does not establish an open enrollment window.

TR-069 configuration does not establish a successful connection, active
provider session, or WAN listener reachability. Earlier TCP 7547 checks
reported refusal; no new service check was performed here. Credential fields
in `tr69 display` are kept private. NAT usage does not enumerate firewall or
forwarding rules.

## Exact submitted console commands

```text
sys telnetd ?
sys state ?
lan ?
net route ?
nat ?
tr69 ?
lan help
tr69 help
lan show ?
lan portstatus ?
nat natp ?
wlan ?
voip ?
igmp ?
portmirror ?
lan show primary
tr69 display
nat natp show
wlan --help
voip profile ?
voip fxs ?
sys state
wlan show status
wlan show channel
wlan show protocol
wlan show bw
wlan show wps
wlan show encryption
nat natp show dmz
sys state mem
sys state cpu
sys state nat
```

Every session also sent `exit`. No `telnetd -t`, console `save`, reset,
reboot, password change, configuration setter, mirror activation, or firmware
write was submitted. Restricted LAN SSH remains enabled from MITRA-SSH-015.

## Evidence

Paths are relative to the original lab checkout, not the master documentation
worktree. Capture JSON includes command text and private console output.

| Artifact | SHA-256 |
| --- | --- |
| `.local/ssh-015/1791438572/console.json` | `4a69189fc5c926516fab7ddc18991d6b90be9aee62bddcaf1268a9ede1c8df2a` |
| `.local/ssh-015/1791438605/console.json` | `95eb9dac3417329d7e7107284f1f75baf84bf9976f60a427fc8e921cd4258fbe` |
| `.local/ssh-015/1791438639/console.json` | `cd6ac8239fb5fad9f6d3531f245fdd347a71a9e2685cc9f47eb6f9ca80b7e607` |
| `.local/ssh-015/1791438692/console.json` | `afa00bc73c76d7be334ba93ef3e395067c3343c1790056a52981de5abb501b1f` |
| `.local/ssh-015/1791438743/console.json` | `2873f8810928898fc949ebed758e345d30c6cd67e760c82a1b3b8a881a0ff216` |

## Next decision

The console provides usable network and system inventory, but it remains a
vendor interface. No Linux shell, executable ABI, filesystem access, or
arbitrary native program execution has been established.

The `sys telnetd -t` handler is an implementation lead, not a verified shell
entry point. Determine its meaning from exact-build code/firmware or reliable
matching documentation before considering a service change. A further
service or policy change needs the explicit instruction required by
`AGENTS.md`. Do not infer that a help entry grants root access.

Continue toward the console/SSH handler implementation through an exact-build
firmware image or a filesystem dump. Owner-planned powered-off board photos
will independently support identifying the UART/SPI hardware route. Preserve
both paths; the present console round is not proof that all software routes
are exhausted.
