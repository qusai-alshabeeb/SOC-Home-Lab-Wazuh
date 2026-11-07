# Attack & Detection Roadmap

A structured, checkbox-based roadmap for authorized attack simulations and Blue Team investigations in the SOC Home Lab.

> **Scope:** Perform tests only against VMs and intentionally vulnerable applications owned or explicitly authorized by the lab operator. Keep the lab isolated. This catalog is a learning and detection plan, not a claim that every technique is currently supported by the lab.

## How to track progress

Each technique has independent execution and analysis checkboxes:

- [ ] **Executed** — the activity was safely simulated and evidence was collected.
- [ ] **Analyzed in Wazuh** — relevant telemetry was checked in Wazuh and the result was recorded (detected, partially detected, or not detected).
- [ ] **Documented** — steps, timestamps, evidence, findings, limitations, and lessons learned were added to a report.

Difficulty is a separate label:
- **Beginner** — basic commands and a single host or service.
- **Intermediate** — multiple tools, protocols, or correlated events.
- **Advanced** — multi-stage activity, complex prerequisites, or deeper detection engineering.

A test is not considered complete merely because a command ran. Record negative results too: **not detected** is a valid finding, not a success.

## Existing completed investigations

These were previously performed and investigated using Wazuh in this lab:

- [x] **Failed Windows logon** — Event ID 4625; Wazuh Rule 60122; unknown-user / bad-password investigation. **Beginner**
- [x] **Repeated Windows logon failures / brute-force pattern** — Event ID 4625; Wazuh Rule 60204, Level 10; MITRE ATT&CK T1110. **Beginner**
  
The checkmarks above represent the completed execution and Wazuh investigation already documented in the repository. Keep the incident reports as the evidence of record: `Incidents/INC-001-Failed-Login.md` and `Incidents/INC-002-Brute-Force.md`.

---

## 01. Reconnaissance & OSINT

Information gathering before interacting with a target. Use only lab-owned assets or public information that is safe and appropriate to collect.

- [ ] Passive OSINT and public-footprint review — **Beginner**
- [ ] DNS records and domain information review — **Beginner**
- [ ] Subdomain discovery against a lab-owned domain — **Intermediate**
- [ ] Website and technology fingerprinting — **Beginner**
- [ ] Metadata discovery in intentionally provided files — **Beginner**
- [ ] Network range and host discovery inside the lab — **Beginner**
- [ ] Active scanning and port discovery with Nmap — **Beginner**
- [ ] Service/version enumeration — **Beginner**
- [ ] Network topology and firewall-path mapping — **Intermediate**

## 02. Vulnerability Assessment & Exposure Discovery

Identify weaknesses before attempting any exploit. Prefer intentionally vulnerable targets such as OWASP Juice Shop, DVWA, or Metasploitable in an isolated segment.

- [ ] Vulnerability scanning of lab targets — **Beginner**
- [ ] Outdated service/version identification — **Beginner**
- [ ] Weak/default configuration review — **Beginner**
- [ ] SMB exposure and share-permission assessment — **Intermediate**
- [ ] TLS/HTTPS configuration review — **Intermediate**
- [ ] Web application security assessment — **Intermediate**
- [ ] Prioritize findings by severity and practical impact — **Intermediate**

## 03. Initial Access & Social Engineering Simulations

Practice how an attacker may obtain an initial foothold. Social engineering is tabletop or consent-based simulation only.

- [ ] Phishing-awareness simulation using a mock email — **Beginner**
- [ ] Malicious-link awareness exercise using a harmless local landing page — **Beginner**
- [ ] Attachment-handling simulation using a harmless test file — **Beginner**
- [ ] Password-spraying simulation against a dedicated test account with strict attempt limits — **Intermediate**
- [ ] Exploit a deliberately vulnerable service in a disposable VM — **Intermediate**
- [ ] Web application entry-point testing on an intentionally vulnerable app — **Intermediate**

**Safety boundary:** Do not send phishing to real people, collect real credentials, impersonate real organizations, or target accounts outside the lab.

## 04. Web Application Attacks

Use an intentionally vulnerable local application and test data only.

- [ ] SQL Injection (SQLi) — **Beginner / Intermediate**
- [ ] Cross-Site Scripting (XSS), reflected and stored — **Beginner / Intermediate**
- [ ] Broken authentication and session-management testing — **Intermediate**
- [ ] IDOR / broken access-control testing — **Intermediate**
- [ ] Path traversal / local file inclusion in a lab app — **Intermediate**
- [ ] File-upload validation weaknesses — **Intermediate**
- [ ] Server-Side Request Forgery (SSRF) in a controlled target — **Advanced**
- [ ] Command injection in a deliberately vulnerable app — **Advanced**
- [ ] Server-Side Template Injection (SSTI) in a lab app — **Advanced**
- [ ] API authorization and input-validation testing — **Intermediate**

## 05. Credential Access

Use synthetic accounts and passwords created for the lab. Never use real or reused credentials.

- [x] Failed-login event generation and investigation — **Beginner**
- [x] Repeated failed logons / brute-force detection — **Beginner**
- [ ] Controlled password spraying — **Intermediate**
- [ ] Offline password-hash auditing with synthetic hashes — **Intermediate**
- [ ] Windows credential-storage exposure review (non-destructive) — **Advanced**
- [ ] Kerberos authentication and Kerberoasting concepts in a dedicated AD lab — **Advanced**
- [ ] LLMNR/NBT-NS poisoning and relay concepts in a dedicated isolated lab — **Advanced**

## 06. Discovery & Host Enumeration

- [ ] Windows host, OS, and patch-level discovery — **Beginner**
- [ ] Local users and groups enumeration — **Beginner**
- [ ] Running processes and services enumeration — **Beginner**
- [ ] Network interfaces, routes, and connections discovery — **Beginner**
- [ ] Shared folders and permissions discovery — **Intermediate**
- [ ] Scheduled tasks and startup locations discovery — **Intermediate**
- [ ] Security products and logging configuration discovery — **Intermediate**
- [ ] Active Directory users, groups, computers, and trust discovery — **Advanced; requires a separate AD lab**

## 07. Exploitation & Privilege Escalation

Only exploit intentionally vulnerable lab systems. Take VM snapshots first and document the vulnerability, prerequisites, impact, and remediation.

- [ ] Exploit a known vulnerability in a deliberately vulnerable service — **Intermediate**
- [ ] Windows weak service-permission misconfiguration assessment — **Intermediate**
- [ ] Insecure file/folder permission assessment — **Intermediate**
- [ ] Unquoted service-path misconfiguration demonstration — **Advanced**
- [ ] UAC and privilege-boundary concepts in a disposable VM — **Advanced**
- [ ] Linux sudo / SUID misconfiguration assessment in a separate Linux target — **Advanced**
- [ ] Validate remediation and confirm the weakness is no longer exploitable — **Intermediate**

## 08. Execution & Scripting

Use benign commands and harmless payloads that create clear, reversible telemetry.

- [ ] Command-line execution telemetry — **Beginner**
- [ ] PowerShell operational and security logging review — **Beginner**
- [ ] Script execution and process-tree investigation — **Intermediate**
- [ ] Windows Management Instrumentation (WMI) activity simulation — **Intermediate**
- [ ] Living-off-the-land binary (LOLBin) behavior simulation using harmless actions — **Advanced**
- [ ] Suspicious parent-child process relationship investigation — **Intermediate**

## 09. Persistence

Create reversible test artifacts only; remove them after collecting evidence.

- [ ] Scheduled-task persistence simulation — **Intermediate**
- [ ] Windows service creation simulation — **Intermediate**
- [ ] Run-key / Startup-folder persistence simulation — **Intermediate**
- [ ] New local account and group-membership change monitoring — **Intermediate**
- [ ] Persistence artifact discovery and cleanup verification — **Intermediate**

## 10. Defense Evasion & Security-Control Tampering

Focus on detecting and recovering from attempted tampering. Do not disable protection on a host connected to real networks.

- [ ] Windows audit-policy change detection — **Intermediate**
- [ ] Security-log clearing detection using a controlled disposable VM — **Advanced**
- [ ] Defender configuration/tampering alert review without weakening the host — **Advanced**
- [ ] Obfuscated-but-benign script detection — **Advanced**
- [ ] File rename/location changes and masquerading indicators — **Intermediate**
- [ ] Wazuh agent stop/restart and loss-of-telemetry alerting — **Intermediate**

## 11. Lateral Movement

Requires two Windows endpoints or a dedicated multi-host lab. Client 01 can be enabled when a scenario needs a second endpoint.

- [ ] SMB connection and share-access monitoring — **Intermediate**
- [ ] RDP logon and remote-session monitoring — **Intermediate**
- [ ] WinRM remote-administration activity simulation — **Advanced**
- [ ] Remote service / scheduled-task activity simulation — **Advanced**
- [ ] Admin-share access monitoring — **Intermediate**
- [ ] Correlate source host, destination host, account, and logon type — **Advanced**

## 12. Network Attacks & Traffic Manipulation

Run only on the isolated Host-only networks. Some tests can interrupt connectivity; snapshot and plan rollback first.

- [ ] Nmap TCP/UDP scanning and firewall-log correlation — **Beginner**
- [ ] Packet capture and protocol analysis with Wireshark — **Beginner**
- [ ] ARP spoofing / MITM demonstration between disposable lab VMs — **Advanced**
- [ ] DNS spoofing demonstration against a lab-only domain — **Advanced**
- [ ] Rogue DHCP detection in a disposable segment — **Advanced**
- [ ] Suspicious DNS and outbound-connection analysis — **Intermediate**
- [ ] pfSense firewall deny/allow rule validation — **Intermediate**
- [ ] Port-scan and connection-rate alerting — **Intermediate**

## 13. Command & Control (C2) Simulation

Use benign, clearly identified traffic generators or an approved adversary-emulation framework in the isolated lab. Do not deploy an uncontrolled remote-access tool.

- [ ] Periodic HTTP request / beacon-like traffic simulation — **Intermediate**
- [ ] DNS query-pattern analysis — **Intermediate**
- [ ] Unusual destination and port analysis — **Intermediate**
- [ ] Long-lived connection and repeated callback analysis — **Advanced**
- [ ] Correlate endpoint process activity with network traffic — **Advanced**

## 14. Collection & Exfiltration

Use dummy documents created for the exercise. Do not collect personal or real confidential data.

- [ ] Discovery and staging of dummy files — **Beginner**
- [ ] Archive/compression activity monitoring — **Beginner**
- [ ] Copy dummy files to a lab network share — **Intermediate**
- [ ] Simulated exfiltration over an internal controlled channel — **Intermediate**
- [ ] Detect unusual outbound transfer volume or destination — **Advanced**
- [ ] Validate data-loss prevention and firewall controls — **Advanced**

## 15. Malware Analysis & Ransomware Behavior Simulation

Do not download or execute live malware on the daily-use host. Start with EICAR or purpose-built benign simulators, disposable snapshots, and an isolated analysis VM. Never connect a malware-analysis VM to a bridged network.

- [ ] EICAR antivirus test-file detection — **Beginner**
- [ ] Static analysis of a harmless test executable — **Beginner**
- [ ] File hashes, strings, metadata, and reputation workflow — **Beginner**
- [ ] Process, file, and registry behavior monitoring for a benign simulator — **Intermediate**
- [ ] Ransomware-like file activity simulation on disposable dummy files — **Intermediate**
- [ ] Detect mass file modifications and suspicious rename patterns — **Advanced**
- [ ] Malware sandbox workflow and report writing — **Advanced**
- [ ] Recovery validation from a clean VM snapshot — **Intermediate**

## 16. Impact & Availability

Use disposable targets and conservative limits. No tests against the physical host, home router, public services, or third-party systems.

- [ ] Harmless file-integrity impact simulation — **Beginner**
- [ ] Controlled service stop/start monitoring in a disposable VM — **Intermediate**
- [ ] Resource-exhaustion concepts using strict low limits — **Advanced**
- [ ] Backup and recovery validation — **Intermediate**
- [ ] Distinguish benign administrative activity from destructive behavior — **Advanced**

## 17. Blue Team Detection Engineering & Incident Response

These are defender exercises paired with the attack simulations.

- [ ] Map each scenario to relevant MITRE ATT&CK techniques
- [ ] Identify required Windows Event IDs and telemetry sources
- [ ] Verify Wazuh agent collection and event retention behavior
- [ ] Review built-in Wazuh rules and alert levels
- [ ] Create and test custom Wazuh rules
- [ ] Identify false positives and detection gaps
- [ ] Build alert correlation and investigation queries
- [ ] Write an incident report with timeline, evidence, impact, and remediation
- [ ] Re-test after remediation and record detection coverage

---

## Standard workflow for every scenario

1. Define the objective, target VM, scope, and expected telemetry.
2. Take a VM snapshot and confirm the lab is isolated.
3. Record start time, source IP, destination IP, and test account (if any).
4. Execute one controlled test.
5. Preserve endpoint, firewall, and packet-capture evidence as applicable.
6. Start or verify Wazuh and check whether telemetry arrived. If Wazuh was offline, do not assume every event was queued and replayed; verify actual results.
7. Record the Wazuh rule ID, alert level, event IDs, MITRE mapping, and detection gaps.
8. Clean up test artifacts, restore the snapshot if needed, and document remediation.

## Lab limitations and prerequisites

- The current lab is centered on Windows Client 02, Kali, pfSense, and Wazuh. Not every scenario is currently supported.
- Active Directory attacks require a separate domain-controller lab.
- Wireless attacks require dedicated wireless hardware and a separate isolated setup.
- Web attacks require a deliberately vulnerable web application VM/container.
- Advanced malware analysis requires a dedicated disposable analysis VM and strict network isolation.
- Client 01 remains a standby endpoint and should be started only when a scenario needs a second Windows host.

## References

- [MITRE ATT&CK Enterprise Matrix](https://attack.mitre.org/matrices/enterprise/)
- [MITRE ATT&CK Enterprise Tactics](https://attack.mitre.org/tactics/enterprise/)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [Atomic Red Team](https://github.com/redcanaryco/atomic-red-team) — controlled, ATT&CK-mapped test ideas; review prerequisites and execute only in the lab.
