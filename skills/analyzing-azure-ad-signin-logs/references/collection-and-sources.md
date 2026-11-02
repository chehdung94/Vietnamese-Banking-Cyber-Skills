# Thu thập và xác thực dữ liệu - analyzing-azure-ad-signin-logs

## Loại log Entra ID

| Loại log | Giữ lại (mặc định) | Chi tiết |
|---|---|---|
| Interactive Sign-ins | 30 ngày (P1), 90 ngày (P2) | Đăng nhập tương tác bằng mật khẩu, MFA |
| Non-interactive Sign-ins | 30 ngày | Service account, script, tự động hoá |
| Azure AD Federation | 30 ngày | Luồng SAML, OAuth |
| Risky Sign-ins | 90 ngày | Microsoft Entra ID Protection |

> Giữ lại thực tế có thể khác theo hợp đồng thuê bao và cấu hình tenant. Ghi rõ giá trị thực tế
> quan sát được trong báo cáo thay vì giả định.

## Cấu trúc trường tối thiểu

| Trường | Bắt buộc | Ghi chú |
|---|---|---|
| Timestamp (CreatedDateTime) | Co | Entra ID gốc UTC, phải quy đổi theo `../../_shared/off-hours-and-baseline.md` |
| UserPrincipalName / UserDisplayName | Co | Khớp tên theo quy tắc so sánh tên trong file shared |
| IPAddress | Co | Trục nhóm cho brute force, spray |
| Location (country/region, city) | Co | Trục nhóm cho impossible travel |
| Status / ErrorCode | Co | Phân biệt auth failure với app failure |
| DeviceDetail (device id, OS, browser) | Khuyen nghi | Trục nhóm cho new device |
| AppDisplayName / ClientAppUsed | Khuyen nghi | Phân biệt người dùng với dịch vụ đồng bộ |
| RiskLevel / RiskState | Tùy chọn | Chỉ có ở risky sign-in |
| CorrelationId | Khuyen nghi | Ghép sự kiện giữa các nguồn |

## Cách export

### Azure Portal (thủ công)

```text
Azure Portal > Microsoft Entra ID > Sign-in logs > bộ lọc theo user / khoảng thời gian > Export
```

### AzureAD PowerShell

```powershell
# Đăng nhập
Connect-AzureAD

# Log đăng nhập của một user
Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '[EMAIL]'" |
    Select-Object CreatedDateTime, UserPrincipalName, IpAddress, Location, Status |
    Export-Csv -Path "signin_logs.csv"

# Risky sign-in
Get-AzureADMSriskySignInActivity |
    Select-Object UserPrincipalName, RiskLevel, RiskState, TopRiskLevel |
    Export-Csv -Path "risky_signins.csv"
```

### Microsoft Graph

```powershell
Import-Module Microsoft.Graph.SignIn
Select-MgProfile -Name "beta"

Get-MgAuditSignInLog -Filter "UserPrincipalName eq '[EMAIL]'" |
    Select-Object CreatedDateTime, UserDisplayName, IPAddress, Location, Status
```

## Checklist xác thực dữ liệu

- [ ] File đọc được, không rỗng.
- [ ] Đếm tổng số dòng và ghi lại.
- [ ] Xác định khoảng thời gian log (start - end).
- [ ] Kiểm tra chất lượng: trường thiếu, encoding, dòng trùng, số thực tế (giá trị ví dụ).
- [ ] Xác nhận đã quy đổi múi giờ cho cột thời gian trước khi so sánh ngoài giờ.
- [ ] Có baseline riêng cho từng user trong phạm vi (nếu không -> `insufficient_evidence` cho phần baseline).

## Phân quyền tối thiểu

- Security Reader, Security Operator hoặc Global Administrator để đọc sign-in log chi tiết.
- Entra ID Premium P1/P2 cho log chi tiết và Identity Protection risk.
