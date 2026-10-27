# Current task plan — 2026-10-26

Goal: genuine MitraStar OS shell, verified runtime ABI, then a tiny native program in temporary storage. Read [HANDOFF.md](HANDOFF.md) and [MITRA-HARDWARE-020](docs/experiments/mitra-hardware-020.md) before acting.

The previous chronological plan, covering the reset and experiments 001–019, is preserved [at the prior checkpoint](https://github.com/zacmero/mero-mitrastar/blob/7ab49991483ac447412c1b35fa7ceb560715d611/task_plan.md). Individual experiment reports, `findings.md`, `progress.md` and Git history are retained. Their older pending items are not a reason to repeat completed work.

## Completed and recorded

- [x] Preserve and index earlier software investigations 001–019: native management, settings export, restricted LAN SSH and vendor CLI, rejected exec/SFTP, native upload interface, board photographs.
- [x] Record received CH341A kit, adapters and SOIC8 ribbon; distinguish them from still-missing Dupont leads and incoming ESP32.
- [x] Record actual unconnected programmer voltages: 3.31 V, 5.03 V, 4.92 V and 5.03 V at the discussed contacts; preserve the MISO/MIOS label ambiguity.
- [x] Preserve all reported continuity tests, unstable 23–43 ohm candidate-pin result and uncertainty over continuity-display units. Do not promote the earlier photo markers into a verified repair map.
- [x] Record Mint programmer-mode enumeration and expected no-chip result with the clip disconnected.
- [x] Record serial-mode enumeration `1a86:5523` and `/dev/ttyUSB0`. No actual router UART traffic tested.
- [x] Record SOIC8 physical obstruction by the header and neighboring component, independently of the voltage problem.
- [x] Record owner-measured header ground and farthest-to-closest populated-pin voltages: 0 / 3.2 / 3.3 / 3.3 V. TX/RX/VCC remain unknown.
- [x] Add [buying list](BUYING-LIST.md) and Todoist task for one female–female jumper set, with listing and dated price; no order placed.
- [x] Prepare [mero-askey handoff](https://github.com/zacmero/mero-askey/blob/main/HANDOFF.md) as the next independent Ethernet/software investigation.

## Paused MitraStar hardware work

- [ ] Receive the already ordered ESP32 and obtain individual female–female 2.54 mm leads.
- [ ] Identify the actual ESP32/module variant, load and validate suitable serial-bridge firmware, and establish safe 3.3 V receive-only interfacing.
- [ ] Use an unambiguous photo orientation for the four populated header positions; identify TX through actual serial activity, not a 3.2/3.3 V guess.
- [ ] Capture boot output and classify it: log / bootloader / vendor CLI / login / OS shell. Initial literature candidate is 115200 8N1, not a measured setting.
- [ ] After actual shell access, measure ABI and executable temporary storage before compiling/transferring the first native program.

Do not attach the presently uncorrected CH341A, force the clip, remove the UART header, or restart the same multimeter loop. No soldering equipment exists in this session. A documented CH341A repair is a separate future decision requiring appropriate tools, verified mapping and post-repair electrical checks.

## Separate future SPI branch — not required for the Askey start

- [ ] Choose a physically workable contact method and electrically safe programmer.
- [ ] Establish in-circuit isolation/back-powering conditions or a competent off-board method.
- [ ] Acquire repeatable matching full-chip backups, preserve hashes and private originals, inspect the real image and recovery method.
- [ ] Only then propose a specific firmware change with explicit owner authorization. A settings export or a similar-model internet image is not an original full backup.

## Immediate next project

The owner's next hands-on target is the **Askey RTV9015VW**. Follow its own handoff, beginning with Linux Mint interface/route/device identification and bounded management checks. This documentation publication has performed **no Askey device test**. Do not transfer MitraStar credentials, topology, firmware assumptions or hardware measurements to it.
