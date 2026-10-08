# MitraStar investigation

## Shell and access

- Always prefix shell commands with `rtk`. Use `rtk proxy` for unfiltered output.
- For Mero cloud SSH, start or reuse the agent and ControlMaster with
  `/home/zacmero/.local/bin/ssh_stable_merocloud`. Do not create ad-hoc cloud
  sessions or store passphrases in commands/configuration.
- The cloud helper is not the procedure for the local lab laptop.

```bash
rtk /home/zacmero/.local/bin/ssh_stable_merocloud \
  /home/zacmero/projects/content-factory-stack/ops/content-factory-vm.ssh.conf \
  content-factory-vm
rtk ssh -F /home/zacmero/projects/content-factory-stack/ops/content-factory-vm.ssh.conf \
  -o IdentityAgent="${XDG_RUNTIME_DIR:-/tmp}/mero-cloud-ssh-agent.sock" \
  content-factory-vm '<command>'
```

## Evidence and scope

- Target: MitraStar DSL-100HN-T1-NV, reported firmware BR_SA_113WUK0b15.
- Read `README.md`, `task_plan.md`, `findings.md`, and `progress.md` when resuming.
- Separate owner-reported UI values, directly captured evidence, external
  reports, and hypotheses. Do not promote another unit's hardware or ABI to a
  confirmed property of this unit.
- The predecessor is Sagemcom DSI74 V2/STiH237/SH-4. Its IPs, MACs, firmware,
  provisioning endpoints, toolchains, and success/failure results are not router
  defaults. Refer to `docs/reference/predecessor.md` for recovery.
- Use the smallest native tool that answers the experiment's question.

## Lab operations

- Identify the cable-facing host, interface, SSH route, and router neighbor MAC
  before target probing or interface changes.
- Preserve the SSH control path. Do not bridge Wi-Fi and Ethernet or start a
  DHCP/DNS server for this router lab.
- Begin with UI inventory, passive observation, and bounded service checks on
  the verified device. Record the scope, timestamp, commands, and outcome.
- Firmware writes, bootloader/environment writes, factory resets, router policy
  changes, and disruptive tests require an explicit session instruction before
  execution. The owner explicitly authorized native LAN SSH on port 22,
  restricted to laptop 192.168.15.3, and supplied-credential access checks.
  MITRA-SSH-015 records the applied policy and vendor console results. This
  authorization does not cover resets, reboots, other service changes, or flash
  writes. Documentation, repository hygiene, and bounded read-only inventory
  remain authorized.
- Do not guess UART pinouts, voltage levels, flash parts, or cross-compilation
  settings. UART output alone does not establish shell access.

## Repository hygiene

- Keep raw traffic, settings exports, dumps, credentials, private keys, generated
  binaries, bytecode, and agent caches in ignored local storage.
- Commit reviewed experiment summaries with artifact SHA-256 values. Redact
  session cookies, passwords, WAN/PPP credentials, and personal traffic.
- Do not rewrite the predecessor history as part of normal cleanup. Removing
  files from the working tree does not remove them from existing Git history.
