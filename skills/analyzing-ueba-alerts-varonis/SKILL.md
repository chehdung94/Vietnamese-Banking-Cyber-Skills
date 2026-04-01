---
name: analyzing-ueba-alerts-varonis
description: Analyze and triage User Entity Behavior Analytics (UEBA) alerts from Varonis Data Advantage platform, identifying insider threats and anomalous user behavior in banking environments
domain: cybersecurity
subdomain: threat-detection-ueba
tags: [varonis, ueba, insider-threat, user-behavior, anomaly-detection, banking]
version: "1.0"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1074, T1114, T1539, T1484]
nist_csf: [DE.CM-3, DE.AE-3, PR.AC-4]
---

# Analyzing UEBA Alerts from Varonis

## Overview

Varonis Data Advantage platform monitors user behavior across file systems, email, Teams, OneDrive, and cloud applications. This skill provides a structured procedure for analyzing UEBA alerts, prioritizing high-risk anomalies, and generating actionable investigation reports for banking security operations.

## When to Use

- When investigating specific user alerts from Varonis dashboard
- When triaging a batch of UEBA alerts after export
- When responding to potential insider threat indicators
- When conducting proactive user behavior analysis
- When preparing incident investigation reports for compliance

## Prerequisites

- Access to Varonis Data Advantage platform (or exported alert data)
- Alert data in supported formats: CSV, XLSX, JSON
- Understanding of user's normal baseline behavior
- Knowledge of banking data sensitivity classifications

## Steps

### Step 1 – Collect and Validate Alert Data

**If using Varonis UI:**
- Export alerts with filters: Date range, Severity, Alert type
- Include context columns: User, Asset, Action, Risk score

**If using exported file:**
- Validate file structure and required columns
- Note any data quality issues (missing values, encoding)

**Expected columns:**
```
AlertID, Timestamp, User, UserEmail, AlertType, Severity, 
RiskScore, Department, Asset, Action, SourceIP, Destination,
AdditionalDetails
```

### Step 2 – Categorize Alerts by Type

Group alerts into MITRE ATT&CK tactics:

| Alert Type | MITRE Tactic | Priority |
|------------|--------------|----------|
| Abnormal file access | T1074 (Staging) | High |
| Bulk email download | T1114 (Email Collection) | High |
| Failed login attempts | T1539 (Steal Credentials) | Medium |
| Permission changes | T1484 (Defense Evasion) | Critical |
| Data exfiltration attempt | T1048 (Exfiltration) | Critical |
| Malware indicators | T1566 (Phishing) | Critical |

### Step 3 – Calculate Risk Scoring

For each alert, calculate composite risk score:

```
RiskScore = (SeverityWeight × 0.4) + (BehavioralDeviation × 0.3) + (AssetSensitivity × 0.2) + (HistoricalContext × 0.1)
```

**Severity Weights:**
- Critical: 1.0
- High: 0.75
- Medium: 0.5
- Low: 0.25

**Asset Sensitivity Weights:**
- Confidential (customer data, PII): 1.0
- Restricted (financial records): 0.75
- Internal (general business): 0.5
- Public: 0.1

### Step 4 – Identify High-Risk Users

Flag users meeting any criteria:

- [ ] Risk score ≥ 70
- [ ] Multiple alert types (≥ 3 different alert categories)
- [ ] After-hours activity on sensitive assets
- [ ] Access from unusual location/device
- [ ] Rapid increase in activity volume
- [ ] Access to assets outside normal job function

### Step 5 – Investigate Context

For each high-risk user, gather:

1. **Baseline Profile**
   - Normal working hours
   - Typical asset access patterns
   - Department and role
   - Manager/team context

2. **Timeline Analysis**
   - Sequence of suspicious activities
   - Correlation with events (terminations, policy changes)
   - Duration of anomaly

3. **Business Context**
   - Recent organizational changes
   - Performance issues or disgruntlement indicators
   - Business justification for access

### Step 6 – Generate Investigation Report

Structure findings as:

```json
{
  "investigation_id": "INV-YYYYMMDD-XXX",
  "analyst": "analyst-name",
  "date": "YYYY-MM-DD",
  "alert_summary": {
    "total_alerts": 0,
    "high_risk": 0,
    "medium_risk": 0,
    "low_risk": 0
  },
  "high_risk_users": [
    {
      "user": "username",
      "email": "user@domain.com",
      "department": "Department",
      "risk_score": 0,
      "alert_count": 0,
      "primary_concern": "Description",
      "recommended_action": "Action to take",
      "urgency": "Critical|High|Medium"
    }
  ],
  "timeline_of_events": [
    {
      "timestamp": "YYYY-MM-DD HH:MM:SS",
      "event": "Description",
      "alert_id": "AlertID"
    }
  ],
  "ioc_list": [
    {
      "type": "ip|file|user|asset",
      "value": "indicator",
      "alert_id": "AlertID"
    }
  ],
  "recommended_actions": [
    {
      "action": "Description",
      "owner": "Role/Team",
      "priority": "P1|P2|P3",
      "due_date": "YYYY-MM-DD"
    }
  ]
}
```

## Expected Output

1. **Executive Summary** – High-priority findings for management
2. **Alert Triage Table** – All alerts sorted by risk score
3. **User Risk Ranking** – Top 10 high-risk users
4. **IOC List** – Indicators for blocking/monitoring
5. **Investigation Timeline** – Chronological event map
6. **Recommended Actions** – Prioritized remediation steps

## Vietnamese Banking Context

When analyzing alerts in Vietnamese banking environment:

- **Data Classification**: Customer PII, Financial records, Account data
- **Regulatory Reference**: 
  - Nghị định 13/2023/NĐ-CP (PDP) for personal data
  - NHNN Circular on IT Security for financial data
- **Common Alert Scenarios**:
  - Unusual access to customer database
  - Bulk export of account information
  - Access to systems outside job function
  - After-hours activity on core banking systems

## Validation Checklist

- [ ] All alerts categorized by MITRE tactic
- [ ] Risk scores calculated for all high-risk users
- [ ] Investigation timeline documented
- [ ] IOCs extracted and formatted
- [ ] Recommended actions have owners and due dates
- [ ] Report reviewed for accuracy

## References

- Varonis Data Advantage Platform Documentation
- MITRE ATT&CK Framework v14
- NIST Cybersecurity Framework 2.0
- Vietnamese banking security regulations (NHNN)
