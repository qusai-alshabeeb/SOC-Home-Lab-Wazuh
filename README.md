# 🛡️ SOC Home Lab — Wazuh SIEM

A hands-on Security Operations Center (SOC) home lab built to simulate real-world security monitoring, threat detection, log analysis, and incident investigation using Wazuh SIEM and Windows endpoints.

The goal of this project is to develop practical Blue Team and SOC Analyst skills through controlled security simulations, endpoint monitoring, and incident response exercises.

---

## 🎯 Project Objectives

- Build and configure a functional SOC monitoring environment.
- Centralize and analyze Windows security events using Wazuh.
- Detect suspicious authentication activities.
- Investigate security alerts and identify potential threats.
- Understand and apply the MITRE ATT&CK framework.
- Practice incident documentation and SOC investigation workflows.

## 🏗️ Current Lab Architecture

The currently operational environment runs in VMware Workstation.

| Component | Current Configuration |
|---|---|
| SIEM Platform | Wazuh 4.14 |
| Server OS | Ubuntu Server 24.04.5 LTS |
| Endpoint OS | Windows 10 |
| Virtualization | VMware Workstation 17.6.0 |
| Network | VMware NAT (VMnet8) |
| Wazuh Server IP | 192.168.73.132 |
| Active Endpoint | WIN10-CLIENT-02 |
| Endpoint IP | 192.168.73.134 |

### Current Environment Overview

```text
              SOC HOME LAB
                   |
          +-------------------+
          |   Wazuh Server    |
          |   Ubuntu 24.04    |
          |                   |
          |  Wazuh Manager    |
          |  Wazuh Indexer    |
          |  Wazuh Dashboard  |
          +---------+---------+
                    |
              VMware NAT
                    |
             +------+------+
             |             |
      +------+-----+ +-----+------+
      | Windows 10 | | Windows 10 |
      |  Client 01 | |  Client 02 |
      |  Endpoint  | |  Endpoint  |
      +------------+ +------------+
```

## 🧭 Planned Attack & Defense Network

The next development phase is to introduce a pfSense virtual firewall and separate the attacker and victim/SOC systems into dedicated virtual networks.

**Status: Planned / Under Construction.** The IP addresses and topology below are design targets, not yet deployed or validated.

| Zone | VMware Network | Planned Subnet | Purpose |
|---|---|---|---|
| WAN | VMnet8 (NAT) | 192.168.73.0/24 | Uplink for pfSense |
| Attacker LAN | VMnet2 (Host-only) | 192.168.206.0/24 | Kali Linux |
| Victim / SOC LAN | VMnet3 (Host-only) | 192.168.232.0/24 | Windows endpoint and Wazuh |

Planned pfSense interfaces:
- WAN: VMnet8
- Attacker LAN: VMnet2 — 192.168.206.1/24
- Victim / SOC LAN: VMnet3 — 192.168.232.1/24

The existing Wazuh NAT connection will be preserved during the migration. The detailed design and implementation checklist are documented in [Network Architecture Plan](Documentation/03-Network-Architecture-Plan.md).

## 🛠️ Technologies & Tools

- **Wazuh SIEM** — Security monitoring, alerting, and log analysis.
- **Ubuntu Server** — Centralized SIEM server.
- **Windows 10** — Endpoint monitoring and security event generation.
- **Kali Linux** — Authorized security testing.
- **pfSense** — Planned virtual firewall and router.
- **VMware Workstation** — Virtual lab infrastructure.
- **PowerShell** — Windows administration and event investigation.
- **MITRE ATT&CK** — Adversary behavior classification.

## 🔍 Detection & Investigation Scenarios

### 1. Failed Login Detection

- Generated controlled failed authentication attempts.
- Monitored Windows Security Event ID `4625`.
- Investigated authentication failure details.
- Identified the target username and authentication status.

### 2. Brute Force Detection

- Simulated repeated failed authentication attempts.
- Detected multiple Windows logon failures using Wazuh.
- Identified Wazuh Rule ID `60204`.
- Observed a Level 10 alert.
- Reviewed MITRE ATT&CK technique `T1110` — Brute Force.

### 3. Windows Application Event Monitoring

- Monitored Windows Application logs.
- Investigated Software Protection Platform events.
- Distinguished informational system events from security-related alerts.

## 📋 Incident Reports

Detailed investigation reports are maintained in the `Incidents/` directory.

| Incident | Description | Status |
|---|---|---|
| INC-001 | Failed Login Investigation | Completed |
| INC-002 | Multiple Logon Failures / Brute Force Detection | Completed |

## 📸 Screenshots & Evidence

Screenshots of the Wazuh dashboard, detected alerts, and investigation results will be added to the `Screenshots/` directory.

## 🚀 Project Roadmap

- [x] Deploy Wazuh SIEM server.
- [x] Configure Ubuntu Server.
- [x] Connect Windows endpoint to Wazuh.
- [x] Generate and detect failed login events.
- [x] Detect repeated authentication failures.
- [x] Investigate Windows security events.
- [x] Create isolated VMware Host-only networks for the planned attack lab.
- [x] Prepare pfSense VM hardware with three network adapters.
- [ ] Install and configure pfSense.
- [ ] Configure routing, DHCP, and firewall rules.
- [ ] Connect Kali Linux to the attacker network.
- [ ] Connect Windows and Wazuh to the victim/SOC network.
- [ ] Validate network isolation, routing, and event collection.
- [ ] Configure File Integrity Monitoring (FIM).
- [ ] Perform controlled malware detection testing.
- [ ] Develop custom Wazuh detection rules.
- [ ] Expand MITRE ATT&CK detection coverage.
- [ ] Document additional incident response scenarios.

## 📚 Learning Outcomes

This project provides practical experience in:

- SIEM deployment and configuration.
- Windows endpoint security monitoring.
- Security event analysis.
- Authentication failure investigation.
- Brute force detection.
- Alert triage and incident reporting.
- MITRE ATT&CK framework mapping.
- Blue Team operations and SOC workflows.
- Virtual network segmentation and firewall concepts (planned).

## ⚠️ Disclaimer

This project is developed strictly for educational purposes within an isolated and authorized virtual lab environment. Security simulations are performed only on personally managed virtual machines. The planned attacker and victim networks will remain isolated from the physical home network.

---

**Project Status:** In Progress 🚧

**Focus:** Blue Team | SOC Analyst | SIEM | Threat Detection | Incident Response
