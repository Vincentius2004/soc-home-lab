# 10 — Wireshark Network Analysis

## Deskripsi
Wireshark digunakan untuk analisis paket jaringan secara real-time
untuk memvalidasi serangan yang terjadi di level network.

## MITRE ATT&CK
- Tactic: Discovery
- Technique: T1040 — Network Sniffing

## Instalasi
```bash
sudo apt install wireshark -y
```

## Filter per Serangan

### DDoS SYN Flood
tcp.flags.syn == 1 && tcp.flags.ack == 0 && tcp.port == 80

### Brute Force SSH
ip.src == 192.168.217.129 && tcp.port == 22
tcp.flags.syn == 1 and tcp.flags.ack == 0
### Nmap Scan
ip.src == 192.168.217.129 && (tcp || udp || icmp)
### SQL Injection (HTTP)
http.request.method == "GET" && http contains "union"
### Reverse Shell Connection
ip.src == 192.168.217.130 && tcp.dstport == 4444
## Cara Capture
```bash
# Capture semua traffic dari Kali
sudo wireshark -i ens33 -k

# Atau capture via terminal
sudo tcpdump -i ens33 -w /tmp/capture.pcap
```

## Evidence
Lihat folder `screenshots/` untuk hasil capture Wireshark
per serangan.
