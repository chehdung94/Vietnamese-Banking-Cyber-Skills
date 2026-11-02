# Thu thap du lieu va cau lenh truy van - analyzing-dlp-alerts

Chi dung khi nguoi dung xac nhan quyen truy cap. Khong tu chay lenh thay doi tren tenant.
Purview / Entra goc UTC - chuan hoa sang gio VN truoc khi so sanh baseline
(theo `../_shared/off-hours-and-baseline.md`).

## Microsoft Purview (PowerShell)

```powershell
# Ket noi
Connect-IPPSSession -UserPrincipalName "[EMAIL]"

# Export canh bao DLP
Get-DlpDetailReport -StartDate "YYYY-MM-DD" -EndDate "YYYY-MM-DD" |
    Export-CSV -Path "dlp_report.csv"
```

## Varonis DLP

```powershell
# Xuat bao cao tu WebGUI: DLP > Alerts > Export to CSV
```

## Generic syslog / SIEM

```spl
index=dlp sourcetype=dlp_alert
| eval severity=case(risk_score>=80, "Critical", risk_score>=60, "High", risk_score>=40, "Medium", true(), "Low")
```

> Nguong diem rui ro trong cau lenh tren la vi du truy van. Nguong phat hien chinh danh sac nam
> trong `references/thresholds.md`. Neu nguon canh bao khong co diem rui ro, bo qua truong nay
> va ghi `NOT_AVAILABLE`.

## Truy van phu tro (chi doc)

```powershell
# Canh bao truoc do cua cung nguoi dung
# (thuc hien trong DLP Console cua nen tang)

# Dang nhap gan nhat (Entra ID) - chi doc
Get-AzureADUser -ObjectId "<objectId>" |
    Select-Object DisplayName, AccountEnabled
```

## Bao cao du lieu thieu

Neu khong export duoc, hoac thieu truong bat buoc (Alert ID, policy, nguoi dung, thoi diem), ghi
`NOT_AVAILABLE` cho truong do, khong chay lenh thay doi, va ghi vao `missing_artifacts`
theo Rules dong 1-2.
