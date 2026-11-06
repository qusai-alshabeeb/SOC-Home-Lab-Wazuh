# SOC Attack & Defense Lab — Network Architecture Plan

## 1. Purpose

This document records the planned network design for extending the existing Wazuh SOC Home Lab into a routed attack-and-defense environment.

**Status: Planned / Under Construction.** The topology and IP addresses below are design targets. They must not be treated as deployed or tested until implementation and connectivity checks are completed.

## 2. Existing Environment

The current lab includes:

- VMware Workstation 17.6.0 on a Windows 11 host.
- An Ubuntu Server 24.04.5 LTS VM running Wazuh 4.14.
- An existing Wazuh server connection through VMware NAT (VMnet8).
- Windows 10 endpoint monitoring through a Wazuh agent.
- A Kali Linux VM for authorized security testing.

The existing Wazuh server currently uses `192.168.73.132` on its existing NAT network, and Windows Client 02 has been observed at `192.168.73.134`. These settings are to be preserved until the new network is configured and verified.

## 3. VMware Virtual Networks

The following virtual networks have been created in VMware Virtual Network Editor:

| VMware Network | Type | Subnet | DHCP |
|---|---|---|---|
| VMnet8 | NAT | 192.168.73.0/24 | Enabled |
| VMnet2 | Host-only | 192.168.206.0/24 | Disabled |
| VMnet3 | Host-only | 192.168.232.0/24 | Disabled |

VMnet2 and VMnet3 are intended as isolated internal networks. VMware DHCP is disabled on both so pfSense can provide DHCP services after it is configured.

## 4. Planned Topology

The planned pfSense virtual firewall will have three network adapters:

| pfSense Interface | VMware Network | Role |
|---|---|---|
| WAN | VMnet8 | NAT uplink |
| LAN / Attacker | VMnet2 | Attacker network |
| OPT / Victim-SOC | VMnet3 | Victim and monitoring network |

### Planned IP Addressing

| Device / Interface | Network | Planned IP |
|---|---|---|
| pfSense WAN | VMnet8 | DHCP |
| pfSense Attacker LAN | VMnet2 | 192.168.206.1/24 |
| Kali Linux | VMnet2 | 192.168.206.10/24 |
| pfSense Victim/SOC interface | VMnet3 | 192.168.232.1/24 |
| Windows Client 02 | VMnet3 | 192.168.232.20/24 |
| Wazuh Server additional interface | VMnet3 | 192.168.232.30/24 |

These are proposed addresses only. They will be assigned after pfSense installation and interface verification. The Wazuh server's existing NAT adapter is intended to remain connected during migration.

## 5. Security Zones and Traffic Flow

- **Attacker zone:** Kali Linux on VMnet2.
- **Firewall zone:** pfSense routes and filters traffic between the internal networks.
- **Victim/SOC zone:** Windows endpoint and Wazuh server on VMnet3.
- **WAN/uplink:** VMware NAT through VMnet8.

The intended test path is Kali → pfSense → Windows endpoint. Firewall rules will be configured deliberately to permit only the traffic required for each authorized lab exercise. Logging will be enabled where appropriate.

The Wazuh agent will continue forwarding endpoint telemetry to the Wazuh Manager. Network traffic visibility at pfSense and endpoint event visibility in Wazuh are separate telemetry sources and will be evaluated independently.

## 6. Implementation Plan

1. Install pfSense CE in the prepared virtual machine.
2. Assign and verify WAN, Attacker LAN, and Victim/SOC interfaces.
3. Configure the two internal interface addresses and DHCP scopes.
4. Apply restrictive firewall rules between the zones.
5. Connect Kali to VMnet2.
6. Add a second network adapter to the Wazuh server on VMnet3 while preserving its existing NAT adapter.
7. Connect the Windows endpoint to VMnet3, preserving its agent configuration.
8. Verify addressing, routing, allowed traffic, blocked traffic, and Wazuh event collection.
9. Begin controlled attack simulations only after the network checks pass.

## 7. Resource Constraints

The host laptop has 16 GB RAM. The virtual machines will not all be run simultaneously at their maximum memory allocations. Testing will be staged, and unused VMs will be powered off to preserve host resources.

## 8. Safety and Scope

All attack simulations are intended for this isolated, owner-managed virtual lab. The internal networks must remain separate from the physical home LAN. No router port forwarding or direct public exposure of the Wazuh Dashboard, SSH, or pfSense management interface is part of this plan.

## 9. Current Status

- [x] VMnet2 created as Host-only with VMware DHCP disabled.
- [x] VMnet3 created as Host-only with VMware DHCP disabled.
- [x] pfSense VM hardware prepared with three adapters: VMnet8, VMnet2, and VMnet3.
- [ ] pfSense installation and interface assignment.
- [ ] Internal IP and DHCP configuration.
- [ ] Endpoint migration and connectivity validation.
- [ ] Firewall rule validation and attack simulations.
