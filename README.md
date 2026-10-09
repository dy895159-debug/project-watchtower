# project-watchtower
Home SOC lab simulating real-world attacks (brute-force, lateral movement) with Splunk-based detection engineering
Markdown

# Project Watchtower 🛡️

A hands-on home SOC (Security Operations Center) lab built to simulate real-world cyberattacks and develop detection engineering skills using Splunk, Sysmon, and Kali Linux.

## 🎯 Project Overview

This project demonstrates a complete attack-to-detection pipeline in a virtualized environment:
- An attacker machine (Kali Linux) performs reconnaissance and exploitation
- A target machine (Windows 10) generates detailed logs via Sysmon and Windows Event Logs
- Splunk acts as the SIEM, ingesting logs and powering custom detection rules and alerts

## 🏗️ Architecture
┌─────────────────┐ ┌──────────────────┐ ┌─────────────────┐
│ Kali Linux │ ------> │ Windows 10 VM │ ------> │ Splunk │
│ (Attacker) │ Attacks │ (Target/Victim) │ Logs │ (SIEM/Host) │
│ 10.0.2.4 │ │ Sysmon + WinLog │ │ 127.0.0.1:8000 │
└─────────────────┘ └──────────────────┘ └─────────────────┘

text


**Tools used:**
- Splunk Enterprise (Trial)
- Splunk Universal Forwarder
- Sysmon (SwiftOnSecurity config)
- Kali Linux
- NetExec (SMB brute-forcing)
- Impacket (psexec.py - lateral movement simulation)
- VirtualBox (NAT Network)

## 📂 Attack Scenarios

| # | Scenario | Technique | Detection Method |
|---|----------|-----------|-------------------|
| 1 | [SMB Brute Force Attack](./scenario-1-bruteforce/README.md) | Credential brute-forcing via SMB | Custom Splunk alert (failed logon correlation) |
| 2 | [Lateral Movement via PsExec](./scenario-2-lateral-movement/README.md) | Remote code execution using stolen credentials | Sysmon registry/file events + Windows Defender |

## 🔍 Key Skills Demonstrated

- Log source configuration (Sysmon, Windows Event Logs → Splunk Universal Forwarder)
- SPL (Search Processing Language) query development
- Detection engineering (correlation searches, scheduled alerts)
- Attack simulation (brute-force, lateral movement)
- Incident analysis and timeline reconstruction
- Troubleshooting real-world log parsing issues (e.g., duplicate field extraction)

## 📈 What's Next

- [ ] Add detection for reconnaissance activity (Nmap scans)
- [ ] Build a Splunk dashboard visualizing all attack scenarios
- [ ] Add MITRE ATT&CK technique mapping for each scenario

**Author:** Daniel Young
**Contact:** [Add your LinkedIn or email here]
