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

1. Read the [device inventory](docs/hardware/device.md).
2. Establish the [direct Ethernet lab](docs/tooling/ethernet-lab.md), preserving
   the laptop's SSH control path.
3. Follow the [roadmap](ROADMAP.md) and record each result using the
   [experiment format](docs/experiments/README.md).

[Research leads](research/mitrastar-leads.md) distinguish reported findings
from local observations. [Predecessor provenance](docs/reference/predecessor.md)
explains what was retained from the copied Sagemcom project and how to recover
its files. The original device's results do not describe this router.

[MITRA-NET-001](docs/experiments/mitra-net-001.md) records the first Ethernet
result. Next is authenticated, read-only UI inventory; no shell is established.

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
