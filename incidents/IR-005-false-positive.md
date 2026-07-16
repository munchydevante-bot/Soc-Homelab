# INCIDENT REPORT: INC-2026-005
**Title:** False Positive Alert - Scheduled Administrator Script Triggers Ransomware Alarm
**Date/Time:** July 14, 2026 | 14:15:00 UTC
**Severity:** LOW (Tuned from CRITICAL - Level 13)
**Status:** CLOSED (FALSE POSITIVE)

---

## 1. Executive Summary
A critical severity alert was triggered indicating a "potential ransomware action in progress" on the Ubuntu file share server after over 100 files were modified and renamed within 10 seconds. Upon manual verification, it was determined to be an authorized, automated log compression script run by the network administrator. The alert has been closed as a False Positive and the SIEM rule tuned to accommodate authorized scripts.

## 2. Incident Details
* **Host:** `ubuntu-server` (IP: `10.10.10.100`)
* **Alert Triggered:** Rule 100201 - "Massive file system modification - Ransomware Behavior"
* **Initiating User:** `root` (via local cron job)

## 3. Incident Timeline (UTC)
* **14:15:00.010** - Weekly scheduled cleanup cronjob runs `compress_logs.sh`.
* **14:15:03.450** - Wazuh File Integrity Monitoring (FIM) records 142 file changes in `/var/log/custom-apps/`.
* **14:15:04.100** - Rule 100201 fires due to high frequency.
* **14:20:00.000** - Analyst verified the script owner, compared the script's cryptographic hash, and confirmed it matches authorized system maintenance tools.

## 4. Resolution & Rule Tuning Actions
1. **De-escalation:** Marked alert status as "False Positive" in the SIEM dashboard.
2. **Rule Modification:** Adjusted Rule 100201 to ignore directory `/var/log/custom-apps/` if the modifying user is `root` and the parent process is `/usr/local/bin/compress_logs.sh`.

## 5. Lessons Learned
* When writing broad heuristic rules (like monitoring file modification frequencies), verify all administrative cron tasks to prevent alert fatigue for operations analysts.
