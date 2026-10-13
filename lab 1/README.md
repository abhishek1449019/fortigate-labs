# FortiGate Lab 1: LAN to Internet (GNS3)

A beginner-to-job-ready lab that gives a private LAN internet access through a FortiGate firewall using interface configuration, a default route, DNS, and a firewall policy with NAT.

## Topology
VPCS1 (192.168.10.10/24)
   |
Ethernet switch
   |
port2 (LAN 192.168.10.1/24)
  FortiGate 7.0.9
port1 (WAN 192.168.118.10/24)
   |
GNS3 Cloud/NAT (gateway 192.168.118.2)
   |
Internet
| Device | Interface | IP address | Gateway |
|---|---|---|---|
| FortiGate | port1 (WAN) | 192.168.118.10/24 | 192.168.118.2 |
| FortiGate | port2 (LAN) | 192.168.10.1/24 | n/a |
| VPCS1 | eth0 | 192.168.10.10/24 | 192.168.10.1 |

## Tools
GNS3, FortiGate VM 7.0.9, VPCS, Ethernet switch, Cloud/NAT node

## Configuration steps (Web GUI)
1. **Network > Interfaces**: set port1 to 192.168.118.10/255.255.255.0 (role WAN) and port2 to 192.168.10.1/255.255.255.0 (role LAN). Enable PING, HTTPS and SSH administrative access.
2. **Network > Static Routes**: create a default route 0.0.0.0/0 via gateway 192.168.118.2 on port1.
3. **Network > DNS**: set primary 8.8.8.8 and secondary 1.1.1.1.
4. **Policy & Objects > Firewall Policy**: create LAN-to-WAN (port2 to port1, all/all/ALL, Accept, NAT enabled, log all sessions).
5. On VPCS1: ip 192.168.10.10/24 192.168.10.1

The same configuration as CLI is in [fortigate-lab1.conf](fortigate-lab1.conf).

## Verification
| Test | Command | Expected |
|---|---|---|
| LAN gateway reachable | ping 192.168.10.1 (VPCS) | Replies |
| Internet by IP | ping 8.8.8.8 (VPCS) | Replies |
| Routing table | get router info routing-table all | S* default via 192.168.118.2 |
| FortiGate internet | execute ping 8.8.8.8 | Replies |
| Traffic logs | Log & Report > Forward Traffic | LAN-to-WAN sessions |

## Why each concept matters
**Interface roles (WAN/LAN):** Define trust boundaries. WAN is untrusted, LAN is trusted. Policies are written between interfaces.
**Default route:** Without it the firewall does not know where internet-bound traffic goes.
**Firewall policy:** FortiGate denies all traffic by default (implicit deny). Nothing passes until a policy allows it.
**Source NAT:** Private IPs (192.168.10.0/24) are not routable on the internet. NAT rewrites them to the WAN IP so replies come back.
**Logging:** Required for troubleshooting and auditing in real companies.
**Allow access (ping/https/ssh):** Controls how admins manage the device. Enable only on trusted interfaces in production.

## Troubleshooting
Ping to 8.8.8.8 fails but gateway works: check default route and that the policy has NAT enabled.
Policy does not match: check policy order and the srcintf/dstintf pair.
Run a packet capture: diagnose sniffer packet any 'host 8.8.8.8' 4
Trace a session: diagnose debug flow commands

## Key takeaways
Interfaces, route, policy and NAT are the four things every FortiGate deployment needs for basic internet access.