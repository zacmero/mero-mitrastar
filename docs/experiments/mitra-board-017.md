# MITRA-BOARD-017 — Board photo archive and visual identification

Status: photographic review only. No device probing, electrical measurement,
UART hookup, programmer attachment, reset, reboot, or flash write performed.
The owner supplied 17 HEIC photos of both PCB sides and component close-ups.
Camera timestamps are not treated as a verified experiment date.

## Organization

The canonical checkout is `/home/zacmero/projects/mero-mitrastar`, on master.
A temporary second worktree used for parallel branch documentation has been
removed. Both worktrees were at ff63a8a, its tracked working tree was clean,
and its photo files had already been moved by the owner into the canonical
checkout. No user files were discarded during removal.

Originals remain under `board_pictures/originals/`, with every original hash
checked before and after organization. Metadata-stripped JPEG review copies
are under `board_pictures/reviewed/`. The complete [photo index](../../board_pictures/README.md)
and [hash manifest](../../board_pictures/manifest.json) map every image.
Raw camera originals and larger viewing crops remain local, inside this repo.

## Observations and qualification

| Item | Evidence | What is established |
| --- | --- | --- |
| Main package | IMG_8356 / IMG_8364 | Printed MediaTek MT7505N marking |
| Eight-pin flash package | IMG_8361 | MXIC and 25L12835F markings; sticker partially covers another area |
| RF-area package | IMG_8363 | Printed MediaTek MT7592N marking |
| Companion package | IMG_8364 | Printed MediaTek MT7583N marking; function not established |
| Serial-header candidate | IMG_8356 / IMG_8361 | Five positions, four populated pins beside processor/flash |
| PCB solder-side text | IMG_8350 | A1AE marking; meaning/revision mapping unverified |

The flash marking identifies a Macronix MX25L12835F lead on this unit.
Its [manufacturer datasheet](https://www.macronix.com/Lists/Datasheet/Attachments/8653/MX25L12835F%2C%203V%2C%20128Mb%2C%20v1.6.pdf) specifies serial flash with
128-Mbit density (16,777,216 bytes = 16 MiB), using a 2.7–3.6 V supply.
That is a flash specification, not a measurement of this board's actual
supply or the UART logic voltage. The full package/order suffix is not
reliably transcribed here. No flash contents, JEDEC ID, partition map, or
recovery behavior has been read.

The main package marking now independently supports the MT7505 family
reported on other exact-model units. Photos do not establish active CPU ISA,
byte order, executable ABI, libc, kernel version, or operating-system shell
access. The earlier console memory value is runtime-reported usable memory,
not a photographically identified physical RAM capacity.

The five-position header resembles the header described in the
[exact-model UART investigation](../../research/mitrastar-leads.md), but its
function is still a hypothesis. No GND/TX/RX/VCC assignments or left-to-right
pin numbering are inferred from another board. Photos do not prove the
adapter voltage or that the device was powered off when photographed.

## Next measurements

1. With router power disconnected, identify ground by continuity on this
   board. Record an unambiguous photo orientation and pad numbering.
2. Inspect the candidate header and traces; measure logic voltage and activity
   with suitable tools before attaching a USB-to-TTL adapter. Use measurements
   to distinguish supply, transmit, receive, and any other pins.
3. Once verified, prepare a passive receive-only serial capture using GND and
   router TX to adapter RX. Keep adapter VCC disconnected. Adapter and signal
   levels must match the measured interface.
4. Capture ordinary boot output only within an explicitly authorized power/
   reboot procedure; do not interrupt U-Boot, edit environment, or write flash.
   115200 baud is a published test candidate, not a confirmed setting here.
5. Keep the software path active: inspect matching console/SSH/telnetd handler
   code or firmware. UART output may still present a login barrier.

No flash-writing procedure is proposed by this photographic identification.
An independent verified full flash backup and recovery method remain separate
requirements before firmware modification.
