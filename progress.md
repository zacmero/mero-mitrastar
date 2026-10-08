# Progress

## 2026-10-07 — recognize and reset

- Read the copied repository inventory, baseline commit, device overview,
  direct-Ethernet methodology, and old lab runner/relay defaults.
- Inspected local interface addresses and the route to the management IP.
- Requested the cable-facing machine's SSH identity and interface.
- Created persistent planning and evidence notes.
- Documented source retrieval limitations without promoting partner claims to
  local facts.
- Archived all 170 predecessor files and verified archive completeness before
  retiring them from the active tree. Preserved Git history and local agent state.
- Added the MitraStar device inventory, roadmap, Ethernet runbook, research
  status, predecessor recovery/methods, experiment format, and ignore rules.
- Owner clarified Wi-Fi access and requested deferring SSH/device tests until
  after repo completion and main-machine rewiring.
- Next: final local documentation, archive, and Git hygiene checks.
- First final check found three untracked bytecode files and an old USB pcap;
  preserved them under .local/predecessor/untracked-probes rather than deleting
  them. Rechecking the resulting workspace.
- Recorded cris / zacmero as owner-supplied host labels; login/destination
  mapping is deferred to the next SSH session.
- Local verification passed: documentation links/formatting, Git diff whitespace,
  ignore rules, absence of old binary/capture/key files from the active tree,
  and byte-for-byte archive comparison for all 170 tracked predecessor files.
  Four additional ignored artifacts were preserved separately. Archive mode 0600.
- Repo correction phase complete locally. Changes have not been committed/pushed.
- Owner then authorized SSH testing now: laptop Wi-Fi is on the main network,
  Ethernet is newly connected to the MitraStar. Beginning host/access discovery.
- Confirmed cris is the laptop login user. The default-key SSH attempt failed;
  the existing dedicated mero_stb_isolated_lab key succeeded. Laptop hostname
  cris-MS-1454, destination 192.168.1.80; no tunnel required.
- Verified device-facing enp6s0/192.168.15.3, expected router MAC, 100 Mb/s full
  duplex, two ping replies, separate Wi-Fi control route, and disabled forwarding.
- Collected source-bound unauthenticated HTTP response and nine TCP connect
  checks. Saved raw evidence privately and documented MITRA-NET-001.
- Updated identity, README, roadmap, and Ethernet instructions with the observed
  topology and the absence of RTK/nmap on the laptop. No network settings changed.
- Final consistency check passed: 13 active files, valid local documentation
  links, clean whitespace, no predecessor generated/secret files in the active
  tree, and matching network artifact hashes. All requested work and authorized
  connection checks are complete. Git changes remain local and uncommitted;
  shell access remains unverified.
