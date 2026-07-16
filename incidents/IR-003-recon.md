# INCIDENT REPORT: INC-2026-003
**Title:** Multi-Host Network Reconnaissance Scan From Kali Segment
**Date/Time:** July 14, 2026 | 13:51:52 UTC
**Severity:** MEDIUM (Wazuh Level 8)
**Status:** CONTAINED

---

## 1. Executive Summary
The security monitoring console detected an aggressive, multi-port TCP SYN scan targeting the Server VLAN (VLAN 10) subnet. The scan originated from the designated Security testing segment (VLAN 20). Network isolation rules were checked and validated to ensure the scanning traffic did not cross unauthorized boundaries.

## 2. Incident Details
* **Source Host:** `Kali Linux` (IP: `10.10.20.100`)
* **Target Subnet:** `VLAN 10 Servers` (`10.10.10.0/24`)
* **Detected Protocol:** TCP Port Scan (Nmap scanning signatures identified)

## 3. Incident Timeline (UTC)
* **13:51:50.010** - Rapid succession of connection drop logs observed on the VyOS firewall.
* **13:51:52.420** - Alert triggered: "Kali VLAN20: port scan detected against Servers VLAN10" (Rule 100091 / T1046).
* **13:53:00.000** - Log analysis initiated to verify if any port states returned "open" to the scanning machine.

## 4. Impact Assessment
* **Tactic:** Discovery (TA0007)
* **Technique:** Network Service Discovery (T1046)
* **Risk:** Medium. Threat actor mapping network topology to identify active systems and exposed services.

## 5. Containment & Verification Actions
1. **Firewall Verification:** Confirmed that the VyOS default drop policy successfully blocked access to all ports except for pre-approved exceptions (Pi-hole port 53, Wazuh port 1514).
2. **Recon Audit:** Reviewed destination IP logs. The scanning host only discovered the DNS and SIEM services; no administrative database management interfaces (like Proxmox Web GUI on port 8006) were exposed.

## 6. Lessons Learned & Recommendations
* Enforce strict rate-limiting on the VyOS firewall interface for VLAN 20 to prevent future rapid scanning from degrading network performance.
