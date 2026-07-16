# INCIDENT REPORT: INC-2026-001
**Title:** Successful SSH Login After Brute Force Attack
**Date/Time:** July 14, 2026 | 12:22:58 UTC
**Severity:** CRITICAL (Wazuh Level 15)
**Status:** RESOLVED / CONTAINED

---

## 1. Executive Summary
At 12:22:58 UTC, a critical alert (Rule 100002) triggered indicating a successful SSH login immediately following a sustained brute-force attack. The attack originated internally from the security testing VLAN (VLAN 20). The target host was isolated, the attacker's source IP was dropped, and the compromised user's credentials were flagged for immediate rotation.

## 2. Incident Details
* **Victim Host:** `ubuntu-server` (IP: `10.10.10.100`)
* **Compromised Account:** `munchy`
* **Attacker Source IP:** `10.10.20.100` (Kali Linux - VLAN 20)
* **Attack Protocol:** SSH (TCP Port 22)

## 3. Incident Timeline (UTC)
* **12:08:43.201** - Multiple failed authentication attempts logged (Rule 5760).
* **12:10:43.607** - Brute force threshold exceeded (Rule 100001: 5 failed attempts in 60s).
* **12:22:58.772** - Password successfully accepted for user `munchy` from IP `10.10.20.100` (Rule 100002).

## 4. Impact Assessment
* **Tactic:** Credential Access (TA0006), Initial Access (TA0001)
* **Technique:** Brute Force (T1110), Valid Accounts (T1078)
* **Risk:** High. Unauthorized interactive bash shell accessed on critical server segment.

## 5. Containment & Eradication Actions
1. **Host-Level Block:** Wazuh Active Response successfully executed on `ubuntu-server` to drop all incoming TCP traffic from `10.10.20.100`.
2. **Network Block:** Confirmed the rule addition on the VyOS firewall to permanently block `10.10.20.100` from entering the Server VLAN (VLAN 10).
3. **Account Hardening:** Terminated the active SSH session for user `munchy`. Forced a password reset on next login.

## 6. Lessons Learned & Recommendations
* **Root Cause:** Weak password policy allowed user `munchy` to use a guessable credential.
* **Fix:** Disable SSH password authentication in `/etc/ssh/sshd_config` and enforce SSH key-only authentication.
