# MitraStar DSL-100HN-T1-NV — external research and source index

Last source check: 2026-10-07. This page is designed for terminal access and preserves the distinction between **published results on other units** and **verified evidence from our own router**.

## Our unit: known baseline (see README and MITRA-NET-001)

- Model: MitraStar DSL-100HN-T1-NV (Vivo).
- UI-reported firmware: `BR_SA_113WUK0b15`; hardware identifier: `tmp_hardware1.0`.
- Its web UI and direct laptop-to-router Ethernet link were reached successfully.
- **Now photographed on this board:** MT7505N and MXIC 25L12835F package markings; see MITRA-BOARD-017.
- **Still unverified:** physical RAM capacity, UART pinout/voltage, Linux version, bootloader version, root shell, executable ABI. Other people's runtime reports below are **leads, not our measurements**.
- Avoid including admin credentials, router IDs, raw configurations or flash dumps in this public repository.

## Primary exact-model references — start here

| Topic | Original source | What was demonstrated / reported | Relevance and qualification |
| --- | --- | --- | --- |
| SPI extraction | [Maycon Vitali — Extração de Firmware usando SPI (2018-01-26)](https://maycon.hacknroll.io/embedded-hacking/2018/01/26/iot-hacking-extracao-de-firmware-usando-spi.html) | Explains reading external flash from a MitraStar DSL-100HN-T1-NV, including physical flash inspection and firmware analysis. | Compare actual chip markings, flash capacity, layout and read method before attaching a programmer; do not assume a dump fits our revision. |
| UART / serial | [Maycon Vitali — Acessando a interface UART/Serial (2018-03-10)](https://maycon.hacknroll.io/embedded-hacking/2018/03/10/embedded-hacking-acessando-interface-UART.html) | On the same model, reported five adjacent header positions, four populated, and usable serial boot output. Describes identifying pins with a multimeter. | Header pattern is a *visual clue*, not authorization to assume an identical pinout. TX->RX, RX->TX and GND->GND; never connect the USB-UART adapter's VCC to router VCC. Verify logic level before connecting. |
| Linux / CPU | [Kitz forum — Compatible firmware to unbrand this ZyXEL router? (DSL-100HN-T1-NV)](https://forum.kitz.co.uk/index.php?topic=20015.0) | A participant posted details of a DSL-100HN-T1-NV running Linux 2.6.36, a Ralink/MediaTek MT7505 with MIPS 24Kc, SquashFS root and RAM-backed writable temporary storage. | Forum output concerns someone else's firmware. Independently collect `uname`, `/proc/cpuinfo`, mounts and ELF details from ours when a shell is available. |
| Boot console | [Luiz Boina — Hardware Hacking: Playing Around With Routers](https://medium.com/@luizboina55/hardware-hacking-playing-around-with-routers-e5f95c4b07f3) | Exact-model disassembly/serial investigation, readable boot output at 115200 baud and U-Boot interaction; Linux boot ends at a login prompt in the published example. | Readable UART output is **not** an unlocked Linux shell. Begin with passive capture, without interrupting the bootloader. |
| Firmware modification | [Luiz Boina — Hardware Hacking: Modifying an Old Router Firmware to Play a Snake Game](https://medium.com/@luizboina55/hardware-hacking-modifying-an-old-router-firmware-to-play-a-snake-game-7e309484185a) | Extracted/modified firmware with BusyBox/Boa web content, then served a Snake page after flashing. | The router *serves* HTML/JavaScript executed by a browser; it does not prove arbitrary native MIPS programs can be run without obtaining shell access. Never flash this author's image onto ours. |

## Chip and firmware leads to cross-check

- MT7505 / MIPS 24Kc and Linux 2.6.36: reported on a Kitz forum user's exact-model unit; **not confirmed on ours**.
- 16 MiB SPI flash: reported in external work. Maycon's physical example identifies a Macronix `MX25L12835F`; another published bootloader identification mentions `MX25L12805D`. These **must not** be represented as the marking on our chip.
- Read-only SquashFS and writable `/tmp` have been reported; inspect actual mounts and free space before planning file transfer/execution.
- UART baud 115200 is a useful *test candidate* from Boina's example, not proof of our firmware's serial settings.
- The web UI success and firmware string in our README do not demonstrate root privileges or an installed compiler.

## Safe next steps and evidence gates

1. Preserve the working Ethernet/SSH setup described in `docs/experiments/mitra-net-001.md`. Avoid changing routes or bridging Wi-Fi/Ethernet.
2. Inventory authenticated **read-only** UI pages/firmware details; keep exports and session cookies out of Git.
3. If opening the case, unplug the 12 V supply first. Photograph PCB front/back and all chip markings, particularly SPI and any UART candidate.
4. Before UART hookup, identify ground, signal voltage and pins on **this physical board**. Use a compatible USB-to-TTL serial adapter, not RS-232 voltage levels. With the router on its normal power supply, connect only TX/RX/GND after verification and initially capture output passively.
5. If and only if a genuine shell becomes available, record `uname -a`, `cat /proc/cpuinfo`, `cat /proc/meminfo`, `cat /proc/mtd`, `mount`, and binary ELF/interpreter details. Confirm endian/ABI/libc/kernel compatibility before cross-compiling.
6. First programming milestone: run a minimal C program in temporary writable storage **without rewriting firmware**.
7. Before any persistent modification: verified independent original flash backup, hashes, map of partitions and a realistic restore path. Firmware/bootloader writes and factory reset require explicit owner instruction.

## Quick terminal lookup

From the repo:
```sh
rtk rg -n 'UART|SPI|MIPS|Boina|Maycon|Kitz|ABI|Snake' research/mitrastar-leads.md
rtk sed -n '1,200p' research/mitrastar-leads.md
```

To see the links directly:
```sh
rtk rg 'https?://' research/mitrastar-leads.md
```

## Provenance/history note

An earlier 2026-10-07 version of this file recorded a failed attempt by another agent to retrieve original sources. Exact original URLs are now supplied above, and the accessible articles/forum pages were checked independently on 2026-10-07. These published references remain external-unit evidence; they are **not proof** that our `BR_SA_113WUK0b15` unit has the same hardware or firmware. Continue to record first-hand evidence in `findings.md` and `docs/experiments/`.

## Update: this board photographed

[MITRA-BOARD-017](../docs/experiments/mitra-board-017.md) now observes MT7505N and MXIC 25L12835F markings directly on this unit. This resolves the earlier lack of package photographs; runtime architecture/kernel/ABI and UART pinout remain unverified. Other-unit chip variants listed above remain external evidence.
