---
name: analyzing-dlp-alerts
description: Analyzes Data Loss Prevention (DLP) alerts from Microsoft Purview, Varonis, or Symtcc DLP to identify potential data exfiltration attempts. Provides step-by-step analysis procedures, IOC extraction, and escalation criteria for data leak incidents.
domain: cybersecurity
subdomain: data-loss-prevention
tags: [DLP, data-leak, exfiltration, Microsoft Purview, Varonis, alerts, incident-response]
version: "1.0"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1041, T1074, T1115]
prerequisites:
  - DLP solution (Microsoft Purview, Varonis DLP, or Symtcc)
  - Access to DLP alert dashboard
  - SIEM correlation capability (optional)
expected_output: JSON format with alert analysis and recommendations
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
| **Symtcc DLP** | DLP Console, Event Logs |
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
- [`playbook-data-leak-response`](../playbook-data-leak-response/SKILL.md) - Full data breach response
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) - Insider threat investigation
- [`analyzing-ueba-alerts-varonis`](../analyzing-ueba-alerts-varonis/SKILL.md) - UEBA correlation
