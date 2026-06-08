---
name: playbook-daily-watchlist-monitoring
description: Tier 2 Playbook for daily monitoring and analysis of employees on insider threat watchlist. Provides structured procedures for processing Varonis logs (AD, Email, Fileserver), correlating activities across data sources, generating alerts, and escalating high-risk cases. Designed for Vietnamese banking environments with compliance requirements.
domain: cybersecurity
subdomain: insider-threat
tags: [playbook, tier-2, watchlist, daily-monitoring, insider-risk, Varonis, UEBA, correlation]
version: "1.0"
author: cybersecurity-expert-mode
license: Apache-2.0
mitre_attack: [T1074, T1114, T1041, T1074.001, T1530]
nist_csf: [DE.CM-3, DE.AE-3, PR.AC-4, RS.RP-1]
---

# Playbook: Daily Watchlist Monitoring (Tier 2)

## Overview

This playbook provides a structured procedure for daily monitoring of employees on the insider threat watchlist. It covers log collection, data cleaning, multi-source correlation, alert generation, and escalation procedures. The playbook is designed for security analysts who process Varonis logs (Active Directory, Email, Fileserver) on a daily basis.

This playbook was developed based on real-world analysis of 334 departing employees at a Vietnamese commercial bank, with cross-correlation of AD authentication logs (36,964 events), email logs (3,675 events), and fileserver logs (12,077 events).

## When to Use

- Daily morning review of watchlist employee activities
- Processing Varonis logs exported from AD, Email, and Fileserver sources
- Correlating activities across multiple data sources for the same user
- Generating daily summary reports for SOC leadership
- Identifying users requiring escalation to Phase 2 investigation

## 0. Mandatory Prerequisites

> **⚠️ KHÔNG TIẾN HÀNH PHÂN TÍCH nếu thiếu các thông tin sau. Yêu cầu bổ sung từ request owner.**

| # | Prerequisite | Source | Required For |
|---|---|---|---|
| 1 | Watchlist file (Excel/CSV) with employee names and IDs | HR System | User identification |
| 2 | Varonis AD logs (CSV format) | Varonis DatAdvantage | Authentication analysis |
| 3 | Varonis Email logs (CSV format) | Varonis Email Analytics | Email activity analysis |
| 4 | Varonis Fileserver logs (CSV format) | Varonis DatAdvantage | File activity analysis |
| 5 | Working hours definition | Company policy | Off-hours detection |
| 6 | External domain whitelist | IT Security | Email risk assessment |

## 1. Log Collection and Preprocessing

### 1.1 Log Sources

| Source | Varonis Module | Key Columns | Typical Volume |
|--------|---------------|-------------|----------------|
| **AD Logs** | Active Directory | Account name, Event time, Event type, Event description | 30,000-50,000/day |
| **Email Logs** | Exchange Online | Account name, Mail recipient, Event type, Attachment name | 3,000-5,000/day |
| **Fileserver Logs** | Windows File Server | Account name, Path, Object name, Event operation | 10,000-20,000/day |

### 1.2 Log Cleaning Procedure

**Step 1: Filter by Watchlist**
```
INPUT: Raw log file + Watchlist
PROCESS: Match Account name against watchlist names
OUTPUT: Filtered log containing only watchlist users
```

**Step 2: Normalize Time**
```
INPUT: Event time (UTC)
PROCESS: Convert to local timezone (Asia/Ho_Chi_Minh, UTC+7)
OUTPUT: Event time local, date, hour, day_of_week
```

**Step 3: Classify Off-Hours**
```
WORKING HOURS: 07:45 - 17:30 (Monday - Friday)
OFF-HOURS:
  - Before 07:45 or after 17:30 on weekdays
  - All day on Saturday, Sunday, holidays
  - Lunch break (12:00-13:00) is NOT off-hours
```

**Step 4: Remove Noise**
```
AD LOGS - REMOVE:
  - System accounts (SERVICE$, COMPUTER$, $)
  - TGT authentication events (normal Kerberos ticket renewal - occurs every 10 hours)
  - Events from service accounts (accounts ending with _svc, _service)
  - Duplicate events (same user, same action, same second)
  - Events from decommissioned systems

EMAIL LOGS - REMOVE:
  - Received emails (focus on SENT emails for insider threat)
  - Internal emails (sender and recipient both @company domain)
  - Auto-generated emails (noreply@, system@, automated@)
  - Calendar invites and meeting responses
  - Duplicate events (same message ID)

FILESERVER LOGS - REMOVE:
  - File opened/read events for normal work files (.lnk, .url, .tmp)
  - Events from system folders (Windows, Program Files, AppData)
  - Events from recycle bin ($RECYCLE.BIN)
  - Shortcut file access (.lnk files)
  - Duplicate events (same user, same file, same second)
  - Events from backup/sync processes (OneDrive sync, SharePoint sync)
```

### 1.3 Name Matching Algorithm

```
WATCHLIST FORMAT: "Nguyen Van A" (Vietnamese order)
AD LOG FORMAT: "A, Nguyen Van" (Western order)

MATCHING STEPS:
1. Remove diacritics from both names
2. Extract given name (last word in Vietnamese order)
3. Match given name first (fast lookup)
4. If no match, reconstruct full name and compare
5. Return match if given name OR full name matches
```

## 2. Multi-Source Analysis

### 2.1 AD Authentication Analysis

**Metrics to Calculate:**
- Total authentication events per user per day
- Off-hours authentication count
- Unique active days
- Authentication pattern (NTLM, Kerberos, TGT, TGS)

**Alert Thresholds:**
| Alert Type | Threshold | Severity |
|------------|-----------|----------|
| HIGH_LOGIN_COUNT | > 50 logins/day | MEDIUM |
| HIGH_LOGIN_COUNT | > 100 logins/day | HIGH |
| OFF_HOURS_AUTH | > 5 off-hours events/week | MEDIUM |
| ACCESS_REQUEST | Any TGS request to sensitive resource | CRITICAL |

### 2.2 Email Activity Analysis

**Metrics to Calculate:**
- Total email events (sent/received/deleted) per user
- External email count (recipient not @company domain)
- Attachment total size per user
- Off-hours email count

**Alert Thresholds:**
| Alert Type | Threshold | Severity |
|------------|-----------|----------|
| EXTERNAL_EMAIL_WITH_ATTACHMENT | Any to personal email (gmail, yahoo) | HIGH |
| EXTERNAL_EMAIL_WITH_ATTACHMENT | Attachment > 10MB | HIGH |
| MASS_EMAIL_DELETE | > 20 emails deleted/day | HIGH |
| OFF_HOURS_EMAIL | > 5 emails sent off-hours | MEDIUM |
| HIGH_EXTERNAL_EMAIL | > 10 external emails/day | HIGH |

### 2.3 Fileserver Activity Analysis

**Metrics to Calculate:**
- Total file events (read/create/update/delete) per user
- Off-hours file activity count
- Sensitive file access count
- Folder creation/deletion count

**Alert Thresholds:**
| Alert Type | Threshold | Severity |
|------------|-----------|----------|
| MASS_FILE_ACCESS | > 100 files/hour | HIGH |
| MASS_FILE_DELETE | > 20 files/day | CRITICAL |
| SENSITIVE_FILE_ACCESS | > 5 sensitive files | HIGH |
| OFF_HOURS_FILE_ACCESS | > 10 off-hours events | MEDIUM |
| FOLDER_DELETE | Any folder deletion | HIGH |

### 2.4 Sensitive File Keywords

```
SENSITIVE_KEYWORDS = [
  'RRGL',           # Risk management
  'KHACH HANG',     # Customer
  'PII',            # Personal identifiable information
  'SALARY', 'LUONG', # Salary data
  'THONG TIN',      # Information
  'CREDIT', 'TIN DUNG', # Credit data
  'ACCOUNT', 'TAI KHOAN' # Account data
]
```

## 3. Cross-Source Correlation

### 3.1 User Activity Profile

For each watchlist user, create a consolidated profile:

| Metric | AD | Email | Fileserver | Total |
|--------|-----|-------|------------|-------|
| Total Events | X | X | X | SUM |
| Off-Hours Events | X | X | X | SUM |
| Active Days | X | X | X | UNION |
| Alert Count | X | X | X | SUM |

### 3.2 Correlation Rules

| Rule | Condition | Severity | Action |
|------|-----------|----------|--------|
| **Multi-Source Off-Hours** | Off-hours activity in ≥ 2 sources | HIGH | Escalate |
| **Delete Pattern** | Delete emails AND delete files same day | CRITICAL | Escalate immediately |
| **Mass Access + External** | Mass file access + external email same day | CRITICAL | Escalate immediately |
| **Sensitive + Off-Hours** | Sensitive file access during off-hours | HIGH | Investigate |
| **Folder Delete + Email Delete** | Delete folders + delete emails same day | CRITICAL | Escalate immediately |

### 3.3 Risk Classification

```
RISK LEVELS:
├── 🔴 CRITICAL: 
│   ├── Delete pattern detected (emails + files)
│   ├── Mass access + external email
│   ├── Folder deletion + email deletion
│   └── Action: Escalate to Phase 2 immediately
│
├── 🟠 HIGH:
│   ├── Multi-source off-hours activity
│   ├── Sensitive file access during off-hours
│   ├── Mass email/file delete (single source)
│   └── Action: Enhanced monitoring, investigate within 24h
│
├── 🟡 MEDIUM:
│   ├── Single source off-hours activity
│   ├── High login count
│   └── Action: Continue monitoring, document in report
│
└── 🟢 LOW:
    ├── Normal activity pattern
    └── Action: No action required
```

## 4. Daily Report Generation

### 4.1 Report Structure

```markdown
# Daily Watchlist Monitoring Report
Date: YYYY-MM-DD
Analyst: [Name]

## Executive Summary
- Total users on watchlist: X
- Users with activity today: X
- Critical alerts: X
- High alerts: X
- Users requiring escalation: X

## Critical Findings
[List users with CRITICAL risk level]

## High-Risk Users
[List users with HIGH risk level]

## Activity Summary by Source
| Source | Total Events | Filtered Events | Alerts |
|--------|-------------|-----------------|--------|
| AD | X | X | X |
| Email | X | X | X |
| Fileserver | X | X | X |

## Top 10 Users by Activity
| User | AD | Email | File | Total | Risk Level |
|------|-----|-------|------|-------|------------|

## Escalation Recommendations
[List users requiring Phase 2 investigation with rationale]
```

### 4.2 Output Templates

**JSON Alert Format:**
```json
{
  "alert_id": "WL-YYYYMMDD-XXX",
  "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "user": {
    "name": "Account name",
    "watchlist_reason": "Resignation/Disciplinary/Suspicious"
  },
  "alert_type": "MASS_FILE_DELETE|EXTERNAL_EMAIL|...",
  "severity": "CRITICAL|HIGH|MEDIUM",
  "sources": ["AD", "Email", "Fileserver"],
  "details": {
    "description": "Detailed description",
    "count": 0,
    "threshold": 0
  },
  "correlation": {
    "multi_source": true,
    "related_alerts": []
  },
  "recommended_action": "Escalate|Investigate|Monitor"
}
```

## 5. Escalation Procedures

### 5.1 Phase 2 Triggers

Escalate to Phase 2 (full investigation) when:
- [ ] CRITICAL alert from cross-source correlation
- [ ] ≥ 3 HIGH alerts for same user in 7 days
- [ ] User shows delete pattern (emails + files)
- [ ] User accesses sensitive data during off-hours
- [ ] User sends external emails with large attachments

### 5.2 Phase 2 Data Sources

When escalating, collect additional logs:
| Source | Purpose | Retention |
|--------|---------|-----------|
| SIEM Logs | Network activity, lateral movement | 30 days |
| USB/Device Logs | Data copy via USB | 30 days |
| Endpoint Logs | Process execution, file access | 30 days |
| Email Gateway | Confirmed sent emails | 90 days |
| VPN Logs | Connection location | 30 days |

### 5.3 Communication Plan

| Time | Action | Recipient |
|------|--------|-----------|
| T+0 | Alert detected | SOC Analyst |
| T+15 min | Initial assessment | SOC Lead |
| T+1 hour | CRITICAL alert notification | CISO |
| T+4 hours | Escalation decision | CISO + HR + Legal |
| T+24 hours | Investigation report | CISO |

## 6. Vietnamese Banking Compliance

### 6.1 Regulatory Requirements

| Regulation | Requirement | Application |
|------------|-------------|-------------|
| **Luật An ninh mạng 2018** | Protect information systems | Log retention, access monitoring |
| **Nghị định 13/2023/NĐ-CP** | Personal data protection | Monitor PII access |
| **Nghị định 356/2025/NĐ-CP** | Breach notification within 36 hours | Alert on data exfiltration |
| **NHNN Circulars** | IT security for financial institutions | Monitor financial data access |

### 6.2 Data Classification

```
BANKING DATA SENSITIVITY:
├── PII: Name, ID card, DOB, address
├── Financial: Account numbers, balances, transactions
├── Credit: Credit files, credit scores
└── Contact: Phone numbers, emails

PROTECTION REQUIREMENTS:
├── Log all access to sensitive data
├── Alert on off-hours access to sensitive data
├── Alert on bulk access to customer data
└── Report breach within 36 hours (NĐ 356/2025)
```

## 7. Automation Scripts

### 7.1 Script Inventory

| Script | Purpose | Input | Output |
|--------|---------|-------|--------|
| `Process_AD_Log.py` | Process AD authentication logs | Raw AD CSV | Filtered log, summary, alerts |
| `Process_Email_Log.py` | Process email activity logs | Raw Email CSV | Filtered log, summary, alerts |
| `Process_Fileserver_Log.py` | Process fileserver activity logs | Raw Fileserver CSV | Filtered log, summary, alerts |

### 7.2 Execution Order

```
DAILY PROCESSING:
1. python Process_AD_Log.py
2. python Process_Email_Log.py
3. python Process_Fileserver_Log.py
4. Merge outputs and generate correlation report
5. Review alerts and classify risk levels
6. Generate daily report
7. Escalate if needed
```

## Expected Output

1. **Filtered Logs** - Cleaned logs containing only watchlist users
2. **User Summary** - Per-user activity statistics across all sources
3. **Alerts** - Categorized alerts with severity levels
4. **Daily Report** - Executive summary with escalation recommendations
5. **Correlation Matrix** - Cross-source activity correlation

## Related Skills

- [`playbook-pre-departure-insider-review`](../playbook-pre-departure-insider-review/SKILL.md) - Pre-departure compliance review
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) - General insider threat investigation
- [`analyzing-ueba-alerts-varonis`](../analyzing-ueba-alerts-varonis/SKILL.md) - Detailed Varonis UEBA analysis
- [`analyzing-dlp-alerts`](../analyzing-dlp-alerts/SKILL.md) - DLP alert analysis
- [`analyzing-email-audit-logs`](../analyzing-email-audit-logs/SKILL.md) - Email log analysis
