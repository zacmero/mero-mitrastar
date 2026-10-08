# Configuration export analysis

Date: 2026-10-08. Device: MitraStar DSL-100HN-T1-NV, reported build
`BR_SA_113WUK0b15`. This reconciles the `romfile-analysis` branch with the
reviewed experiment records. No restore or service activation has been tested.

## Artifact and format

The authenticated legacy interface exported a 93,315-byte settings backup.
Canonical private artifact: `.local/exports/1791432359/romfile.cfg` in the
original lab checkout. SHA-256:
`c903943d9f2522263d45de1226d39b6c95b01ba3ad88539039a27a3ef40a85bf`.

The export is readable vendor XML-like text with a `<ROMFILE>` root and
`ConfigVersion="20171123"`. Python's standard XML parser rejects the captured
bytes. Preserve the original bytes rather than reserialize the document.
No visible signature block proves neither the absence of restore-time
validation nor acceptance of edits. The firmware parser and integrity checks
have not been inspected. This is a settings backup, not a flash image.

The raw file contains credential-bearing fields. It is excluded from the
merged working tree. Its earlier commit on `romfile-analysis` remains in Git
history; merging and deleting a file do not remove historical copies.

## Observed management policies

The `<ACL>` entries explicitly list these configured application policies:

| Application | Activation | Interface | Port when recorded |
| --- | --- | --- | --- |
| ALL | No | Both | — |
| Web / Web2 | Yes | LAN | 80 |
| Telnet | Yes | Disable | 23 |
| FTP | Yes | Disable | 21 |
| SNMP | Yes | Disable | 161 |
| DNS / Ping | Yes | LAN | — |
| SSH | Yes | Disable | 22 |
| HTTPS | Yes | Disable | 443; WAN 13443 |
| TFTPD | No | Disable | 69 |
| TR64 | Yes | Disable | 5555 |

These are configuration entries, not an inventory of installed or running
daemons. SSH timeouts are consistent with the disabled policy but do not
prove the packet-dropping mechanism. TCP 161 checks do not establish SNMP
availability, since conventional SNMP uses UDP. A DNS `REFUSED` reply alone
does not identify the reason for refusing recursion.

The legacy menu references an SSH management page. Its actual controls and
submission handler are the next evidence to collect. Changing a policy may
expose or start a service, or do neither; a bounded port and protocol check
must establish the result. Successful web authentication does not establish
console credentials, Linux shell access, or privilege level.

## Platform and boot-hook leads

`AutoFwUpgrade` includes `firmware.mitrastar.com.tr` and `P660HNT1Av2`.
Those strings are research leads that may be inherited defaults. They do not
establish this board's identity, ABI, or compatible firmware image.

An empty `<Autoexec />` tag is present. Its accepted schema, execution
semantics, target script path, and privileges are unknown on this build.
The other branch's proposed command example was speculative; it is not a
verified shell route or a planned change. No global `ALL` policy override
or unauthenticated listener is needed for the SSH management investigation.

## Configuration and recovery route

Start with the native SSH page, preserve the original configuration, and
identify the exact LAN-only option and rollback controls before a change.
Keep HTTP access and the laptop's Wi-Fi SSH path available. The detailed
sequence is in [configuration-and-firmware.md](../docs/tooling/configuration-and-firmware.md).

The legacy backup/restore page is observed; restore behavior, validation,
reboot duration, and failure recovery have not been tested. The exported
snapshot is not proven factory-pristine. Factory reset erases settings and
is not a substitute for a verified flash recovery method. Do not assume
reset duration, filesystem behavior, or resulting credentials from another
model. Hardware photographs will document this unit independently.
