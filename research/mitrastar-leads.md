# Exact-model research leads

## Provenance and verification status

The owner pasted a partner conversation on 2026-10-07. It named Kitz Forum,
Maycon Vitali / Hack N Roll, and Luiz Boina / Medium, but supplied no exact
article URLs or firmware versions for those examples.

These reports are useful hypotheses for **DSL-100HN-T1-NV**, not verified
hardware or software properties of the owner's `BR_SA_113WUK0b15` unit.

| Reported lead | Attribution in partner conversation | Verification needed |
| --- | --- | --- |
| Linux 2.6.36, MT7505, MIPS 24Kc; read-only SquashFS and writable RAM-backed `/tmp` | Owner shell output on Kitz Forum | Locate original output and compare local `uname`, CPU, and mount evidence. |
| 16 MB SPI flash; physical Macronix MX25L12835F marking | Maycon Vitali / Hack N Roll | Locate article and inspect this unit's actual chip. |
| Bootloader detects MX25L12805D | Another investigation, not uniquely attributed | Locate boot log; do not merge this identification with the physical part above. |
| Accessible UART; examples with five positions, four populated | Independent exact-model investigations | Verify this PCB's ground, TX/RX, and voltage level before connecting. |
| Readable console at 115200 baud, U-Boot interaction, Linux login prompt | Luiz Boina / Medium | Locate article; confirm baud and access state on this unit. |
| Flash extraction, BusyBox/Boa, modified firmware serving a Snake page | Luiz Boina / Medium, second part | Locate original demonstration and firmware details; do not flash its image. |

A login prompt is not an unlocked shell. A browser executing Snake JavaScript
served by a router is different from the router executing a native MIPS
program. The desired first milestone here is the latter, from temporary
storage, without a flash modification.

## Source retrieval attempt — 2026-10-07

- Built-in web search failed twice with a response decoding error.
- The alternate search connector returned an error with no content.
- Direct search-engine requests did not yield usable article links; Google
  returned a redirect/interstitial and DuckDuckGo no parsed result links.
- [Kitz Forum's search page](https://forum.kitz.co.uk/index.php?action=search)
  returned bot verification; the original shell output was not read.
- [Hack N Roll's homepage](https://www.hacknroll.com/) was reachable, but it did
  not verify the cited device investigation. The homepage is a source-location
  lead, not support for the chip claims.
- The exact Medium articles remain unidentified.

No claim in the table was promoted to independently verified status. A later
source record should include the exact URL, author/date, firmware/board variant,
relevant output, access date, and comparison with this unit. Retrieve original
sources or obtain their links from the partner; meanwhile local Ethernet
inventory can proceed independently.

## ABI gate before compiling

With a genuine shell, capture `uname -a`, `/proc/cpuinfo`, `/proc/meminfo`,
`/proc/mtd`, and `mount`. Copy one existing executable and inspect its ELF header
and program headers offline. Determine endianness, ABI, ISA requirements, ELF
interpreter, libc and kernel compatibility. Do not select `mips` versus
`mipsel`, or a dynamic versus static build, from the model name alone.
