# 🛡️ Network-Detection-and-Response-SOC-Investigation

![Suricata](https://img.shields.io/badge/IDS-Suricata-orange)
![Zeek](https://img.shields.io/badge/NSM-Zeek-blue)
![Splunk](https://img.shields.io/badge/SIEM-Splunk-black)
![VirusTotal](https://img.shields.io/badge/Threat%20Intel-VirusTotal-success)
![MITRE ATT&CK](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)

An end-to-end SOC investigation of a captured malware incident — detecting, correlating, and triaging suspicious network traffic using Suricata, Zeek, Splunk, and VirusTotal.

## 🎯 Skills Demonstrated

| Competency | Implementation |
| :--- | :--- |
|  **Network Intrusion Detection** | Suricata ruleset alert analysis (`eve.json`) |
|  **Network Traffic Forensics** | Zeek protocol log correlation (`conn`, `http`, `dns`) |
|  **SIEM Investigation (SPL)** | Splunk search building, filtering, and timeline construction |
|  **Threat Intelligence Enrichment** | VirusTotal IOC verification and IP reputation checks |
|  **Alert Triage & Disposition** | Evidence-based classification of True Positives and Benign True Positives |

---

## 📋 Executive Summary

This project documents a network security investigation of a captured malware traffic file (PCAP). Using an Ubuntu analysis host running **Suricata** and **Zeek**, network traffic was parsed and forwarded into **Splunk** for SIEM-based correlation.

The investigation identified an internal host (`10.10.1.128`) executing an automated `curl` request to download a Windows executable (`runner_ilove.exe`) from an external, VirusTotal-flagged IP address (`107.175.82.242`, flagged 21/92 by security vendors). Suricata rules and Zeek connection logs were systematically triaged, confirming a **True Positive** malicious file download, alongside routine operational traffic such as Google DNS-over-HTTPS (DoH) classified as a **Benign True Positive**.

---

## 🏗️ Lab Architecture & Data Pipeline

```text
[ Source PCAP: Malware Traffic Capture ]
                 │
                 ▼
   ┌──────────────────────────────┐
   │  Ubuntu 24.04 Analysis Host  │
   ├──────────────────────────────┤
   │  • Suricata (IDS Engine)     │ ───> Generates: eve.json Alerts
   │  • Zeek (Network Parsers)    │ ───> Generates: conn / http / dns Logs
   └──────────────┬───────────────┘
                  │
                  ▼ (Log Ingestion / Forwarding)
   ┌──────────────────────────────┐
   │   Splunk Enterprise (SIEM)   │
   ├──────────────────────────────┤
   │   SPL Queries & Triage       │ ───> Extracted IOCs (External IP / Domain)
   └──────────────┬───────────────┘
                  │
                  ▼ (Query IOC Reputation)
   ┌──────────────────────────────┐
   │  VirusTotal Threat Intel     │
   ├──────────────────────────────┤
   │  Reputation Check (21/92)    │
   └──────────────┬───────────────┘
                  │
                  ▼
[ Final SOC Incident Report & Triage Verdict ]
```

---

## 🔎 Detailed Alert Triage & Disposition

| # | Alert Signature | Verdict | Justification |
| :--- | :--- | :--- | :--- |
| **1** | `ET INFO PE EXE or DLL Windows File Download HTTP` | 🔴 **True Positive** | Confirmed file transfer of a Windows Portable Executable over port 9000. |
| **2** | `ET INFO Executable Download from Dotted-Quad Host` | 🔴 **True Positive** | File retrieved directly from a raw IP address rather than a trusted software repository. |
| **3** | `ET HUNTING curl User-Agent to Dotted Quad` | 🔴 **True Positive** | Automated tool (`curl`) utilized instead of a standard web browser. |
| **4** | `ET HUNTING SUSPICIOUS Dotted Quad Host MZ Response` | 🔴 **True Positive** | Server response contained the valid `MZ` executable magic bytes header. |
| **5** | `ET INFO Observed Google DNS over HTTPS Domain` | 🟢 **Benign True Positive** | Legitimate encrypted DNS query to Google public resolvers (`8.8.4.4`). Background operational noise. |

---

## 🎯 MITRE ATT&CK Mapping

| Technique ID | Technique Name | Observed Activity / Context |
| :--- | :--- | :--- |
| **T1059.003** | Windows Command Shell | Execution initialized via command-line utility (`cmd.exe /c runner_ilove.exe`). |
| **T1204.002** | User Execution: Malicious File | Downloaded file executed on `WORKSTATION-07` under user account `JSmith`. |

---




## 📁 Repository Structure

- [README.md](README.md) — Project overview, architecture, and executive summary
- [SOC Incident Report.pdf](SOC%20Incident%20Report.pdf) — Comprehensive SOC incident report and detailed triage notes
- [Splunk SPL Investigation Queries.pdf](Splunk%20SPL%20Investigation%20Queries.pdf) — Splunk SPL search queries and EDR/NDR correlation logic                # 


