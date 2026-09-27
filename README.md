# 🔐 SOC Homelab Project

> Enterprise-grade SOC homelab built from scratch using Proxmox VE
>
> **Author:** Jimmy Mwendwa | **Location:** Nairobi, Kenya | **Updated:** July 2026

---

## 📋 Project Overview

This project documents the design, implementation and operation of a fully segmented SOC homelab environment. Built to develop real-world skills in network security, threat detection, incident response and attack simulation.

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
    ├── VLAN 10 → 10.10.10.0/24  → Servers (Wazuh, Ubuntu, Pi-hole)
    ├── VLAN 20 → 10.10.20.0/24  → Security (Ubuntu Laptop)
    ├── VLAN 30 → 10.10.30.0/24  → Desktop (Windows 10)
    └── VLAN 40 → 192.168.40.0/24 → Honeypot (Cisco FreeWiFi + OpenCanary)

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
| Ubuntu Laptop | Ubuntu 24.04 | 10.10.20.x | 20 | Attack/Defend | Active ✅ |
| OpenCanary VM | Ubuntu 24.04 | 192.168.40.x | 40 | Honeypot VM | Active ✅ |
| Cisco EPC3928S | Firmware | 192.168.40.2 | 40 | WiFi Lure AP | N/A |

---

## ✅ Features Implemented

### Network Security
- ✅ Multi-VLAN segmentation (VLANs 10, 20, 30, 40)
- ✅ VyOS stateful firewall with 15+ custom rules
- ✅ Zero-trust inter-VLAN security policies
- ✅ Pi-hole DNS filtering (264,000+ domains blocked)
- ✅ NAT routing across all VLANs
- ✅ Dedicated Proxmox management NIC (USB ethernet → vmbr1)
- ✅ Honeypot WiFi lure (Cisco EPC3928S broadcasting "FreeWiFi")

### SIEM & Monitoring
- ✅ Wazuh SIEM deployed and operational
- ✅ 6 endpoints monitored (Windows, Linux x3, Pi-hole, Honeypot)
- ✅ Custom detection rules mapped to MITRE ATT&CK
- ✅ Sysmon installed on Windows 10
- ✅ OpenCanary honeypot (fake SSH, FTP, HTTP, MySQL, RDP, Telnet)
- ✅ File Integrity Monitoring (FIM) on /etc/ with realtime alerts

### Attack Simulation
- ✅ SSH brute force simulation and detection
- ✅ Network reconnaissance (Nmap)
- ✅ File system tampering
- ✅ Vulnerability detection (CVE)
- ✅ False positive investigation and tuning
- ✅ 5 formal incident reports written

---

## 🍯 Honeypot Architecture (VLAN 40)

Two-layer approach to attract and detect attackers:

**Layer 1 — Cisco EPC3928S WiFi Lure**
- Broadcasts SSID: `FreeWiFi` — open network, no password
- Any device connecting gets isolated on VLAN 40
- Cannot reach real servers (VyOS blocks all except Wazuh ports 1514/1515)

**Layer 2 — OpenCanary VM (Fake Services)**

| Service | Port | Simulates |
|---|---|---|
| SSH | 2222 | Linux SSH server |
| FTP | 21 | File transfer server |
| HTTP | 80 | Web login page |
| MySQL | 3306 | Database server |
| RDP | 3389 | Windows Remote Desktop |
| Telnet | 23 | Legacy remote access |

---

## 🛡️ Custom Wazuh Detection Rules (MITRE ATT&CK)

| Rule ID | Description | Level | MITRE |
|---|---|---|---|
| 100001 | SSH Brute Force — 5 failures in 60s from same IP | 10 High | T1110 |
| 100002 | Successful SSH login after brute force — possible compromise | 15 Critical | T1110, T1078 |

---

## 🔴 Attack Simulation & Incident Reports

Real attacks simulated, detected and investigated:

| Report | Attack Type | Rule | Level | MITRE |
|---|---|---|---|---|
| [IR-001](incidents/IR-001-brute-force.md) | SSH Brute Force + Successful Login | 100002 | 15 CRITICAL | T1110, T1078 |
| [IR-002](incidents/IR-002-vulnerability.md) | CVE-2025-45582 Vulnerability | 23504 | 7 HIGH | T1068 |
| [IR-003](incidents/IR-003-recon.md) | Network Reconnaissance — Nmap | 5402 | 3 MEDIUM | T1046 |
| [IR-004](incidents/IR-004-fim.md) | File Integrity Alert — /etc/ tampered | 510 | 7 HIGH | T1543 |
| [IR-005](incidents/IR-005-false-positive.md) | False Positive Analysis — /usr/bin/diff | 510 | 7 INFO | T1574 |

---

## 🔒 Firewall Policy Summary

| Rule | Source | Destination | Action | Purpose |
|---|---|---|---|---|
| 1 | Any | Any | Accept | Established/Related traffic |
| 5 | VLAN 10 | VLAN 10 | Accept | Intra-server communication |
| 10 | VLAN 10 | Internet | Accept | Server internet access |
| 20 | VLAN 20 | Any | Accept | Security VLAN full access |
| 25 | VLAN 30 | Wazuh | Accept | Windows agent traffic |
| 26 | VLAN 30 | VLAN 10 | Drop | Block desktop from servers |
| 30 | VLAN 30 | Internet | Accept | Desktop internet access |
| 40 | 192.168.2.0/24 | VLAN 10 | Accept | Management access |
| 51 | VLAN 40 | Internet | Accept | Honeypot internet |
| 56 | VLAN 40 | Wazuh:1514 | Accept | Honeypot agent logs only |
| 57 | VLAN 40 | Wazuh:1515 | Accept | Honeypot agent enrollment |
| Default | Any | Any | Drop | Deny all other traffic |

---

## 🔧 Technologies Used

| Category | Tool | Version |
|---|---|---|
| Hypervisor | Proxmox VE | 8.x |
| Firewall | VyOS | 2026.02 |
| SIEM | Wazuh | 4.14.4 |
| DNS | Pi-hole | Latest |
| Honeypot | OpenCanary | Latest |
| WiFi Lure | Cisco EPC3928S | Firmware |
| OS (Server) | Ubuntu Server | 24.04 LTS |
| OS (Laptop) | Ubuntu Desktop | 24.04 LTS |
| OS (Desktop) | Windows 10 | Latest |
| Monitoring | Sysmon | Latest |
| Attack Tools | Hydra, Nmap | Latest |

---

## 📚 Documentation

| File | Description |
|---|---|
| [SOC_Homelab_Portfolio_v3.docx](SOC_Homelab_Portfolio_v3.docx) | Full technical documentation v3 |
| [Wazuh_Rules_Study_Notes.pdf](Wazuh_Rules_Study_Notes.pdf) | Detection rule learning notes |
| [incidents/](incidents/) | 5 incident reports from attack simulation |

---

## 🚀 Planned Next Steps

- [ ] Forward VyOS firewall logs to Wazuh
- [ ] Deploy Suricata IDS on VyOS
- [ ] Add Active Directory (Windows Server 2022)
- [ ] Set up TheHive for incident ticketing
- [ ] Write detection rules 100003–100010
- [ ] Complete TryHackMe SOC Level 1
- [ ] Implement fail2ban on Ubuntu Server

---

## 📫 Contact

- **LinkedIn:** [Jimmy Mwendwa](https://www.linkedin.com/in/jimmy-mwendwa-581985379)
- **GitHub:** [munchydevante-bot](https://github.com/munchydevante-bot)
- **Location:** Nairobi, Kenya
- **Open to:** SOC Analyst, Security Analyst, IT Security roles
