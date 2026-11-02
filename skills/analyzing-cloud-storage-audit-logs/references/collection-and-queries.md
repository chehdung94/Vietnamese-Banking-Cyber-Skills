# Thu thap du lieu va cau lenh truy van - analyzing-cloud-storage-audit-logs

Chi dung khi nguoi dung xac nhan quyen truy cap. Khong tu chay lenh thay doi tren cloud tenant.
CloudTrail / Google Cloud Audit / M365 Unified Audit goc UTC - chuan hoa sang gio VN truoc khi
so sanh baseline (theo `../_shared/off-hours-and-baseline.md`).

## Microsoft 365 (OneDrive / SharePoint)

```powershell
# Ket noi
Connect-SPOService -Url https://<tenant>-admin.sharepoint.com

# Dung luong OneDrive cua nguoi dung
Get-SPOUserOneDriveUsage -AccountEmail "[EMAIL]" |
    Select-Object Owner, TotalFileCount, TotalStorageMB

# Audit log SharePoint
Search-UnifiedAuditLog -RecordType SharePointFileOperation `
    -StartDate (Get-Date).AddDays(-30) -EndDate (Get-Date) |
    Where-Object { $_.UserId -eq "[EMAIL]" } |
    Export-CSV -Path "spo_audit.csv"
```

### Microsoft Graph

```powershell
Import-Module Microsoft.Graph.Reports
Get-MgReportSharePointActivityFileCounts -Period D30
Get-MgReportSharePointActivityUserDetail -Period D30
```

## AWS S3

```bash
# CloudTrail cho S3
aws cloudtrail lookup-events \
    --lookup-attributes AttributeKey=EventName,GetObject \
    --start-time <UTC-ISO8601> \
    --end-time <UTC-ISO8601>

# Liet ke doi tuong trong bucket (chi doc)
aws s3api list-objects --bucket <bucket> --query 'Contents[].{Key: Key, Size: Size}'

# Tai mot doi tuong ve (vi du, can xac nhan)
aws s3api get-object --bucket <bucket> --key <key> <local-file>
```

> Khong tai key/secret vao file bao cao. Chi ghi dinh danh access key da che (vi du `AKIA...HIDDEN`).

## Azure Blob

```powershell
# Ket noi
Connect-AzAccount

# Bat log storage neu chua bat (can xac nhan)
Set-AzStorageServiceLoggingProperty -ServiceType Blob `
    -LoggingOperations All `
    -RetentionDays 30

# Truy van log blob trong Log Analytics
Get-AzOperationalInsightsQueryResult -WorkspaceId $workspaceId `
    -Query "StorageBlobLogs | where AccountName == '<account>' | where OperationName == 'PutBlob'"
```

> KHONG truy xuat gia tri secret cua storage account. Chi ghi loai key duoc su dung.

## Chuan hoa du lieu sau khi thu thap

| Truong | Quy tac |
|---|---|
| Timestamp | UTC -> gio VN (cong 7) theo `../_shared/off-hours-and-baseline.md` |
| Nguoi dung / access key | Khop bang ma nhan vien > ten day du > ten goi; trung ten -> `ambiguous`, hoi nguoi dung |
| Ten tep | Giu nguyen duong dan day du de kiem tra phu lieu nhay cam |

## Bao cao du lieu thieu

Neu khong export duoc, hoac thieu truong bat buoc (thoi diem, nguoi dung/key, hanh dong, tep),
ghi `NOT_AVAILABLE` cho truong do, khong chay lenh thay doi, va ghi vao `missing_artifacts`
theo Rules dong 1-2.
