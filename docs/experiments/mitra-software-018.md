# MITRA-SOFTWARE-018 — Fresh settings export and SSH channel tests

Date/time: 2026-10-08, 15:51–16:00 UTC.
Device: DSL-100HN-T1-NV / BR_SA_113WUK0b15.
Question: can the native backup reflect live SSH settings, does SSH offer a
command/file-transfer channel, and what further network information can the
vendor console display?

## Connection and scope

Reverified `cris-MS-1454`, Ethernet `enp6s0` at `192.168.15.3/24`, route to
`192.168.15.1`, and neighbor MAC `ac:c6:62:8d:99:78`. Laptop control remained
on Wi-Fi through `cris@192.168.1.80`. Used the existing owner-authorized support
credential, pinned router host key, and restricted LAN SSH setting.

No policy changes, service activation, restore, save, reboot, reset, upload,
flash access, or hardware connection were submitted. Raw captures/settings
remain in ignored local storage; credential values are excluded here.

## Native export procedure and result

Authenticated through the existing legacy support flow. GET of
`/cgi-bin/pages/maintenance/remotemgmt/RemMagSSH.html` returned HTTP 200 and
exactly the previously recorded LAN-enabled page bytes. GET of
`/cgi-bin/pages/maintenance/backupRestore/backupRestore.html` returned HTTP 200.
An initial GET of `/romfile.cfg` returned HTTP 404.

The native `backup_settings()` JavaScript first loads
`/cgi-bin/pages/maintenance/backupRestore/ConfigFilter.cgi`, then navigates to
`/romfile.cfg`. Reproduced that sequence with the authenticated cookie session,
`X-Requested-With: XMLHttpRequest`, and the backup-page Referer:

1. GET ConfigFilter.cgi, bounded by a 20-second socket timeout: HTTP 200,
   zero response-body bytes, completed in **14.229 seconds**.
2. GET `/romfile.cfg`, eight-second socket timeout: HTTP 200 in 0.067 seconds,
   **93,434 bytes**.

The fresh SSH ACL entry contains `Activate="Yes"`, `Application="SSH"`,
`Port="22"`, `Interface="LAN"`, `AllorRange="range"`, and all four source
ranges set to `192.168.15.3` through `192.168.15.3`. This agrees with the live
page; the prior downloaded file contained `Interface="Disable"`.

Earlier three-to-ten-second generation timeouts were shorter than this
successful generation. This resolves export freshness through the native
backup flow. The header's necessity and exact generation-time variation were
not isolated. Do not infer that an existing `/romfile.cfg` is refreshed merely
by downloading it. This remains a settings backup, not a firmware/flash image.

## SSH protocol requests

From the verified laptop, authenticated as support with password, using strict
host-key checking and `HostKeyAlgorithms=+ssh-rsa`. Each test had an eight-second
client deadline and sent no file data. Equivalent commands, entered on laptop:

```sh
ssh -T -v -o HostKeyAlgorithms=+ssh-rsa \
  -o UserKnownHostsFile="$HOME/.local/state/mero-mitrastar/known_hosts" \
  -o StrictHostKeyChecking=yes support@192.168.15.1 'sys swversion'

ssh -T -v -o HostKeyAlgorithms=+ssh-rsa \
  -o UserKnownHostsFile="$HOME/.local/state/mero-mitrastar/known_hosts" \
  -o StrictHostKeyChecking=yes -s support@192.168.15.1 sftp
```

The local helper supplied the credential through transient askpass/environment,
kept it off command arguments, and removed the helper afterward. Authentication
succeeded in both tests. The known-valid vendor command's exec request failed
on channel 0, exit 255, in 2.265 seconds. The SFTP subsystem request failed on
channel 0, exit 255, in 1.593 seconds. Thus the earlier `id` failure was not the
only failed exec request. These observations concern this account/build/session;
they do not establish every account's capability or the server's internal cause.
No command-execution or file-transfer channel was obtained by these requests.

## Interactive console results

Submitted `sys swversion`, `sys uptime`, `net ?`, `net route`, and
`igmp showtable`, then a separate `net route disp` session. Ended both with
`exit`. The console still answers read-only vendor commands.

- Software version: `BR_SA_113WUK0b15`.
- Uptime: approximately eight minutes, versus multi-hour uptime in the earlier
  session. Restricted LAN SSH remains accessible. This is consistent with
  persistence through an intervening restart; its cause and timing were not
  independently observed and no controlled reboot test was run.
- `net route` prints `net route [disp|add|del]` usage. Only `disp` was used.
- `net route disp` prints an IPv4 kernel route table containing LAN
  `192.168.15.0/24` on br0, `127.0.0.0/16` on lo, and `239.0.0.0/8` on br0.
  **No IPv4 default route appears.** This snapshot helps explain the lack of
  observed outbound provider activity; it does not cover IPv6, other routing
  tables, or prove the DSL/WAN interface's full state.
- `igmp showtable` prints column headings without group rows.

## Referenced web status follow-up

GET `/menu.json` returned 200 and referenced the legacy connection-status view.
GET `/cgi-bin/pages/connectionStatus/naviView_partialLoad.html` returned 200;
its source references `/cgi-bin/statusview.cgi`. GET of that display handler
returned 200 in 2.56 seconds. Its rendered interface row labels `ETHER WAN`
as `Down`, address `N/A`. Preserve that literal label: it does not independently
establish every DSL or IPv6 interface's state. The route/status snapshots agree
that no active IPv4 WAN route was demonstrated.

The menu also references a traffic-status tab. The resolved candidate
`/cgi-bin/pages/systemMonitoring/trafficStatus/tab.json` returned 404; no traffic
test or settings action was attempted. A menu entry does not prove its view
exists in this build. HTTP remained available after the backup/SSH tests.

## Primary-source follow-up

Queries recorded: exact build plus firmware/download; exact model plus GPL;
exact model plus firmware/GitHub/dump; vendor `sys telnetd -t`; MT7505 command
interpreter. No exact-build firmware/source or matching flag explanation was
acquired in these bounded searches. This is not an exhaustive absence claim.

[Maycon's February 2018 emulation article](https://maycon.hacknroll.io/embedded-hacking/2018/02/19/embedded-hacking-emulando-binarios.html)
demonstrates offline SquashFS extraction and QEMU user-mode execution from
another exact-model unit's SPI dump. It supplies a useful analysis method,
not this unit's ABI or a retrieved dump.
[Boina's Snake article](https://medium.com/@luizboina55/hardware-hacking-modifying-an-old-router-firmware-to-play-a-snake-game-7e309484185a)
was revisited: its extraction/modification/write route remains physical SPI,
not an Ethernet shell method. Neither inspected article yielded a downloadable
exact-build image in the rendered links. The generic GNU telnet daemon manual
is a different implementation and does not explain this vendor flag.

## Artifacts

All paths are within the canonical repo and ignored. SHA-256 identifies private
evidence without publishing settings, authenticated pages, or SSH debug logs.

| Artifact | SHA-256 |
| --- | --- |
| `.local/software-018/1791474660/0-RemMagSSH.html` | `7bf42d0281aa4342a67bff1bc98692969ca0233950d00568b9b6e2ebd1346425` |
| `.local/software-018/1791474660/1-backupRestore.html` | `668ca54512c4e033a47b4b6585393e9d83ffc52d5592810d39c0ac8c55f2d595` |
| `.local/software-018/1791474660/metadata.json` | `e4eb6173497572573db6a9c0683565fddcacca3b1c6610bb095ed39048e91fcf` |
| `.local/software-018/1791474894/1-romfile.cfg` | `61767b23a9bf2afc5b639c6612640af3ed65728c02b974d97846fa29b46e38dc` |
| `.local/software-018/1791474894/metadata.json` | `df281d760263fd3721c380a8dc582f0981cf6569c637effe357e14da9d3fd9c6` |
| `.local/software-018/1791474909/ssh-protocol.json` | `200344c29d540502821ad1fb2f87feee77290dbaf935e4df40b53089ae032805` |
| `.local/ssh-015/1791474989/console.json` | `33580af059d9b460d402b920ba3e24bdd2b771d160f092554c24889b497a66b3` |
| `.local/ssh-015/1791475025/console.json` | `a0de460befac2267335f9e70189cf0fe84b78bc078765dd6ff7cfc790fee259c` |
| `.local/software-018/1791475123/0-menu.json` | `11bc0959e56853c4fae79d7263a839329ef015cdf5e10041bfe3453a45d8c1e8` |
| `.local/software-018/1791475172/0-naviView_partialLoad.html` | `9da31db1aa8c1615e929ab0a05ccb125211ed60be990833a6a8e092f01cd3268` |
| `.local/software-018/1791475191/0-statusview.cgi` | `76a90bc778ce3e90d34d5e6dde4a509b0ddce6bffceeedf0b9f870477ad0390f` |

## Result and next decision

Fresh native export now agrees with live SSH policy. Interactive vendor display
works; the tested exec and SFTP channels do not. No Linux shell, file access,
executable ABI, or native program execution was established. Sessions closed;
LAN SSH remains enabled under the earlier authorization, and HTTP/control
connectivity stayed available.

Continue software work with deeper observed read-only status/log views and
exact-build firmware/handler research. The route snapshot makes WAN status a
more useful next measurement than another undirected port scan. Establish
`sys telnetd -t` semantics before proposing any service activation. UART pin
measurement and an independently verified flash read remain complementary
hardware routes to obtain implementation evidence; photos alone do not do so.
