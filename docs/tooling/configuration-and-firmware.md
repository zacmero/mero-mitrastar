# Configuration access before firmware modification

Reviewed 2026-10-08 UTC. This is a decision record, not a submitted router change.
The owner requested review and publication; no service or firmware setting was
changed during this review.

## What the latest pass established

[MITRA-BACKUP-014](../experiments/mitra-backup-014.md) discovered the legacy
configurator through `/padrao`, authenticated `support`, and exported the
93,315-byte `romfile.cfg`. Its hash was independently checked against the
recorded artifact. This overturns the idea that the Sophia interface describes
the complete management surface.

The exported ACL records Web/Web2 and DNS/Ping on LAN. SSH, Telnet, FTP, SNMP,
HTTPS, and TR64 have `Activate="Yes"` with `Interface="Disable"`. TFTPD has both
activation and interface disabled. These are direct configuration observations.
They fit the measured port profile, but do not prove which service binaries
are present, which processes are running, or what privileges a login would have.

Legacy menu references include `RemMagSSH.html`, `RemMagSNMP.html`, and
`RemMagWWW.html` under remote management. The exact controls and apply-handler
behavior have not yet been reviewed in this record. Separate encrypted
`web_passwd` and `console_passwd` fields mean successful web authentication does
not establish the same console password or account privileges.

## Proposed next experiment — MITRA-SSH-015

1. Read the legacy SSH management view using the established support login.
   Resolve its path from the observed menu; record current values, interface
   choices, source-IP restrictions, apply endpoint, session fields, and warnings.
2. Preserve the current export and its hash. Keep the original HTTP access and
   laptop Wi-Fi SSH control route available. Specify exactly how the original
   SSH setting would be restored.
3. Present a concrete single-service change: SSH on the isolated LAN, restricted
   to the lab client if the interface supports it. Do not activate all services
   or use WAN/Both as a shortcut. This is a proposed policy change; it has not
   been submitted and needs an explicit instruction for that operation.
4. After an authorized apply, verify TCP 22 and the SSH handshake first. Then
   attempt only the understood owner-authorized account/credential combination.
   Record whether the result is a vendor CLI, a Linux shell, or authentication
   failure. Do not assume the historical Spanish-firmware behavior applies.
5. If execution is available, collect bounded read-only runtime information:
   id, uname, CPU, memory, MTD, mounts, and available transfer tools. Establish
   ISA, byte order, libc/interpreter, and executable temporary storage before
   choosing a cross-compiler or custom program.
6. Verify the management/control path and restore the original access setting
   according to the agreed experiment scope. Record both configuration and
   runtime effects. Do not reboot or activate additional services implicitly.

Prefer the understood native management operation over editing/restoring the
entire export. Review found the raw vendor XML-like export is not accepted by
a standard XML parser; generic XML serialization could alter vendor syntax.

## Can we make custom firmware?

A modified vendor image is a plausible later branch: the original Boina report
demonstrated filesystem modification and physical SPI rewriting on another
unit. It does not establish compatibility or recovery for this build/board.
Building a replacement OS is a separate problem requiring board/kernel/driver
support, including the modem functions we intend to retain.

The configuration export contains settings, not kernel/root-filesystem/bootloader
images. It cannot be repacked into working firmware by itself. Before a flash
write we need this unit's verified image/flash backup, chip/partition/layout
identification, compression and image-integrity requirements, a compatible write
method, and a recovery path. A full source release has not been obtained.

The strings `P660HNT1Av2` and `firmware.mitrastar.com.tr` are useful investigation
leads. They may be inherited defaults; they do not identify the SoC or authorize
using a P-660 image. Preserve this distinction in any subsequent image research.

## Hardware and publication

Board photographs remain useful for chip and UART identification, but are no
longer a prerequisite for inspecting this concrete software configuration route.
They do not authorize flash writes or establish a UART voltage/pinout.

Publish reviewed experiment notes and artifact hashes. Keep complete configuration
exports private: readable settings and encrypted credential fields are still
sensitive. The separate `romfile-analysis` branch's raw export is excluded from
this master documentation publication. Do not treat a configuration export as
a full flash recovery backup.

## MITRA-SSH-015 result

After explicit owner authorization, native LAN-only SSH with laptop client ranges opened Dropbear 2019.78. The support account reaches an interactive vendor console; remote exec requests are denied. Software version and uptime were queried successfully. Linux shell access, client-filter enforcement, and persistence across reboot remain unverified; exports in that round still reflected the old ACL. Read [MITRA-SSH-015](../experiments/mitra-ssh-015.md) for the exact observations and remaining gates.

## MITRA-SOFTWARE-018 update

Fresh export generation is understood: the native backup JavaScript waits for ConfigFilter.cgi, then downloads `/romfile.cfg`. A bounded 20-second request with the observed XHR headers succeeded in 14.229 seconds. The subsequent fresh export matches LAN / port 22 / client range 192.168.15.3. Previous shorter timeouts did not cover that duration. Reboot persistence is supported by short runtime uptime with SSH retained, but no controlled restart was performed.

Known vendor-command exec and SFTP requests both authenticated and failed at the channel request. The interactive vendor console is still available. These are access distinctions, not proof that SSH or the filesystem lacks any other capability. See [the new record](../experiments/mitra-software-018.md).

## Native firmware upload — MITRA-SOFTWARE-019

The legacy menu references /cgi-bin/pages/maintenance/firewareUpgrade/firewareUpgrade.html, which returns this build's multipart firmware-upload form. Managed-status fields render 0 in the main page and referenced iframe; backend image acceptance is untested. This supersedes any inference from Sophia alone that no native upload surface exists. No upload, upgrade check, firmware image download, or recovery validation occurred. A matching image, format/integrity analysis and recovery remain prerequisites to writing firmware. See [019](../experiments/mitra-software-019.md).
