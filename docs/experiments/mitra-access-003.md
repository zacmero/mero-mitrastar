# MITRA-ACCESS-003 — diagnostic baseline

Date: 2026-10-08 UTC (2026-10-07 in the lab timezone).
Target: DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`.

## Scope and connection

The owner authorized investigating diagnostics before pursuing the published
Snake modification. Tests used the authenticated management UI through Mero
Browser and a laptop SOCKS tunnel. The separate automation browser does not
reuse the owner's Firefox login.

SSH confirmed laptop `cris-MS-1454`, Ethernet `enp6s0` at `192.168.15.3/24`,
and router neighbor `192.168.15.1` at `ac:c6:62:8d:99:78`. The route uses that
interface and source address. Initial SSH banner timeouts resolved during the
experiment; a later authenticated inventory succeeded.

## Observed handler

Page: `/cgi-bin/html_sophia/device-management-utilities-internet.html`.
The frontend sends a POST to the corresponding `.asp` endpoint with
`sessionKey`, `wanPVCFlag`, `PINGACT`, `PingformSaveFlag`, `pingIPAddr`, and
`pingNUM`. IPv4 Ping uses action 1, TraceRoute 4, and DNS lookup 5. Results are
loaded from `device-management-utilities-internet.cgi` into an iframe's
`InfoDisplay` textarea. Reading only body text can miss the output.

The visible UI controls were operated with native browser input. Verified
destinations were submitted once per mode; no malformed destination or
command-execution payload was submitted.

| Mode | Destination | Result |
| --- | --- | --- |
| Ping | `127.0.0.1`, count 1 | 1 transmitted, 1 received, 0% loss; 0.714 ms |
| TraceRoute | `127.0.0.1` | Reached loopback at hop 1; 0.101/0.051/0.114 ms |
| DNS lookup | `localhost` | Resolver shown as `127.0.0.1`; name could not be resolved |

All three result panes included their completion marker. The DNS result
confirms invocation and an unresolved name; it does not establish a broken
resolver or external connectivity. No persistent router configuration, flash,
bootloader, firewall, or interface setting was changed.

## Frontend validation and limits

Validator functions were called in the browser without submitting their test
values. The address validator accepts spaces and a leading option-like string
such as `-c 1 127.0.0.1`, but rejects a semicolon and newline. The count validator
accepts `1`, `01`, and `999999`, and rejects `0` and `1.5`; it has no observed
upper bound in that function. These are frontend observations only. Backend
validation and command construction remain unknown, and no injection
vulnerability or arbitrary command execution has been demonstrated.

The output is consistent with router-side command-line diagnostic utilities.
It does not provide an interactive shell, privilege level, CPU/ABI, or kernel
version. Phase 3's shell-access milestone remains open.

## Published Snake comparison

The original author articles were retrieved through
[Luiz Boina's Medium feed](https://medium.com/feed/@luizboina55), after direct
article retrieval failed. The
[UART investigation](https://medium.com/@luizboina55/hardware-hacking-playing-around-with-routers-e5f95c4b07f3)
reports 115200 baud, U-Boot interaction, and a login prompt after boot; it does
not demonstrate an unlocked Linux shell.

The [Snake modification](https://medium.com/@luizboina55/hardware-hacking-modifying-an-old-router-firmware-to-play-a-snake-game-7e309484185a)
used a CH341A to read SPI flash, extracted SquashFS, added portal/menu files,
rebuilt the filesystem using LZMA with 131072-byte blocks, and wrote modified
flash with the programmer. It reports BusyBox and Boa. The game ran in the
browser from files served by the modified router. This is a documented
firmware-modification precedent, not an Ethernet shell-access recipe or proof
that the same image layout applies to our unit. Published offsets must not be
copied into our flashing procedure.

## Evidence

Private artifacts are under `.local/captures/mitra-access-003/`. Credentials
and live session tokens are excluded from the diagnostic artifacts below.

| Artifact | SHA-256 |
| --- | --- |
| `diagnostic-results.json` | `3bc5a2a1fa9f16557bdeec748a641dbc96b2e41d3d342a6069a09c32bbfc32b4` |
| `diagnostic-handler.json` | `439adc299e5f6eb593618062f89cd86dff36829755c09ea41233ad77287cbd0b` |
| `boina-feed.xml` | `c46f985b7b79e249e77d97498fed43597fcc3d8da4f77c6094d995a92725a477` |

## Next step

Inspect firmware or server-side code for this exact build if obtainable, to
determine how destinations reach the diagnostic utilities. The client-side
blacklist alone does not justify claiming a shell route. UART identification
from this board's photographs remains the documented hardware alternative;
serial access may still require login. Flash modification stays a separate
later milestone requiring this unit's verified backup and recovery path.
