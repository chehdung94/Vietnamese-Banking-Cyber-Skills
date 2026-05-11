---
name: playbook-pre-departure-insider-review
description: Tier 2 Playbook for pre-departure compliance review of departing employees with access to sensitive data. Includes multi-channel analysis, risk scoring, collusion detection, and DLP effectiveness assessment.
domain: cybersecurity
subdomain: insider-threat
tags: [playbook, tier-2, pre-departure, insider-risk, offboarding, compliance, collusion]
version: "1.0"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1041, T1074, T1114, T1530]
---

# Playbook: Pre-Departure Insider Risk Review (Tier 2) 🆕

## Overview
This playbook guides the pre-departure compliance review of departing employees who have access to sensitive data or systems. It provides a structured approach to detect potential data exfiltration before the employee's last working day.

This playbook was developed based on Case Study PREDEP-20260429-001 (Vietbank – 2 departing department heads in Card Operations/Digital Banking).

## When to Use
- HR notification of employee resignation with access to customer data or sensitive systems
- Compliance/Legal preventive review request for offboarding
- Multiple employees from same department departing simultaneously (collusion risk)
- Pre-departure baseline assessment for risk scoring

## 0. Mandatory Prerequisites

> **⚠️ KHÔNG TIẾN HÀNH PHÂN TÍCH nếu thiếu các thông tin sau. Yêu cầu bổ sung từ request owner.**

| # | Prerequisite | Source | Required For |
|---|---|---|---|
| 1 | User real identity (email, username, employee ID) | HR / IAM | User identification |
| 2 | **Resignation notice date** | HR | Baseline comparison (pre vs post notice) |
| 3 | **Planned last working date** | HR | Risk window assessment |
| 4 | Access profile (AD groups, app roles, data permissions) | IAM | Sensitive data assessment |
| 5 | Performance/disciplinary history | HR | Job Performance Issues indicator |
| 6 | Log schema for each source (column names, data types) | Log Owner | Analysis planning |

## 1. Pre-Analysis Setup

### 1.1 Domain Whitelist

Before analyzing email logs, establish a whitelist of business-justified external domains:

| Category | Examples |
|---|---|
| Banking Partners | bidv.com.vn, sacombank.com, vietcombank.com.vn, napas.com.vn... |
| Known Vendors | microsoft.com, service providers |
| Regulators | sbv.gov.vn, nhnn.gov.vn |

### 1.2 Off-Hours Definition

- Before 07:00 or after 18:00 on weekdays
- Any time on weekends/holidays
- Adjust based on company policy

## 2. Multi-Channel Analysis

### 2.1 Email Analysis (Mandatory)

Reference Tier 3 skill: `analyzing-email-audit-logs`

**Quy trình bắt buộc:**
1. Thiết lập domain whitelist (banking partners, vendors)
2. Phân loại email theo hướng (INBOUND/OUTBOUND) – Section 2B
3. Phát hiện standalone Gmail emails – Section 2D
4. Cross-user correlation nếu ≥2 users – Section 2E
5. Phân tích attachment (size, type, frequency)

### 2.2 Web Gateway Analysis (Mandatory)

Reference Tier 3 skill: `analyzing-dlp-alerts`

**Focus areas:**
- Personal cloud storage: `onedrive.live.com`, `drive.google.com`, `dropbox.com`
- Personal webmail: `mail.google.com`, `mail.yahoo.com`
- Social media uploads
- File sharing sites

### 2.3 Device Control Analysis (Mandatory)

Reference Tier 3 skill: `analyzing-dlp-alerts`

**Focus areas:**
- USB storage devices (Disk Drives)
- External HDD/SSD
- MTP devices (phones)
- Network adapters (potential data transfer)

### 2.4 Endpoint Security Analysis (Mandatory)

**Focus areas:**
- Malware/Virus events (magnitude ≥7)
- Access Protection rule violations
- OUTGOING_HTTP policy violations
- Web policy violations

### 2.5 VPN Analysis (Conditional)

Reference Tier 3 skill: `analyzing-vpn-logs`

**Chỉ phân tích nếu:** Đáp ứng ≥3/5 tiêu chí trong Value Assessment của VPN skill.

## 3. Cross-User Collusion Detection

> **Kích hoạt:** Khi có ≥2 departing employees, đặc biệt nếu cùng phòng ban.

**Quy trình:**
1. Kiểm tra email communication giữa các user
2. Cross-mailbox: Tìm Gmail cá nhân của User A trong recipients của User B
3. Timeline correlation: coordinated off-hours activity
4. Shared resource access patterns

## 4. Risk Assessment

Apply **Risk Indicators Matrix** from `playbook-abnormal-user-behavior-response` §3.1.

### Scoring Calibration (Pre-Departure Context)

| Indicator | Specific Considerations for Pre-Departure |
|---|---|
| Recent HR Event | Score based on resignation context: voluntary? performance-related? |
| Access to Sensitive Data | Department heads = elevated access by default |
| Personal Device Usage | DLP BLOCKED vs ALLOWED – different scoring |
| External Communication | Standalone emails = strongest indicator |

### DLP Effectiveness Assessment

Evaluate each protection layer:
- ✅ Blocked successfully → DLP working
- ❌ Allowed without policy → DLP gap
- ⚠️ Detected but not blocked → partial protection

## 5. Output Templates

### 5.1 Executive Summary
```markdown
## Báo cáo Pre-Departure Review
- User: [Name] | Risk Score: X | Classification: CRITICAL/HIGH/MEDIUM/LOW
- Key Findings: [Top 3]
- DLP Gaps Identified: [List]
- Recommendation: [Action]
```

### 5.2 Investigation Report Structure
1. Executive Summary
2. User Profile
3. Log Scope & Methodology
4. Findings per Source
5. Timeline Synthesis
6. Cross-User Correlation (if applicable)
7. Risk Scoring
8. DLP Effectiveness Assessment
9. Conclusion & Recommendations

## Related Skills
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) – General insider threat investigation
- [`playbook-master-incident-triage`](../playbook-master-incident-triage/SKILL.md) – Tier 1 classification
- [`analyzing-email-audit-logs`](../analyzing-email-audit-logs/SKILL.md) – Tier 3 email analysis
- [`analyzing-dlp-alerts`](../analyzing-dlp-alerts/SKILL.md) – Tier 3 DLP analysis
