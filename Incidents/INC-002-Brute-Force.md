# Incident Report — INC-002: Brute Force Detection

## 1. Incident Overview

| Field | Details |
|---|---|
| Incident ID | INC-002 |
| Incident Type | Multiple Failed Authentication Attempts |
| SIEM Platform | Wazuh 4.14 |
| Endpoint | WIN10-CLIENT-02 |
| Endpoint IP | 192.168.73.134 |
| Target Account | FakeSOCUser |
| Windows Event ID | 4625 |
| Wazuh Rule ID | 60204 |
| Wazuh Rule Level | 10 |
| Detection Frequency | 8 events |
| MITRE ATT&CK | T1110 — Brute Force |
| Status | Detected and Investigated |

## 2. Incident Description

A controlled brute-force simulation was performed within the SOC Home Lab by generating repeated failed authentication attempts against a non-existent Windows account named `FakeSOCUser`.

Wazuh successfully detected the repeated authentication failures and generated a Level 10 security alert.

The detection was triggered by Wazuh Rule ID `60204`, titled:

`Multiple Windows Logon Failures`

## 3. Evidence Collection

The following information was extracted from the Wazuh alert:

| Field | Observed Value |
|---|---|
| Target Username | FakeSOCUser |
| Target Domain | DESKTOP-FD34VA8 |
| Source IP | fe80::432b:d508:add5:bd5c |
| Source Port | 50371 |
| Logon Type | 3 — Network |
| Authentication Package | NTLM |
| Logon Process | NtLmSsp |
| Status | 0xC000006D |
| SubStatus | 0xC0000064 |
| Event Channel | Security |
| Event Severity | AUDIT_FAILURE |

## 4. Detection Analysis

Wazuh identified repeated Windows authentication failures and triggered the following detection rule:

- **Rule ID:** 60204
- **Rule Description:** Multiple Windows Logon Failures
- **Rule Level:** 10
- **Frequency:** 8
- **MITRE Technique:** T1110 — Brute Force
- **MITRE Tactic:** Credential Access

The Windows Event ID `4625` indicates a failed logon attempt.

The SubStatus value `0xC0000064` indicates that the specified username does not exist.

The Logon Type `3` represents a network logon attempt, and NTLM was used as the authentication package.

## 5. MITRE ATT&CK Mapping

| Attribute | Details |
|---|---|
| Tactic | Credential Access |
| Technique | T1110 — Brute Force |
| Description | Repeated authentication attempts to obtain valid credentials |

Reference: https://attack.mitre.org/techniques/T1110/

## 6. Investigation Findings

- Multiple failed authentication events were generated on the Windows endpoint.
- The attempts targeted the non-existent account `FakeSOCUser`.
- Wazuh successfully correlated repeated failures and generated a Level 10 alert.
- The observed source address was an IPv6 link-local address.
- No successful authentication or account compromise was established from the collected alert.

## 7. Analyst Assessment

**Classification:** Authorized Lab Simulation

**Severity:** Level 10 — Wazuh Rule Level

The activity was intentionally generated as part of a controlled SOC Home Lab exercise.

Wazuh successfully identified the repeated authentication failure pattern and generated the expected alert.

Although the detection matched a brute-force pattern, the collected evidence does not establish an actual compromise.

## 8. Recommended Security Actions

- Monitor repeated failed authentication attempts.
- Investigate unusual source addresses and targeted accounts.
- Correlate Event ID `4625` with successful logon Event ID `4624`.
- Review Windows account lockout policies.
- Monitor authentication activity across endpoints.
- Investigate suspicious authentication patterns in a wider time window.

## 9. Lab Outcome

Successfully demonstrated:

- Windows Security Event Collection.
- Failed Authentication Monitoring.
- Wazuh Brute Force Detection.
- Alert Severity Analysis.
- MITRE ATT&CK Mapping.
- Basic SOC Incident Investigation.

---

**Incident Status:** Closed — Expected Lab Activity

**Project:** SOC Home Lab — Wazuh SIEM

**Environment:** VMware Workstation / Ubuntu Server / Windows 10
