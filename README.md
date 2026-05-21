# Multi-Host Intrusion Investigation — Incident Response Report

A full incident response investigation and forensic timeline built as the final project for the Incident Response Methods course at the University of South Florida (Fall 2025). The project involved analyzing a simulated enterprise intrusion across two compromised Windows workstations using Splunk as the primary analysis platform.

---

## Scenario Overview

Between June 17 and June 25, 2025, two internal Windows hosts were compromised by a malicious executable delivered through a drive-by download. The investigation covers the full attack lifecycle — from initial delivery through command-and-control (C2) registration, credential elevation, persistence, data exfiltration, and active anti-forensic cleanup.

**Affected Hosts:** `ACC-WIN10-13`, `ACC-WIN10-16`  
**Malware:** `QRCodeScanner.exe` (delivered via external HTTP GET)  
**C2 Infrastructure:** External domain communicating over HTTP POST  
**Investigation Window:** June 25, 2025  

---

## Attack Chain Summary

```
[1] Malware Delivery
    └─ User-driven HTTP GET downloads QRCodeScanner.exe from external domain

[2] Initial Execution
    └─ Executable written to C:\Users\Public\ and launched immediately
    └─ Confirmed via Prefetch artifacts and MFT metadata

[3] Credential Elevation
    └─ PowerShell spawns elevated process using hardcoded credentials
    └─ QRCodeScanner.exe re-executed under SYSTEM/Administrator context

[4] C2 Registration
    └─ Outbound HTTP GET to /register endpoint on malicious domain
    └─ Confirmed via proxy logs and IDS alerts

[5] Reconnaissance
    └─ cmd.exe used to enumerate system info, environment variables,
       active network connections, local users, firewall config

[6] Data Exfiltration
    └─ PowerShell loop collects and POSTs local files to C2
    └─ Multiple HTTP POST requests observed via proxy and PCAP

[7] Persistence
    └─ Scheduled task "ThreadCleaner" created under SYSTEM context
    └─ Executes malicious binary at logon for continued access

[8] Anti-Forensics
    └─ cmd.exe deletes secrets.zip and secrets.txt post-exfiltration
    └─ Active evidence removal to reduce forensic traceability

[9] Multi-Host Spread
    └─ Identical behavior observed on second host in same time window
    └─ Confirms shared infection vector or lateral movement
```

---

## Forensic Artifacts Analyzed

| Artifact Type | Tool / Source | Purpose |
|---------------|---------------|---------|
| Sysmon Event Logs (Event ID 1, 3) | Splunk | Process creation, network connections |
| Windows Event Logs | Splunk | PowerShell execution, authentication events |
| Proxy Logs | Splunk | HTTP GET/POST traffic to C2 |
| IDS Alerts | Splunk | Signature-based detections |
| Prefetch Files | PECmd | Execution history and run counts |
| MFT Metadata | MFTECmd | File creation and modification timestamps |
| Registry Artifacts | Splunk | Persistence keys, IE security zone modifications |
| Shellbag Data | Splunk | Directory navigation by attacker |
| PCAP / Packet Captures | Splunk | Network-level C2 traffic validation |

---

## Deliverables

| File | Description |
|------|-------------|
| `IRM_Final_Report.pdf` | Full incident report with executive summary, technical findings, and remediation recommendations |
| `FINAL_TIMELINE.xlsx` | Chronological forensic timeline correlating all artifact types across both hosts |

---

## Key Findings

- **C2 communication** was established within seconds of execution and maintained via repeated HTTP POST beaconing
- **Credential elevation** was achieved through embedded plaintext credentials in PowerShell — a common red-team technique
- **Scheduled task persistence** was established under SYSTEM context, surviving reboots
- **Anti-forensic activity** (file deletion) occurred immediately after confirmed exfiltration, indicating an automated or scripted attacker workflow
- **Cross-host correlation** of artifacts strengthens attribution and confirms the incident affected multiple endpoints simultaneously

---

## Tools Used

- **Splunk** — primary SIEM platform for log ingestion, querying, and timeline construction
- **PECmd** — Prefetch artifact parsing
- **MFTECmd** — Master File Table analysis
- **Microsoft Excel** — forensic timeline documentation

---

## Skills Demonstrated

- SIEM-based investigation and log correlation (Splunk)
- Multi-source forensic artifact analysis
- Attack chain reconstruction using the incident response lifecycle
- Professional technical writing (executive summary, findings, recommendations)
- Windows forensics: Prefetch, MFT, Registry, Shellbag, Sysmon

---

## Author

**Gabriel Garcia Crepaldi**  
B.S. Cybersecurity — University of South Florida, 2026  
Course: Incident Response Methods — Prof. Ryan Irving  
[LinkedIn](https://www.linkedin.com/in/gabriel-crepaldi-ab7b18388/) · [GitHub](https://github.com/gabrielcrepaldi)
