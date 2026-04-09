# Nmap Reconnaissance

## Deskripsi
Nmap digunakan untuk port scanning dan service enumeration
sebagai langkah awal reconnaissance sebelum serangan.

## MITRE ATT&CK
- Tactic: Reconnaissance
- Technique: T1046 — Network Service Scanning
- Technique: T1595 — Active Scanning

## Attack Commands
```bash
# Basic scan
nmap 192.168.217.130

# Aggressive scan dengan version detection
nmap -A -sV -sC 192.168.217.130

# Full port scan
nmap -p- 192.168.217.130
```

## Detection
- Tool: Suricata IDS
- Rule ID: 86601
- Signature: ET SCAN Possible Nmap User-Agent
- Level: 3
