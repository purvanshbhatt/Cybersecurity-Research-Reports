# Forensic Report: Cloud-Native SOC Deployment & Threat Detection
**Date:** April 17, 2026  
**Author:** Purvansh Bhatt  
**Project Link:** [Portfolio](https://purvanshbhatt.github.io) | [GitHub Repository](https://github.com/purvanshbhatt/Cybersecurity-Research-Reports)

---

## 1. Executive Summary
This report documents the architecture and validation of a Cloud-Native Security Operations Center (SOC) hosted on **Google Cloud Platform (GCP)**. The objective was to evaluate detection efficacy against high-volume authentication attacks and "Living off the Land" (LotL) techniques. By ingesting over **64,000 security events**, the lab identified a critical GRC vulnerability: a lack of account lockout controls. A NIST-compliant remediation was implemented and verified, successfully reducing the risk of unauthorized access.

## 2. Environment Architecture
- **SIEM/XDR:** Wazuh Manager (Ubuntu 24.04 LTS)
- **Endpoint:** Windows Server 2025 + Sysmon (High-Fidelity Telemetry)
- **Attacker:** Kali Linux (Cloud-Native)
- **Network:** Isolated VPC with custom Ingress/Egress firewall rules.

## 3. MITRE ATT&CK Mapping & Validation

| Technique ID | Technique Name | Tooling | Detection Method |
| :--- | :--- | :--- | :--- |
| **T1595** | Active Scanning | Nmap | Wazuh Rule ID 60100 (Level 10) |
| **T1110** | Brute Force | Hydra | Wazuh Rule ID 60122 / 60204 (Level 10) |
| **T1003** | OS Credential Dumping | Mimikatz | Sysmon Event ID 10 (LSASS Access) |
| **T1059.001** | PowerShell Obfuscation | CLI | Sysmon Event ID 1 (Encoded Commands) |
| **T1565** | Data Manipulation | Manual | File Integrity Monitoring (FIM) |

---

## 4. Detection Analysis

### 4.1 Brute Force (T1110)
During simulation, **64,167 authentication failures** were generated via RDP using Hydra from the Kali Linux attacker node.

- **Forensic Finding:** Individual failures (Rule 60122) were successfully correlated into a composite high-severity alert (Rule 60204 — *Multiple Windows Logon Failures*), demonstrating real-time big-data log processing.
- **Evidence:** High-severity spikes visible in the Wazuh Events dashboard confirmed continuous ingestion and correlation across the full attack window.

![Wazuh Dashboard — 64,167 Authentication Events](https://github.com/user-attachments/assets/34ab2e80-442e-4309-907a-e33647bbd37c)

### 4.2 Obfuscated Execution (T1059.001)
A Base64-encoded PowerShell payload was executed to simulate an adversary bypassing simple string-based detection.

- **Forensic Finding:** **Sysmon Event ID 1** (Process Creation) captured the fully de-obfuscated command line, exposing the script's intent before execution completed.
- **Key Insight:** Signature-based AV was evaded by the encoded payload; Sysmon's process telemetry provided the ground truth required for detection.

### 4.3 OS Credential Dumping (T1003)
Mimikatz was executed against LSASS to simulate credential harvesting post-exploitation.

- **Forensic Finding:** **Sysmon Event ID 10** (Process Access) flagged the direct handle open to `lsass.exe`, triggering a Level 12 Wazuh alert — the highest severity generated during testing.

### 4.4 File Integrity Monitoring (T1565)
A sensitive file (`c:\confidential\payroll.txt`) was modified to validate the FIM pipeline.

- **Forensic Finding:** Wazuh **Rule 550** (*Integrity checksum changed*) fired within seconds of the modification, capturing the SHA1, SHA256, MD5, file size, and the responsible user account (`purvansh95b`) in the alert metadata.

![Wazuh FIM — payroll.txt Integrity Alert](https://github.com/user-attachments/assets/765f86ba-d78a-46b2-88e2-bc2cf6a5b768)

---

## 5. GRC Audit & Remediation

**Control Failure Identified:** The default Windows Server 2025 configuration permitted unlimited failed login attempts, enabling persistent brute-force campaigns with no automated lockout response.

| Control | Baseline State | Risk |
| :--- | :--- | :--- |
| Account Lockout Threshold | Not configured (infinite attempts) | Critical |
| RDP Access Restriction | Open to all source IPs | High |
| Process Creation Auditing | Disabled by default | High |

**Remediation Actions Implemented:**

- **Policy:** Configured *Local Security Policy → Account Lockout Policy* in alignment with NIST SP 800-53 AC-7.
- **Threshold:** 5 invalid logon attempts triggers a 30-minute lockout.
- **Verification:** Post-remediation, the Hydra attack failed after 5 attempts. Wazuh Rule **60109** (*Account Locked Out*) fired as expected, confirming the control was operational.
- **Network Hardening:** Restricted RDP ingress to authorized administrative IP ranges via GCP VPC firewall rules.
- **Advanced Auditing:** Enabled Process Creation and LSASS access auditing via the deployed Sysmon configuration (SwiftOnSecurity baseline).

---

## 6. Conclusion
This lab demonstrates that SIEM visibility is only as effective as the underlying telemetry pipeline. By integrating **Sysmon** with tuned **Wazuh** correlation rules, raw log data was transformed into actionable, prioritized security intelligence. The discovery and mitigation of the account lockout control gap confirm that continuous control validation — not one-time configuration — is essential for maintaining a defensible GRC posture.

**Key Forensic Takeaways:**
- Default cloud baselines are insufficient; explicit hardening is required from day one.
- Endpoint telemetry (Sysmon) is the critical layer that surfaces LotL and obfuscation techniques invisible to network-layer controls.
- FIM provides a forensic audit trail that satisfies both operational detection and compliance evidence requirements (NIST, PCI-DSS).
- SIEM correlation rules bridge the gap between noise (individual Event ID 4625s) and signal (composite brute-force campaign alert).

---

**Contact:** [purvansh95b@gmail.com](mailto:purvansh95b@gmail.com) | [LinkedIn](https://www.linkedin.com/in/purvanshbhatt) | [GitHub](https://github.com/purvanshbhatt)
