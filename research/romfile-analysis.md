# Reverse Engineering Analysis of MitraStar `romfile.cfg`

Date: 2026-10-08
Device: MitraStar DSL-100HN-T1-NV (Vivo Brazil)
Software Build: `BR_SA_113WUK0b15`
Artifact: [`research/romfile.cfg`](romfile.cfg)
Pristine Recovery Backup: `.local/backups/pristine-factory-romfile.cfg`
SHA-256: `c903943d9f2522263d45de1226d39b6c95b01ba3ad88539039a27a3ef40a85bf`

---

## 1. Executive Summary

The router configuration backup file `romfile.cfg` was exported non-destructively through the authenticated generic ZyXEL/MitraStar management interface at `/cgi-bin/pages/maintenance/backupRestore/ConfigFilter.cgi` using the `support` administrative account.

Analysis of this artifact provides:
1. **Zero Integrity Barriers:** The configuration file is pure ASCII XML with no CRC checksum, digital signature, or binary packaging.
2. **Exhaustive Service Accounting:** The internal `<ACL>` table explicitly accounts for the port scan measurements: Telnet, SSH, FTP, and SNMP daemons are present in firmware but set to `Interface="Disable"`.
3. **Firmware Lineage Identification:** Confirms the base platform is `P660HNT1Av2` (ZyXEL P-660HN-T1A v2 / TrendChip/MediaTek MIPS architecture).
4. **Viable Software Shell Vectors:** Identified both ACL service enabling and `<Autoexec>` boot-time command injection hooks as actionable software paths to obtain shell access.

---

## 2. File Format and Integrity Verification

* **File Size:** 93,315 bytes.
* **Encoding:** 7-bit ASCII / UTF-8 compatible plaintext XML.
* **Header / Trailer:** Starts with `<ROMFILE> <Romconvert> ...` and terminates with `</ROMFILE>\n`.
* **Integrity Validation:** Unlike older ZyNOS binary ROM files or compressed LZW blobs, this build does **not** employ a checksum, hash trailer, or signature block. The firmware's `cfg_manager` daemon parses the XML tags directly into memory at boot time.

---

## 3. Platform Architecture and Components

* **Architecture Lineage:**
  * Tag `<AutoFwUpgrade>` specifies `ServerAddr="firmware.mitrastar.com.tr"` and `Directory="P660HNT1Av2"`.
  * JavaScript runtime globals in `common.js` specify `model-name: "HGW-2501GNU-RC"`.
  * The system runs TrendChip/MediaTek Linux with `cfg_manager` orchestrating system initialization, iptables, and daemon lifecycles.
* **Configuration Version:** `ConfigVersion="20171123"`.

---

## 4. Access Control List (`<ACL>`) Analysis

The `<ACL>` block defines port and interface accessibility for all management services:

```xml
<ACL>
	<Common Activate="Yes" />
	<Entry0 Activate="No" ScrIPAddrBegin="0.0.0.0" ScrIPAddrEnd="0.0.0.0" Application="ALL" Interface="Both" />
	<Entry1 Activate="Yes" Application="Web" Port="80" Interface="LAN" AllorRange="all" ... />
	<Entry2 Activate="Yes" Application="Telnet" Port="23" Interface="Disable" AllorRange="all" ... />
	<Entry3 Activate="Yes" Application="FTP" Port="21" Interface="Disable" AllorRange="all" ... />
	<Entry4 Activate="Yes" Application="SNMP" Port="161" Interface="Disable" AllorRange="all" ... />
	<Entry5 Activate="Yes" Application="DNS" Interface="LAN" AllorRange="all" ... />
	<Entry6 Activate="Yes" Application="Ping" Interface="LAN" AllorRange="all" ... />
	<Entry7 Activate="Yes" Application="SSH" Port="22" Interface="Disable" AllorRange="all" ... />
	<Entry8 Activate="Yes" Application="HTTPS" Port="443" WanPort="13443" Interface="Disable" AllorRange="all" ... />
	<Entry9 Activate="No" Application="TFTPD" Port="69" Interface="Disable" AllorRange="all" ... />
	<Entry10 Activate="Yes" Application="Web2" Port="80" Interface="LAN" AllorRange="all" ... />
	<Entry11 Activate="Yes" Application="TR64" Port="5555" Interface="Disable" AllorRange="all" ... />
</ACL>
```

### Direct Correlation with Network Probes
* **Port 80 (Web):** Set to `Interface="LAN"` -> Probed `OPEN`.
* **Ports 21 (FTP), 22 (SSH), 23 (Telnet), 161 (SNMP), 443 (HTTPS):** Set to `Interface="Disable"` -> Probed `TIMEOUT` (iptables drops packets).
* **Port 53 (DNS):** Set to `Interface="LAN"` -> Responded `REFUSED` due to inactive WAN routing.

---

## 5. Shell Access Vectors via Configuration Modification

### Vector 1: ACL Interface Unlocking
Modifying the ACL entries in `romfile.cfg`:
```xml
<Entry2 Activate="Yes" Application="Telnet" Port="23" Interface="LAN" AllorRange="all" ScrIPAddrBegin1="0.0.0.0" ScrIPAddrEnd1="0.0.0.0" />
<Entry7 Activate="Yes" Application="SSH" Port="22" Interface="LAN" AllorRange="all" ScrIPAddrBegin1="0.0.0.0" ScrIPAddrEnd1="0.0.0.0" />
```
* **Effect:** On boot, `cfg_manager` updates iptables and binds `telnetd` (port 23) and `dropbear`/`sshd` (port 22) to the LAN interface.
* **Authentication:** Telnet and SSH authenticate against the local user database. The `support` account password is the factory label password.

### Vector 2: `<Autoexec>` Boot Hook Injection
The `<Autoexec>` tag is currently empty (`<Autoexec />`). On TrendChip/MediaTek firmware, `cfg_manager` writes commands inside `<Autoexec>` to `/etc/autoexec.sh` at boot:
```xml
<Autoexec>
	<Entry cmd1="telnetd -p 2323 -l /bin/sh" />
</Autoexec>
```
* **Effect:** Spawns an unauthenticated BusyBox root shell listening on port 2323 upon boot, completely bypassing login authentication.

### Vector 3: Global ACL Override
Setting `Entry0` to active:
```xml
<Entry0 Activate="Yes" ScrIPAddrBegin="0.0.0.0" ScrIPAddrEnd="0.0.0.0" Application="ALL" Interface="Both" />
```
* **Effect:** Globally permits all applications on both LAN and WAN.

---

## 6. Safety and Emergency Recovery Runbook

Before applying any modified configuration to the router, verify the recovery gates:

### Pristine Backup Storage
* **Tracked In-Repo Backup:** `research/romfile.cfg`
* **Local Pristine Golden Image:** `.local/backups/pristine-factory-romfile.cfg`
* **SHA-256 Checksum:** `c903943d9f2522263d45de1226d39b6c95b01ba3ad88539039a27a3ef40a85bf`

### Restoration Procedure (Web UI)
1. Navigate to `http://192.168.15.1/padrao` -> log in as `support`.
2. Go to `/cgi-bin/pages/maintenance/backupRestore/backupRestore.html`.
3. Under "Restore Configuration", select the golden backup `.local/backups/pristine-factory-romfile.cfg`.
4. Click **Upload** (`uiDoUpdate()`).
5. The router will write the configuration to flash and automatically reboot (approx. 60–90 seconds).

### Hardware Emergency Reset (Failsafe)
If the router becomes unresponsive due to an XML syntax error:
1. Depress the physical **RESET** button on the rear panel with a pin for 10–15 seconds while powered on.
2. Release the button; the SYS LED will blink rapidly while the router reloads factory defaults from read-only SquashFS.
3. The router IP will return to `192.168.15.1`, restoring factory `support` credentials and default settings.
