# Bảng ngưỡng - analyzing-azure-ad-signin-logs

Nguồn sự thật duy nhất cho ngưỡng của skill này. SKILL.md không nhắc lại số liệu.
Đổi model/dữ liệu -> chạm lại và ghi ngay thay đổi ở cuối file.

Mức nghi nghi (High / Medium / Low) **KHÔNG phải** mức P1-P4. Phân cấp sự cố cuối cùng
luôn lấy từ `../../_shared/severity-and-escalation.md` (nguyên tắc: phân cấp = mức cao nhất
trong các điều kiện, không hạ cấp).

## Đăng nhập thất bại / brute force / password spray

| Điều kiện | Ngưỡng | Mức nghi nghi | Hành động | Nguồn |
|---|---|---|---|---|
| Nhiều lần thất bại trên cùng một tài khoản | > 5 lần trong 10 phút | High | Ghi nhận brute force; gom theo IP, thành phố, thiết bị, ứng dụng; kiểm tra có đăng nhập thành công ngay sau đó không | SKILL.md v2.0 (Step 2.1) |
| Thất bại trên nhiều tài khoản từ cùng một IP | > 5 tài khoản | Critical | Nghi ngờ password spray / credential stuffing; đối chiếu danh sách tài khoản bị thử với danh sách user trong phạm vi | SKILL.md v2.0 (Step 2.1) |
| Mẫu "low and slow": thất bại thưa dần trên nhiều user | Hình dạng, không có số cố định | High | Nghi ngờ password spray chậm; xem chu kỳ giữa các lần thất bại và số tài khoản bị chạm | SKILL.md v2.0 (Step 2.1) |
| Tài khoản bị khóa sau chuỗi thất bại | Bất kỳ lần khóa nào | Medium | Ghi nhận khóa tài khoản; kiểm tra hoạt động sau thời điểm khóa | SKILL.md v2.0 (Step 2.1) |

## Impossible travel

| Kiểm tra | Cách tính | Ngưỡng | Mức nghi nghi | Hành động | Nguồn |
|---|---|---|---|---|---|
| Hai lần đăng nhập cùng user ở hai quốc gia khác nhau, thời gian không đủ để di chuyển | So sánh thời điểm (đã quy đổi múi giờ) và địa điểm của hai sự kiện | Thời gian di chuyển < 2 giờ | Critical | Ghi nhận impossible travel; kiểm tra có phải dịch vụ đồng bộ, VPN trung tâm, hoặc IP dùng chung không | SKILL.md v2.0 (Step 2.2) |
| Chênh lệch giờ địa phương giữa hai địa điểm | Múi giờ gốc của log theo `../../_shared/off-hours-and-baseline.md` | Dung sai hợp lý cho di chuyển thực tế: 2-4 giờ | Medium (cần đối chiếu thêm) | Không kết luận nếu chỉ dựa vào dung sai; cần thêm dữ liệu lịch trình/địa chỉ quen | SKILL.md v2.0 (Step 2.2) |

## Truy cập ngoài giờ

| Điều kiện | Ngưỡng | Hành động | Nguồn |
|---|---|---|---|
| Đăng nhập ngoài khung giờ làm việc của ngân hàng | Theo khung giờ trong `../../_shared/off-hours-and-baseline.md` | Ghi nhận sự kiện ngoài giờ; ghi rõ ngoại lệ đã đăng ký (làm việc ngoài giờ, đồng bộ) nếu có | `../../_shared/off-hours-and-baseline.md` |
| Đăng nhập vào cuối tuần | Bất kỳ sự kiện nào | Ghi nhận ngoài giờ; đối chiếu lịch trình làm việc | SKILL.md v2.0 (Step 2.3) |
| Đăng nhập vào ngày lễ của ngân hàng | Bất kỳ sự kiện nào | Ghi nhận ngoài giờ; đối chiếu lịch làm việc được duyệt | SKILL.md v2.0 (Step 2.3) |

## Vị trí / thiết bị / mạng mới

| Điều kiện | Ngưỡng | Mức nghi nghi | Hành động | Nguồn |
|---|---|---|---|---|
| Quốc gia / thành phố chưa từng xuất hiện với user | Không có trong baseline riêng của user | High | Ghi nhận new location; kiểm tra trùng với lịch trình công tác | SKILL.md v2.0 (Step 2.4) |
| Device ID chưa từng xuất hiện với user | Không có trong baseline riêng của user | Medium | Ghi nhận new device; kiểm tra user agent, OS, tin cậy thiết bị | SKILL.md v2.0 (Step 2.4) |
| ASN / mạng khác lịch sử của user | Khác lịch sử đã ghi nhận | Low | Ghi nhận; xác định mạng di động, mạng gia đình hay VPN | SKILL.md v2.0 (Step 2.4) |
| IP thuộc VPN / proxy / dịch vụ ẩn danh | Không nằm trong danh sách allowlist của tổ chức | Medium | Ghi nhận; kiểm tra chính sách truy cập từ xa | SKILL.md v2.0 (Step 2.4) |

## Risky sign-in (Microsoft Entra ID Protection)

| Loại risk | MITRE dùng được | Mức nghi nghi | Hành động | Nguồn |
|---|---|---|---|---|
| Leaked Credentials (mật khẩu lộ trong sự cố) | T1078 | Critical | Đề xuất đặt lại mật khẩu và thu hồi token; đánh giá khả năng chiếm đoạt | SKILL.md v2.0 (Step 2.5) |
| Malicious IP (IP nguồn tấn công đã biết) | T1078 | Critical | Đề xuất chặn IP và thu hồi token; đối chiếu log khác | SKILL.md v2.0 (Step 2.5) |
| Unfamiliar Location (quốc gia mới) | T1078 | High | Đối chiếu baseline và lịch trình công tác | SKILL.md v2.0 (Step 2.5) |
| Impossible Travel (Identity Protection gắn nhãn) | T1078 | Critical | Kết hợp với hồ sơ đăng nhập và hành vi sau đó | SKILL.md v2.0 (Step 2.5) |
| Anonymous IP (Tor / VPN không có exit node được duyệt) | T1078 | Medium | Ghi nhận; mapping hiện chưa có ID riêng cho proxy/anonymizer nên dùng T1078 | SKILL.md v2.0 (Step 2.5) |

## Ghi chú calibration

- Ngưỡng trên được kế thừa nguyên trạng từ SKILL.md v2.0 (bản gốc chưa có nguồn đo lường độc lập).
- Chưa có tập nhãn đủ lớn để hiệu chỉnh tự động; không được dùng bảng này để nâng mức sự cố.
- Khi đổi mô hình phát hiện hoặc bổ sung nhãn điều tra, ghi lại: ngày, nguồn dữ liệu, số mẫu, và ngưỡng cũ -> ngưỡng mới.
- Mọi ngưỡng chưa có bằng chứng đo lường đều ghi rõ trong bảng; không tự đặt thêm ở nơi khác.
