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
