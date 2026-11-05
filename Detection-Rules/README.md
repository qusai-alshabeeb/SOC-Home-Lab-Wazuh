# Detection Rules

## 1. Overview

This directory documents the detection rules used in the SOC Home Lab project.

The main objective is to understand how Wazuh detects suspicious activities, generates security alerts, and helps analysts investigate potential security incidents.

## 2. Built-in Wazuh Rules

This project currently uses built-in Wazuh detection rules to monitor Windows security events.

| Rule ID | Description | Severity Level | Event ID |
|---|---|---:|---:|
| 60122 | Logon Failure - Unknown user or bad password | 5 | 4625 |
| 60204 | Multiple Windows Logon Failures | 10 | 4625 |

### Rule 60122 – Failed Login Detection

- **Purpose:** Detects failed Windows authentication attempts.
- **Windows Event ID:** 4625
- **Severity Level:** 5
- **Lab Scenario:** A failed login attempt using a non-existent account.
- **Related Incident:** [INC-001](../Built-in-Rules/60122-Failed-Login.md)

### Rule 60204 – Multiple Logon Failures

- **Purpose:** Detects multiple failed Windows logon attempts.
- **Windows Event ID:** 4625
- **Severity Level:** 10
- **Lab Scenario:** Simulated repeated authentication failures using PowerShell.
- **MITRE ATT&CK:** T1110 – Brute Force
- **Related Incident:** [INC-002](../Built-in-Rules/60204-Multiple-Logon-Failures.md)

## 3. Custom Detection Rules

Custom detection rules have not yet been implemented.

This section will be updated when custom rules are created, tested, and validated in the lab.

## 4. Future Improvements

- Develop custom Wazuh detection rules.
- Improve detection accuracy and reduce false positives.
- Add additional Windows security event monitoring.
- Expand MITRE ATT&CK technique coverage.
- Test and document new detection scenarios.

## 5. Important Notes

The rules documented above are built-in Wazuh rules. They are not custom rules created for this project.

All detection scenarios are performed in an isolated and authorized home lab environment.
