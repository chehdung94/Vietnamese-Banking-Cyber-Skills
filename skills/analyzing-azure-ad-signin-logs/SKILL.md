---
name: analyzing-azure-ad-signin-logs
description: Analyzes Azure Active Directory sign-in logs to detect account compromise, anomalous login patterns, and potential insider threat activities. Provides log analysis procedures, detection rules, and correlation with UEBA alerts.
domain: cybersecurity
subdomain: identity-security
tags: [Azure AD, Identity, signin-logs, account-takeover, insider-threat, Microsoft,UEBA]
version: "1.0"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1078, T1110, T1098]
prerequisites:
  - Azure AD Premium P1/P2 license
  - Access to Azure AD Sign-in logs
  - Microsoft Sentinel or Log Analytics (optional)
  - AzureAD PowerShell module
expected_output: JSON format with login analysis and threat indicators
---

# Analyzing Azure AD Sign-in Logs (Tier 3)

## Overview
This skill guides the analysis of Azure Active Directory (Azure AD/Microsoft Entra ID) sign-in logs to detect account compromise, anomalous login patterns, and potential insider threat activities. It supports the **Abnormal User Behavior** playbook with identity-based evidence.

## When to Use
Triggered by:
- **playbook-abnormal-user-behavior-response**: To analyze login anomalies
- **playbook-phishing-response**: To check for account compromise after phishing
- **playbook-data-leak-response**: To identify initial access vector

## Prerequisites
- Azure AD Premium P1/P2 (for detailed sign-in logs)
- Permissions: Security Reader, Security Operator, or Global Admin
- Tools: Azure Portal, Microsoft Graph API, or AzureAD PowerShell module

---

## 1. Log Collection

### 1.1 Sign-in Log Types
| Log Type | Retention | Details |
|----------|-----------|---------|
| **Interactive Sign-ins** | 30 days (P1), 90 days (P2) | User login with password, MFA |
| **Non-interactive Sign-ins** | 30 days | Service accounts, scripts |
| **Azure AD Federation** | 30 days | SAML, OAuth flows |
| **Risky Sign-ins** | 90 days | Azure AD Identity Protection |

### 1.2 Collection Methods

#### Azure Portal (Manual)
```
Azure Portal > Azure Active Directory > Sign-in logs > Filter by user/date
```

#### PowerShell Collection
```powershell
# Connect to Azure AD
Connect-AzureAD

# Get sign-in logs for specific user
Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq 'huyhq@domain.com'" |
    Select-Object CreatedDateTime, UserPrincipalName, IpAddress, Location, Status |
    Export-CSV -Path "signin_logs_huyhq.csv"

# Get risky sign-ins
Get-AzureADMSriskySignInActivity |
    Select-Object UserPrincipalName, RiskLevel, RiskState, TopRiskLevel |
    Export-CSV -Path "risky_signins.csv"
```

#### Microsoft Graph API
```powershell
# Using Graph SDK
Import-Module Microsoft.Graph.SignIn
Select-MgProfile -Name "beta"

Get-MgAuditSignInLog -Filter "UserPrincipalName eq 'huyhq@domain.com'" |
    Select-Object CreatedDateTime, UserDisplayName, IPAddress, Location, Status
```

---

## 2. Analysis Procedures

### Step 2.1: Failed Sign-in Analysis
*Identify brute force or credential stuffing attempts.*

| Indicator | Threshold | Risk |
|-----------|-----------|------|
| **Failed attempts** | > 5 failures in 10 minutes | High |
| **Multiple accounts** | Failures across > 5 accounts from 1 IP | Critical |
| **Password spray** | Low and slow failures across many users | High |
| **Lockout pattern** | Account locked after failures | Medium |

```powershell
# Query failed sign-ins for brute force
Get-AzureADAuditSignInLogs -Filter "Status/ErrorCode eq 50053" |
    Where-Object { $_.CreatedDateTime -gt (Get-Date).AddHours(-1) } |
    Group-Object IpAddress |
    Where-Object { $_.Count -gt 5 } |
    Select-Object Name, Count
```

### Step 2.2: Impossible Travel Detection
*Identify logins from geographically impossible locations.*

| Check | Calculation |
|-------|-------------|
| **Logic** | Same user, different country, time < travel duration |
| **Tolerance** | Allow 2-4 hours for legitimate travel |
| **Red Flag** | < 2 hours between countries |

```json
{
  "impossible_travel": {
    "user": "huyhq@domain.com",
    "login_1": {
      "location": "Ho Chi Minh City, Vietnam",
      "time": "2024-01-15 08:00:00 UTC",
      "ip": "103.x.x.x"
    },
    "login_2": {
      "location": "London, UK",
      "time": "2024-01-15 10:30:00 UTC",
      "ip": "185.x.x.x"
    },
    "time_difference_hours": 2.5,
    "travel_time_hours": 12,
    "is_impossible": true,
    "risk": "Critical"
  }
}
```

### Step 2.3: Unusual Hours Analysis
*Detect after-hours or weekend access.*

| Pattern | Normal Hours | Suspicious Hours |
|---------|--------------|------------------|
| **Weekday** | 06:00 - 22:00 | 22:00 - 06:00 |
| **Weekend** | No access expected | Any access |
| **Holiday** | No access expected | Any access |

```powershell
# Find unusual hours login
Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq 'huyhq@domain.com'" |
    Where-Object {
        $hour = $_.CreatedDateTime.Hour
        ($hour -lt 6 -or $hour -gt 22) -or
        ($_.CreatedDateTime.DayOfWeek -eq 'Saturday') -or
        ($_.CreatedDateTime.DayOfWeek -eq 'Sunday')
    } |
    Select-Object CreatedDateTime, Location, IpAddress, DeviceDetail
```

### Step 2.4: New Location/Device Detection
*Identify first-time access from new locations.*

| Check | Description |
|-------|-------------|
| **New Location** | Country/city never seen for this user |
| **New Device** | Device ID not previously seen |
| **New ISP** | ASN different from historical |
| **VPN/Proxy** | Known VPN IPs, anonymizers |

```powershell
# Get all unique locations for user (baseline)
$baseline = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq 'huyhq@domain.com'" |
    Select-Object -ExpandProperty Location -Unique

# Recent logins
$recent = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq 'huyhq@domain.com' and CreatedDateTime gt 2024-01-01"

# Find new locations
$recent | Where-Object { $baseline -notcontains $_.Location }
```

### Step 2.5: Risky Sign-in Indicators
*Analyze Azure AD Identity Protection risky sign-ins.*

| Risk Type | Indicators | MITRE |
|-----------|------------|-------|
| **Leaked Credentials** | Password found in breach | T1078 |
| **Anonymous IP** | Tor, VPN without exit node | T1090 |
| **Impossible Travel** | As above | T1078 |
| **Malicious IP** | Known attack source | T1078 |
| **Unfamiliar Location** | New country for user | T1078 |

---

## 3. IOC Extraction

### 3.1 Indicators
```json
{
  "analysis_id": "AZAD-SIGNIN-20240115-001",
  "user": "huyhq@domain.com",
  "time_range": {
    "start": "2024-01-01T00:00:00Z",
    "end": "2024-01-15T23:59:59Z"
  },
  "summary": {
    "total_signins": 145,
    "failed_signins": 12,
    "unique_locations": 5,
    "unique_ips": 8,
    "after_hours_count": 3,
    "risky_signins": 1
  },
  "anomalies": [
    {
      "type": "ImpossibleTravel",
      "details": "Ho Chi Minh City to London in 2.5 hours",
      "timestamp": "2024-01-15T10:30:00Z",
      "risk": "Critical"
    },
    {
      "type": "NewLocation",
      "details": "First login from London, UK",
      "timestamp": "2024-01-15T10:30:00Z",
      "risk": "High"
    }
  ],
  "iocs": {
    "ips": ["185.x.x.x", "103.x.x.x"],
    "locations": ["London, UK", "Ho Chi Minh City, Vietnam"],
    "devices": ["Windows 11, Chrome"]
  },
  "risk_score": 85
}
```

---

## 4. Remediation Actions

### 4.1 For Compromised Account
| Action | Command | Priority |
|--------|---------|----------|
| Force password reset | `Set-AzureADUserPassword -ObjectId <id> -ForceChangePasswordNextSignIn` | P1 |
| Revoke sessions | `Revoke-AzureADUserAllRefreshToken -ObjectId <id>` | P1 |
| Disable account | `Set-AzureADUser -ObjectId <id> -AccountEnabled $false` | P1 |
| Enable MFA | Require MFA registration | P1 |

### 4.2 For Insider Threat (No Compromise)
| Action | Purpose | Priority |
|--------|---------|----------|
| Enhanced monitoring | Watch for additional anomalies | P2 |
| Remove sensitive access | Temporary until investigation complete | P2 |
| HR consultation | Coordinate with HR for interview | P2 |

---

## 5. Correlation with Other Sources

*Cross-reference with:*
- **Varonis UEBA**: Compare login times with file access
- **Email logs**: Check if account used to send suspicious emails
- **SharePoint/OneDrive**: Verify data access after unusual login
- **VPN logs**: Confirm VPN connection before Azure login

---

## 6. Expected Output

```json
{
  "analysis_id": "AZAD-ANALYSIS-20240115-001",
  "user_analyzed": "huyhq@domain.com",
  "time_range_analyzed": "2024-01-01 to 2024-01-15",
  "findings": {
    "compromised_indicators": {
      "impossible_travel": true,
      "unusual_hours": true,
      "new_location": true,
      "failed_brute_force": false
    },
    "overall_risk": "High",
    "most_likely_scenario": "AccountCompromised"
  },
  "timeline": [
    {
      "time": "2024-01-15T08:00:00Z",
      "location": "Ho Chi Minh City, Vietnam",
      "ip": "103.x.x.x",
      "device": "Windows 11",
      "risk": "Low",
      "status": "Normal"
    },
    {
      "time": "2024-01-15T10:30:00Z",
      "location": "London, UK",
      "ip": "185.x.x.x",
      "device": "Unknown",
      "risk": "Critical",
      "status": "Anomalous"
    }
  ],
  "actions_taken": ["SessionRevoked", "MFAEnabled"],
  "recommendation": "ForcePasswordReset",
  "escalation": {
    "escalate": true,
    "playbook": "playbook-abnormal-user-behavior-response"
  }
}
```

---

## Related Playbooks
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) - Full behavioral investigation
- [`playbook-phishing-response`](../playbook-phishing-response/SKILL.md) - Post-phishing account check
- [`playbook-data-leak-response`](../playbook-data-leak-response/SKILL.md) - Initial access investigation
