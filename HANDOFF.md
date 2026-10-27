# MitraStar — current handoff

Updated **2026-10-26, America/Sao_Paulo**. This file supersedes older next-step instructions. The full previous handoff is preserved in Git at [7ab4999](https://github.com/zacmero/mero-mitrastar/blob/7ab49991483ac447412c1b35fa7ceb560715d611/HANDOFF.md); individual experiments 001–019 and private artifact locations remain documented. No Git history or local evidence was removed.

## Read first

1. [MITRA-HARDWARE-020](docs/experiments/mitra-hardware-020.md): complete new session, exact readings, Mint output, kit limitations and corrections.
2. [BUYING-LIST.md](BUYING-LIST.md): missing female–female jumpers, checked Brazilian listings and Todoist task.
3. [AGENTS.md](AGENTS.md), [task_plan.md](task_plan.md), and the dated experiment reports. `findings.md`, `progress.md` and `ROADMAP.md` also contain historical entries; their older pending statements must not override this checkpoint.
4. [Askey handoff](https://github.com/zacmero/mero-askey/blob/main/HANDOFF.md): the owner's immediate next software-only shell target.

## Current decision

**Pause hands-on MitraStar work pending suitable UART wiring/interface; do not repeat the CH341A probing loop.** The owner has no female–female jumpers, the ordered ESP32 has not arrived, and soldering equipment is unavailable. Proceed next on the Askey over Ethernet using the Mint laptop, after documentation is complete.

Goal remains a genuine OS shell and execution of a tiny native program from temporary storage. A vendor CLI, boot log or browser interface is not that milestone. The owner explicitly rejects more UI-only GVT/Ekioh work as a substitute.

## What this unit already established before today

- Model DSL-100HN-T1-NV; UI build `BR_SA_113WUK0b15`, hardware string `tmp_hardware1.0`.
- Legacy `/padrao` management and native configuration export are available in the recorded experiments. Export generation must finish before download; [018](docs/experiments/mitra-software-018.md) recorded 14.229 seconds and the fresh saved policy.
- [015](docs/experiments/mitra-ssh-015.md): authorized LAN-restricted SSH reached Dropbear 2019.78 and the authenticated `support` **vendor console**, not a Linux shell. Initial TCP-80-only scans describe the earlier disabled policy, not that later state.
- [016](docs/experiments/mitra-cli-016.md): read-only vendor CLI inventory. [018](docs/experiments/mitra-software-018.md): tested remote exec and SFTP requests rejected. Do not guess undocumented `sys telnetd -t` semantics from unrelated SDK source.
- [019](docs/experiments/mitra-software-019.md): native firmware-upload form exists; no suitable image, backend acceptance or recovery procedure was validated. Settings backup is not full flash firmware.
- [017](docs/experiments/mitra-board-017.md): photographed MT7505N and MXIC 25L12835F; [17-photo archive](board_pictures/README.md). Flash specification is 16 MiB, not a measured dump. Runtime ABI remains unknown.

## New hardware evidence — 26 October

### CH341A kit

Black MiniProgrammer, SOIC8 clip/ribbon and green adapters received. With no clip attached: **3.31 V** on the 3.3 V supply, **5.03 V** on the 5 V supply, **4.92 V** on the contact discussed as MISO/owner-read `MIOS`, and **5.03 V** on the reported MOSI contact. Treat the target-facing electrical setup as unsafe for direct 3.3 V use.

The regulator tab-to-3.3 V test beeped. Candidate pin 28-to-5 V beeped, but a later resistance check fluctuated approximately 23–43 ohms, so the exact controller pin/trace mapping is **not certified for repair**. Candidate pin 9-to-regulator tab did not beep. Raw continuity-display values have uncertain units; do not repeat the earlier automatic `0.1 ohm` interpretation. Full table: [020](docs/experiments/mitra-hardware-020.md).

No pins were lifted, no traces were cut and no soldering was performed. The [official flashrom repair reference](https://github.com/flashrom/flashrom/blob/main/doc/supported_hw/supported_prog/ch341ab.rst) is retained for later competent rework/verification, not a present instruction to modify this board. Never join intact 5 V and 3.3 V rails.

### USB functionality on Linux Mint

- Programmer jumper `1–2`: `1a86:5512`; `sudo flashrom -p ch341a_spi` ran and reported no flash found with nothing connected. Expected result, **not a flash-read test**.
- Serial jumper `2–3`: `1a86:5523`, `/dev/ttyUSB0` appeared.
- Last tested position: serial mode. The jumper selects interface mode, **not logic voltage**. No UART voltage-safety check or actual serial traffic capture was completed.
- Reported banner: `flashrom unknown on Linux 7.0.0-38-generic (x86_64)`. Do not invent the flashrom package version.

### SOIC8 mechanical blocker

The purchased clip cannot seat around the flash: the adjacent four-pin header and a second nearby component obstruct its jaws. Do not force it or remove the header. This is independent of the programmer's 5 V problem. Maycon used separate test clips with a Bus Pirate; his source is linked in [020](docs/experiments/mitra-hardware-020.md). The generic clip recommendation had not verified clearance and must not be repeated as plug-and-play.

### Router UART candidate

Owner readings, **four populated pins counted from farthest from the flash toward the flash**:

| Session label, not PCB pin number | Voltage | Interpretation |
| --- | ---: | --- |
| A, farthest | 0 V | Owner found continuity to ground while unpowered |
| B | 3.2 V | Unknown signal/supply |
| C | 3.3 V | Unknown signal/supply |
| D, closest | 3.3 V | Unknown signal/supply |

The fifth capped/missing position is not counted. Do not map this to another photo's left/right orientation without checking. **TX/RX/VCC are not identified.** DC voltage alone cannot distinguish idle UART from a supply. No boot output, baud measurement, login or shell has been obtained through this header.

## Equipment-dependent next milestone

After the ordered ESP32 and the [jumpers](BUYING-LIST.md) are present, validate the actual board/bridge configuration and 3.3 V interface before attaching it. Begin receive-only: common GND plus a router TX candidate into a compatible RX input; keep adapter TX and all power leads disconnected from the router. The router uses its proper supply. Do not treat a stock ESP32 as a preconfigured serial bridge or reuse a GPIO number without checking the module variant.

115200 8N1 is an external starting candidate, not locally verified. `picocom` is the host terminal, not a voltage converter. Record boot text versus bootloader/vendor/login/shell access separately. Once a true shell is available, inventory runtime/ABI and writable executable temporary storage before building native code.

Any later flash write needs matching original full-chip reads, layout/integrity analysis and a realistic restore route. No such read exists here yet.

## Host/access and evidence preservation

The canonical main-machine checkout remains `/home/zacmero/projects/mero-mitrastar`, branch `master`. Private `.local/` evidence and original HEICs are not in a fresh clone. Earlier `cris-MS-1454` / `enp6s0` / `192.168.15.3` and Wi-Fi `192.168.1.80` values are historical, not the current Mint session's verified network configuration. Recheck topology before any device traffic and preserve the control path. Use `rtk` on the established main-machine workflow; use native Mint commands if it is absent on the laptop.

Existing owner credentials remain private in the original workspace's ignored `.local/handoff/credentials.json`, as documented in the historical handoff. Do not print them, copy them into another router repo, assume the same credentials apply to Askey, or store them in command arguments.

This update publishes documentation and a shopping task only; it does not start device tests or authorize firmware writes, service changes, reboots, soldering or purchases. The earlier bounded LAN SSH authorization is recorded in AGENTS/015; it is not blanket authority for new persistent changes.

## Other projects

- [mero-askey](https://github.com/zacmero/mero-askey): next active target, independent device identity/firmware/service checks required.
- [GVT UART-reference audit](https://github.com/zacmero/mero-stb-lab/blob/main/research/uart-reference-audit-2026-10-26.md): preserve prior work, do not confuse related-board anecdotes with a measured failure on our DSI74 V2.
