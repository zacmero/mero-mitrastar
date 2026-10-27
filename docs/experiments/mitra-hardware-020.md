# MITRA-HARDWARE-020 — CH341A kit, SPI blockers and UART measurements

Session date: **2026-10-26, America/Sao_Paulo (UTC-03:00)**.
Evidence: measurements, physical-fit observations and terminal output reported by Zac in the hardware session. This report was transcribed from that conversation; the documenting agent did not operate the multimeter or connect to the router. No raw instrument log or flash image was supplied, so no artifact hashes are invented.

## Decision / current status

**Pause the MitraStar hardware route until the missing leads and a suitable 3.3 V serial interface are available. Move the immediate software-only shell investigation to [mero-askey](https://github.com/zacmero/mero-askey).**

The goal remains a genuine OS shell and native program execution, not another web UI, the restricted vendor CLI, or an endless sequence of continuity tests. The owner explicitly asked to stop the repetitive probing loop. No firmware write, CH341A solder repair, flash removal, successful flash read, UART boot capture, or new Linux shell was reported today.

There are **two independent SPI blockers**: unsafe measured programmer signal voltage and insufficient physical clearance for the purchased clip. Fixing either one does not fix the other.

## 1. Equipment actually available

| Item | Status and purpose |
| --- | --- |
| Black CH341A MiniProgrammer, USB-A, ZIF socket, yellow mode jumper | Received; USB programmer and serial modes both enumerate on Linux. Not approved for direct connection to the router's 3.3 V signals. |
| SOIC8/SOP8 spring clip, grey ribbon, 2x4 female cable connector | Received; temporary eight-contact connection tool, not the programmer itself. Does not fit around this board's flash with the current surrounding components. |
| Small green adapter, long green solder-pad adapter, loose pin headers | Received in the pictured kit. Connector/package adaptation only; no evidence that these provide level shifting. An adapter intended for a removed chip does not solve in-circuit jaw clearance. |
| DT830D-style multimeter | Used for the measurements below. Exact continuity/diode display units were not captured. |
| ESP32 | Ordered, **not yet arrived**, according to the owner. Do not assume it is available to run a bridge now. |
| Female-to-female Dupont jumpers | **Not owned.** Only the supplied SOIC ribbon and pictured accessories are available. See [BUYING-LIST.md](../../BUYING-LIST.md). |
| Soldering equipment | Not currently available according to the owner; the CH341A repair would require obtaining tools/help. Do not present it as an immediate no-cost/simple step. |

The standalone 3.3 V and 5 V contacts are separate supply rails. The yellow jumper's `1–2` / `2–3` positions select **programmer mode / TTL serial mode**, not 3.3 V / 5 V logic. Its mode change does not repair the electrical design.

## 2. Powered CH341A measurements — clip disconnected

All voltages below are relative to the board contact labelled GND.

| Contact as discussed in the session | Owner's reading |
| --- | ---: |
| 3.3 V supply | 3.31 V |
| 5 V supply | 5.03 V |
| Data contact discussed as MISO; owner read the silk as `MIOS` | 4.92 V |
| Data contact discussed as MOSI | 5.03 V |

Preserve the labelling ambiguity rather than silently renaming a physical contact. Exact signal mapping must be checked before any future connection. MISO is normally an input to the programmer, so its unloaded reading can reflect a pull-up rather than an actively driven output. The approximately 5 V reading on the reported MOSI output is already enough to **reject direct 3.3 V-target use in this state**.

The [existing board record](mitra-board-017.md) identifies the router's MXIC 25L12835F marking and links Macronix's specification: 128 Mbit / 16 MiB, 2.7–3.6 V operating supply. Supply range is not the same specification as allowed input voltage. A good 3.31 V supply reading does not certify signal compatibility. Static meter readings do not characterize waveform peaks during activity either.

## 3. Unpowered continuity/resistance tests — preserve uncertainty

| Test performed | Reported result | Supported interpretation |
| --- | --- | --- |
| Two contacts labelled 5 V | Beep; display `0.1` | Low-resistance connection indicated. |
| 3.3 V rail to 5 V rail | No beep; display approximately `1012` to `1100+` | No low-resistance short detected in that test; not proof of complete electrical isolation. |
| Photo-marked **candidate** CH341A pin 28 to 5 V | Beep on two attempts; values not captured | Consistent with a connection, but exact pin/contact quality was not independently established. |
| Same candidate pin 28 to 5 V, subsequently on requested 200-ohm range | Unstable, mostly `23`, sometimes `30` or `43` | **Not a verified near-zero connection.** Do not treat this as a confirmed trace map for cutting or lifting a pin. |
| Regulator's exposed tab to labelled 3.3 V | Beep; display `0.1` | Tab connected to the external 3.3 V rail under this test. |
| Photo-marked **candidate** pin 9 to regulator tab | No beep; display stayed `1` | No continuity detected on that setting; not proof the pin is defective. |

**Correction to earlier coaching:** `0.1` was repeatedly called `0.1 ohm` without confirming the meter's selected continuity/diode mode and units. Retain the raw reading and beep, not an invented unit. Likewise `1012–1100` must not automatically be converted to 1.0–1.1 kohm; combined diode/continuity modes can report a test-voltage-related display. The isolated `1` is consistent with over-range/open indication on this style of meter, not a 1-ohm reading.

No soldering, trace cutting, pin lifting or power-rail modification was reported. The older annotated photo points remain candidates, not a validated repair map. Do not restart the same sequence solely to fill a checklist: it did not resolve the mapping and the electrical blocker is already established sufficiently for the stop decision.

## 4. Linux Mint: actual USB/programmer results

The owner is now working on a **Linux Mint laptop**, not the Arch desktop. Use Mint/Ubuntu package commands where needed; do not give `pacman` instructions for this session. Do not assume RTK is installed on the laptop. The previously documented Arch/SSH topology is historical and must be rechecked before future network access.

Programmer mode (`1–2`), no flash connected:

```text
Bus 002 Device 006: ID 1a86:5512 QinHeng Electronics CH341 in EPP/MEM/I2C mode, EPP/I2C adapter
```

Owner ran:

```sh
sudo flashrom -p ch341a_spi
```

Output supplied:

```text
flashrom unknown on Linux 7.0.0-38-generic (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).
No EEPROM/flash device found.
Note: flashrom can never write if the flash chip isn't found automatically.
```

Interpretation: USB detection and the programmer probe path work sufficiently to run without a reported programmer-initialization error. No flash being detected with the clip disconnected is expected. This is **not a test of actual chip read/write reliability**. The version string says `unknown`; do not invent an installed flashrom version.

After unplugging, moving the yellow jumper to `2–3`, and reconnecting:

```text
Bus 002 Device 007: ID 1a86:5523 QinHeng Electronics CH341 in serial mode, usb to serial port converter
/dev/ttyUSB0
```

Serial enumeration is confirmed on this laptop. No router serial connection, serial loopback, or successful capture was reported. `picocom` can open a serial device independently of an ESP32, but enumeration alone does not make this CH341A safe to attach. Its UART electrical levels/pull-ups have not been approved for the target. Last tested jumper position was `2–3`; recheck rather than assume it later.

## 5. Why the SOIC8 clip cannot currently be used here

The owner attempted a physical fit and reported that the clip cannot seat around the flash: the nearby four populated header pins obstruct one side and an adjacent component obstructs the other. The second component's identity was not established. Do not call it a specific IC or remove it on speculation.

An eight-pin package match does not guarantee jaw clearance. The [photo index](../../board_pictures/README.md), especially [IMG_8361](../../board_pictures/reviewed/IMG_8361.jpg), remains the board reference. Do not force the clip, bend flash legs, remove the candidate UART header, or assume another generic clip will fit.

**Correction to the earlier purchase recommendation:** the assistant recommended a generic SOIC8 clip without verifying this clearance and then underestimated the CH341A voltage-repair burden. Those were planning errors, not mistakes by the owner. Do not repeat the recommendation as a plug-and-play solution.

[Maycon Vitali's SPI extraction article](https://maycon.hacknroll.io/embedded-hacking/2018/01/26/iot-hacking-extracao-de-firmware-usando-spi.html) describes Bus Pirate connections using individual test clips; a SOIC8 clip is mentioned as an alternative. His [setup photograph](https://maycon.hacknroll.io/assets/images/posts/2018/01/buspirate-connected.png) is a reference for the different contact method, not evidence that our clip fits. [Luiz Boina's Snake firmware article](https://medium.com/@luizboina55/hardware-hacking-modifying-an-old-router-firmware-to-play-a-snake-game-7e309484185a) is a separate CH341A modification precedent; do not conflate the two setups.

Possible later SPI contact methods are appropriately sized individual probes, fine leads soldered to verified pads, or off-board flash access. None is purchased, physically validated, or authorized as today's next operation. In-circuit back-powering/bus contention would still need consideration even after solving voltage and fit.

## 6. MitraStar header measurements

Owner's physical description: the ground pin is the populated pin **farthest from the flash chip, beyond the capped/missing position as described by the owner**. In the recorded direction **from the farthest populated pin toward the flash**, powered readings were:

| Local session label (not PCB numbering) | Reading | What is established |
| --- | ---: | --- |
| A — farthest populated pin | 0 V | Owner found continuity to ground with the router unpowered. |
| B | 3.2 V | Unknown signal or supply function. |
| C | 3.3 V | Unknown signal or supply function. |
| D — closest populated pin | 3.3 V | Unknown signal or supply function. |

These labels count only the **four populated pins**, not all five PCB positions. Do not convert A/B/C/D into a left-to-right connector pinout without an oriented photograph. The exact ground reference was not captured in the report; the observation is owner-measured, not agent-measured.

This is compatible with the published UART lead, but **TX, RX and VCC are still unassigned**. A 3.2 V average does not prove TX; an idle TX/RX pull-up can look like a supply. No baud rate or runtime output has been measured. [Maycon's UART article](https://maycon.hacknroll.io/embedded-hacking/2018/03/10/embedded-hacking-acessando-interface-UART.html) and [Boina's console article](https://medium.com/@luizboina55/hardware-hacking-playing-around-with-routers-e5f95c4b07f3) remain exact-model external references, not this board's verified pinout. 115200 baud is an initial literature candidate, not a measured setting here.

## 7. Repair and usefulness of the CH341A — decision, not instructions to execute

The primary [flashrom CH341A/B documentation](https://github.com/flashrom/flashrom/blob/main/doc/supported_hw/supported_prog/ch341ab.rst) documents the black-board issue and a hardware correction: isolate the controller's VCC pin 28 from its original 5 V feed, then power the controller appropriately at 3.3 V, including its V3 pin 9 connection. **Never bridge the existing 5 V rail to the 3.3 V rail.** This is an external repair reference, not a confirmed pin map or an approved novice procedure for this exact board.

Keep the programmer for a later competent soldering/verification session or a suitable verified-voltage target. It is not electrically dead, but it is **not ready for these 3.3 V router targets**. Even a 5 V-compatible target would need the correct target supply, interface mapping and software support; the stock socket's 3.3 V supply does not automatically make it a complete 5 V EEPROM setup.

No repair is required to begin Ethernet work on the Askey. Do not create a dependency on buying a soldering station, another programmer, or another clip before that work.

## 8. Next MitraStar milestone, once equipment exists

1. Obtain the female–female leads in [BUYING-LIST.md](../../BUYING-LIST.md); the ESP32 is still pending delivery.
2. Identify the received ESP32/module variant and configure a genuine 3.3 V UART receiver. A stock ESP32 is not automatically a USB-to-router UART bridge. Validate the bridge before interpreting router silence. Any firmware sketch remains untested until built and exercised.
3. Start receive-only: board GND to adapter GND, verified router TX candidate to a suitably configured adapter RX input. Leave adapter TX and **all VCC/5 V/3.3 V power leads disconnected from the router**. Router uses its own proper supply. Change wiring only with power removed; do not attach an unpowered adapter in a way that back-powers it.
4. Capture boot output with correct serial framing/flow-control settings. A boot log, bootloader prompt, vendor CLI, login prompt and Linux shell are distinct results.
5. After a real shell: collect bounded `id`, `uname -a`, CPU/MTD/mount information, verify ABI and temporary storage, then run the tiny native program. Before a future flash write, require matching full-chip backups, image-layout understanding and a recovery method. The settings export is not that backup.

## 9. Related devices and owner priorities

The GVT Sagemcom receiver belongs in [mero-stb-lab](https://github.com/zacmero/mero-stb-lab), not in this router's experiment results. Its custom Vivo Play/Ekioh application is a preserved achievement, but the owner does **not** want more UI-only work as a substitute for shell access. Other-device UART failure reports must not be presented as measurements of our receiver. See the [GVT reference audit](https://github.com/zacmero/mero-stb-lab/blob/main/research/uart-reference-audit-2026-10-26.md).

The immediate next target is the **Askey RTV9015VW**, with a separate [handoff](https://github.com/zacmero/mero-askey/blob/main/HANDOFF.md). Its services, firmware and access must be measured independently; MitraStar credentials/IPs/results are not transferable evidence.

This publication changes documentation only. It does not initiate device tests, activate services, purchase equipment, or authorize soldering/flash writes.
