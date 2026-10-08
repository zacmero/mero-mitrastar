# MITRA-NET-001 — direct Ethernet and management baseline

Date: 2026-10-07 local (UTC−03); HTTP/port checks started
2026-10-08T00:50:29.780964Z.

Device: owner-reported DSL-100HN-T1-NV / BR_SA_113WUK0b15.

Question: can the main machine supervise the laptop over Wi-Fi while the
laptop reaches the correct MitraStar over a direct Ethernet cable?

## Access and topology

- Main host: `192.168.1.97`, user `zacmero`.
- Laptop hostname: `cris-MS-1454`; login user `cris`.
- Laptop control: `wlp4s0`, `192.168.1.80/24`.
- Laptop Ethernet: `enp6s0`, `192.168.15.3/24`; NIC MAC `6c:62:6d:f2:89:ff`.
- Target: `192.168.15.1`; neighbor MAC `ac:c6:62:8d:99:78`, matching the
  owner's LAN MAC report.
- Successful main-to-laptop SSH used the existing key
  `/home/zacmero/.ssh/mero_stb_isolated_lab`, with `IdentitiesOnly=yes`,
  `BatchMode=yes`, a five-second connection timeout, and strict known-host
  verification. No tunnel was needed.
- `arch-local` is the owner's laptop-to-main SSH alias; it is a separate direction.

```bash
rtk proxy ssh -i ~/.ssh/mero_stb_isolated_lab -o IdentitiesOnly=yes cris@192.168.1.80
```

The initial attempt without the dedicated key failed authentication. Reading
an existing `mero-oz` SSH config entry did not establish or attempt a connection
to that machine. Only the laptop address was targeted.

## Procedure and observations

Remote inventory commands were sent through host `rtk proxy ssh`. RTK is absent
on the laptop, so its command payloads used native tools. Read-only `ip`,
`sysctl`, and `/sys/class/net/enp6s0` reads established:

| Check | Result |
| --- | --- |
| Router route | `192.168.15.1 dev enp6s0 src 192.168.15.3` |
| Main-machine route | `192.168.1.97 dev wlp4s0 src 192.168.1.80` |
| Preferred default | `192.168.1.1` via Wi-Fi, metric 600 |
| Additional default | `192.168.15.1` via Ethernet, DHCP, metric 20100 |
| Link | Carrier 1; 100 Mb/s; full duplex |
| Forwarding | IPv4 0; IPv6 0 |
| Bound ping | 2/2 replies, 0% loss, average 0.509 ms |

No route/profile change was needed for this test. The Ethernet default is
present but lower priority than Wi-Fi; preserve that distinction in future
setup rather than claiming there is no Ethernet default.

After confirming the neighbor MAC, a Python HTTP client bound its source to
`192.168.15.3`, sent one unauthenticated GET for
`/cgi-bin/html_sophia/sophia_main.html`, and saved the response locally. It
returned HTTP **200 OK**, 3,726 bytes, HTML title **Vivo**. No login credentials
or cookies were supplied. This does not establish authenticated management
access, session state, or a shell.

Python TCP sockets, also bound to `192.168.15.3`, attempted one connection per
listed port with a 1.5-second timeout. No banners, credentials, exploit payloads,
or application requests were sent to these sockets.

| TCP port | Result |
| --- | --- |
| 21 | Timeout |
| 22 | Timeout |
| 23 | Timeout |
| 53 | Timeout |
| 80 | Open |
| 443 | Timeout |
| 7547 | Timeout |
| 8080 | Connection refused |
| 8443 | Connection refused |

Timeouts are inconclusive about whether services exist or are filtered. TCP
53 says nothing about UDP DNS. UDP and other TCP ports were not tested.
`nmap` is absent on the laptop; standard-library connect checks were sufficient
for this bounded question. `curl` and `tcpdump` are installed; no passive pcap
was collected in this experiment.

## Artifacts

Ignored local directory:
`.local/captures/20261008T005029Z-mitra-net-001/`.

- `network-baseline.json`: timestamp, neighbor check, source/target, HTTP headers
  and base64 body, body hash, and per-port outcomes. Mode 0600; raw response not
  published.
- Raw JSON SHA-256:
  `446f3ffe1cd97266766a810f6b95e55136f4eb226ff948086c21d7baf885d04d`.
- HTTP body SHA-256:
  `dbda93efa32878a6ffcd8281a9b6459a4c1035124e04602b601f9f69d51d4288`.
- `link-inventory.json`: local record of remote interface, route, link,
  neighbor, forwarding, and tool availability observations.

## Result, changes, and next decision

**Positive:** reverse SSH and direct Ethernet management reachability work
simultaneously, with the expected router MAC.

No host interface configuration, router setting, firewall policy, credentials,
firmware, or bootloader state was changed. The short-lived SSH/HTTP/TCP
connections exited; only local evidence files and repository documentation
were added.

Next: `MITRA-UI-002`, authenticated read-only management inventory using the
owner's existing login. Shell, SoC, kernel, ABI, and writable execution storage
remain unverified. A bounded passive capture is still available as a separate
follow-up when useful.
