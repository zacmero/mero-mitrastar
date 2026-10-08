# Copied Sagemcom project: provenance and retained methods

## Recovery point

The initial MitraStar repository commit was a copy of **mero-stb-lab**, for a
**Sagemcom DSI74 V2 HD GVT** with an **STiH237 / ST40 / SH-4** platform.

- Baseline commit: `d694ccf` (resolve its full object ID with Git).
- Repository: https://github.com/zacmero/mero-mitrastar.
- Cleanup date: 2026-10-07.
- Files retired from the active tree: 170 tracked predecessor files.
- Local archive: `.local/predecessor/sagemcom-d694ccf.tar`.
- Archive SHA-256:
  `5019a57b41246a1461a87342eb476ad13ad29d994a8019ae4bb07cd52f869cf9`.

Every tracked predecessor file was verified present in the archive before its
working-tree copy was removed. The archive is ignored and local to this machine.
Git retains the complete predecessor snapshot independently. Existing
untracked `.serena/` state was preserved locally and ignored.

Four additional untracked predecessor artifacts (three Python bytecode files
and one USB-probe pcap) were found during final verification. They were moved
intact to `.local/predecessor/untracked-probes/`, outside the active workspace.

Inspect or restore a reference into local storage without replacing current
documents:

```bash
rtk git show d694ccf:docs/tooling/isolated-ethernet-lab.md
rtk proxy mkdir -p .local/reference
rtk proxy git archive --format=tar --output=.local/reference/sagemcom.tar d694ccf
rtk proxy sha256sum .local/predecessor/sagemcom-d694ccf.tar
```

## What carries over

| Method | Application to MitraStar |
| --- | --- |
| A lab laptop with separate control and device links | Supervise over SSH while observing the dedicated Ethernet segment. |
| Inventory routes before assigning addresses | Keep control access available; never assume an old interface name. |
| Match ARP/neighbor identity to the target MAC | Prevent probing another device that happens to own the expected IP. |
| Capture on the device-facing interface | Obtain traffic without inserting ARP spoofing into the household LAN. |
| Bounded runs and one action at a time | Associate UI operations with network events and negative findings. |
| Record timestamps, exact commands, hashes, and limitations | Reproduce experiments and distinguish missing evidence from failure. |
| Verify cleanup and control connectivity | Remove only lab changes and confirm SSH remains usable. |

## What must change

The old lab **supplied DHCP/DNS and emulated provisioning services** for a TV
receiver. The new target is a router with an already working management IP.
The laptop should initially be its client. Do not start the old DHCP/DNS runner,
add old public-IP aliases, relay old provisioning endpoints, or bridge the
device segment to the household network.

Old firmware probes, SH-4 build plans, Ekioh applications, remote-control maps,
USB/update filenames, ARP spoofing scripts, and negative service results are
device-specific. Preserve them as references in history, not as active
MitraStar tasks or evidence.

## Hygiene limitation

The predecessor commit includes captures, generated files, and
`web/certs/server.key` (a lab TLS private key). They have been removed from the
active tree, but remain in Git history and the local archive. Treat the key as
exposed and do not reuse it. Captures/configuration files may also contain
identifiers or sensitive traffic; their full contents have not been audited.
This cleanup did not rewrite remote history or establish that its contents are
safe for public redistribution.
