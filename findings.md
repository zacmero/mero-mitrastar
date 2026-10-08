# Findings

## This unit — user-reported on 2026-10-07

- MitraStar DSL-100HN-T1-NV; software BR_SA_113WUK0b15;
  hardware string tmp_hardware1.0.
- Web login succeeded at
  http://192.168.15.1/cgi-bin/html_sophia/sophia_main.html using label details.
- UI language changed to English. Firewall and Games & Applications pages
  were supplied; selected policy values were not supplied.
- Owner clarified access is currently via MitraStar Wi-Fi. Direct Ethernet
  has not yet been tested.
- Serial and LAN/WAN MAC identifiers are recorded in the device inventory.
- No shell, local boot log, chip photograph, or executable format has been
  observed for this unit.

## Copied repository

- Baseline commit: d694ccf (the only commit at task start).
- Origin: https://github.com/zacmero/mero-mitrastar.git.
- Active docs and scripts describe a Sagemcom DSI74 V2/STiH237/SH-4 device.
- Old lab scripts include receiver DHCP/DNS emulation, public-address aliases,
  ARP spoofing, provisioning endpoints, and hardcoded old MAC/IP/interface
  values. They are inappropriate defaults for this router.
- Tracked content includes pcaps, logs, generated probes, Python bytecode,
  and a lab TLS private key. Git history retains the original material.
- Untracked .serena/ existed before this task; preserve it locally and ignore it.

## Current shell host

- enp5s0: 192.168.1.97/24.
- Route to 192.168.15.1: via 192.168.1.1 on enp5s0.
- No local 192.168.15.0/24 interface was shown. This does not test the router's
  availability on the cable-facing laptop.

## Research leads

Partner reports exact-model Linux/MT7505/MIPS/UART/flash investigations and a
modified web UI serving Snake. Original articles were not retrieved; all claims
remain explicitly attributed and unverified. Retrieval attempts and comparison
requirements are recorded in research/mitrastar-leads.md.

## Preservation and new workspace

- Archived all 170 tracked predecessor files before removing active copies;
  verified every tracked path exists in .local/predecessor/sagemcom-d694ccf.tar.
- Original commit d694ccf and remote history are preserved. The old lab TLS key
  remains in history; removing it from the active tree does not unpublish it.
- Preserved existing untracked .serena/ locally; it is now ignored.
- Final verification found four additional ignored predecessor probe artifacts;
  moved them intact to .local/predecessor/untracked-probes/.
- New docs contain device identity, research confidence, predecessor recovery,
  Ethernet procedure, experiment format, and the shell/ABI/native-code roadmap.

## Next-session access facts

- Laptop-to-main SSH uses alias arch-local, according to the owner.
- Owner supplied cris for the laptop and zacmero for the main machine as host
  names. Confirm host identity versus username during the next setup session.
- Reverse SSH is said to be set up, but its address/user/interface have not
  been established in this session.
- Owner requested repo completion first, then main-machine rewiring, then SSH
  and direct-device connection tests. No network session is started now.
- Latest update supersedes that deferral: laptop Wi-Fi is now on the main
  network and Ethernet is connected to the MitraStar; SSH testing is authorized.
  Physical connection is owner-reported, IP/link/identity verification pending.

## Verified access — MITRA-NET-001

- Laptop: hostname cris-MS-1454, user cris, Wi-Fi wlp4s0 at 192.168.1.80.
- Main-to-laptop SSH succeeded using ~/.ssh/mero_stb_isolated_lab with
  IdentitiesOnly=yes; default-key authentication had failed. No tunnel required.
- Ethernet enp6s0: 192.168.15.3/24, carrier 1, 100 Mb/s, full duplex.
- 192.168.15.1 resolves to ac:c6:62:8d:99:78; bound ping 2/2, average 0.509 ms.
- Router route uses Ethernet; main-machine control route uses Wi-Fi.
- Wi-Fi default metric 600; Ethernet DHCP default metric 20100. IPv4/IPv6
  forwarding both 0. No route/profile/settings changes were needed.
- Source-bound HTTP GET returned 200, 3726 bytes, title Vivo. No authenticated
  session was established by the agent.
- TCP 80 open; 8080/8443 refused; 21/22/23/53/443/7547 timed out. UDP untested.
- RTK and nmap absent on laptop; curl/tcpdump present. No passive pcap collected.
- Raw evidence saved privately under
  .local/captures/20261008T005029Z-mitra-net-001/; reviewed result is
  docs/experiments/mitra-net-001.md.
