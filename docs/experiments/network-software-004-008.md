# Network/software study — first round

Date: 2026-10-08 UTC (2026-10-07 in the lab timezone).
Owner's device: MitraStar DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`.
Sequence: [network/software plan](../tooling/network-software-plan.md).

This report records completed measurements and the remaining questions. The
owner subsequently stopped Codex device testing and requested documentation
first. No message or testing instruction was sent to the other agent. No
marker-command comparison has been submitted, and no shell is established.

## Connection and method

SSH confirmed laptop `cris-MS-1454`, user `cris`, control address
`192.168.1.80`. Ethernet `enp6s0` had `192.168.15.3/24`, with a direct route to
`192.168.15.1` and neighbor MAC `ac:c6:62:8d:99:78`. The final scan inventory
confirmed the same route and neighbor. No route, bridge, DNS-server, firewall,
or router configuration change was made.

Browser work used the separate Mero Browser session through a laptop SOCKS
tunnel. It did not reuse the owner's Firefox login. Raw pages, captures, and
scripts remain in ignored `.local` storage. Credentials were supplied through
hidden stdin, not command arguments or tracked documents.

## MITRA-SERVICES-004 — TCP and UDP

The TCP-connect scan covered ports **1–65535** once. It capped connection
starts at 100/s and pending connects at 64, with a 0.5-second deadline. It
completed in **755.08 seconds**, with no socket errors beyond the classified
refusals/timeouts. An HTTP health check ran initially and after each 2048-port
batch; all passed. This was not an idle observation interval.

| Socket result | Count | Ports |
| --- | --- | --- |
| Open | 1 | 80 |
| Connection refused | 65527 | All remaining ports except the seven below |
| Timeout | 7 | 21, 22, 23, 53, 161, 443, 7547 |

Two-second rechecks also timed out on 21/22/23/53/443/7547. TCP 8443 refused
immediately in its recheck. TCP 161 still needs a longer recheck. A timeout
does not distinguish an absent service from filtering or other lack of response.
No Telnet/SSH service was confirmed reachable from this LAN client.

HTTP About returned 200. Observed headers included SAMEORIGIN framing,
X-XSS-Protection, and nosniff; no Server header identified a server/version in
that response. These headers do not establish backend security.

| UDP test | Deadline | Result |
| --- | --- | --- |
| DNS A query for localhost, port 53 | 3 s | Router replied with RCODE 5, REFUSED; no answers |
| SSDP root-device discovery, 239.255.255.250:1900 | 3 s | No router reply observed |
| mDNS A query for mitrastar.local, 224.0.0.251:5353 | 3 s | No router reply observed |

The UDP DNS response confirms a reachable responder even though TCP 53 timed
out. It does not establish working external resolution. The discovery tests
do not exclude all UPnP/mDNS behavior: one search type/name and short windows
were used. No SNMP community guessing or credential guessing occurred.

## MITRA-WEB-005 — referenced resources

Nine pages were retrieved in an authenticated session: the menu plus eight
protected views for statistics, logs, diagnostics, account, resets, LAN,
Internet, and operation mode. Six referenced static scripts returned 200.
The offline reference map contains **55 unique candidate paths**. A reference
is not proof that an endpoint exists or that its action is understood.

- The referenced `settings-usb.html` view returned 404.
- Log/result handlers and network-performance functions occur in captured
  client-side source. Start, stop, deletion, and unknown action handlers were
  not invoked.
- The operation-mode view offers Router (default) and Bridge. It does not
  establish a configurable Ethernet WAN port. No mode change was submitted.
- No new visible shell, firmware-upload, or configuration-backup control was
  established in the inspected views.

Some login attempts reached a transient index/blank state but protected-page
requests redirected to login. A fresh login with caching disabled subsequently
retrieved the protected pages. The experiment does not isolate the cause of
the earlier failure. Index-page arrival alone is not an authentication check.
The session later expired, and a fresh login restored diagnostics.

This is a client-resource and request map. No server-side handler source or
exact-build firmware was acquired.

## MITRA-DIAG-006 — supplied arguments

All submitted tests used IPv4 Ping, destination on loopback, and count **1**.
The first three used the visible controls. The fourth used the observed
authenticated diagnostic POST directly, with its current session key and the
same request fields. No persistent setting was changed.

| Destination field | Observed result |
| --- | --- |
| `127.0.0.1` | 56 data bytes; 1/1 received; 0% loss; 0.766 ms |
| ` 127.0.0.1 ` | 56 data bytes; 1/1 received; 0% loss; 0.575 ms |
| `-c 1 127.0.0.1` | 56 data bytes; 1/1 received; 0% loss; 0.499 ms |
| `-c 1 -s 0 127.0.0.1` | **0 data bytes**; 1/1 received; 0% loss |

The changed payload size confirms that supplied option text affected the ping
utility's arguments. The destination is therefore not handled exclusively as
a literal hostname/IP. This does **not** distinguish shell-string construction
from another argument-tokenization mechanism, and it does not demonstrate
arbitrary command execution, privilege level, or an interactive shell.

Native input checks stopped early attempts when the exact desired value was
not populated. They were not counted as submitted tests. No command separator,
large packet size, large count, external target, or marker command was submitted.

Next question: does the backend also reject command punctuation independently
of the frontend? One bounded constant-text comparison was planned but **not
run**. If resumed, record the actual returned marker or rejection; HTTP 200
alone is insufficient. Runtime/ABI inventory remains conditional on genuine
router-side execution evidence.

## MITRA-PASSIVE-007 — capture and visibility

Unprivileged tcpdump initially failed with Operation not permitted. After the
owner supplied sudo authentication, both bounded captures succeeded:

| Capture | Filter/scope | Duration | Packets | Kernel drops |
| --- | --- | --- | --- | --- |
| UDP/ARP | ARP or UDP involving the router | 30 s | 4 | 0 |
| HTTP | Router TCP port 80 | 30 s | 1476 | 0 |

The UDP/ARP sample contains laptop-originated DNS questions and router REFUSED
responses. They are not router-originated WAN requests. The HTTP sample covers
browser/login/resource activity and concurrent scan health traffic; it is not
a pure idle sample. Raw HTTP includes authentication material and must remain
private. No suitable router-originated provisioning/firmware request has been
identified in these samples.

The LAN capture cannot establish what traverses the DSL/WAN interface. The
router may originate traffic on an interface the laptop cannot observe.
Promiscuous mode does not move the laptop onto that path. See the plan's WAN
visibility section for controlled upstream, router-side capture, and DSL
capture alternatives. No uplink reconfiguration or interception was performed.

## MITRA-FW-008 — initial source check

The official vendor homepage was reachable and saved privately, but the
inspected homepage exposed no firmware/GPL download link. Existing primary
author reports were retained; their published SPI modifications concern
other units. No exact-build image or server implementation was obtained.
This is an initial source check, not an exhaustive firmware-source search.

Next: identify an official legacy support/GPL source or understood read-only
export, establish its build/version and hash, and inspect offline. Never apply
another article's offsets or image to this unit without independent verification.

## Private evidence hashes

Paths below are relative to `.local/captures/`.

| Artifact | SHA-256 |
| --- | --- |
| `mitra-services-004/tcp-scan.jsonl` | `e73ef89bc2cf8b7d6fa501b218f6928fe8a144a3d1a6bb91a57eb1936ca9ac73` |
| `mitra-services-004/udp-checks.json` | `e22c10063ff0f0bafde89359b08b090bbcff8ef40e5016724f62ee2848216003` |
| `mitra-services-004/http-and-rechecks.json` | `475f83aa95b3de7f8172b71cadbf5a63ebc5d7f9496626d65f5d2ac1df1a72c4` |
| `mitra-services-004/passive-udp-arp.pcap` | `7496d5c07dd3f35d30bb7788c9b877c1d2f7c2fc9d4d90e1d3089d0207f0f30c` |
| `mitra-web-005/authenticated-reference-map.json` | `5ea5d65ea83ed25a5d99ffa8090cd8a1ae8a4fa6faa8bb89ff9b082b323e5ef9` |
| `mitra-web-005/diagnostic-variations.json` | `caa9058055c6edf60da9b258593c7d606c69a76cbfadae4f7a4ef8e90eca28ca` |
| `mitra-web-005/ui-http.pcap` | `d6853ab177780ee7f9ec23b9b14072572cf86151db53fe740615a4deff8f5561` |

Raw artifacts are excluded from Git. The owner authorized committing/pushing
this reviewed round and the [complete handoff](../../HANDOFF.md). No additional
device test or agent message was performed while preparing that handoff.
