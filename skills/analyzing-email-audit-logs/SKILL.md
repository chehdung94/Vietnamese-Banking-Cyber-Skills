---
name: analyzing-email-audit-logs
description: Analyzes email audit logs from Exchange Online, Microsoft 365, or on-premises Exchange to detect data exfiltration via email, phishing campaigns, and unauthorized email rules. Provides log analysis procedures and exfiltration detection.
domain: cybersecurity
subdomain: email-security
tags: [email, Exchange, M365, audit-logs, exfiltration, BEC, phishing, data-leak]
version: "1.0"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1114, T1566, T1041]
prerequisites:
  - Exchange Online PowerShell or Exchange Admin Center
  - Microsoft 365 E3/E5 license
  - Audit log search enabled
  - Mail gateway logs (optional)
expected_output: JSON format with email analysis and exfiltration indicators
---

# Analyzing Email Audit Logs (Tier 3)

## Overview
This skill guides the analysis of email audit logs to detect data exfiltration via email, phishing campaigns, and unauthorized email rules. It supports the **Data Leak Response** and **Abnormal User Behavior** playbooks by providing email-based evidence.

## When to Use
Triggered by:
- **playbook-data-leak-response**: To identify email as exfiltration route
- **playbook-abnormal-user-behavior-response**: To check email patterns for insider threat
- **playbook-phishing-response**: To analyze email delivery and impact

---

## 1. Log Collection

### 1.1 Collection Sources
| Source | Log Type | Retention |
|--------|----------|-----------|
| **Exchange Online** | MessageTrace, MailboxAudit, AuditLog | 90 days |
| **Exchange On-Premises** | Transport logs, Message Tracking | 30 days (configurable) |
| **Mail Gateway** | SMTP logs, Filter logs | Varies |
| **Egress Monitor** | DLP alerts, Transport Rules | 90 days |

### 1.2 Collection Commands

#### Exchange Online PowerShell
```powershell
# Connect to Exchange Online
Connect-ExchangeOnline

# Message Trace (last 48 hours)
Get-MessageTrace -SenderAddress "huyhq@domain.com" -StartDate (Get-Date).AddDays(-7) |
    Select-Object Received, SenderAddress, RecipientAddress, Subject, Status, IPAddress |
    Export-CSV -Path "message_trace.csv"

# Detailed Message Trace
Get-MessageTraceDetail -MessageTraceId <ID> -RecipientAddress user@domain.com

# Mailbox Audit Logs (requires auditing enabled)
Search-UnifiedAuditLog -UserIds "huyhq@domain.com" -StartDate (Get-Date).AddDays(-30) |
    Export-CSV -Path "audit_logs.csv"
```

#### Mailbox Folder Permissions
```powershell
# Check delegate access (possible mailbox compromise)
Get-MailboxPermission -Identity "huyhq@domain.com" |
    Where-Object { $_.User -notlike "NT AUTHORITY\*" }

# Check inbox rules created
Get-InboxRule -Mailbox "huyhq@domain.com" |
    Select-Object Name, Enabled, ForwardTo, RedirectTo, DeleteMessage
```

---

## 2. Exfiltration Detection Patterns

### 2.1 Large Email Attachment Detection
*Detect users sending large attachments externally.*

| Pattern | Threshold | Risk |
|---------|-----------|------|
| **Single large file** | > 10 MB to external | Medium |
| **Multiple files** | > 5 files in 1 hour | High |
| **Daily total** | > 50 MB sent externally | Medium |
| **Zipped files** | .zip with sensitive names | High |

```powershell
# Find large attachments sent externally
Get-MessageTrace -StartDate (Get-Date).AddDays(-7) |
    Where-Object { $_.Size -gt 10MB -and $_.RecipientAddress -notlike "*@domain.com" } |
    Select-Object Received, SenderAddress, RecipientAddress, Subject, Size
```

### 2.2 External Recipient Pattern
*Detect unusual external communication.*

| Pattern | Normal | Suspicious |
|---------|--------|------------|
| **External volume** | < 10 emails/day | > 50 emails/day |
| **New contacts** | Rare | Frequent |
| **BCC usage** | Occasional | Heavy BCC to hide |
| **Auto-forward** | None | Rule created |

```powershell
# Check for auto-forwarding rules
Get-InboxRule -Mailbox "huyhq@domain.com" |
    Where-Object { $_.ForwardTo -or $_.RedirectTo } |
    Select-Object Name, ForwardTo, RedirectTo

# Check mail forwarding configuration
Get-Mailbox -Identity "huyhq@domain.com" |
    Select-Object DisplayName, ForwardingSmtpAddress, DeliverToMailboxAndForward
```

### 2.3 Suspicious Subject/Content Keywords
*Keyword-based detection.*

| Category | Keywords |
|----------|----------|
| **Financial** | invoice, payment, bank transfer, SWIFT |
| **PII** | salary, password, SSN, passport |
| **Sensitive** | confidential, secret, internal, not for sharing |
| **Archive** | backup, archive, old, 2023, 2024 |

```powershell
# Search for sensitive keywords in emails
Get-MessageTrace -StartDate (Get-Date).AddDays(-7) |
    Where-Object { 
        $_.Subject -match "invoice|password|confidential|backup" -and
        $_.RecipientAddress -notlike "*@domain.com"
    } |
    Select-Object Received, SenderAddress, RecipientAddress, Subject
```

---

## 3. Analysis Procedures

### Step 3.1: Baseline Establishment
*Establish normal email behavior for user.*

| Metric | Normal Baseline | User Value |
|--------|-----------------|------------|
| Daily external emails | < 10 | ? |
| Daily attachments (MB) | < 20 | ? |
| Unique external recipients/week | < 20 | ? |
| After-hours emails | Rare | ? |

### Step 3.2: Anomaly Detection
*Compare current behavior against baseline.*

```json
{
  "user": "huyhq@domain.com",
  "analysis_period": "2024-01-08 to 2024-01-15",
  "baseline_comparison": {
    "external_emails": {
      "baseline_daily_avg": 5,
      "current_daily_avg": 47,
      "delta": "+840%",
      "anomaly": true
    },
    "attachment_size": {
      "baseline_daily_avg_mb": 15,
      "current_daily_avg_mb": 280,
      "delta": "+1867%",
      "anomaly": true
    },
    "unique_recipients": {
      "baseline_weekly": 12,
      "current_weekly": 89,
      "new_domains": ["gmail.com", "yahoo.com", "outlook.com"],
      "anomaly": true
    }
  },
  "risk_level": "Critical"
}
```

### Step 3.3: Email Chain Analysis
*Trace conversation threads for context.*

| Check | Purpose |
|-------|---------|
| Reply chain | Is this response to legitimate conversation? |
| CC/BCC changes | Added recipients without justification? |
| Attachment changes | Added files not in original? |
| Subject changes | Removed warning keywords? |

---

## 4. IOCs Extraction

```json
{
  "analysis_id": "EMAIL-AUDIT-20240115-001",
  "user": "huyhq@domain.com",
  "time_range": "2024-01-08 to 2024-01-15",
  "findings": {
    "total_external_emails": 328,
    "total_attachment_mb": 1960,
    "unique_external_recipients": 89,
    "suspicious_patterns": [
      {
        "type": "LargeDataTransfer",
        "date": "2024-01-15",
        "recipients": ["personal@gmail.com"],
        "size_mb": 450,
        "files": ["customer_database.xlsx", "employee_salaries.xlsx"],
        "risk": "Critical"
      },
      {
        "type": "AutoForwardCreated",
        "date": "2024-01-10",
        "forward_to": "external@gmail.com",
        "status": "StillActive",
        "risk": "High"
      }
    ]
  },
  "iocs": {
    "external_emails": ["personal@gmail.com", "backup@yahoo.com"],
    "attachments": ["customer_database.xlsx", "employee_salaries.xlsx"],
    "keywords_found": ["invoice", "password", "backup"]
  }
}
```

---

## 5. Remediation Actions

### 5.1 Immediate Containment
| Action | Command | Priority |
|--------|---------|----------|
| Block external forwarding | `Set-Mailbox -Identity "user" -ForwardingSmtpAddress $null` | P1 |
| Delete inbox rules | `Remove-InboxRule -Identity "rule name" -Mailbox "user"` | P1 |
| Recall sent emails | `Search-Mailbox -DeleteContent` (if Exchange) | P1 |
| Disable account | `Set-AzureADUser -AccountEnabled $false` | P1 |
| Notify external recipients | Contact recipients to delete | P2 |

### 5.2 Email Recall Commands
```powershell
# Search and delete external emails (Exchange Online)
Search-Mailbox -SearchQuery "From:'huyhq@domain.com' AND Subject:'Confidential'" `
    -TargetMailbox "admin@domain.com" -DeleteContent

# Disable auto-forwarding for entire organization
Get-Mailbox -ResultSize Unlimited |
    Set-Mailbox -ForwardingSmtpAddress $null

# Remove external forwarding rules
Get-InboxRule -Mailbox "huyhq@domain.com" |
    Where-Object { $_.ForwardTo -notlike "*@domain.com" } |
    Remove-InboxRule -Confirm:$false
```

---

## 6. Escalation Criteria

*Escalate to playbook-data-leak-response if:*

| Criteria | Threshold |
|----------|-----------|
| **Data Type** | PII, Financial, Credentials |
| **Volume** | > 100 records or > 50 MB |
| **Pattern** | Scheduled/bulk transfer |
| **Intent** | Evidence of willful exfiltration |

---

## 7. Expected Output

```json
{
  "analysis_id": "EMAIL-ANALYSIS-20240115-001",
  "user_analyzed": "huyhq@domain.com",
  "time_range": {
    "start": "2024-01-08T00:00:00Z",
    "end": "2024-01-15T23:59:59Z"
  },
  "summary": {
    "external_emails_sent": 328,
    "total_attachment_size_mb": 1960,
    "unique_external_recipients": 89,
    "suspicious_activities": 3
  },
  "anomalies": [
    {
      "type": "LargeDataTransfer",
      "timestamp": "2024-01-15T14:30:00Z",
      "recipient": "personal@gmail.com",
      "size_mb": 450,
      "subject": "FW: Customer Database Backup",
      "risk": "Critical"
    }
  ],
  "iocs": {
    "recipients": ["personal@gmail.com", "backup@yahoo.com"],
    "attachments": ["customer_database.xlsx"],
    "domains": ["gmail.com", "yahoo.com"]
  },
  "actions_taken": ["ForwardingRuleRemoved", "ExternalAccessBlocked"],
  "escalation": {
    "escalate": true,
    "playbook": "playbook-data-leak-response",
    "reason": "PII exfiltration confirmed via email"
  }
}
```

---

## Related Playbooks
- [`playbook-data-leak-response`](../playbook-data-leak-response/SKILL.md) - Full data breach response
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) - Insider threat investigation
- [`playbook-phishing-response`](../playbook-phishing-response/SKILL.md) - Phishing investigation
