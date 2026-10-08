# This unit's board photographs

All photos and documentation belong to the single project at
`/home/zacmero/projects/mero-mitrastar`. The temporary `mero-mitrastar-docs`
worktree was removed after verifying its tracked content matched master and
there were no remaining untracked files there.

## Archive layout

- `originals/`: 17 original HEIC files, preserved byte-for-byte locally and
  ignored by Git. Camera metadata remains in those originals.
- `reviewed/`: JPEG copies with orientation applied, camera metadata removed,
  and maximum dimension 2400 pixels, for GitHub and documentation viewing.
- `manifest.json`: original and reviewed paths, sizes, and SHA-256 values.

No original photo was deleted or overwritten. Copies are viewing derivatives;
inspect the originals for the highest available detail. Do not move the photo
archive into another project. Larger review crops are ignored under
`.local/hardware/board-photo-review/`.

## Index

| Photo | Subject |
| --- | --- |
| [IMG_8349](reviewed/IMG_8349.jpg) | PCB solder-side overview |
| [IMG_8350](reviewed/IMG_8350.jpg) | Solder-side lower section and A1AE marking |
| [IMG_8351](reviewed/IMG_8351.jpg) | Solder-side component footprints |
| [IMG_8352](reviewed/IMG_8352.jpg) | Solder-side close-up near header footprint |
| [IMG_8353](reviewed/IMG_8353.jpg) | Solder-side upper section |
| [IMG_8354](reviewed/IMG_8354.jpg) | Solder-side Ethernet connectors and nearby header pads |
| [IMG_8355](reviewed/IMG_8355.jpg) | Component-side overview in enclosure |
| [IMG_8356](reviewed/IMG_8356.jpg) | MT7505N, flash and populated header overview |
| [IMG_8357](reviewed/IMG_8357.jpg) | RF section and MT7592N vicinity |
| [IMG_8358](reviewed/IMG_8358.jpg) | MT7505N and MT7583N vicinity |
| [IMG_8359](reviewed/IMG_8359.jpg) | Ethernet/DSL connector and transformer area |
| [IMG_8360](reviewed/IMG_8360.jpg) | Power input and RF area |
| [IMG_8361](reviewed/IMG_8361.jpg) | MXIC flash and header close-up |
| [IMG_8362](reviewed/IMG_8362.jpg) | Power-regulator area |
| [IMG_8363](reviewed/IMG_8363.jpg) | MT7592N close-up |
| [IMG_8364](reviewed/IMG_8364.jpg) | MT7505N and MT7583N close-up |
| [IMG_8365](reviewed/IMG_8365.jpg) | Power-regulator close-up |

## Initial findings

- Main package marking: MediaTek `MT7505N`, readable in IMG_8356 / IMG_8364.
- Eight-pin package marking: MXIC `25L12835F`, readable in IMG_8361, consistent
  with Macronix MX25L12835F. Published density is 128 Mbit, or 16 MiB.
- MediaTek `MT7592N` is readable in the RF-area close-up IMG_8363.
- MediaTek `MT7583N` is readable in IMG_8364; its exact function is not
  established by this photographic review.
- A five-position header with four populated pins is beside the processor
  and flash. This matches a published exact-model UART lead, but function,
  pin assignment, voltage, and baud have not been measured on this board.

See [MITRA-BOARD-017](../docs/experiments/mitra-board-017.md) for source
qualification and the next measurement steps.
