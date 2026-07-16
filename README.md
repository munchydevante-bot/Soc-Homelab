# 🔐 SOC Homelab Project

## Overview
Enterprise-grade SOC homelab built from scratch using Proxmox VE.
Features multi-VLAN network segmentation, stateful firewall, 
centralized SIEM, and DNS-level threat filtering.

## Architecture
```
Internet → Tenda F3 → VyOS Firewall → Managed Switch
                                      ├── VLAN 10 → Servers (Wazuh, Ubuntu, Pi-hole)
                                      ├── VLAN 20 → Security (Kali Linux)
                                      └── VLAN 30 → Desktop (Windows 10)
```

## Technologies Used
| Tool | Purpose |
|------|---------|
| Proxmox VE | Hypervisor |
| VyOS 2026 | Firewall & Routing |
| Wazuh 4.x | SIEM & EDR |
| Pi-hole | DNS Filtering |
| Ubuntu Server 24.04 | Server OS |
| Kali Linux | Security Testing |
| Windows 10 | Endpoint Monitoring |

## Features
- ✅ Multi-VLAN network segmentation
- ✅ Stateful firewall with inter-VLAN rules
- ✅ Centralized SIEM with Windows & Linux agents
- ✅ DNS-level ad and threat filtering
- ✅ NAT routing across all VLANs
- ✅ DHCP per VLAN

## Documentation
See `SOC_Homelab_Portfolio.docx` for full technical documentation.
arkdown# 🔐 SOC Homelab Project v2

> Enterprise-grade SOC homelab built from scratch using Proxmox VE
> 
> **Author:** Jimmy Mwendwa | **Location:** Nairobi, Kenya | **Updated:** April 2026

---

## 📋 Project Overview

This project documents the design, implementation and operation of a fully 
segmented SOC homelab environment. Built to develop real-world skills in 
network security, threat detection and incident response.

---

## 🏗️ Architecture
Internet
│
Tenda F3 (192.168.2.1) ← WAN Gateway
│
VyOS Firewall (192.168.2.100) ← Stateful Firewall & Router
│
Managed Switch ← VLAN Segmentation
├── VLAN 10 → 10.10.10.0/24 → Servers (Wazuh, Ubuntu, Pi-hole)
├── VLAN 20 → 10.10.20.0/24 → Security (Kali Linux)
└── VLAN 30 → 10.10.30.0/24 → Desktop (Windows 10)
---

## 🖥️ Infrastructure

| VM/CT | OS | IP Address | VLAN | Role |
|---|---|---|---|---|
| Wazuh Server | Ubuntu 24.04 LTS | 10.10.10.101 | 10 | SIEM Manager |
| Ubuntu Server | Ubuntu 24.04 LTS | 10.10.10.100 | 10 | Wazuh Agent |
| Pi-hole | Debian LXC | 10.10.10.2 | 10 | DNS Filtering |
| VyOS Firewall | VyOS 2026.02 | 10.10.10.1 | Trunk | Firewall/Router |
| Windows 10 | Windows 10 | 10.10.30.100 | 30 | Endpoint Agent |
| Kali Linux | Kali Linux | 10.10.20.x | 20 | Security Testing |

---

## ✅ Features Implemented

### Network Security
- ✅ Multi-VLAN segmentation (VLANs 10, 20, 30)
- ✅ VyOS stateful firewall with 13 custom rules
- ✅ Zero-trust inter-VLAN security policies
- ✅ Pi-hole DNS filtering (264,000+ domains blocked)
- ✅ NAT routing across all VLANs

### SIEM & Monitoring
- ✅ Wazuh SIEM deployed and operational
- ✅ 5 endpoints monitored (Windows, Linux, Kali, Pi-hole)
- ✅ Custom detection rules mapped to MITRE ATT&CK
- ✅ Sysmon installed on Windows 10

### Infrastructure
- ✅ Proxmox VE hypervisor
- ✅ Static IPs on all VMs
- ✅ Auto-start configured on all VMs
- ✅ Remote access via Tailscale VPN

---

## 🛡️ Wazuh Detection Rules (MITRE ATT&CK)

| Rule ID | Description | Level | MITRE |
|---|---|---|---|
| 100001 | SSH Brute Force (5 attempts/60s) | 10 | T1110 |
| 100002 | Login Success After Brute Force | 12 | T1110 |
| 100003 | New Linux User Created | 8 | T1136 |
| 100004 | Sudo Privilege Escalation | 6 | T1548 |
| 100005 | Windows Brute Force | 8 | T1110 |
| 100006 | New Windows User Created | 10 | T1136 |
| 100007 | Nmap Port Scan Detected | 10 | T1046 |
| 100008 | /etc/ File Modified | 8 | T1543 |
| 100009 | Direct Root Login | 12 | T1078 |
| 100010 | Same IP Brute Force (10/120s) | 10 | T1110 |

---

## 🔧 Technologies Used

| Category | Tool | Version |
|---|---|---|
| Hypervisor | Proxmox VE | 8.x |
| Firewall | VyOS | 2026.02 |
| SIEM | Wazuh | 4.14.4 |
| DNS | Pi-hole | Latest |
| OS (Server) | Ubuntu Server | 24.04 LTS |
| OS (Security) | Kali Linux | 2024.x |
| OS (Desktop) | Windows 10 | Latest |
| Monitoring | Sysmon | Latest |

---

## 📚 Documentation

See `SOC_Homelab_Portfolio_v2.docx` for full technical documentation including:
- Complete network architecture
- Firewall policy table
- Challenges and solutions
- Static IP configuration
- Stability improvements

---

## 🚀 Planned Improvements

- [ ] Add Wazuh detection rules
- [ ] Forward VyOS logs to Wazuh
- [ ] Simulate attacks from Kali Linux
- [ ] Deploy Suricata IDS
- [ ] Set up TheHive for incident ticketing
- [ ] Complete TryHackMe SOC Level 1

---

## 📫 Contact

- **LinkedIn:** [Jimmy Mwendwa](https://www.linkedin.com/in/jimmy-mwendwa-581985379)
- **GitHub:** [munchydevante-bot](https://github.com/munchydevante-bot)
- **Location:** Nairobi, Kenya
- **Open to:** SOC Analyst, Security Analyst roles
