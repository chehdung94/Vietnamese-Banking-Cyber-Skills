# Thu thap du lieu va cau lenh truy van - analyzing-email-audit-logs

Chi dung khi nguoi dung xac nhan quyen truy cap. Khong tu chay lenh thay doi tren tenant.
Luu y: Exchange Online / Entra goc UTC - chuan hoa sang gio VN truoc khi so sanh baseline
(theo `../_shared/off-hours-and-baseline.md`).

## Exchange Online PowerShell

```powershell
# Ket noi
Connect-ExchangeOnline

# Message trace theo nguoi gui
Get-MessageTrace -SenderAddress "[EMAIL]" -StartDate (Get-Date).AddDays(-7) |
    Select-Object Received, SenderAddress, RecipientAddress, Subject, Status, IPAddress |
    Export-CSV -Path "message_trace.csv"

# Message trace chi tiet theo ID
Get-MessageTraceDetail -MessageTraceId <ID> -RecipientAddress "[EMAIL]"

# Mailbox audit log
Search-UnifiedAuditLog -UserIds "[EMAIL]" -StartDate (Get-Date).AddDays(-30) |
    Export-CSV -Path "audit_logs.csv"
```

## Quyen truy cap hop thu (kiem tra tu chuyen tiep va quy tac)

```powershell
# Quyen delegate (dau hieu hop thu bi chiem doat)
Get-MailboxPermission -Identity "[EMAIL]" |
    Where-Object { $_.User -notlike "NT AUTHORITY\*" }

# Inbox rule
Get-InboxRule -Mailbox "[EMAIL]" |
    Select-Object Name, Enabled, ForwardTo, RedirectTo, DeleteMessage

# Rule co chuyen tiep ra ngoai
Get-InboxRule -Mailbox "[EMAIL]" |
    Where-Object { $_.ForwardTo -or $_.RedirectTo } |
    Select-Object Name, ForwardTo, RedirectTo

# Config chuyen tiep cap muc hop thu
Get-Mailbox -Identity "[EMAIL]" |
    Select-Object DisplayName, ForwardingSmtpAddress, DeliverToMailboxAndForward
```

## Truy van theo mau phat hien

```powershell
# Tep dinh kem lon gui ra ngoai (nguong: xem references/thresholds.md)
Get-MessageTrace -StartDate (Get-Date).AddDays(-7) |
    Where-Object { $_.Size -gt 10MB -and $_.RecipientAddress -notlike "*@domain.com" } |
    Select-Object Received, SenderAddress, RecipientAddress, Subject, Size

# Tu khoa nhay cam gui ra ngoai
Get-MessageTrace -StartDate (Get-Date).AddDays(-7) |
    Where-Object {
        $_.Subject -match "invoice|password|confidential|backup" -and
        $_.RecipientAddress -notlike "*@domain.com"
    } |
    Select-Object Received, SenderAddress, RecipientAddress, Subject
```

> So lieu `10MB` va danh sach tu khoa la vi du truy van, nguong phat hien chinh danh sac nam
> trong `references/thresholds.md`. Dieu chinh theo nguong da chot truoc khi dua vao bao cao.

## Thu thap mau, khong chay thay doi

```powershell
# Liet ke rule (chi doc)
Get-InboxRule -Mailbox "[EMAIL]" | Select-Object Name, Enabled, ForwardTo, RedirectTo

# Xac nhan rule da dung trong bao cao
Get-InboxRule -Mailbox "[EMAIL]" | Where-Object { $_.Enabled -eq $true }
```

## Bao cao du lieu thieu

Neu khong co quyen doc hop thu hoac export khong chua du pham vi, ghi `NOT_AVAILABLE` cho truong
do, khong chay lenh thay doi, va ghi vao `missing_artifacts` theo Rules dong 1-2.
