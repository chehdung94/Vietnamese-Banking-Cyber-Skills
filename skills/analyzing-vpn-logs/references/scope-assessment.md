# Đánh giá phạm vi trước khi phân tích - analyzing-vpn-logs

Trả lời các câu hỏi sau TRƯỚC khi phân tích log VPN. Nếu phần lớn câu trả lời là "không",
ghi rõ lý do và phạm vi thực tế có thể phân tích, thay vì bỏ qua case.

| # | Câu hỏi | Yes/No | Ghi chú |
|---|---|---|---|
| 1 | VPN logs có sẵn cho TẤT CẢ user trong phạm vi không? | | |
| 2 | Có chính sách VPN ngoài giờ chính thức của tổ chức không? | | |
| 3 | Có thể đối chiếu VPN IP với IP nội bộ của user không? | | |
| 4 | Case có liên quan đến remote access hoặc anomaly theo vị trí không? | | |
| 5 | Có timeline correlation giữa sự kiện VPN và sự kiện từ nguồn log khác không? | | |

Nếu không phân tích được, ghi trong báo cáo:

```text
VPN analysis was skipped because [ly do]. VPN logs available at [path] for future reference if needed.
```
