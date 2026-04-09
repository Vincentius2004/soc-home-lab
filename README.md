# soc-home-lab
A lab simulating a Security Operations Center using a SIEM to monitor DVWA. Includes attack scenarios (SQLi, XSS, brute force) and demonstrates log analysis, threat detection, and incident response workflows.


# 🛡️ SOC Home Lab — Attack Detection & Incident Response

## Overview
Project ini adalah simulasi SOC (Security Operations Center) home lab yang 
dibangun untuk mempelajari deteksi dan respons terhadap serangan siber.

## Arsitektur Lab
| VM | IP | Role |
|---|---|---|
| Wazuh SIEM | 192.168.217.128 | Manager + Indexer + Dashboard |
| Ubuntu Target | 192.168.217.130 | Victim Machine + DVWA |
| Kali Linux | 192.168.217.129 | Attacker Machine |

## Stack Teknologi
- **SIEM**: Wazuh v4.14
- **IDS/IPS**: Suricata v6.0.4
- **Vulnerable App**: DVWA (Damn Vulnerable Web Application)
- **Web Server**: Apache2 + Nginx
- **OS Monitoring**: Auditd
- **Attacker Tools**: Hydra, hping3, nmap, curl

## Serangan yang Disimulasikan
| # | Serangan | Layer | Tool | Rule ID |
|---|---|---|---|---|
| 1 | SQL Injection | 7 | curl | 100011 |
| 2 | XSS | 7 | curl | 100012 |
| 3 | Command Injection | 7 | curl | 100013 |
| 4 | Directory Traversal | 7 | curl | 100015 |
| 5 | File Upload Webshell | 7 | curl | 100053 |
| 6 | Reverse Shell | 7 | nc | 100050 |
| 7 | SSH Brute Force | 4 | hydra | 5551 |
| 8 | Nmap Reconnaissance | 3 | nmap | 86601 |
| 9 | DDoS SYN Flood | 4 | hping3 | 86601 |

## Struktur Repository
\`\`\`
soc-home-lab/
├── 01-infrastructure/     # Setup dan konfigurasi lab
├── 02-wazuh-rules/        # Custom detection rules
├── 03-attack-simulations/ # Dokumentasi setiap serangan
├── 04-detection-evidence/ # Screenshot bukti deteksi
├── 05-suricata/           # Konfigurasi Suricata IDS
├── 06-incident-response/  # Active Response dan IR Report
└── docs/                  # Dokumentasi tambahan
\`\`\`

## MITRE ATT&CK Coverage
- T1190 — Exploit Public-Facing Application
- T1059 — Command and Scripting Interpreter
- T1505.003 — Web Shell
- T1110 — Brute Force
- T1046 — Network Service Scanning
- T1499 — Endpoint Denial of Service
