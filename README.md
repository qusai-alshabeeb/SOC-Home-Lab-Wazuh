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

The lab runs in VMware Workstation and now includes three virtual networks: one NAT uplink and two isolated Host-only networks. pfSense connects the two isolated segments.

| Component | Current Configuration |
|---|---|
| SIEM Platform | Wazuh 4.14 |
| SIEM Server OS | Ubuntu Server 24.04.5 LTS |
| Endpoint OS | Windows 10 |
| Attacker VM | Kali Linux |
| Firewall / Router | pfSense CE |
| Virtualization | VMware Workstation 17.6.0 |
| WAN Network | VMnet8 (NAT) |
| Victim / SOC Network | VMnet2 (Host-only), 192.168.206.0/24 |
| Attacker Network | VMnet3 (Host-only), 192.168.232.0/24 |
| pfSense WAN | VMnet8, DHCP address 192.168.73.135 (observed) |
| pfSense LAN | VMnet2, 192.168.206.1/24 |
| pfSense OPT1 | VMnet3, 192.168.232.1/24 |
| Active Windows Endpoint | WIN10-CLIENT-02 |
| Standby Windows Endpoint | WIN10-CLIENT-01 (powered off; start when a scenario needs two endpoints) |

### Current Environment Overview

```text
                         INTERNET / HOST NAT
                                |
                         VMware VMnet8 (NAT)
                                |
                         pfSense WAN (em0)
                         192.168.73.135
                                |
                    +-----------+-----------+
                    |       pfSense         |
                    |   Firewall / Router   |
                    +-----------+-----------+
                                |
             +------------------+------------------+
             |                                     |
       pfSense LAN (em1)                      pfSense OPT1 (em2)
       VMnet2 - Host-only                     VMnet3 - Host-only
       192.168.206.1/24                       192.168.232.1/24
       VICTIM / SOC                            ATTACKER
             |                                     |
       +-----+----------------+                    |
       |                      |                    |
  +----+---------+     +------+----------+    +----+---------+
  | Ubuntu       |     | Windows 10      |    | Kali Linux   |
  | Wazuh Server |     | Client 02       |    | Attacker VM  |
  +--------------+     +-----------------+    +--------------+
                             
                       +-----------------+
                       | Windows 10      |
                       | Client 01       |
                       | Standby / OFF   |
                       +-----------------+
                       (Connected to VMnet2 when needed)
```

**Network status:** VMnet2 and VMnet3 are configured as isolated Host-only networks with VMware DHCP disabled. pfSense has WAN, LAN, and OPT1 interfaces assigned and configured with the addresses shown above. Inter-network routing, firewall policy, endpoint addressing, and Wazuh telemetry across the segmented topology must still be validated before treating the attack path as fully operational.

## 🛠️ Technologies & Tools

- **Wazuh SIEM** — Security monitoring, alerting, and log analysis.
- **Ubuntu Server** — Centralized SIEM server.
- **Windows 10** — Endpoint monitoring and security event generation.
- **Kali Linux** — Authorized security testing.
- **pfSense** — Virtual firewall and router connecting the isolated attacker and victim/SOC networks.
- **VMware Workstation** — Virtual lab infrastructure.
- **PowerShell** — Windows administration and event investigation.
- **MITRE ATT&CK** — Adversary behavior classification.


## 🧪 Attack & Detection Roadmap

A categorized, difficulty-tagged checklist of authorized lab scenarios—from reconnaissance and vulnerability assessment through exploitation simulations, credential access, persistence, network activity, malware behavior simulation, and Blue Team detection engineering—is maintained in the [Attack & Detection Roadmap](Attack-Catalog/README.md). Completed failed-login and brute-force investigations are already marked there based on the existing incident reports.

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
- [x] Install pfSense and assign WAN, LAN, and OPT1 interfaces.
- [ ] Validate DHCP, inter-network routing, and restrictive firewall rules.
- [x] Assign Kali Linux to the isolated attacker network (VMnet3).
- [x] Assign Wazuh and Windows endpoints to the victim/SOC network (VMnet2).
- [ ] Validate network isolation, routing, and Wazuh event collection across the new topology.
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
- Virtual network segmentation and firewall concepts.

## ⚠️ Disclaimer

This project is developed strictly for educational purposes within an isolated and authorized virtual lab environment. Security simulations are performed only on personally managed virtual machines. The planned attacker and victim networks will remain isolated from the physical home network.

---

**Project Status:** In Progress 🚧

**Focus:** Blue Team | SOC Analyst | SIEM | Threat Detection | Incident Response
