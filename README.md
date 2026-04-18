# Cybersecurity Research & Forensic Reports 🛡️
**Author: Purvansh Bhatt**

This repository serves as a technical annex to my [Professional Portfolio](http://localhost:5173/). It contains deep-dive forensic analysis, incident response playbooks, and GRC policy frameworks developed through active lab simulations.

## 🚀 Featured Project: Cloud-Native SOC (GCP)
*Architecture, Detection Engineering, and Control Validation.*

- **The Challenge:** Default cloud configurations often lack the telemetry needed to detect "Living off the Land" (LotL) attacks.
- **The Solution:** Built a 3-node SOC in GCP using Wazuh and Sysmon.
- **The Data:** Successfully ingested and analyzed **64,000+ authentication events** to identify distributed brute-force patterns.
- **The GRC Win:** Identified a critical control gap and implemented a NIST-compliant remediation plan.

---

## 🔍 Research Categories

### 🛠️ [Incident Reports](./Incident-Reports/)
*Technical post-mortems and forensic findings.*
- **[GCP SOC Lab Report](./Incident-Reports/2026-04-17-GCP-SOC-Lab.md):** Detecting T1110 (Brute Force) and T1059 (PowerShell Obfuscation).
- **[LockBit 3.0 Simulation](./Incident-Reports/Ransomware-Analysis.md):** Containment and recovery workflows.

### ⚖️ [Compliance & GRC](./Compliance-GRC/)
*Policy engineering and risk management.*
- **Vulnerability Management Policy:** Standards for cloud-native asset scanning.
- **Account Lockout Framework:** Mitigating credential access risk via technical controls.

---

## 🛠️ Tools & Standards
- **Platforms:** Google Cloud Platform (GCP), Ubuntu, Windows Server 2025.
- **Tools:** Wazuh, Sysmon, Nmap, Hydra, Mimikatz.
- **Frameworks:** MITRE ATT&CK, NIST SP 800-53, NIST CSF.

---
**Contact:** [purvansh95b@gmail.com](mailto:purvansh95b@gmail.com) | [LinkedIn](https://www.linkedin.com/in/purvanshbhatt)
