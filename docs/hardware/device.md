# Device inventory — DSL-100HN-T1-NV

Identity recorded 2026-10-07 from the owner's management-page transcription.
The agent independently matched every identity field through the About page
and inspected protected pages in an authenticated browser session. See
[MITRA-NET-001](../experiments/mitra-net-001.md) and
[MITRA-UI-002](../experiments/mitra-ui-002.md).

## Identity

| Field | Reported value |
| --- | --- |
| Manufacturer | MitraStar |
| Model | DSL-100HN-T1-NV |
| Software version | BR_SA_113WUK0b15 |
| Hardware version string | tmp_hardware1.0 |
| Serial number | ACC6628D9978 |
| WAN MAC | AC:C6:62:8D:99:78 |
| LAN MAC | AC:C6:62:8D:99:78 |
| Management URL | http://192.168.15.1/cgi-bin/html_sophia/sophia_main.html |
| Current UI language | English, changed by owner |
| Current laptop connection | Ethernet to MitraStar; Wi-Fi to main network for SSH control |

The identical LAN/WAN MAC entries are copied as displayed. Confirm the LAN
neighbor MAC before probing. A Wi-Fi BSSID need not equal the LAN MAC.
`tmp_hardware1.0` is a UI string, not an established PCB revision.

The serial and MAC identify this unit. No label password or login cookie is
stored here. Public sharing can omit the unit identifiers if desired.

## Management pages supplied

**Firewall:** controls for default policy and WAN-interface ping, each with
Accept/Reject options. New rules expose name, protocol, local/remote ports,
local/remote IPs (`*` means all IPs), and action. The rules table lists local
and remote policy details. The transcription does not identify the selected
policies or any installed rules. Follow-up browser inspection confirmed Reject
selected for both Default Policy and WAN ping. No rule entries were visible in
the captured view.

**Games & Applications:** application/device selection for automatic port
forwarding, an IP address field, and a selection/removal list. No mapping was
reported as saved. These controls describe traffic policy and forwarding;
they do not establish a shell or native program execution.

**About Vivo Box:** model/version identifiers, LED explanations, and button
descriptions. These explanations are UI documentation, not a live LED log.

## LED legend supplied by the UI

| LED | Meaning |
| --- | --- |
| Internet | Steady green: PPP/DHCP active; green blink: negotiation; rapid green blink: online traffic; steady red: authentication failure. |
| Synchronism | Steady green: line active; green blink: no line detected; rapid green blink: training; off: router off, according to the supplied legend. |
| WPS | Steady green: active; green blink: open negotiation; red for 20 seconds: registration problem; off: disabled. |
| Wi-Fi | Steady green: available; green blink: negotiation/transmission; off: unavailable. |
| LAN | Steady green: Ethernet available; green blink: port traffic; off: unavailable. |
| Power | Steady green: operating normally; green blink: booting; off: powered off. |
| All LEDs blink green together | Firmware update in progress; do not power off or reboot. |

## Buttons supplied by the UI

- **WPS:** opens a short registration window.
- **Reconfigurar:** holding the button restores factory configuration and
  removes customized settings, including Wi-Fi names/passwords.
- **ON/OFF:** powers the device on or off.

## Direct network observations

Laptop `cris-MS-1454`, user `cris`: Ethernet `enp6s0` at `192.168.15.3/24`,
100 Mb/s full duplex. The neighbor for `192.168.15.1` matched
`ac:c6:62:8d:99:78`; two bound pings replied. The management path returned an
unauthenticated HTTP 200 response with title Vivo. TCP 80 accepted a connection;
8080 and 8443 refused; the other six tested ports timed out. No shell access
or authenticated session was established by these observations.

Wi-Fi control remains `wlp4s0` at `192.168.1.80`. Native tools are invoked
remotely because RTK is not installed on the laptop.

## Still unknown for this unit

- SoC marking, PCB revision, RAM, SPI flash model/capacity, UART layout/levels.
- Kernel version, CPU details, ELF byte order/ABI, libc, dynamic loader.
- Full DHCP configuration, services beyond the short TCP baseline, shell access.
- Root filesystem, writable storage, flash partition map, recovery procedure.
- Complete firewall rule set, selected WAN operation mode, remote-management
  services beyond the inspected menu. Default policy and WAN ping are confirmed
  Reject; status showed no active DSL/PPP data at collection time.

Authenticated management inventory is complete. It exposes diagnostics but no
visible shell control. The next step is the access investigation.
Board photographs are conditional on network findings; opening is not required
for the first session.
