# Đánh giá phạm vi trước khi phân tích - analyzing-vpn-logs

Trả lời các câu hỏi sau TRƯỚC khi phân tích log VPN.

> **BẮT BUỘC:** Nếu **>= 2/5 câu trả lời là "Không"** -> **cân nhắc SKIP** và ghi rõ lý do.
> "Cân nhắc SKIP" nghĩa là đánh giá rồi quyết định, không phải bỏ qua case ngay lập tức.

| # | Câu hỏi | Yes/No | Ghi chú |
|---|---|---|---|
| 1 | VPN logs có sẵn cho TẤT CẢ user trong phạm vi không? | | |
| 2 | Có chính sách VPN ngoài giờ chính thức của tổ chức không? | | |
| 3 | Có thể đối chiếu VPN IP với IP nội bộ của user không? | | |
| 4 | Case có liên quan đến remote access hoặc anomaly theo vị trí không? | | |
| 5 | Có timeline correlation giữa sự kiện VPN và sự kiện từ nguồn log khác không? | |

## Khi không phân tích được

Nếu quyết định SKIP, ghi trong báo cáo:

```text
VPN analysis was skipped because [ly do]. VPN logs available at [path] for future reference if needed.
```

## Khi vẫn phân tích

Nếu sau khi cân nhắc SKIP vẫn phân tích được (ví dụ chỉ thiếu một nguồn đối chiếu), ghi rõ trong
báo cáo phạm vi thực tế đã phân tích được và artefact còn thiếu, theo Rules dòng 1-2. Không
ghi "skipped" khi đã phân tích.

## Liên quan

`playbook-pre-departure-insider-review` dùng tiêu chí Value Assessment của skill này để quyết
định có phân tích phần VPN hay không. Ngưỡng và nghĩa "cân nhắc SKIP" phải giữ nhất quán giữa
hai skill, xem `thresholds.md` (mục "Đánh giá phạm vi (Value Assessment)").
