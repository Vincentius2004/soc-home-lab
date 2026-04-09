# 05 — Suricata IDS Configuration

## Instalasi
```bash
sudo apt install suricata -y
sudo suricata-update
```

## Konfigurasi `/etc/suricata/suricata.yaml`
```yaml
# Network interface
af-packet:
  - interface: ens33

# Home network
vars:
  address-groups:
    HOME_NET: "[192.168.217.0/24]"

# Rules location
rule-files:
  - /var/lib/suricata/rules/suricata.rules
```

## Integrasi ke Wazuh
Tambahkan di `/var/ossec/etc/ossec.conf` Ubuntu Target:
```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
</localfile>
```

## Serangan yang Terdeteksi

| Serangan | Signature | Rule ID Wazuh |
|---|---|---|
| Nmap Scan | ET SCAN Possible Nmap User-Agent | 86601 |
| SYN Flood | SURICATA STREAM 3way handshake | 86601 |
