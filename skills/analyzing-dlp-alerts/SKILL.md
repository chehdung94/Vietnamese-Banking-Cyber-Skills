---
name: analyzing-dlp-alerts
description: "Triage data-loss-prevention alerts from Purview or Varonis into confirmed, allowed, and policy-error cases with a severity. Use when a DLP alert export is supplied or a suspected leak must be classified. Never auto-execute containment."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.0.0
domain: cybersecurity
subdomain: data-loss-prevention
mitre_attack: [T1048, T1074, T1115]
prerequisites: DLP solution (Microsoft Purview, Varonis DLP, or Symtcc) Access to DLP alert dashboard SIEM correlation capability (optional)
expected_output: JSON format with alert analysis and recommendations
tags:
  - [DLP
  - data-leak
  - exfiltration
  - Microsoft
  - Purview
  - Varonis
  - alerts
  - incident-response]
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Analyzing DLP Alerts (Tier 3)

## Overview
This skill guides the analysis of Data Loss Prevention (DLP) alerts to identify potential data exfiltration attempts. It supports the **Data Leak Response** and **Abnormal User Behavior** playbooks by providing detailed technical analysis procedures.

## When to Use
Triggered by:
- **playbook-data-leak-response**: To analyze DLP alerts during data breach investigation
- **playbook-abnormal-user-behavior-response**: To correlate UEBA alerts with DLP violations

## Supported DLP Platforms
| Platform | Analysis Method |
|----------|----------------|
| **Microsoft Purview** | Purview Compliance Portal, PowerShell |
| **Varonis DLP** | Varonis WebGUI, Export Reports |
| **Symantec DLP** | DLP Console, Event Logs |
| **Generic Syslog** | SIEM Query (Splunk, ELK, QRadar) |

---

## 1. Alert Information Collection

### 1.1 Required Information
*Before starting analysis, collect the following from DLP system.*

| Check | Field | Example |
|---|---|---|
| [ ] | **Alert ID** | PURVIEW-2024-001234 |
| [ ] | **Alert Type** | EmailAttachment, FileUpload, Print |
| [ ] | **Triggered Policy** | "Block Sensitive Data External Email" |
| [ ] | **User/Account** | huyhq@domain.com |
| [ ] | **Timestamp** | 2024-01-15 14:30:00 UTC |
| [ ] | **Data Classification** | PII, Financial, Confidential |
| [ ] | **Action Taken** | Blocked, Warning, Allowed |

### 1.2 Export DLP Logs
```powershell
# Microsoft Purview - Export DLP alerts via PowerShell
Connect-IPPSSession -UserPrincipalName "admin@domain.com"
Get-DlpDetailReport -StartDate "2024-01-01" -EndDate "2024-01-31" |
    Export-CSV -Path "dlp_report.csv"

# Varonis - Export from WebGUI
# Go to: DLP > Alerts > Export to CSV

# Generic Syslog - Splunk query
# index=dlp sourcetype=dlp_alert
# | eval severity=case(risk_score>=80, "Critical", risk_score>=60, "High", risk_score>=40, "Medium", true(), "Low")
```

---

## 2. Alert Analysis Procedures

### Step 2.1: Alert Classification
*Classify the alert based on policy match and action.*

| Alert Category | Description | Severity |
|----------------|-------------|----------|
| **True Positive** | Legitimate policy violation, actual data at risk | High |
| **False Positive** | Policy triggered but no real risk (test, authorized) | Low |
| **Intentional** | Authorized business justification exists | Medium |
| **Policy Error** | Wrong policy rule, needs tuning | Medium |

### Step 2.2: Data Identification
*Determine what data was at risk.*

| Data Type | Examples | Risk Level |
|-----------|---------|------------|
| **PII** | Name, ID, DOB, Address | High |
| **Financial** | Account numbers, Credit cards, Transactions | Critical |
| **Healthcare** | Medical records, Insurance | Critical |
| **Credentials** | Passwords, API keys, Tokens | Critical |
| **IP/Trade Secret** | Source code, Business plans | High |
| **Internal** | Memos, HR data, Internal docs | Medium |

### Step 2.3: Route Analysis
*Identify where the data was going.*

| Route | Description | Risk |
|-------|-------------|------|
| **External Email** | Sent to personal/gmail/yahoo | Critical |
| **Personal Cloud** | Uploaded to Google Drive, Dropbox | Critical |
| **USB** | Copied to removable media | High |
| **Print** | Printed sensitive documents | High |
| **Cloud App** | Uploaded to unauthorized SaaS | High |
| **Social Media** | Posted on social platforms | High |

### Step 2.4: Volume Assessment
*Quantify the data exposure.*

| Metric | Analysis |
|--------|----------|
| **Record Count** | Number of data items exposed |
| **File Size** | Total size of data involved |
| **Recipient Count** | Number of external recipients |
| **Repeat Behavior** | Is this first time or pattern? |

---

## 3. IOCs Extraction

### 3.1 Indicators of Concern
*Extract and document:*

```json
{
  "alert_id": "PURVIEW-2024-001234",
  "user": "huyhq@domain.com",
  "timestamp": "2024-01-15T14:30:00Z",
  "data_type": ["PII", "Financial"],
  "record_count": 2500,
  "exfiltration_route": "External Email",
  "recipients": ["personal@gmail.com"],
  "files_involved": [
    "customer_data_2024.xlsx",
    "transaction_summary.csv"
  ],
  "pattern": "FirstOccurrence",
  "risk_score": 85
}
```

### 3.2 User Behavior Pattern
*Check if this is isolated or part of a pattern:*

| Check | Query Method |
|-------|-------------|
| Previous DLP alerts for this user | DLP Console history |
| Login from unusual location | Azure AD sign-in logs |
| Large data downloads | Varonis file activity |
| After-hours activity | SIEM time analysis |

---

## 3B. Multi-Source Timeline Correlation 🆕

> **Khi có ≥2 nguồn log (Device Control + Endpoint Security + Web Gateway):**

### Correlation Procedure

1. Tạo unified timeline từ tất cả nguồn log
2. Sắp xếp theo timestamp tăng dần
3. Đánh dấu các event cách nhau < 1 giờ → potential causal relationship
4. Tạo correlation chain nếu event sequence có logic (VD: DLP block → virus detected → access violation)

### Correlation Rules

| Trigger Event | Follow-up Event | Time Window | Assessment |
|---|---|---|---|
| DLP Block (USB) | Virus Detected (Endpoint) | < 1 giờ | 🔴 CRITICAL: USB mang malware |
| DLP Block (USB) | Access Protection Violation | < 1 giờ | 🔴 CRITICAL: User cố tình bypass control |
| DLP Block (USB) | OUTGOING_HTTP | < 24 giờ | 🟠 HIGH: Potential data staging |
| Web Filter Block | SMTP email to same domain | < 24 giờ | 🟠 HIGH: User switching channel |

### DLP Effectiveness Assessment

| Protection Layer | Events | Blocked | Allowed (Gap) | Assessment |
|---|---|---|---|---|
| Device Control | | | | |
| Email DLP | | | | |
| Web Filter | | | | |
| Endpoint Protection | | | | |

---

## 4. Remediation Actions

### 4.1 Immediate Actions
| Action | Command/Method | Priority |
|--------|----------------|----------|
| Block recipient domain | Purview: Update DLP policy | P1 |
| Recall email | Exchange: `Search-Mailbox -DeleteContent` | P1 |
| Revoke cloud access | Azure AD: Revoke sessions | P1 |
| Suspend user | AD: Disable account (with HR coordination) | P2 |
| Notify DPO | Email/Calendar | P1 (if PII/Financial) |

### 4.2 Containment Commands
```powershell
# Recall sent email (if Exchange Online)
Search-Mailbox -SearchQuery "Subject:'[Original Subject]' AND From:'huyhq@domain.com'" -DeleteContent

# Block external domain in DLP
# Purview > Data loss prevention > Policies > Edit policy > Conditions

# Disable OneDrive sync for user
Set-SPOTenant -DisablePersonalFeatures $true

# Block USB storage (via Group Policy)
# Computer Configuration > Administrative Templates > System > Removable Storage Access
```

---

## 5. Escalation Criteria

*Escalate to playbook-data-leak-response if:*

| Criteria | Threshold |
|----------|-----------|
| **Data Type** | PII, Financial, Healthcare, Credentials |
| **Record Count** | > 100 records |
| **User Count** | Multiple users involved |
| **Pattern** | Repeat offense or coordinated |
| **External** | Data confirmed sent outside organization |

---

## 6. Expected Output

```json
{
  "analysis_id": "DLP-ANALYSIS-20240115-001",
  "alert_summary": {
    "total_alerts_analyzed": 1,
    "true_positives": 1,
    "false_positives": 0
  },
  "findings": {
    "user": "huyhq@domain.com",
    "alert_type": "EmailAttachment",
    "policy_violated": "Block PII External Email",
    "data_exposed": {
      "type": ["PII", "Account Numbers"],
      "record_count": 2500,
      "files": ["customer_list.xlsx"]
    },
    "exfiltration_route": "External Email",
    "recipients": ["personal@gmail.com"],
    "risk_level": "Critical",
    "pattern": "FirstOccurrence"
  },
  "ioc_list": {
    "users": ["huyhq@domain.com"],
    "emails": ["personal@gmail.com"],
    "files": ["customer_list.xlsx"],
    "domains": ["gmail.com"]
  },
  "actions_taken": ["EmailRecalled", "UserNotificationSent"],
  "escalation": {
    "escalate_to": "playbook-data-leak-response",
    "reason": "PII data exfiltration confirmed"
  },
  "next_steps": ["HRConsultation", "DPONotification"]
}
```

---

## Related Playbooks
- [`playbook-data-leak-response`](../../drafts/playbook-data-leak-response/SKILL.md) - Full data breach response
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) - Insider threat investigation
- [`analyzing-ueba-alerts-varonis`](../analyzing-ueba-alerts-varonis/SKILL.md) - UEBA correlation

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
