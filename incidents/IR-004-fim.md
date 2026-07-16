# INCIDENT REPORT: INC-2026-004
**Title:** File Integrity Violation - Unsigned Executable Added to System32
**Date/Time:** July 14, 2026 | 13:51:39 UTC
**Severity:** HIGH (Wazuh Level 12)
**Status:** UNDER INVESTIGATION

---

## 1. Executive Summary
A high-severity Sysmon alert notified the SOC that a non-standard, unsigned binary file named `mimikatz.exe` was written to the `C:\Windows\System32\` directory of a Windows 10 workstation in VLAN 30. File Integrity Monitoring (FIM) instantly flagged the creation event. The workstation has been isolated from the network.

## 2. Incident Details
* **Compromised Host:** `DESKTOP-46UHFOD` (IP: `10.10.30.100` - VLAN 30)
* **Affected Directory:** `C:\Windows\System32\mimikatz.exe`
* **File MD5 Hash:** `5D3C3E...[Truncated]`
* **Executing Process:** Unknown CMD parent process.

## 3. Incident Timeline (UTC)
* **13:51:38.900** - File creation event occurred.
* **13:51:39.002** - Sysmon Event ID 11 (FileCreate) parsed by Wazuh agent.
* **13:51:39.521** - Wazuh alert fired (Rule 100085: Unsigned binary written to System32 - T1036 Masquerading).
* **13:55:00.000** - SOC analyst executed manual network containment of the Windows 10 workstation.

## 4. Impact Assessment
* **Tactic:** Defense Evasion (TA0005), Credential Access (TA0006)
* **Technique:** Masquerading (T1036), LSASS Memory Dumping (T1003.001)
* **Risk:** Critical. The presence of known credential dumping tools indicates highly active, manual exploitation.

## 5. Containment & Remediation Actions
1. **Host Isolation:** Issued host isolation request via active-response script to sever all outbound network connections on the Windows 10 workstation.
2. **Forensic Collection:** Pulled Sysmon and USN Journal logs from the host to determine the initial execution vector (e.g., did it originate from a malicious email attachment or web download).

## 6. Lessons Learned & Recommendations
* Configure Windows AppLocker or Software Restriction Policies (SRP) to block executables from running outside of approved directory structures (like `Program Files`).
