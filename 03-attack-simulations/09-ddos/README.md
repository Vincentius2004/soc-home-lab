# DDoS SYN Flood Attack

## Deskripsi
SYN Flood membanjiri target dengan TCP SYN packets
tanpa menyelesaikan three-way handshake, menyebabkan
server kehabisan resources.

## MITRE ATT&CK
- Tactic: Impact
- Technique: T1499 — Endpoint Denial of Service

## Attack Commands
```bash
# SYN Flood ke port 80
sudo hping3 --flood --syn -p 80 192.168.217.130

# Jalankan 15-20 detik lalu Ctrl+C
```

## Detection
- Tool: Suricata IDS
- Rule ID: 86601
- Signature: SURICATA STREAM 3way handshake
- Hits: 5000+ dalam hitungan detik
