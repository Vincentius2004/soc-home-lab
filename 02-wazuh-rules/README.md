# 02 — Wazuh Custom Detection Rules

## Lokasi File
- **Wazuh Server**: `/var/ossec/etc/rules/local_rules.xml`

## Cara Apply Rules
```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
sudo systemctl restart wazuh-manager
```

## Rules Overview

| Rule ID | Level | Deskripsi | MITRE |
|---|---|---|---|
| 100001 | 5 | SSH Login gagal | T1110 |
| 100002 | 12 | SSH Brute Force | T1110 |
| 100010 | 3 | DVWA Request masuk | - |
| 100011 | 10 | SQL Injection | T1190 |
| 100012 | 10 | XSS Attack | T1059.007 |
| 100013 | 12 | Command Injection | T1059 |
| 100014 | 15 | Reverse Shell via Web | T1059.004 |
| 100015 | 10 | Directory Traversal | T1083 |
| 100050 | 15 | Reverse Shell (Auditd) | T1059.004 |
| 100051 | 12 | Command Injection (Auditd) | T1059 |
| 100052 | 10 | Akses File Sensitif | T1083 |
| 100053 | 14 | Webshell Upload | T1505.003 |
| 100054 | 13 | Privilege Escalation | T1548 |

## Wazuh Rule Level Reference

| Level | Severity |
|---|---|
| 0-3 | Informational |
| 4-7 | Low |
| 8-11 | Medium |
| 12-14 | High |
| 15 | Critical |
