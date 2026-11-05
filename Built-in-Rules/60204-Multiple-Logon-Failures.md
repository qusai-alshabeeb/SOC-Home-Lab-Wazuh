# Wazuh Rule 60204 – Multiple Windows Logon Failures

## 1. Overview

This document explains the investigation of Wazuh built-in rule `60204`, which detects multiple Windows logon failures.

The rule was triggered during a controlled brute-force simulation against a Windows endpoint in the SOC Home Lab.

## 2. Rule Information

| Field | Value |
|---|---|
| Rule ID | 60204 |
| Description | Multiple Windows Logon Failures |
| Severity Level | 10 |
| Windows Event ID | 4625 |
| Agent Name | WIN10-CLIENT-02 |
| Agent IP | 192.168.73.134 |
| Target Username | FakeSOCUser |
| Logon Type | 3 – Network |
| Authentication Package | NTLM |
| Frequency | 8 |
| MITRE ATT&CK | T1110 – Brute Force |

## 3. Lab Simulation

A controlled simulation was performed using PowerShell to generate repeated failed network authentication attempts against the Windows endpoint.

The attempts used a non-existent account named `FakeSOCUser` and an incorrect password.

The objective was to test whether Wazuh could identify a repeated authentication failure pattern.

## 4. Event Analysis

The collected Windows Security events contained the following details:

| Field | Value | Meaning |
|---|---|---|
| Event ID | 4625 | Failed logon |
| Target User | FakeSOCUser | Account used in the simulation |
| Logon Type | 3 | Network logon |
| Authentication Package | NTLM | Authentication protocol |
| Status | 0xC000006D | Logon failure |
| SubStatus | 0xC0000064 | User name does not exist |
| Source IP | fe80::432b:d508:add5:bd5c | IPv6 link-local address |
| Source Port | 50371 | Source port observed in one event |

### Wazuh Alert Details

- **Rule ID:** 60204
- **Severity Level:** 10
- **Frequency:** 8
- **Description:** Multiple Windows Logon Failures
- **MITRE Technique:** T1110 – Brute Force
- **Windows Computer:** DESKTOP-FD34VA8

The alert's frequency value represents the rule's configured correlation threshold. It should not automatically be interpreted as the total number of attempts performed.

## 5. Key Findings

- Multiple failed authentication events were generated.
- Windows recorded the failures using Event ID 4625.
- Wazuh correlated repeated failures and triggered rule 60204.
- The target account was not a valid account on the endpoint.
- The source address was an IPv6 link-local address, not evidence of an external attacker.
- The alert was associated with MITRE ATT&CK technique T1110 – Brute Force.

## 6. MITRE ATT&CK Mapping

**T1110 – Brute Force**

This technique describes attempts to gain access by repeatedly guessing or testing credentials.

The simulated repeated authentication failures match the general behavior described by this technique.

## 7. Analyst Assessment

**Classification:** Authorized Lab Simulation

The alert was generated intentionally to test Wazuh's ability to detect repeated authentication failures.

The collected alert confirms a repeated failed-login pattern. However, this alert alone does not establish that an attacker successfully authenticated or compromised the endpoint.

A further investigation should check for successful logon events (Event ID 4624) around the same time and correlate them with the failed attempts.

## 8. Conclusion

This experiment demonstrated how Wazuh can correlate multiple Windows authentication failures and generate a higher-severity alert.

It also demonstrated the importance of investigating the source, target account, logon type, and surrounding events before deciding whether an alert represents a real security incident.

## 9. Related Incident

[INC-002 – Brute Force](../Incidents/INC-002-Brute-Force.md)
