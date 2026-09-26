


# Lab 01 - FortiGate Basic Setup and Internet Access

## Purpose (Security Concept)
Establishes the firewall's core function: separating LAN and WAN zones and
enforcing a default-deny policy where only explicitly allowed traffic passes.
This is the foundation every other firewall feature builds on.

## Topology
Cloud1 (eth1) -- port1 [FortiGate] port2 -- Alpine

## IP Addressing
| Device | Interface | IP |
|---|---|---|
| FortiGate | port1 (WAN) | 192.168.118.10/24 |
| FortiGate | port2 (LAN) | 192.168.1.1/24 |
| Alpine | eth0 | DHCP 192.168.1.100-200 |

## What I configured
- Static WAN/LAN interface IPs and admin access
- Default route via 192.168.118.2
- DNS servers
- DHCP server on LAN
- Firewall policy LAN -> WAN with NAT and logging enabled

## Verification
- Alpine received a DHCP IP and pinged 8.8.8.8 and google.com successfully
- Log & Report > Forward Traffic shows allowed sessions

## What I learned
- A FortiGate blocks all traffic by default until a policy explicitly allows it
- Interfaces, routes and policies each serve a distinct role
- NAT lets private LAN addresses reach the internet while hiding internal IPs

## Files
- lab01-basic-setup.conf