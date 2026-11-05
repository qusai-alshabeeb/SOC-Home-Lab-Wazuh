# SOC Home Lab — Architecture Documentation

## 1. Project Overview

This document describes the architecture and infrastructure of the SOC Home Lab built using VMware Workstation and Wazuh SIEM.

The environment is designed to monitor Windows endpoints, collect security events, detect suspicious authentication activities, and support incident investigation.

## 2. Infrastructure

| Component | Configuration |
|---|---|
| Virtualization | VMware Workstation 17.6.0 |
| SIEM Server | Ubuntu Server 24.04.5 LTS |
| SIEM Platform | Wazuh 4.14 |
| Endpoint Operating System | Windows 10 |
| Network Mode | NAT |
| Server IP | 192.168.73.132 |
| Active Endpoint | WIN10-CLIENT-02 |
| Endpoint IP | 192.168.73.134 |

## 3. Wazuh Components

- **Wazuh Manager:** Processes security events and applies detection rules.
- **Wazuh Indexer:** Stores and indexes security data.
- **Wazuh Dashboard:** Provides visualization, monitoring, and investigation capabilities.
- **Wazuh Agent:** Collects endpoint security events and forwards them to the server.

## 4. Network Architecture

The virtual machines communicate through VMware NAT networking.

The Wazuh Server acts as the centralized monitoring platform, while Windows endpoints generate security events that are collected and analyzed by Wazuh.

## 5. Monitoring Scope

The current monitoring scope includes:

- Windows Security Events.
- Failed authentication attempts.
- Repeated logon failures.
- Windows Application Events.

## 6. Project Status

The initial SIEM deployment and Windows endpoint integration have been completed successfully.

Additional monitoring capabilities and custom detection scenarios are planned for future development.

