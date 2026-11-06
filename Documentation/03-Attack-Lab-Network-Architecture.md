# SOC Attack & Defense Lab — Network Architecture

## 1. Purpose

This document describes the planned routed network topology for expanding the existing Wazuh SOC Home Lab into an isolated attack-and-defense environment.

The design separates the attacker network from the victim and monitoring network. A pfSense virtual firewall will route and filter traffic between the internal networks.

**Implementation status:** Planned / In Progress. The network design is documented before deployment. The IP assignments and firewall behavior below are proposed configuration values, not proof that the network is already operational.

## 2. Existing Environment

The current lab includes:

| Component | Current configuration |
|---|---|
| Hypervisor | VMware Workstation 17.6.0 |
| Wazuh Server | Ubuntu Server 24.04.5 LTS |
| SIEM | Wazuh 4.14 |
| Existing Wazuh NAT address | 192.168.73.132 |
| Windows endpoint | WIN10-CLIENT-02 |
| Existing Windows endpoint address | 192.168.73.134 |
| Existing VMware network | VMnet8 (NAT), 192.168.73.0/24 |

The existing Wazuh deployment and NAT connectivity must be preserved while the new topology is introduced.

## 3. Planned Network Topology

The planned design uses three VMware networks:

- **VMnet8 (WAN / existing NAT):** Provides the pfSense WAN connection and preserves the existing Wazuh management path.
- **VMnet2 (Attacker LAN):** Isolated network for Kali Linux.
- **VMnet3 (Victim / SOC LAN):** Isolated network for the Windows endpoint and an additional Wazuh network interface.

### Logical Diagram

```text
                         Internet
                            |
                    VMware VMnet8 (NAT)
                     192.168.73.0/24
                            |
                      pfSense WAN
                            |
                  +---------+---------+
                  |    pfSense VM     |
                  | Firewall / Router|
                  +---------+---------+
                            |
             +--------------+--------------+
             |                             |
       VMnet2 — Attacker LAN         VMnet3 — Victim / SOC LAN
       192.168.206.0/24              192.168.232.0/24
             |                             |
         Kali Linux                 +------+------+
         192.168.206.10             |             |
         Attacker                Windows 10   Wazuh Server
                                  .232.20      .232.30 (planned
                                               second NIC)
```

## 4. IP Addressing Plan

The following addresses are planned for the internal networks:

| Device / Interface | VMware Network | Planned IP Address |
|---|---|---|
| pfSense WAN | VMnet8 | DHCP |
| pfSense Attacker LAN | VMnet2 | 192.168.206.1/24 |
| Kali Linux | VMnet2 | 192.168.206.10/24 |
| pfSense Victim / SOC LAN | VMnet3 | 192.168.232.1/24 |
| Windows 10 endpoint | VMnet3 | 192.168.232.20/24 |
| Wazuh Server additional interface | VMnet3 | 192.168.232.30/24 |

The Wazuh server's existing VMnet8 address, 192.168.73.132, is intended to remain available during migration. The additional VMnet3 interface is planned for endpoint communication and monitoring traffic.

VMnet2 and VMnet3 are configured as Host-only custom VMware networks with VMware DHCP disabled. DHCP, if enabled for the lab endpoints, will be provided by pfSense after its installation and configuration.

## 5. Firewall and Traffic Policy

The intended security policy is deny-by-default between internal zones, with only explicitly required traffic allowed.

Planned policy examples:

- Allow Kali to reach approved Windows lab services during authorized exercises.
- Allow the Windows endpoint to communicate with the Wazuh Manager on the required agent communication ports.
- Restrict access to pfSense administration to a trusted management path.
- Log relevant allowed and blocked traffic for investigation.
- Do not expose the lab's management interfaces directly to the public Internet.

Exact rules and required ports will be documented after interface configuration and connectivity testing.

## 6. Attack and Monitoring Workflow

1. Kali Linux generates controlled test traffic from the attacker network.
2. pfSense routes and filters traffic between the internal networks.
3. The Windows endpoint receives only traffic permitted by the firewall policy.
4. Windows generates endpoint security events where applicable.
5. The Wazuh Agent forwards collected events to the Wazuh Manager.
6. The analyst reviews Wazuh alerts and, where available, pfSense firewall logs.

A network scan or blocked connection does not automatically guarantee a Wazuh alert. Detection depends on the telemetry collected and the detection rules configured.

## 7. Deployment Phases

- [x] Create VMware VMnet2 as a Host-only network.
- [x] Create VMware VMnet3 as a Host-only network.
- [x] Disable VMware DHCP on VMnet2 and VMnet3.
- [x] Configure the pfSense VM with three virtual network adapters: VMnet8, VMnet2, and VMnet3.
- [ ] Install pfSense.
- [ ] Assign and verify WAN, Attacker LAN, and Victim / SOC LAN interfaces.
- [ ] Configure pfSense LAN addressing and DHCP scopes.
- [ ] Connect Kali Linux to VMnet2.
- [ ] Connect Windows endpoint to VMnet3.
- [ ] Add a second network adapter to the Wazuh Server while preserving its existing NAT connection.
- [ ] Configure and test firewall rules.
- [ ] Verify Wazuh agent connectivity and event collection across the new topology.

## 8. Safety and Scope

All attack simulations are restricted to virtual machines owned or administered by the project owner.

The internal attacker and victim networks are intended to remain isolated from the physical home LAN. No router port forwarding or direct public exposure of lab services is planned.

## 9. Current Status

The VMware internal networks have been created and verified. The pfSense virtual machine has been created with the planned three network adapters:

- Network Adapter 1: VMnet8 (NAT / WAN)
- Network Adapter 2: VMnet2 (Attacker LAN)
- Network Adapter 3: VMnet3 (Victim / SOC LAN)

pfSense installation and network configuration have not yet been completed.
