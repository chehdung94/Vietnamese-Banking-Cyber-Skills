# Danh mục cảnh báo và kiểm tra dữ liệu - analyzing-ueba-alerts-varonis

## Cột dữ liệu kỳ vọng

```text
AlertID, Timestamp, User, UserEmail, AlertType, Severity,
RiskScore, Department, Asset, Action, SourceIP, Destination,
AdditionalDetails
```

| Cột | Bắt buộc | Dùng để |
|---|---|---|
| AlertID | Co | Truy vết cảnh báo trong báo cáo, IOC |
| Timestamp | Co | Timeline, so sánh ngoài giờ |
| User / UserEmail | Co | Ghép hồ sơ baseline, khớp tên |
| AlertType | Co | Phân loại hành vi và chiến lược MITRE |
| Severity | Co | Trọng số SeverityWeight |
| RiskScore | Co | Đối chiếu điểm tự chấm |
| Department / Asset | Co | Xác định chức năng công việc, độ nhạy cảm |
| Action | Co | Phân biệt đọc / ghi / xóa / gửi |
| SourceIP / Destination | Co | IOC, kiểm tra vị trí và thiết bị lạ |
| AdditionalDetails | Khuyen nghi | Số lượng file, số thư, tên file |

## Kiểm tra chất lượng dữ liệu

- [ ] File đọc được, không rỗng.
- [ ] Ghi tổng số cảnh báo và khoảng thời gian.
- [ ] Kiểm tra giá trị thiếu ở các cột bắt buộc.
- [ ] Kiểm tra encoding và cột không nhất quán giữa các file cùng nguồn.
- [ ] Kiểm tra trùng AlertID.
- [ ] Chuẩn hoá múi giờ về giờ Việt Nam theo `../../_shared/off-hours-and-baseline.md`.
- [ ] Khớp tên người dùng theo quy tắc so sánh tên trong file shared; tên mơ hồ ghi `ambiguous`.

## Phân loại cảnh báo theo hành vi

| Loại cảnh báo | ID MITRE dùng được | Ưu tiên |
|---|---|---|
| Truy cập file bất thường / gom dữ liệu tạm | T1074 | High |
| Tải hàng loạt email | T1114 | High |
| Nhiều lần đăng nhập thất bại | T1110 | Medium |
| Thay đổi quyền file / thư mục | T1222 | Critical |
| Dấu hiệu rò rỉ dữ liệu ra ngoài | T1048 | Critical |
| Dấu hiệu malware qua người dùng | T1204 | Critical |

Chỉ dùng ID có trong `../../_shared/mitre-nist-mapping.md`.

## Bối cảnh thu thập

**Từ giao diện Varonis:**

- Export theo bộ lọc: khoảng ngày, mức độ, loại cảnh báo.
- Bắt buộc kèm cột ngữ cảnh: User, Asset, Action, Risk score.

**Từ file export:**

- Xác thực cấu trúc và các cột bắt buộc ở trên.
- Ghi lại các vấn đề chất lượng dữ liệu phát hiện được.
