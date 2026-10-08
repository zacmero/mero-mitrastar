# MITRA-SSH-015 — Native SSH management inspection

Date: 2026-10-08 UTC. Status: native LAN SSH enabled after explicit owner
authorization; `support` vendor console reached. Linux shell not established.
The initial inspection below was read-only; subsequent authorized results
are recorded separately.

## Verified connection

SSH control host: `cris@192.168.1.80`, hostname `cris-MS-1454`.
Router-facing interface: `enp6s0`, IPv4 `192.168.15.3/24`.
Route to `192.168.15.1` uses that interface. Neighbor MAC:
`ac:c6:62:8d:99:78`, matching the recorded device.

Authenticated legacy web access used the owner-provided `support` credentials.
Only login and bounded HTTP reads were performed; no settings handler was
submitted. Credentials and raw responses remain in ignored local storage in
the original lab checkout.

## Native page and controls

GET `/cgi-bin/pages/maintenance/remotemgmt/RemMagSSH.html` returned HTTP 200,
20,342 bytes. Artifact: `.local/ssh-015/1791437026/RemMagSSH.html`.
SHA-256: `21f0171f193ab1d2100606be33a1fee51a2f1452cea78c88291ffbec608e967e`.

| Control | Observed value / behavior |
| --- | --- |
| `SSHAccessInterface` | `Disable` selected; options `Both`, `Disable`, `LAN`, `WAN` |
| `SSHservicePort` | `22` |
| Secured client selector | `all` selected; `range` available |
| Client ranges | Four begin/end pairs, `txtsship1` through `txtsship8`; currently `0.0.0.0` |
| Form action | POST `/cgi-bin/pages/maintenance/remotemgmt/RemMagSSH.asp` |
| Apply marker | `RemMagSSH_H=1` in JavaScript submission |
| Other form values | `PageNum=7`, `Activate_H=Yes`, `Application_H=SSH` |
| Session handling | Page loads `/cgi-bin/sessionkey.cgi`; copies `gblsessionKey` into POST data |

`RemMagSSHSave()` checks port and IP ranges, sets `btnssh1` from `radioFlag`,
and submits via the parent page's jQuery POST. `radioFlag=0` selects range;
`1` selects all. It also includes the common activation fields. The script
compares each begin/end pair, so an equal begin and end is accepted by its
client-side range comparison. Server behavior remains untested.

The session-key endpoint returned HTTP 200. Its value is private, ephemeral,
and must be obtained again within the same authenticated session as a future
submission. A fresh key must also be obtained for rollback.

## Proposed next step, not yet submitted

1. Reverify the laptop address and preserve the current configuration snapshot.
2. Use the native form to select `LAN`, keep port `22`, and restrict client
   ranges to `192.168.15.3` (equal begin/end; avoid unused wildcard ambiguity).
3. Read the management page back and verify its actual saved values; perform
   one bounded TCP/SSH banner check from the laptop.
4. If SSH responds, use the supplied owner credentials without guessing;
   distinguish vendor command interface from Linux shell and privilege level.
5. If the service does not become available, report that observation. Do not
   widen access to WAN or enable unrelated services.

Rollback: submit the same native SSH form with `SSHAccessInterface=Disable`,
then verify the saved page and TCP behavior. This rollback is identified from
the UI but has not been executed or validated. Existing HTTP management and
the laptop's Wi-Fi SSH control path remain available during the proposed test.

The repository's `AGENTS.md` requires an explicit session instruction before
router policy changes. Owner authorization was requested after preparing
this concrete change. This is configuration work, not a firmware rewrite.


## Authorized application and observed access

The owner explicitly authorized restricted LAN SSH and a credential login.
The initial attempt timed out; a subsequent read showed the unchanged page
and TCP 22 timed out. Retrying after preserving the generated export returned
HTTP 200 from the native settings handler. Readback selected `LAN`, port `22`,
and the `range` radio option. All four begin/end pairs were set to
`192.168.15.3` to avoid unused wildcard ambiguity. `radioFlag=0` was read back.
The hidden `btnssh1` field is rendered as `all` even when the range radio is
checked; it is updated by the native save script and is not itself evidence
that the range was lost.

TCP 22 opened and advertised `SSH-2.0-dropbear_2019.78`. Host identity was
recorded in the laptop's dedicated lab known-hosts file. The server offered
an RSA host key; `HostKeyAlgorithms=+ssh-rsa` was scoped to this connection,
without changing global SSH configuration.

- `admin`: an exec request for `id` was rejected; interactive access closed
  without a console prompt. No command output or Linux shell was obtained.
- `support`: password authentication was explicitly confirmed by the SSH
  client. Exec requests were rejected, but a PTY session yielded a `>` vendor
  prompt. `id` was rejected with guidance to use `?`; `exit` closed normally.
- `?` listed `exit`, `save`, `sys`, `restoredefault`, `lan`, `igmp`, `wlan`,
  `nat`, `voip`, `tr69`, `net`, and `portmirror`. Only help and identity checks
  were sent. No save, reset, or other console mutation was invoked.

The currently downloaded export still reports the earlier disabled SSH ACL,
with the same hash as the pre-change snapshot. The export-generation request
was time-limited and did not establish a completed refresh. Therefore the
artifact does not establish the persisted post-change policy. Live UI and
service state are verified; source filtering enforcement and persistence
across reboot remain untested. No reboot was performed.

Evidence in the original lab checkout:

| Artifact | SHA-256 |
| --- | --- |
| `.local/ssh-015/1791437333/result.json` | `cb08396a27b757c790536286b4717b271cf4cd6590f2a387f79b1464e192a895` |
| `.local/ssh-015/1791437333/after.html` | `7bf42d0281aa4342a67bff1bc98692969ca0233950d00568b9b6e2ebd1346425` |
| `.local/ssh-015/1791437333/before-romfile.cfg` | `4d30a77dbf594f7c9fa8cb4bd49bf075355cf46cf5aaefa0d9f663eaab121c92` |
| `.local/ssh-015/1791437472/support-login.json` | `49dc5769611a7f523ba590aeb5387758deebe5c572e9ceb9cb11234285177771` |
| `.local/ssh-015/1791437537/support-interactive.json` | `887e87aff1a58064b2f14483ac3241ee6df11ce419ca299bf7cd443bd8d7ffad` |
| `.local/ssh-015/1791437602/console.json` | `be0d43c9af265125a2f854f996a0806700b7706c09eda52987cce7a0dbd9c8a0` |
| `.local/ssh-015/1791437643/console.json` | `5d9a0acf9a2344e361de673841dd08355da04e6bda5bb370f5d2a402c9391dfb` |
| `.local/ssh-015/1791437679/console.json` | `716a58ffa56fd94e7dec4386c8fccb6ef6317c722aa3bb3e5cd31429f5dbc20a` |

The original MITRA-BACKUP-014 export remains separately preserved with hash
`c903943d9f2522263d45de1226d39b6c95b01ba3ad88539039a27a3ef40a85bf`.
Further read-only help inspection should identify supported inventory
commands before attempting any operating-system-level work.

## Initial vendor inventory

`sys ?` lists `passwd`, `telnetd`, `state`, `swversion`, `uptime`, `reboot`,
`exitOnIdle`, `wan2lan`, `tecal`, and `countryCode`. Only the explicitly
informational commands below were then invoked:

```text
>sys swversion
BR_SA_113WUK0b15
>sys uptime
 06:12:12 up  6:12,  load average: 0.97, 0.90, 0.82
>net ?
Valid commands are:
?                route
```

This directly confirms the reported software build through the console.
The uptime result does not establish CPU architecture, executable ABI, or
Linux shell privileges. The network route subcommand is a further inventory
lead; its usage and permitted operations still need inspection. No SSH
session was left running. Restricted LAN SSH remains enabled as authorized;
HTTP management and the laptop control connection continued working.
