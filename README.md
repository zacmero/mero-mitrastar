# Mero MitraStar

Investigation of a **MitraStar DSL-100HN-T1-NV Vivo Box**, aiming for a genuine operating-system shell and a tiny native program running from verified writable temporary storage.

## Current checkpoint — 2026-10-26

**Read [HANDOFF.md](HANDOFF.md) first.** The complete new session is [MITRA-HARDWARE-020](docs/experiments/mitra-hardware-020.md), including exact readings, Linux Mint output, limitations and corrections to earlier advice.

| Area | Current evidence |
| --- | --- |
| Management access | Earlier experiments reached native management and restricted LAN SSH with a `support` vendor CLI. Linux shell remains unestablished. |
| Programmer | CH341A enumerates in both programmer (`1a86:5512`) and serial (`1a86:5523`, `/dev/ttyUSB0`) modes on Mint. No flash was attached to the probe; no actual read/write test was completed. |
| Electrical blocker | Owner measured 3.31 V at the 3.3 V rail but approximately 5 V at the reported data contacts. Do not connect this unmodified programmer directly to the router's 3.3 V signals. |
| Mechanical blocker | The supplied SOIC8 clip cannot seat around the flash because the nearby header and another component obstruct it. This is independent of the voltage problem. |
| Header measurements | Owner reports ground continuity at the farthest populated pin from the flash. Powered readings toward the flash: **0 / 3.2 / 3.3 / 3.3 V**. TX/RX/VCC remain unassigned; no UART log has been captured. |
| Missing equipment | ESP32 ordered but not arrived; **no female–female jumper wires or soldering equipment** available. |
| Immediate decision | Pause MitraStar hardware work; pursue the separate [Askey Ethernet-first handoff](https://github.com/zacmero/mero-askey/blob/main/HANDOFF.md) while awaiting a usable serial setup. |

The yellow jumper selects programmer/TTL mode, **not voltage**. Continuity readings and candidate controller pins were not sufficiently established for a repair map; see the raw observations in 020. Do not repeat the unsuccessful probe loop or present a solder repair as an immediate no-cost step.

### Buying list

[**BUYING-LIST.md**](BUYING-LIST.md) contains the exact female–female Dupont lead requirements, checked Brazilian listings and the Todoist task. Only the missing leads are on the immediate shopping list; the ESP32 is already ordered. No purchase has been placed by the agent.

## Device identity and preserved evidence

- UI: software `BR_SA_113WUK0b15`, hardware string `tmp_hardware1.0`.
- [Board photographs](board_pictures/README.md): MT7505N and MXIC 25L12835F markings observed on this unit. The flash's published density is 16 MiB; no full dump has been acquired.
- [Device inventory](docs/hardware/device.md) and [board review](docs/experiments/mitra-board-017.md) retain the earlier observations. Their original statements that no header measurements existed are superseded by the owner-reported measurements in 020, not by a completed UART pinout.
- The earlier direct Ethernet link was through `cris-MS-1454`, with `enp6s0` at `192.168.15.3` and Wi-Fi control at `192.168.1.80`. These are **historical topology values**, not the current Mint session's verified configuration. Recheck interface, route and target identity before traffic.
- Bootloader/runtime CPU details, ELF ABI, root shell, flash layout and recovery remain unverified on this unit.

## Investigation index

| Record | Result / purpose |
| --- | --- |
| [MITRA-NET-001](docs/experiments/mitra-net-001.md) | Verified Ethernet connectivity and initial bounded service baseline. |
| [MITRA-UI-002](docs/experiments/mitra-ui-002.md) | Authenticated Sophia UI inventory and device identity. |
| [MITRA-ACCESS-003](docs/experiments/mitra-access-003.md) | Local diagnostic results and exact-model firmware-modification precedent; no shell. |
| [Network/software 004–008](docs/experiments/network-software-004-008.md) | Initial TCP/UDP checks, resources, diagnostic arguments and LAN captures. |
| [MITRA-DIAG-009](docs/experiments/mitra-diag-009.md) | Punctuation comparison produced no marker; no demonstrated command execution. |
| [MITRA-PASSIVE-010](docs/experiments/mitra-passive-010.md) | Idle capture and action attribution. |
| [MITRA-RES-011](docs/experiments/mitra-res-011.md) | Sophia-only resource map; its missing controls did not describe the whole firmware. |
| [MITRA-FW-012](docs/experiments/mitra-fw-012.md) | Firmware/source and historical-vulnerability research, with applicability limits. |
| [MITRA-IPV6-013](docs/experiments/mitra-ipv6-013.md) | Bounded IPv6 management-port results. |
| [MITRA-BACKUP-014](docs/experiments/mitra-backup-014.md) | Discovered `/padrao`, authenticated `support`, obtained a settings export. |
| [MITRA-SSH-015](docs/experiments/mitra-ssh-015.md) | Authorized restricted LAN SSH opened Dropbear 2019.78; authenticated vendor console, not Linux shell. |
| [MITRA-CLI-016](docs/experiments/mitra-cli-016.md) | Read-only vendor command and status inventory. |
| [MITRA-BOARD-017](docs/experiments/mitra-board-017.md) | Seventeen-photo archive and physical chip markings. |
| [MITRA-SOFTWARE-018](docs/experiments/mitra-software-018.md) | Fresh export generation, restricted SSH policy, rejected exec/SFTP requests. |
| [MITRA-SOFTWARE-019](docs/experiments/mitra-software-019.md) | Working legacy status/log views and native firmware-upload form; no matching image or shell. |
| [MITRA-HARDWARE-020](docs/experiments/mitra-hardware-020.md) | New programmer, voltage/clip blockers, Mint USB results and owner UART-header measurements. |

The original TCP-80-only scans preceded authorized SSH activation; do not mistake them for the latest policy. A settings export is not kernel/root-filesystem/bootloader firmware. The upload form's presence does not establish image compatibility or recovery.

## References and next milestones

[Research leads](research/mitrastar-leads.md) include Maycon Vitali's SPI and UART articles and Luiz Boina's independent investigations. Maycon's use of individual test clips is now distinguished from our bulky clip's failed fit. [Configuration/firmware decision](docs/tooling/configuration-and-firmware.md) and [romfile analysis](research/romfile-analysis.md) preserve the software work.

Use [task_plan.md](task_plan.md) and the current handoff for next actions. The broader [roadmap](ROADMAP.md), `findings.md` and `progress.md` contain historical steps; 020/current handoff supersede old pending instructions. The next MitraStar hardware milestone is a validated, voltage-compatible receive-only UART capture after equipment is available—not a flash rewrite. No guarantee of shell access follows from a UART header.

Related projects: [Askey](https://github.com/zacmero/mero-askey), [GVT receiver reference audit](https://github.com/zacmero/mero-stb-lab/blob/main/research/uart-reference-audit-2026-10-26.md). Device-specific findings must not be transferred between them.

## Workspace and evidence hygiene

Canonical main-machine checkout: `/home/zacmero/projects/mero-mitrastar`, branch `master`. The older temporary `mero-mitrastar-docs` worktree was removed. Original photos and ignored evidence remain local to that checkout.

```text
docs/hardware/       identity and observed capabilities
docs/tooling/        host topology and reproducible procedures
docs/experiments/    dated results, limitations and artifact references
research/           external sources and applicability
board_pictures/     reviewed photographs, local originals and hashes
.local/             ignored captures, credentials, exports and binaries
```

Use `rtk`/`rtk proxy` in the established main-machine workflow; do not assume RTK exists on Mint. The [Ethernet runbook](docs/tooling/ethernet-lab.md) preserves the control-path method. Keep passwords, session cookies, exports and raw dumps private; commit reviewed summaries and genuine hashes only.

The 2026-10-07 reset preserved the copied Sagemcom history and local archive. [Predecessor provenance](docs/reference/predecessor.md) explains recovery; it is not this router's firmware or pinout. No history was rewritten. The [previous README](https://github.com/zacmero/mero-mitrastar/blob/7ab49991483ac447412c1b35fa7ceb560715d611/README.md) remains available for the earlier checkpoint.
