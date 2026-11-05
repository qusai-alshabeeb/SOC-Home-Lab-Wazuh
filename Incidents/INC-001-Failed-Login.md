# Incident Report — INC-001: Failed Login

## Incident Overview

| Field | Details |
|---|---|
| SIEM | Wazuh 4.14 |
| Endpoint | WIN10-CLIENT-02 |
| Endpoint IP | 192.168.73.134 |
| Windows Event ID | 4625 |
| Wazuh Rule ID | 60122 |
| Rule Level | 5 |
| Target Account | FakeSOCUser |
| Status | Investigated |

## Description

A failed Windows authentication attempt was generated using a non-existent test account named `FakeSOCUser`.

Wazuh successfully collected and analyzed the Windows Security Event.

## Evidence Analysis

- Event ID: `4625`
- Logon Type: `2` (Interactive)
- Target Username: `FakeSOCUser`
- Source Address: `::1` (IPv6 Loopback)
- Status: `0xC000006D`
- SubStatus: `0xC0000064`
- Authentication Package: `Negotiate`

## Investigation Findings

The SubStatus `0xC0000064` indicates that the specified user account does not exist.

The source address `::1` indicates that the authentication attempt originated locally on the endpoint.

## MITRE ATT&CK

The Wazuh alert displayed technique `T1531`. However, this mapping does not appear to match the observed failed authentication event and requires validation.

## Analyst Conclusion

The event was generated intentionally as part of an authorized SOC Home Lab exercise.

The activity was classified as expected lab activity. No evidence of compromise was established.

## Outcome

Successfully demonstrated Windows Event ID 4625 collection, Wazuh alert generation, and authentication failure investigation.
