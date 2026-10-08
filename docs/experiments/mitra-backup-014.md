# MITRA-BACKUP-014 — Legacy configurator discovery, support authentication, and romfile.cfg export

Date/time: 2026-10-08T04:05:59Z (lab timezone: 2026-10-08 01:05:59 -0300)
Device: MitraStar DSL-100HN-T1-NV, firmware `BR_SA_113WUK0b15`
Questions:
1. Does an unreferenced or legacy web management interface exist beyond the carrier-skinned Sophia UI?
2. Does the `support` user account authenticate to administrative maintenance pages?
3. Can the router's configuration be exported safely, and what does it reveal about service configuration and disabled daemons?

Host and interfaces: Laptop `cris-MS-1454`, Wi-Fi `wlp4s0` at `192.168.1.80/24` (control route), Ethernet `enp6s0` at `192.168.15.3/24` (device-facing route).
Control route: SSH key `/home/zacmero/.ssh/mero_stb_isolated_lab` to `cris@192.168.1.80`.
Target IP and observed MAC: `192.168.15.1`, `ac:c6:62:8d:99:78`.

## Procedure

1. Probed `/padrao` endpoint via HTTP GET; observed 302 redirect to `/cgi-bin/login.asp` with `SESSIONID` cookie.
2. Followed redirect to generic ZyXEL Web-Based Configurator at `/cgi-bin/login.html`.
3. Inspected `Multi_Language.js` (698,160 bytes), identifying comprehensive maintenance strings (Backup, Restore, Firmware Upgrade, Remote Management).
4. Authenticated user `support` using label credentials via challenge-response to `/cgi-bin/index.asp?<token>`:
   - Digest: `base64(support:md5(sid + ":" + password))`.
5. Navigated to `/cgi-bin/pages/maintenance/backupRestore/backupRestore.html` (HTTP 200).
6. Invoked `/cgi-bin/pages/maintenance/backupRestore/ConfigFilter.cgi` to generate the backup file.
7. Downloaded `/romfile.cfg` via authenticated GET.
8. Saved the raw file to ignored local storage (`.local/exports/1791432359/romfile.cfg`) and calculated SHA-256 hash.
9. Analyzed configuration XML tags and ACL entries offline.

## Observations

1. **Configurator Architecture:**
   - The device hosts a dual-interface web system: the carrier-branded Sophia interface (`/cgi-bin/html_sophia/`) and the full generic ZyXEL/MitraStar configurator (`/cgi-bin/main.html` / `/cgi-bin/pages/`).
   - The user `support` with the label password authenticates successfully to the generic interface.
   - Menu structure in `/menu.json` reveals hidden maintenance paths including `remotemgmt` (`RemMagSSH.html`, `RemMagSNMP.html`, `RemMagWWW.html`) and `backupRestore.html`.

2. **Configuration File Export (`romfile.cfg`):**
   - File format: readable vendor XML-like configuration with a `<ROMFILE>` root tag, 93,315 bytes. The whole export is not encrypted; some credential fields are encrypted.
   - Configuration version: `20171123`.
   - Firmware upgrade parameters contain directory `P660HNT1Av2` and host `firmware.mitrastar.com.tr`. These are source leads, not confirmation of the physical platform, a compatible firmware image, or a currently usable download service.

3. **Service & Port Accounting via ACL Table:**
   The `<ACL>` configuration block is consistent with the observed reachability:
   - **Web (Port 80):** `Activate="Yes" Application="Web" Port="80" Interface="LAN"` (Open).
   - **Telnet (Port 23):** `Activate="Yes" Application="Telnet" Port="23" Interface="Disable"` (Filtered / Timed out).
   - **FTP (Port 21):** `Activate="Yes" Application="FTP" Port="21" Interface="Disable"` (Filtered / Timed out).
   - **SNMP (Port 161):** `Activate="Yes" Application="SNMP" Port="161" Interface="Disable"` (Filtered / Timed out).
   - **DNS (Port 53):** `Activate="Yes" Application="DNS" Interface="LAN"` (Listening, replies REFUSED).
   - **SSH (Port 22):** `Activate="Yes" Application="SSH" Port="22" Interface="Disable"` (Filtered / Timed out).
   - **HTTPS (Port 443):** `Activate="Yes" Application="HTTPS" Port="443" WanPort="13443" Interface="Disable"` (Filtered / Timed out).
   - **Ping (ICMP):** `Activate="Yes" Application="Ping" Interface="LAN"` (Active).
   - **TR64 (Port 5555):** `Activate="Yes" Application="TR64" Port="5555" Interface="Disable"`.
   - **TFTPD (Port 69):** `Activate="No" Application="TFTPD" Port="69" Interface="Disable"`.
   - **Web2 (Port 80):** `Activate="Yes" Application="Web2" Port="80" Interface="LAN"`.
   - **ALL rule:** `Activate="No" Application="ALL" Interface="Both"`.

   SNMP normally uses UDP 161; the previous TCP 161 timeout is not a UDP SNMP
   availability test. The ACL independently records its interface as disabled.
   See [RFC 3417](https://www.rfc-editor.org/rfc/rfc3417.html#section-3.2).

4. **Account Entries:**
   - Account block contains two entries (`Entry0` and `Entry1`) with AES-encrypted display masks (`_encrypted_41455300...`) and separate `web_passwd` and `console_passwd` attributes.

## Artifacts

Stored privately under `.local/exports/1791432359/`:
- `romfile.cfg` (93,315 bytes, SHA-256 `c903943d9f2522263d45de1226d39b6c95b01ba3ad88539039a27a3ef40a85bf`).

## Result and Limits

- Successfully executed an authenticated, non-destructive configuration export from the router.
- Directly established configuration entries for SSH, Telnet, FTP, SNMP, HTTPS, and TR64 with disabled interfaces. This supports a service-access-policy explanation for the timeouts; it does not prove each daemon binary is present or currently running.
- Established that unreferenced administrative handlers and interfaces exist on this firmware build.
- A configuration backup is not a firmware/flash dump. Kernel, root filesystem, image layout, and recovery data were not exported by this operation.
- Review reproduced the export's size/hash and the allowlisted ACL fields. A standard XML parser rejected the raw document under both UTF-8 and Latin-1 decoding; preserve the original bytes and do not assume a generic XML edit/round-trip is a valid vendor restore file.
- Configuration modification / restore was not attempted and requires explicit owner instruction.

## Next Decision

Inspect the discovered legacy SSH management view and its save handler before
proposing a single LAN-only service change with rollback. This is a concrete
software lead; firmware rewriting is not the first requirement. See the
[configuration/firmware decision](../tooling/configuration-and-firmware.md).
Board photographs remain useful for independent hardware identification.
