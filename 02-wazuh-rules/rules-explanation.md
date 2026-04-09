# Penjelasan Custom Rules

## SSH Rules (100001-100002)
Mendeteksi brute force SSH berdasarkan frekuensi login gagal.
Rule 100002 trigger jika ada 5 kali gagal dalam 60 detik dari IP yang sama.

## DVWA Web Attack Rules (100010-100015)
Pipeline deteksi:
1. Rule 100010 sebagai base rule — match semua request ke DVWA
2. Rules 100011-100015 sebagai child rules — match pattern spesifik

Semua rules menggunakan `<regex type="pcre2">` untuk pattern matching
di full_log karena field `url` adalah static field di Wazuh.

## Auditd Rules (100050-100054)
Mendeteksi serangan di level OS menggunakan audit keys:
- `reverse_shell` — eksekusi shell dari folder web
- `command_injection` — eksekusi command berbahaya
- `sensitive_file` — akses /etc/passwd, /etc/shadow
- `webshell_upload` — file baru di folder uploads
- `privilege_escalation` — penggunaan sudo/setuid
