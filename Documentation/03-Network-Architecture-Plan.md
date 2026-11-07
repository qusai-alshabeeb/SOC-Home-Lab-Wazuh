# SOC Attack & Defense Lab — Network Architecture

## 1. Purpose and current status

This document describes the current VMware network layout for the Wazuh SOC Home Lab. The lab has two isolated Host-only segments connected through a pfSense virtual firewall, plus a NAT uplink for WAN access.

**Current configuration:** VMnet2 is the Victim/SOC network and VMnet3 is the Attacker network. This is the actual assignment currently used in the VMs, and differs from the earlier draft topology.

**Validation status:** The VMware networks and pfSense interfaces have been configured. End-to-end routing, firewall policy, endpoint IP assignments, and Wazuh event delivery across the new topology still require explicit testing. Do not treat those paths as verified yet.

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
| VMnet2 | Host-only | 192.168.206.0/24 | Disabled | Victim / SOC segment |
| VMnet3 | Host-only | 192.168.232.0/24 | Disabled | Attacker segment |

VMnet2 and VMnet3 are isolated Host-only networks. VMware DHCP is disabled on both internal networks so address assignment can be managed through pfSense or static configuration.

## 4. Current VM-to-network assignments

| VM / Component | VMware Network | Role |
|---|---|---|
| pfSense Adapter 1 (WAN / em0) | VMnet8 | NAT uplink |
| pfSense Adapter 2 (LAN / em1) | VMnet2 | Victim / SOC gateway |
| pfSense Adapter 3 (OPT1 / em2) | VMnet3 | Attacker gateway |
| Ubuntu Wazuh Server | VMnet2 | SIEM / monitoring server |
| Windows Client 02 | VMnet2 | Active monitored endpoint |
| Windows Client 01 | VMnet2 | Standby endpoint; powered off until a two-endpoint scenario is needed |
| Kali Linux | VMnet3 | Attacker workstation |

## 5. Current pfSense interface addressing

| pfSense Interface | VMware Network | Current Address / Mode |
|---|---|---|
| WAN (em0) | VMnet8 | DHCP; observed address 192.168.73.135 |
| LAN (em1) | VMnet2 | 192.168.206.1/24 |
| OPT1 (em2) | VMnet3 | 192.168.232.1/24 |

The pfSense dashboard previously showed these WAN, LAN, and OPT1 addresses. Confirm the current values in the console or WebGUI before relying on them during a test. The IP addresses of Ubuntu Wazuh, Kali, and the Windows clients should be recorded from their current network configuration rather than inferred from the earlier NAT setup.

## 6. Network diagram

```text
                         VMware VMnet8 (NAT)
                                |
                         pfSense WAN / em0
                         192.168.73.135
                                |
                         +------+------+
                         |   pfSense   |
                         | Firewall /  |
                         |   Router    |
                         +------+------+
                                |
             +------------------+------------------+
             |                                     |
      LAN / em1                              OPT1 / em2
      VMnet2                                  VMnet3
      192.168.206.1/24                        192.168.232.1/24
      VICTIM / SOC                             ATTACKER
             |                                     |
       +-----+----------------+                    |
       |                      |                    |
  +----+---------+     +------+----------+    +----+---------+
  | Ubuntu       |     | Windows 10      |    | Kali Linux   |
  | Wazuh Server |     | Client 02       |    | Attacker VM  |
  +--------------+     +-----------------+    +--------------+

                       Windows Client 01
                       VMnet2 - Standby / OFF
```

## 7. Intended traffic flow and security boundaries

- Kali Linux initiates authorized tests from VMnet3.
- pfSense OPT1 is the attacker-side gateway.
- Windows Client 02 is the primary target on VMnet2.
- Wazuh Server is on VMnet2 to receive endpoint telemetry from the monitored Windows endpoint.
- pfSense controls traffic between the attacker and victim/SOC segments.
- Client 01 remains powered off unless a scenario requires a second Windows endpoint.
- VMnet8 is used as the pfSense WAN uplink; management interfaces should not be exposed to the public internet.

The intended test path is Kali → pfSense → Windows Client 02. This path must be verified with explicit allow/deny tests and firewall logs before running scans or exploitation simulations. Wazuh endpoint telemetry and pfSense network/firewall logs are separate data sources; verify each independently.

## 8. Operational checklist

- [x] Create VMnet2 as Host-only, 192.168.206.0/24, VMware DHCP disabled.
- [x] Create VMnet3 as Host-only, 192.168.232.0/24, VMware DHCP disabled.
- [x] Prepare pfSense VM with three adapters: VMnet8, VMnet2, and VMnet3.
- [x] Install pfSense and assign WAN (em0), LAN (em1), and OPT1 (em2).
- [x] Configure pfSense WAN using DHCP.
- [x] Configure pfSense LAN as 192.168.206.1/24.
- [x] Configure pfSense OPT1 as 192.168.232.1/24.
- [x] Assign Ubuntu Wazuh and Windows endpoints to VMnet2.
- [x] Assign Kali Linux to VMnet3.
- [ ] Verify DHCP scopes or configure and document static IPs for each VM.
- [ ] Verify current IP addresses and default gateways on Kali, Windows Client 02, and Wazuh.
- [ ] Verify pfSense interface enablement and firewall rules for both internal networks.
- [ ] Verify Kali can reach only the intended target services on Windows Client 02.
- [ ] Verify blocked traffic is denied and logged by pfSense.
- [ ] Verify Windows Client 02 can communicate with Wazuh Manager and events arrive.
- [ ] Verify Wazuh dashboard access from the intended management workstation/network.
- [ ] Start Client 01 only when a scenario requires two Windows endpoints and validate its agent connectivity.
- [ ] Begin controlled attack simulations after network validation.

## 9. Resource and safety notes

The host has 16 GB RAM. Run only the VMs needed for the current phase and power off unused VMs. The current staged workflow may run Kali, Windows Client 02, and pfSense during attack execution, then power off Kali and start Ubuntu Wazuh for analysis. If Wazuh is offline during an attack, do not assume all endpoint events will be replayed later; test and verify actual agent behavior.

All activity must remain inside this owner-managed lab. Use Host-only networks for the attacker and victim segments, do not bridge attack VMs to the physical home network, and do not configure public port forwarding for Wazuh, SSH, or pfSense management.
