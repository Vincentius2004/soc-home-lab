# 01 — Infrastructure Setup

## Spesifikasi Virtual Machine

| VM | OS | RAM | vCPU | Disk | IP |
|---|---|---|---|---|---|
| Wazuh SIEM | Ubuntu 22.04 | 3GB | 2 | 60GB | 192.168.217.128 |
| Ubuntu Target | Ubuntu 22.04 | 1GB | 1 | 20GB | 192.168.217.130 |
| Kali Linux | Kali 2024 | 1.5GB | 1 | 20GB | 192.168.217.129 |

## Network Topology
Kali Linux --> 192.168.217.129
Ubuntu Target --> 192.168.217.130
Wazuh SIEM --> 192.168.217.128

Apache2 - Wazuh Manager
DVWA - Wazuh Indexer
Wazuh Agent - Wazuh Dashboard
Suricata - Port 443 (Dashboard)
Auditd - Port 1514 (Agent) │- Port 55000 (API) │ Log & Alert 

## Software yang Diinstall

### Wazuh SIEM (192.168.217.128)
- Wazuh Manager v4.14
- Wazuh Indexer (Elasticsearch)
- Wazuh Dashboard (Kibana)

### Ubuntu Target (192.168.217.130)
- Apache2 (Web Server)
- DVWA (Damn Vulnerable Web Application)
- Wazuh Agent v4.14
- Suricata v6.0.4 (IDS/IPS)
- Auditd (System Call Monitoring)
- Nginx (Log Proxy)

### Kali Linux (192.168.217.129)
- nmap
- hydra
- hping3
- curl
- netcat

## Setup Steps

### 1. Install Wazuh All-in-One
Menggunakan official installer dari dokumentasi Wazuh:
```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash wazuh-install.sh -a
```

### 2. Install DVWA di Ubuntu Target
```bash
sudo apt install apache2 php php-mysqli mysql-server -y
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
```

### 3. Install Wazuh Agent di Ubuntu Target
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | sudo apt-key add -
echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
sudo apt update && sudo apt install wazuh-agent -y
```

### 4. Install Suricata
```bash
sudo apt install suricata -y
sudo suricata-update
```

### 5. Install Auditd
```bash
sudo apt install auditd audispd-plugins -y
```
