# SOP phân tích sign-in - analyzing-azure-ad-signin-logs

Mọi ngưỡng viết trong file này đều lấy từ `thresholds.md`. Không đặt thêm ngưỡng.
Múi giờ, giờ làm việc, quy tắc so sánh tên: `../../_shared/off-hours-and-baseline.md`.

## Trục 1 - Đăng nhập thất bại, brute force, password spray

1. Lọc sự kiện thất bại (`Status.errorCode` khác 0 hoặc `Status.errorCode` cho auth failure).
2. Gom theo UserPrincipalName trong cửa sổ 10 phút -> so với ngưỡng "> 5 lần trong 10 phút".
3. Gom theo IPAddress -> đếm số tài khoản khác nhau bị thử -> so với ngưỡng "> 5 tài khoản".
4. Với mẫu thưa dần, kiểm tra chu kỳ giữa các lần thất bại và mức độ lan tỏa theo user.
5. Với tài khoản bị khóa, tìm sự kiện thành công ngay sau thời điểm khóa.

```powershell
Get-AzureADAuditSignInLogs -Filter "Status/ErrorCode eq 50053" |
    Where-Object { $_.CreatedDateTime -gt (Get-Date).AddHours(-1) } |
    Group-Object IpAddress |
    Where-Object { $_.Count -gt 5 } |
    Select-Object Name, Count
```

Kết luận ghi rõ: có bằng chứng (số lần, user, IP, khung thời gian), hay giả thuyết (spray chậm),
hay cần thêm dữ liệu (chưa có log trước cửa sổ phân tích).

## Trục 2 - Impossible travel

1. Sắp xếp sự kiện theo user theo thời gian đã quy đổi.
2. Với mỗi cặp sự kiện liên tiếp ở hai quốc gia khác nhau, tính khoảng cách thời gian.
3. So sánh với ngưỡng "thời gian di chuyển < 2 giờ" và dung sai hợp lý 2-4 giờ.
4. Loại trừ có căn cứ: dịch vụ đồng bộ, VPN trung tâm, IP dùng chung, ca trực của dịch vụ.
5. Ghi kết luận kèm giá trị quan sát được (giờ, địa điểm, IP) - không bịa số liệu.

Mẫu ghi chú:

```json
{
  "impossible_travel": {
    "user": "[EMAIL]",
    "login_1": {"location": "Ho Chi Minh City, Vietnam", "time": "YYYY-MM-DDTHH:MM:SSZ", "ip": "[IP]"},
    "login_2": {"location": "London, UK", "time": "YYYY-MM-DDTHH:MM:SSZ", "ip": "[IP]"},
    "time_difference_hours": "[OBSERVED]",
    "is_impossible": true,
    "note": "so sanh theo bang nguong tai references/thresholds.md"
  }
}
```

## Trục 3 - Truy cập ngoài giờ

1. Chuyển timestamp sang giờ Việt Nam theo file shared.
2. Lọc sự kiện ngoài khung giờ làm việc, cuối tuần, ngày lễ.
3. Ghi sự kiện kèm lý do ngoại lệ nếu có (đăng ký làm việc ngoài giờ, dịch vụ đồng bộ).
4. Với tài khoản đã bị vô hiệu hoá, mọi sự kiện sau ngày vô hiệu hoá đều cần nêu rõ.

```powershell
Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '[EMAIL]'" |
    Where-Object { $_.CreatedDateTime -gt $baselineStart } |
    Select-Object CreatedDateTime, Location, IpAddress, DeviceDetail, ClientAppUsed
```

(Điều kiện ngoài giờ cụ thể lấy từ khung giờ trong `../../_shared/off-hours-and-baseline.md`, không hard-code trong script.)

## Trục 4 - Vị trí, thiết bị, mạng mới

```powershell
# Baseline vị trí của user
$baseline = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '[EMAIL]'" |
    Select-Object -ExpandProperty Location -Unique

# Vị trí mới trong giai đoạn phân tích
$recent = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '[EMAIL]' and CreatedDateTime gt [START]" |
    Where-Object { $baseline -notcontains $_.Location }
```

Tương tự cho Device ID và ASN. Ghi rõ kết luận nào dựa trên baseline có sẵn, kết luận nào
chỉ là do thiếu dữ liệu baseline.

## Trục 5 - Risky sign-in (Identity Protection)

| Loại risk | MITRE dùng được | Mức nghi nghi |
|---|---|---|
| Leaked Credentials | T1078 | Critical |
| Malicious IP | T1078 | Critical |
| Unfamiliar Location | T1078 | High |
| Impossible Travel | T1078 | Critical |
| Anonymous IP | T1078 | Medium |

Nguồn ID hợp lệ: `../../_shared/mitre-nist-mapping.md`.

## Ghi kết luận

Mỗi phát hiện ghi 4 trường:

```text
Bằng chứng: sự kiện, trường, giá trị quan sát được
Ngưỡng: dòng nào trong references/thresholds.md áp dụng
Kết luận: có bằng chứng | giả thuyết | cần thêm dữ liệu
Hậu quả: hành động đề xuất (không tự thực thi)
```
