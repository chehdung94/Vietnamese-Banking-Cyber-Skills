---
name: analyzing-cloud-storage-audit-logs
description: Analyzes cloud storage audit logs from Microsoft OneDrive, SharePoint, Azure Blob, AWS S3, and Google Drive to detect data exfiltration via cloud storage. Provides log analysis procedures and exfiltration detection for SaaS data leaks.
domain: cybersecurity
subdomain: cloud-security
tags: [cloud, OneDrive, SharePoint, Azure Blob, AWS S3, exfiltration, DLP, CASB]
version: "1.0"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1041, T1074, T1567]
prerequisites:
  - Cloud storage admin access or CASB solution
  - Microsoft 365, AWS, or GCP audit logs enabled
  - SharePoint Online PowerShell or Graph API (for M365)
  - CloudTrail (AWS), Cloud Audit Logs (GCP)
expected_output: JSON format with cloud activity analysis and exfiltration indicators
---

# Analyzing Cloud Storage Audit Logs (Tier 3)

## Overview
This skill guides the analysis of cloud storage audit logs to detect data exfiltration via cloud storage services. It supports the **Data Leak Response** and **Abnormal User Behavior** playbooks by providing cloud-based evidence.

## When to Use
Triggered by:
- **playbook-data-leak-response**: To identify cloud storage as exfiltration route
- **playbook-abnormal-user-behavior-response**: To check cloud usage patterns for insider threat

## Supported Platforms
| Platform | Log Source | Analysis Method |
|----------|------------|-----------------|
| **Microsoft OneDrive/SharePoint** | M365 Compliance Center, Purview | Graph API, PowerShell |
| **Microsoft Azure Blob** | Azure Storage Analytics, Log Analytics | Azure Portal, PowerShell |
| **AWS S3** | CloudTrail, S3 Access Logs | AWS Console, CLI |
| **Google Drive** | Google Workspace Admin, Cloud Audit | Admin Console, API |
| **Dropbox/Box** | CASB, Native Audit Logs | CASB Dashboard |

---

## 1. Log Collection

### 1.1 Microsoft 365 (OneDrive/SharePoint)

#### PowerShell Collection
```powershell
# Connect to SharePoint Online
Connect-SPOService -Url https://domain-admin.sharepoint.com

# Get OneDrive usage and access
Get-SPOUserOneDriveUsage -AccountEmail "huyhq@domain.com" |
    Select-Object Owner, TotalFileCount, TotalStorageMB

# Get SharePoint audit logs (requires audit enabled)
Search-UnifiedAuditLog -RecordType SharePointFileOperation `
    -StartDate (Get-Date).AddDays(-30) -EndDate (Get-Date) |
    Where-Object { $_.UserId -eq "huyhq@domain.com" } |
    Export-CSV -Path "spo_audit.csv"
```

#### Graph API
```powershell
# Using Microsoft Graph
Import-Module Microsoft.Graph.Reports

# Get file activities
Get-MgReportSharePointActivityFileCounts -Period D30
Get-MgReportSharePointActivityUserDetail -Period D30
```

### 1.2 AWS S3

#### AWS CLI
```bash
# Get CloudTrail events for S3
aws cloudtrail lookup-events \
    --lookup-attributes AttributeKey=AccessKeyId,AttributeValue=AKIAIOSFODNN7EXAMPLE \
    --start-time 2024-01-01T00:00:00Z \
    --end-time 2024-01-15T23:59:59Z

# Get S3 access logs
aws s3api get-object --bucket my-bucket --key file.txt file.txt

# List bucket objects with sizes
aws s3api list-objects --bucket my-bucket --query 'Contents[].{Key: Key, Size: Size}'
```

### 1.3 Azure Blob

#### Azure CLI / PowerShell
```powershell
# Connect to Azure
Connect-AzAccount

# Get storage account keys
Get-AzStorageAccountKey -ResourceGroupName "MyResourceGroup" -Name "mystorageaccount"

# Enable storage analytics (if not enabled)
Set-AzStorageServiceLoggingProperty -ServiceType Blob `
    -LoggingOperations All `
    -RetentionDays 30

# Query blob logs in Log Analytics
Get-AzOperationalInsightsQueryResult -WorkspaceId $workspaceId `
    -Query "StorageBlobLogs | where AccountName == 'mystorageaccount' | where OperationName == 'PutBlob'"
```

---

## 2. Exfiltration Detection Patterns

### 2.1 Large Download Detection
*Detect bulk downloads from cloud storage.*

| Pattern | Threshold | Risk |
|---------|-----------|------|
| **File count** | > 100 files in 1 hour | High |
| **Data volume** | > 1 GB download in 24h | Critical |
| **Sensitive types** | Download of .pst, .bak, .csv | High |
| **Shared links** | > 10 external sharing events | High |

### 2.2 External Sharing Detection
*Detect unauthorized external sharing.*

| Pattern | Normal | Suspicious |
|---------|--------|------------|
| **Sharing events** | Rare | Daily > 5 |
| **External domains** | None | Multiple personal |
| **Link type** | Organization-only | Anyone with link |
| **Permission level** | Read only | Edit/Full control |

```powershell
# Check external sharing in SharePoint
Get-SPOSite -Limit All | Where-Object { $_.SharingCapability -ne "Disabled" } |
    Select-Object URL, SharingCapability, StorageUsageTB
```

### 2.3 Access from Unusual Locations
*Detect cloud access from unusual places.*

| Indicator | Normal | Suspicious |
|-----------|--------|------------|
| **IP range** | Office/VPN | Personal ISP, VPN |
| **Country** | Vietnam | New country |
| **Device** | Managed | Personal device |
| **Time** | Business hours | After hours |

---

## 3. Analysis Procedures

### Step 3.1: User Activity Summary
*Get overview of user's cloud activity.*

```json
{
  "user": "huyhq@domain.com",
  "analysis_period": "2024-01-08 to 2024-01-15",
  "onedrive_summary": {
    "total_files_accessed": 3420,
    "total_download_mb": 4500,
    "total_upload_mb": 150,
    "external_shares": 23,
    "sharing_breakdown": {
      "internal": 15,
      "external": 8
    }
  },
  "sharepoint_summary": {
    "sites_accessed": 12,
    "files_accessed": 890,
    "files_modified": 45,
    "sensitive_files_accessed": ["customer_list.xlsx", "salary_data.csv"]
  }
}
```

### Step 3.2: Pattern Analysis
*Identify exfiltration patterns.*

| Pattern | Detection Method | Exfiltration Type |
|---------|-----------------|------------------|
| **Zip and download** | Bulk download after file creation | Scheduled exfil |
| **Personal cloud sync** | Uploads to personal storage | Auto-exfil |
| **Sharing link abuse** | Mass sharing to external | Manual exfil |
| **API extraction** | Programmatic bulk access | Scripted exfil |

### Step 3.3: Data Classification Check
*Identify sensitive data accessed.*

| Data Type | File Patterns | Risk |
|-----------|---------------|------|
| **PII** | *customer*, *employee*, *personal* | High |
| **Financial** | *invoice*, *payment*, *transaction* | Critical |
| **Credentials** | *password*, *credential*, *secret* | Critical |
| **HR** | *salary*, *performance*, *contract* | High |

---

## 4. IOCs Extraction

```json
{
  "analysis_id": "CLOUD-AUDIT-20240115-001",
  "user": "huyhq@domain.com",
  "time_range": "2024-01-08 to 2024-01-15",
  "findings": {
    "upload_activities": [
      {
        "timestamp": "2024-01-15T16:30:00Z",
        "destination": "personal OneDrive (personal@gmail.com)",
        "files": ["customer_database_10k.xlsx", "employee_records.csv"],
        "size_mb": 850,
        "method": "Sync to personal account",
        "risk": "Critical"
      }
    ],
    "download_activities": [
      {
        "timestamp": "2024-01-15T14:00:00Z",
        "files": ["Q4_financial_report.xlsx", "customer_pricing.xlsx"],
        "size_mb": 250,
        "risk": "High"
      }
    ],
    "external_shares": [
      {
        "timestamp": "2024-01-12T10:00:00Z",
        "file": "Strategic_Plan_2024.pptx",
        "link_type": "Anyone with link",
        "recipient": "external@gmail.com",
        "risk": "High"
      }
    ]
  },
  "iocs": {
    "personal_accounts": ["personal@gmail.com", "backup_drive@outlook.com"],
    "files_exfiltrated": ["customer_database_10k.xlsx", "employee_records.csv"],
    "total_size_mb": 850
  },
  "risk_level": "Critical"
}
```

---

## 5. Remediation Actions

### 5.1 Microsoft 365
| Action | Command | Priority |
|--------|---------|----------|
| Revoke user sessions | `Revoke-AzureADUserAllRefreshToken` | P1 |
| Disable OneDrive sync | Set-SPOTenant -DisablePersonalFeatures | P1 |
| Remove external shares | `Remove-SPOUser -Site https://domain.sharepoint.com` | P1 |
| Block personal cloud | CASB policy: Block non-approved cloud storage | P1 |

```powershell
# Remove all external sharing links for user
Get-SPOSite -Limit All | ForEach-Object {
    Get-SPOUser -Site $_.Url -Limit All |
        Where-Object { $_.Userexternalidentity -and $_.LoginName -like "*huyhq*" } |
        Remove-SPOUser -Site $_.Url -LoginName $_.LoginName -Confirm:$false
}

# Disable OneDrive for user (if licensed)
Set-SPOSite -Identity "https://domain-my.sharepoint.com/personal/huyhq_domain_com" `
    -StorageQuota 0 -LockState NoAccess
```

### 5.2 AWS S3
```bash
# Remove bucket policy allowing public access
aws s3api delete-bucket-policy --bucket my-bucket

# Enable S3 Block Public Access
aws s3control put-public-access-block \
    --account-id 123456789012 \
    --public-access-block-configuration "BlockPublicAcls=true,IgnorePublicAcls=true"

# Identify and revoke access keys
aws iam delete-access-key --access-key-id AKIAIOSFODNN7EXAMPLE --user-name username
```

### 5.3 Azure Blob
```powershell
# Revoke storage account keys
New-AzStorageAccountKey -ResourceGroupName "MyResourceGroup" `
    -Name "mystorageaccount" -KeyName key1 -Force

# Disable anonymous access
Set-AzStorageAccount -ResourceGroupName "MyResourceGroup" `
    -Name "mystorageaccount" -AllowBlobPublicAccess $false

# Regenerate SAS tokens
New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Object `
    -Permission rwdl -ExpiryTime (Get-Date).AddDays(-1)
```

---

## 6. Escalation Criteria

*Escalate to playbook-data-leak-response if:*

| Criteria | Threshold |
|----------|-----------|
| **Data Type** | PII, Financial, Credentials |
| **Volume** | > 100 records or > 500 MB |
| **Destination** | Personal cloud storage confirmed |
| **Intent** | Evidence of willful exfiltration |

---

## 7. Expected Output

```json
{
  "analysis_id": "CLOUD-ANALYSIS-20240115-001",
  "user_analyzed": "huyhq@domain.com",
  "time_range": {
    "start": "2024-01-08T00:00:00Z",
    "end": "2024-01-15T23:59:59Z"
  },
  "summary": {
    "files_accessed": 3420,
    "files_uploaded": 25,
    "files_downloaded": 890,
    "external_shares": 23,
    "sensitive_access": 5,
    "total_size_mb": 4500
  },
  "anomalies": [
    {
      "type": "PersonalCloudUpload",
      "timestamp": "2024-01-15T16:30:00Z",
      "destination": "personal@gmail.com OneDrive",
      "files": ["customer_database_10k.xlsx", "employee_records.csv"],
      "size_mb": 850,
      "risk": "Critical"
    }
  ],
  "iocs": {
    "personal_accounts": ["personal@gmail.com"],
    "files": ["customer_database_10k.xlsx", "employee_records.csv"],
    "domains": ["gmail.com"]
  },
  "actions_taken": ["ExternalSharesRemoved", "OneDriveAccessRevoked"],
  "escalation": {
    "escalate": true,
    "playbook": "playbook-data-leak-response",
    "reason": "PII exfiltration via personal cloud storage"
  }
}
```

---

## Related Playbooks
- [`playbook-data-leak-response`](../playbook-data-leak-response/SKILL.md) - Full data breach response
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) - Insider threat investigation
- [`analyzing-dlp-alerts`](../analyzing-dlp-alerts/SKILL.md) - DLP correlation
