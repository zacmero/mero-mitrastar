# Direct Ethernet lab

## Current state and intended topology

Direct Ethernet is verified through the laptop: `enp6s0`, `192.168.15.3/24`,
100 Mb/s full duplex; the router neighbor matches `ac:c6:62:8d:99:78`.
Control SSH uses `wlp4s0`, `192.168.1.80/24`, independently of the device link.
See [MITRA-NET-001](../experiments/mitra-net-001.md) for the observed baseline.
The agent's main host is `192.168.1.97`; it reaches the router through the laptop
for experiments rather than using its household-gateway route.

```text
agent/main machine --- existing SSH control path --- lab laptop
                                                    |
                                              Ethernet cable
                                                    |
                                             MitraStar LAN port
                                              192.168.15.1
```

The observed control path uses the main household network over Wi-Fi. Keep it
separate from the Ethernet segment. Record routes again before changing settings
or starting a new session; do not assume the topology has stayed the same.

The owner uses `ssh arch-local` from the laptop to reach the main machine.
Reverse SSH was verified as `cris@192.168.1.80`, hostname `cris-MS-1454`, using
the existing dedicated lab key. No tunnel is required on the observed network.

```bash
rtk proxy ssh -i ~/.ssh/mero_stb_isolated_lab -o IdentitiesOnly=yes cris@192.168.1.80
```

RTK is absent on this laptop. Keep main-machine shell commands prefixed with
`rtk`, and send native commands as SSH payloads. For example, the inventory
command `rtk proxy ip -br address` below is executed on this laptop as:

```bash
rtk proxy ssh -i ~/.ssh/mero_stb_isolated_lab -o IdentitiesOnly=yes \
  cris@192.168.1.80 'ip -br address'
```

The remaining examples use RTK-prefixed terminal notation; use the same remote
wrapper and remove the inner `rtk proxy` for this laptop. Artifact paths then
refer to the machine executing the command. Alternatively run native payload
commands in the established laptop console; no RTK installation is required.

Use a **LAN** Ethernet port. Keep the existing Wi-Fi connection initially.
Do not reset the router, alter firewall settings, connect DSL for an experiment,
or enable forwarding, bridging, Internet sharing, DHCP, or DNS servers.

## 1. Inventory on the cable-facing Linux laptop

Run these on the laptop, not on the main machine:

```bash
rtk proxy hostname
rtk proxy ip -br link
rtk proxy ip -br address
rtk proxy ip -4 route
rtk proxy ip -6 route
rtk proxy ip route get 192.168.15.1
rtk proxy nmcli -f DEVICE,TYPE,STATE,CONNECTION device
rtk proxy nmcli -f NAME,UUID,TYPE,DEVICE connection show --active
rtk proxy printenv SSH_CONNECTION
rtk proxy sysctl net.ipv4.ip_forward net.ipv6.conf.all.forwarding
```

`SSH_CONNECTION`, when set, identifies the client/server IPs for that session.
Check the route to its client IP as well. If the laptop is using a different
network manager or OS, use its equivalent inventory before applying the
NetworkManager procedure below.

Connect the Ethernet cable and repeat the address/route inventory. DHCP may
have activated an existing profile automatically. Confirm whether it added a
default route or DNS. A LAN LED indicates link availability; it does not
establish IP reachability or negotiated speed.

## 2. Choose a client profile without changing the control route

If the cable already has a usable lease and preserves control routing, inspect
that profile before replacing it. The router's DHCP behavior is not yet locally
confirmed. Do not assume the laptop should provide DHCP as in the old receiver
lab.

With NetworkManager and an identified dedicated Ethernet interface, a temporary
DHCP client profile can reject new default routes and router DNS. Replace
`LAB_ETH` with the actual interface. Verify that `mitra-lab` does not already
name a profile, and record the previous Ethernet profile first.

```bash
rtk proxy nmcli connection show mitra-lab
rtk proxy sudo nmcli connection add type ethernet ifname LAB_ETH con-name mitra-lab \
  connection.autoconnect no ipv4.method auto ipv4.never-default yes \
  ipv4.ignore-auto-dns yes ipv4.route-metric 50 ipv6.method disabled
rtk proxy sudo nmcli connection up mitra-lab
rtk proxy ip -br address show dev LAB_ETH
rtk proxy ip -4 route
rtk proxy ip route get 192.168.15.1
```

The first command should report the profile absent before creation. Activating
the new profile replaces any active profile on that Ethernet interface.
Proceed from a local console if the existing SSH session depends on it.
Do not activate the profile if doing so would replace the only control path.

If Wi-Fi and Ethernet are both on `192.168.15.0/24`, ordinary traffic might
still choose Wi-Fi. Confirm the selected interface; the commands below bind
device checks explicitly to `LAB_ETH`. Do not disconnect Wi-Fi merely to force
the route while it is carrying control traffic.

If no DHCP lease is obtained, inspect link state and existing settings. A
static address is a later fallback after checking subnet and address conflicts;
do not assume `192.168.15.2` is free.

## 3. Verify link and identity before service checks

```bash
rtk proxy cat /sys/class/net/LAB_ETH/carrier
rtk proxy ethtool LAB_ETH
rtk proxy ip route get 192.168.15.1 oif LAB_ETH
rtk proxy ping -I LAB_ETH -c 2 -W 2 192.168.15.1
rtk proxy ip neigh show 192.168.15.1 dev LAB_ETH
```

Expected LAN MAC from the owner's UI: `ac:c6:62:8d:99:78`. A ping timeout does
not prove the device is absent; inspect neighbor resolution. If the neighbor
MAC differs, stop and identify the actual device before scanning it. Record
carrier, speed/duplex if available, laptop address, route, and MAC.

`ethtool`, `tcpdump`, and `nmap` are optional installed tools. Check their
availability locally before using the following steps; no package installation
or privileged networking change has been performed by the agent.

## 4. Bounded observation and read-only management baseline

Create a private local artifact directory; replace `UTC_STAMP` with the run's
UTC timestamp. Avoid committing raw HTTP responses or captures.

```bash
rtk proxy date -u +%Y%m%dT%H%M%SZ
rtk proxy install -d -m 700 .local/captures/UTC_STAMP
rtk proxy curl --interface LAB_ETH --noproxy '*' --connect-timeout 3 --max-time 10 \
  --dump-header .local/captures/UTC_STAMP/http-headers.txt \
  --output .local/captures/UTC_STAMP/http-body.html \
  http://192.168.15.1/cgi-bin/html_sophia/sophia_main.html
```

An unauthenticated response may be a login page or redirect. Record status,
redirect destination, and any server header. Do not infer shell access from
HTTP reachability. Review content before quoting it; do not store login cookies
or label passwords in commands or tracked files.

For a passive capture, run this in a separate laptop terminal while viewing
read-only UI pages. No reboot is required for the initial capture.

```bash
rtk proxy sudo timeout --signal=INT 60 tcpdump -p -i LAB_ETH -nn -s 0 -U \
  -w .local/captures/UTC_STAMP/ethernet.pcap \
  'ether host ac:c6:62:8d:99:78 or arp or (udp and (port 67 or port 68))'
rtk proxy sha256sum .local/captures/UTC_STAMP/ethernet.pcap
```

`timeout` may return status 124 when its planned limit is reached. Check that
the pcap was written and tcpdump exited. This captures traffic visible on the
laptop port; it is not a mirror of all router WAN/Wi-Fi traffic. When revisiting
the router UI, confirm whether the browser itself is using Wi-Fi or Ethernet.

After the MAC match, run the initial TCP connect check only when the ordinary
route to `192.168.15.1` selects `LAB_ETH`. With a connect scan, `-e` alone does
not force the operating system's socket route. Defer this check if Wi-Fi is
still the selected route. Scope it to the device and a short service list:

```bash
rtk proxy ip route get 192.168.15.1
rtk proxy nmap -n -Pn -sT --max-retries 1 --host-timeout 45s \
  -p 21,22,23,53,80,443,7547,8080,8443 \
  -oA .local/captures/UTC_STAMP/tcp-baseline 192.168.15.1
```

Confirm the actual route selected for TCP connections, especially with two
interfaces on the same subnet. Check capture/source-address evidence before
calling this an Ethernet result. Do not add exploit scripts, broad UDP scans,
credential guessing, or update requests to this baseline. An open Telnet/SSH
port is evidence of a listener, not evidence of accepted credentials or root.

## 5. Record and clean up

Summarize the result as `MITRA-NET-001` using
[the experiment format](../experiments/README.md). Include the route and MAC
check so a later session cannot confuse the Wi-Fi and cable observations.

If this session created `mitra-lab`, remove only that profile and restore the
previous Ethernet profile if there was one:

```bash
rtk proxy sudo nmcli connection down mitra-lab
rtk proxy sudo nmcli connection delete mitra-lab
rtk proxy nmcli connection show --active
rtk proxy ip -br address
rtk proxy ip -4 route
rtk proxy ip -6 route
```

Check the control-peer route and confirm the SSH session still works. Leave
pre-existing settings in place. The procedure creates no bridge/NAT/DHCP/DNS
service and requires no router configuration changes.

## Router SSH from the laptop

Native LAN SSH is enabled for `192.168.15.3`. Use the pinned-host connection command and read-only vendor-console examples in [MITRA-SSH-015](../experiments/mitra-ssh-015.md#owner-connection-from-the-laptop). Login is `support`, with the owner-provided label password. This reaches the vendor console; Linux shell access is unproven.
