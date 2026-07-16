# 🔐 SOC Homelab Project

> Enterprise-grade SOC homelab built from scratch using Proxmox VE
>
> **Author:** Jimmy Mwendwa | **Location:** Nairobi, Kenya | **Updated:** July 2026

---

## 📋 Project Overview

This project documents the design, implementation and operation of a fully
segmented SOC homelab environment. Built to develop real-world skills in
network security, threat detection, incident response and attack simulation.

---

## 🏗️ Architecture

```
Internet
    │
Tenda F3 (192.168.2.1) ← WAN Gateway
    │
Managed Switch ← VLAN Segmentation
    │
VyOS Firewall (192.168.2.100) ← Stateful Firewall & Router
    ├── VLAN 10 → 10.10.10.0/24 → Servers (Wazuh, Ubuntu, Pi-hole)
    ├── VLAN 20 → 10.10.20.0/24 → Security (Ubuntu Laptop)
    ├── VLAN 30 → 10.10.30.0/24 → Desktop (Windows 10)
    └── VLAN 40 → 192.168.40.0/24 → Honeypot (Cisco WiFi + OpenCanary)

Proxmox Host:
    ├── Built-in NIC → vmbr0 → VyOS (all VLANs)
    └── USB NIC      → vmbr1 → Proxmox Management (192.168.2.150)
```

---

## 🖥️ Infrastructure

| VM/CT | OS | IP Address | VLAN | Role | Wazuh Agent |
|---|---|---|---|---|---|
| Wazuh Server | Ubuntu 24.04 | 10.10.10.101 | 10 | SIEM Manager | Local ✅ |
| Ubuntu Server | Ubuntu 24.04 | 10.10.10.100 | 10 | Endpoint | Active ✅ |
| Pi-hole | Debian LXC | 10.10.10.2 | 10 | DNS Filtering | Active ✅ |
| VyOS Firewall | VyOS 2026.02 | 10.10.10.1 | Trunk | Firewall/Router | N/A |
| Windows 10 | Windows 10 | 10.10.30.100 | 30 | Endpoint | Active ✅ |
| Ubuntu Laptop | Ubuntu 24.04 | 10.10.20.x | 20 | Security Testing | Active ✅ |
| OpenCanary | Ubuntu 24.04 | 192.168.40.x | 40 | Honeypot VM | Active ✅ |
| Cisco EPC3928S | Firmware | 192.168.40.2 | 40 | Honeypot WiFi AP | N/A |

---

## ✅ Features Implemented

### Network Security
- ✅ Multi-VLAN segmentation (VLANs 10, 20, 30, 40)
- ✅ VyOS stateful firewall with 15+ custom rules
- ✅ Zero-trust inter-VLAN security policies
- ✅ Pi-hole DNS filtering (264,000+ domains blocked)
- ✅ NAT routing across all VLANs
- ✅ Dedicated Proxmox management NIC (USB ethernet)
- ✅ Honeypot WiFi (Cisco EPC3928S "FreeWiFi")

### SIEM & Monitoring
- ✅ Wazuh SIEM deployed and operational
- ✅ 6 endpoints monitored (Windows, Linux x3, Pi-hole, Honeypot)
- ✅ Custom detection rules mapped to MITRE ATT&CK
- ✅ Sysmon installed on Windows 10
- ✅ OpenCanary honeypot (fake SSH, FTP, HTTP, MySQL, RDP, Telnet)
- ✅ File Integrity Monitoring (FIM) on /etc/

### Attack Simulation
- ✅ SSH brute force simulation and detection
- ✅ Network reconnaissance (Nmap)
- ✅ Privilege escalation testing
- ✅ File system tampering
- ✅ False positive analysis and tuning

---

## 🛡️ Custom Wazuh Detection Rules (MITRE ATT&CK)

| Rule ID | Description | Level | MITRE |
|---|---|---|---|
| 100001 | SSH Brute Force (5 attempts/60s) | 10 | T1110 |
| 100002 | Successful Login After Brute Force | 15 | T1110, T1078 |

---

## 🔴 Attack Simulation & Incident Reports

Real attack scenarios simulated, detected and investigated:

| Report | Attack Type | Severity | Rule | MITRE |
|---|---|---|---|---|
| [IR-001](incidents/IR-001-brute-force.md) | SSH Brute Force + Compromise | CRITICAL | 100002 | T1110, T1078 |
| [IR-002](incidents/IR-002-vulnerability.md) | CVE-2025-45582 Detected | HIGH | 23504 | T1068 |
| [IR-003](incidents/IR-003-recon.md) | Network Reconnaissance (Nmap) | MEDIUM | 5402 | T1046 |
| [IR-004](incidents/IR-004-fim.md) | File Integrity Alert (/etc/) | HIGH | 510 | T1543 |
| [IR-005](incidents/IR-005-false-positive.md) | False Positive Analysis | INFO | 510 | T1574 |

---

## 🔧 Technologies Used

| Category | Tool | Version |
|---|---|---|
| Hypervisor | Proxmox VE | 8.x |
| Firewall | VyOS | 2026.02 |
| SIEM | Wazuh | 4.14.4 |
| DNS | Pi-hole | Latest |
| Honeypot | OpenCanary | Latest |
| OS (Server) | Ubuntu Server | 24.04 LTS |
| OS (Laptop) | Ubuntu Desktop | 24.04 LTS |
| OS (Desktop) | Windows 10 | Latest |
| Monitoring | Sysmon | Latest |
| WiFi Lure | Cisco EPC3928S | Firmware |

---

## 🍯 Honeypot Setup

The honeypot uses a **two-layer approach**:

```
Layer 1: Cisco EPC3928S
→ Broadcasts "FreeWiFi" SSID
→ Lures devices to connect
→ VLAN 40 isolation (192.168.40.0/24)

Layer 2: OpenCanary VM
→ Fake services: SSH(2222), FTP, HTTP, MySQL, RDP, Telnet
→ Logs all connection attempts
→ Wazuh agent forwards alerts to SIEM
→ Completely isolated from real servers
```

---

## 🔒 Firewall Policy Summary

| Rule | Source | Destination | Action | Purpose |
|---|---|---|---|---|
| 1 | Any | Any | Accept | Established/Related |
| 5 | VLAN 10 | VLAN 10 | Accept | Intra-server |
| 10 | VLAN 10 | Internet | Accept | Server internet |
| 20 | VLAN 20 | Any | Accept | Security VLAN |
| 25 | VLAN 30 | Wazuh | Accept | Agent traffic |
| 26 | VLAN 30 | VLAN 10 | Drop | Desktop isolation |
| 30 | VLAN 30 | Internet | Accept | Desktop internet |
| 40 | 192.168.2.0/24 | VLAN 10 | Accept | Management access |
| 51 | VLAN 40 | Internet | Accept | Honeypot internet |
| 56 | VLAN 40 | Wazuh:1514 | Accept | Honeypot agent |
| Default | Any | Any | Drop | Deny all |

---

## 📚 Documentation

| Document | Description |
|---|---|
| [SOC_Homelab_Portfolio_v2.docx](SOC_Homelab_Portfolio_v2.docx) | Full technical documentation |
| [incidents/](incidents/) | Incident reports from attack simulations |
| [Wazuh_Rules_Study_Notes.pdf](Wazuh_Rules_Study_Notes.pdf) | Detection rule learning notes |

---

## 🚀 Planned Improvements

- [ ] Forward VyOS firewall logs to Wazuh
- [ ] Deploy Suricata IDS
- [ ] Add Active Directory (Windows Server 2022)
- [ ] Set up TheHive for incident ticketing
- [ ] Complete TryHackMe SOC Level 1
- [ ] Add more custom detection rules (100003-100010)
- [ ] Implement fail2ban on Ubuntu Server

---

## 📫 Contact

- **LinkedIn:** [Jimmy Mwendwa](https://www.linkedin.com/in/jimmy-mwendwa-581985379)
- **GitHub:** [munchydevante-bot](https://github.com/munchydevante-bot)
- **Location:** Nairobi, Kenya
- **Open to:** SOC Analyst, Security Analyst roles
