# Mero MitraStar

Ethernet-first investigation of a **MitraStar DSL-100HN-T1-NV Vivo Box**.
The first execution milestone is a tiny program running from writable temporary
storage on the router, after obtaining a shell and checking its binary ABI.

## Current evidence

- The owner logged into
  [the management UI](http://192.168.15.1/cgi-bin/html_sophia/sophia_main.html)
  using the device-label credentials, and changed the language to English.
- The UI reports software `BR_SA_113WUK0b15` and hardware `tmp_hardware1.0`.
- Direct Ethernet is verified through laptop `cris-MS-1454`: `enp6s0` at
  `192.168.15.3`, 100 Mb/s full duplex, expected router MAC, and HTTP 200 from
  the management page. SSH control uses the laptop's Wi-Fi at `192.168.1.80`.
- Linux, MT7505/MIPS, UART, and SPI flash information is currently supplied by
  partner research about other units. This unit's shell, chips, and ABI remain
  unverified.

## Start here

For the next agent, read the [complete handoff](HANDOFF.md) first. It records
access, completed measurements, private prerequisites, and the exact next test.

1. Read the [device inventory](docs/hardware/device.md).
2. Establish the [direct Ethernet lab](docs/tooling/ethernet-lab.md), preserving
   the laptop's SSH control path.
3. Follow the [roadmap](ROADMAP.md) and record each result using the
   [experiment format](docs/experiments/README.md).

[Research leads](research/mitrastar-leads.md) distinguish reported findings
from local observations. [Predecessor provenance](docs/reference/predecessor.md)
explains what was retained from the copied Sagemcom project and how to recover
its files. The original device's results do not describe this router.

[MITRA-NET-001](docs/experiments/mitra-net-001.md) records the Ethernet result.
[MITRA-UI-002](docs/experiments/mitra-ui-002.md) independently confirms identity
and inventories the authenticated UI. [MITRA-ACCESS-003](docs/experiments/mitra-access-003.md)
records successful local Ping/TraceRoute and a completed unresolved DNS lookup,
plus the verified published Snake method. No shell is established; backend
diagnostic implementation remains the next network research question.

The [network/software plan](docs/tooling/network-software-plan.md) records the
methodical follow-ups. [First-round results](docs/experiments/network-software-004-008.md)
cover all TCP ports, UDP checks, resource mapping, supplied ping options, and
LAN captures. Only TCP 80 accepted connections; UDP DNS replied with REFUSED.
[MITRA-DIAG-009](docs/experiments/mitra-diag-009.md) records the TCP 161 timeout
recheck and the bounded command-punctuation comparison. Semicolon input returned empty output;
no command execution was demonstrated.
[MITRA-PASSIVE-010](docs/experiments/mitra-passive-010.md) records the clean 30-second
idle capture, discovering autonomous IPv6 Router Advertisements with recursive DNS,
and single HTTP action attribution.
[MITRA-RES-011](docs/experiments/mitra-res-011.md) inventories 63 referenced UI paths,
confirming no backup, export, or firmware-upload facilities in the captured interface.
[MITRA-FW-012](docs/experiments/mitra-fw-012.md) analyzes firmware and vulnerability sources,
noting no exact-build firmware/GPL located in the recorded search and no demonstrated
applicability of the historical Spanish-firmware SSH behavior.
[MITRA-IPV6-013](docs/experiments/mitra-ipv6-013.md) measures IPv6 link-local management
ports, finding port 80 open among eight tested ports, port 7547 refused, and
ports 21, 22, 23, and 443 timed out.
[MITRA-BACKUP-014](docs/experiments/mitra-backup-014.md) discovers the legacy configurator
at `/padrao`, authenticates administrative user `support`, exports the router configuration
`romfile.cfg`, and confirms configuration entries for Telnet, SSH, FTP, SNMP, HTTPS,
and TR64 with interfaces set to `Disable`. Running daemons and shell privileges
remain unverified. This discovery provides a concrete software lead.

Next: inspect the legacy SSH management controls and prepare a bounded LAN-only
change with rollback. Read the [configuration/firmware decision](docs/tooling/configuration-and-firmware.md).
The export is a settings backup, not a firmware image; custom firmware remains
a later branch after image, layout, and recovery verification.


## Working layout

```text
docs/hardware/       this unit's identity and observed capabilities
docs/tooling/        host topology and reproducible lab procedures
docs/experiments/    dated results, evidence references, and negative findings
docs/reference/     predecessor provenance and reusable techniques
research/           external leads and verification status
.local/             ignored raw captures, exports, archives, and build output
```

Shell commands must start with `rtk`; use `rtk proxy` when raw output or a
tool's unsupported subcommand is needed. Raw traffic and exported settings may
contain credentials. Store them locally; commit reviewed summaries and hashes.

The active workspace was reset on 2026-10-07. It contains no inherited firmware,
device-control scripts, generated probes, or old captures. Those files remain
in the predecessor commit and the local archive. No Git history was rewritten.

The merged configuration analysis is in [research/romfile-analysis.md](research/romfile-analysis.md). It distinguishes observed ACL entries from unverified daemon and restore behavior.

Latest software step: [MITRA-SSH-015](docs/experiments/mitra-ssh-015.md) confirms the native LAN SSH controls; restricted LAN SSH is enabled after owner authorization, and the `support` vendor console is accessible. Linux shell access remains unproven.

Latest console inventory: [MITRA-CLI-016](docs/experiments/mitra-cli-016.md) documents read-only command usage, LAN4 link, br0, memory/CPU/NAT snapshots, and private TR-069/Wi-Fi status captures. Linux shell access remains unproven.
