# Bảng ngưỡng - analyzing-vpn-logs

Nguồn sự thật duy nhất cho ngưỡng của skill này. SKILL.md không nhắc lại số liệu.
Đổi model/dữ liệu -> chạm lại và ghi ngay thay đổi ở cuối file.

Mức nghi nghi (High / Medium / Low) **KHÔNG phải** mức P1-P4. Phân cấp sự cố cuối cùng
luôn lấy từ `../../_shared/severity-and-escalation.md`.

## Chỉ báo rủi ro chính

| Chỉ báo | Ngưỡng | Mức nghi nghi | Hành động | Nguồn |
|---|---|---|---|---|
| Số lần xác thực thất bại | >= 5 lần | High | Gom theo user và IP nguồn; kiểm tra có phiên thành công ngay sau đó không | SKILL.md v2.0 (Step 6.1) |
| Nhiều địa chỉ IP nguồn cho cùng một user | >= 3 IP khác nhau | Medium | Đối chiếu với lịch trình công tác và danh sách mạng được duyệt | SKILL.md v2.0 (Step 6.1) |
| Kết nối ngoài giờ | Bất kỳ sự kiện nào | Medium | Ghi nhận; kiểm tra ngoại lệ đã đăng ký (làm việc ngoài giờ, đồng bộ) | SKILL.md v2.0 (Step 6.1) |
| Phiên dài | > 8 giờ | Medium | Ghi nhận; xác định là phiên làm việc bình thường hay phiên treo | SKILL.md v2.0 (Step 6.1) |
| Nhiều phiên đồng thời | >= 2 phiên | High | Đối chiếu thời điểm bắt đầu; kiểm tra tài khoản dùng chung | SKILL.md v2.0 (Step 6.1) |
| Impossible travel (đổi IP ở vị trí địa lý khác nhau) | Thời gian chuyển điểm < 1 giờ | Critical | Ghi nhận; đối chiếu với log đăng nhập danh tính | SKILL.md v2.0 (Step 6.1) |
| Kết nối đầu tiên sau thời điểm tài khoản bị khoá / vô hiệu hoá | Bất kỳ sự kiện nào | Critical | Ghi nhận ngay; đề xuất thu hồi phiên theo quy trình leo thang | SKILL.md v2.0 (Step 6.1) |

## Đánh giá rủi ro kết nối ngoài giờ

| Mẫu | Mức nghi nghi | Hành động |
|---|---|---|
| Thỉnh thoảng ngoài giờ (1-2 lần/tháng) | Low | Ghi nhận vào báo cáo |
| Ngoài giờ thường xuyên | Medium | Điều tra theo mẫu lặp lại |
| Phiên ngắn ngoài giờ (< 5 phút) | Medium-High | Ưu tiên điều tra |
| Kết nối ngay trước / sau ngày nghỉ việc | Critical | Điều tra ngay |

Nguồn: SKILL.md v2.0 (Step 6.3, bảng Risk Assessment).

## Đánh giá phạm vi (Value Assessment)

| Điều kiện | Ngưỡng | Mức nghi nghi | Hành động | Nguồn |
|---|---|---|---|---|
| Trả lời "Không" ở Value Assessment | >= 2/5 câu hỏi | Medium | Cân nhắc SKIP phân tích VPN; ghi rõ lý do và path nguồn log. Nếu vẫn phân tích được thì ghi rõ phạm vi thực tế đã phân tích | SKILL.md v2.0 (Pre-Analysis Value Assessment) |

Dòng này là quyết định quy trình, **KHÔNG phải** mức P1-P4. "Cân nhắc SKIP" nghĩa là đánh giá
rồi quyết định, không phải bỏ qua case ngay lập tức. Câu hỏi và nghĩa của từng câu trả lời:
`scope-assessment.md`.

## Ghi chú calibration

- Ngưỡng kế thừa nguyên trạng từ SKILL.md v2.0; bản gốc chưa có tập nhãn đủ lớn để hiệu chỉnh độc lập.
- Khung giờ ngoài giờ và ngoại lệ đã đăng ký lấy từ `../../_shared/off-hours-and-baseline.md`, không hard-code trong file này.
- Không dùng bảng này để nâng mức sự cố; phân cấp P1-P4 theo `../../_shared/severity-and-escalation.md`.
- Khi đổi mô hình phát hiện hoặc bổ sung nhãn điều tra, ghi lại: ngày, nguồn dữ liệu, số mẫu, ngưỡng cũ -> ngưỡng mới.
