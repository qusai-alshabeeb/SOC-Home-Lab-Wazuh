# SOC Attack & Defense Lab — Network Architecture

## 1. Purpose and current status

This document describes the revised target VMware network layout for the Wazuh SOC Home Lab. The lab is being simplified to one internal Host-only network for Kali, Windows endpoints, and the Wazuh server, with pfSense providing the LAN gateway and NAT uplink through VMnet8.

**Target configuration:** VMnet2 is the single internal lab network. Kali is being moved from VMnet3 to VMnet2, and VMnet3 is planned for removal. This is a topology change in progress, not a completed/validated change.

**Validation status:** Confirm VMnet2's type and DHCP settings, move Kali to VMnet2, verify all device IPs and gateways, and test pfSense WAN access and Wazuh telemetry before treating the revised topology as operational.

## 2. Virtualization and systems

- Host: Windows 11.
- Hypervisor: VMware Workstation 17.6.0.
- Firewall/router: pfSense CE VM, with three virtual network adapters.
- SIEM: Ubuntu Server 24.04.5 LTS running Wazuh 4.14.
- Endpoints: Windows 10 Client 02 (active) and Windows 10 Client 01 (standby / powered off).
- Attacker: Kali Linux VM.

## 3. VMware virtual networks

| VMware Network | Type | Subnet | VMware DHCP | Purpose |
|---|---|---|---|---|
| VMnet8 | NAT | 192.168.73.0/24 | Enabled | pfSense WAN uplink |
| VMnet2 | Host-only (target; verify in VMware) | 192.168.206.0/24 | Disabled (target) | Single internal lab segment |
| VMnet3 | Planned for removal | 192.168.232.0/24 (legacy) | Disabled (legacy) | Retired attacker segment |

The target design uses VMnet2 as the only internal Host-only network. VMware DHCP should remain disabled on VMnet2 if pfSense is intended to provide DHCP; otherwise document static addressing. VMnet3 is no longer part of the target topology and should be removed only after all VMs have been disconnected from it.

## 4. Current VM-to-network assignments

| VM / Component | VMware Network | Role |
|---|---|---|
| pfSense Adapter 1 (WAN / em0) | VMnet8 | NAT uplink |
| pfSense Adapter 2 (LAN / em1) | VMnet2 | Internal lab gateway |
| pfSense Adapter 3 (OPT1 / em2) | To be disconnected/removed after transition | Legacy VMnet3 adapter; not part of target design |
| Ubuntu Wazuh Server | VMnet2 | SIEM / monitoring server |
| Windows Client 02 | VMnet2 | Active monitored endpoint |
| Windows Client 01 | VMnet2 | Standby endpoint; powered off until a two-endpoint scenario is needed |
| Kali Linux | VMnet2 (target; move from VMnet3) | Attacker workstation |

## 5. Current pfSense interface addressing

| pfSense Interface | VMware Network | Current Address / Mode |
|---|---|---|
| WAN (em0) | VMnet8 | DHCP; observed address 192.168.73.135 |
| LAN (em1) | VMnet2 | 192.168.206.1/24 |
| OPT1 (em2) | Legacy VMnet3 | 192.168.232.1/24 (legacy; to be retired) |

The pfSense dashboard previously showed these WAN, LAN, and OPT1 addresses. Confirm the current values in the console or WebGUI before relying on them during a test. The IP addresses of Ubuntu Wazuh, Kali, and the Windows clients should be recorded from their current network configuration rather than inferred from the earlier NAT setup.

## 6. Target network diagram

```text
                         VMware VMnet8 (NAT)
                                |
                         pfSense WAN / em0
                                |
                         +------+------+
                         |   pfSense   |
                         | Firewall /  |
                         |   Router    |
                         +------+------+
                                |
                       LAN / em1 — VMnet2
                       192.168.206.1/24
                       SINGLE INTERNAL LAB
                                |
             +------------------+-------------------+
             |                  |                   |
       +-----+------+    +------+---------+   +-----+----------+
       | Kali Linux |    | Windows Client |   | Ubuntu Wazuh   |
       | Attacker   |    | 02             |   | Server         |
       +------------+    +----------------+   +----------------+
                                |
                         Windows Client 01
                         Standby / OFF

       Legacy VMnet3 / OPT1 is planned for removal.
```

## 7. Intended traffic flow and security boundaries

- Kali Linux initiates authorized tests from VMnet2 after the move is completed.
- Kali and Windows Client 02 will be on the same subnet; their direct same-subnet traffic will not traverse pfSense routing/firewall rules.
- Windows Client 02 is the primary target on VMnet2.
- Wazuh Server is on VMnet2 to receive endpoint telemetry from the monitored Windows endpoint.
- pfSense LAN provides the default gateway and, if configured, outbound internet access. OPT1 is legacy and is planned for removal.
- Client 01 remains powered off unless a scenario requires a second Windows endpoint.
- VMnet8 is used as the pfSense WAN uplink; management interfaces should not be exposed to the public internet.

The intended test path is Kali → pfSense → Windows Client 02. This path must be verified with explicit allow/deny tests and firewall logs before running scans or exploitation simulations. Wazuh endpoint telemetry and pfSense network/firewall logs are separate data sources; verify each independently.

## 8. Operational checklist

- [x] Create VMnet2 as Host-only, 192.168.206.0/24, VMware DHCP disabled.
- [ ] Remove legacy VMnet3 after disconnecting all VMs from it.
- [ ] Remove the unused pfSense OPT1 / third network adapter after confirming no VM depends on VMnet3.
- [x] Install pfSense and assign WAN (em0), LAN (em1), and OPT1 (em2).
- [x] Configure pfSense WAN using DHCP.
- [x] Configure pfSense LAN as 192.168.206.1/24.
- [ ] Retire pfSense OPT1 configuration after the VMnet3 transition is complete.
- [x] Assign Ubuntu Wazuh and Windows endpoints to VMnet2.
- [ ] Move Kali Linux from VMnet3 to VMnet2 and verify its new IP/gateway.
- [ ] Verify DHCP scopes or configure and document static IPs for each VM.
- [ ] Verify current IP addresses and default gateways on Kali, Windows Client 02, and Wazuh.
- [ ] Verify pfSense LAN interface, DHCP scope (if used), LAN firewall policy, and outbound NAT.
- [ ] Verify Kali can reach the intended target services on Windows Client 02 from the shared VMnet2 segment.
- [ ] Verify blocked traffic is denied and logged by pfSense.
- [ ] Verify Windows Client 02 can communicate with Wazuh Manager and events arrive.
- [ ] Verify Wazuh dashboard access from the intended management workstation/network.
- [ ] Start Client 01 only when a scenario requires two Windows endpoints and validate its agent connectivity.
- [ ] Begin controlled attack simulations after network validation.

## 9. Resource and safety notes

The host has 16 GB RAM. Run only the VMs needed for the current phase and power off unused VMs. The current staged workflow may run Kali, Windows Client 02, and pfSense during attack execution, then power off Kali and start Ubuntu Wazuh for analysis. If Wazuh is offline during an attack, do not assume all endpoint events will be replayed later; test and verify actual agent behavior.

All activity must remain inside this owner-managed lab. Use Host-only networks for the attacker and victim segments, do not bridge attack VMs to the physical home network, and do not configure public port forwarding for Wazuh, SSH, or pfSense management.
