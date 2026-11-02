# SOP phân tích log VPN - analyzing-vpn-logs

Mọi ngưỡng viết trong file này lấy từ `thresholds.md`. Không đặt thêm ngưỡng.
Giờ làm việc, ngoài giờ, múi giờ, baseline: `../../_shared/off-hours-and-baseline.md`.

## Bước 1 - Thống kê cơ bản

```text
Total Rows: [number]
Unique Users: [number]
Unique Remote IPs: [number]
Unique Tunnel IPs: [number]
Date Range: [start_date] to [end_date]
```

Phân bố theo loại sự kiện:

| Loại sự kiện | Ý nghĩa |
|---|---|
| tunnel-up | Kết nối thiết lập thành công |
| tunnel-down | Kết nối kết thúc |
| tunnel-stats | Thống kê / keepalive |
| auth-success | Xác thực thành công |
| auth-failed | Xác thực thất bại |
| disconnect | Ngắt kết nối (thường do quản trị viên hoặc chính sách) |

Bảng tổng hợp theo user:

| User | Total Events | Unique IPs | First Connection | Last Connection |
|---|---|---|---|---|
| [user] | [number] | [number] | [datetime] | [datetime] |

## Bước 2 - Lọc theo phạm vi

```text
Theo IP:      Remote IP = [target_ip]
Theo user:    User = [target_username]
Theo thời gian: Date between [start_date] and [end_date]
```

Ghi rõ bộ lọc đã dùng trong báo cáo.

## Bước 3 - Phân tích mẫu kết nối

### 3.1 Thời gian

| Chỉ số | Giá trị |
|---|---|
| Kết nối sớm nhất | [datetime] |
| Kết nối muộn nhất | [datetime] |
| Thời lượng phiên trung bình | [number] giờ |
| Số kết nối ngoài giờ | [number] |
| Số kết nối cuối tuần / ngày lễ | [number] |

Định nghĩa ngoài giờ lấy từ `../../_shared/off-hours-and-baseline.md` (khung giờ làm việc của
ngân hàng, ngày làm việc, và các ngoại lệ đã đăng ký). Không dùng khung giờ riêng.

### 3.2 Địa lý và mạng

- Liệt kê toàn bộ IP nguồn.
- Kiểm tra uy tín IP nếu có nguồn tra cứu trong môi trường.
- Phân biệt dải trong nước và nước ngoài.
- Ghi nhận IP thay đổi giữa các phiên.

### 3.3 Phiên

- Tính thời lượng từng phiên từ cặp tunnel-up / tunnel-down.
- Đánh dấu phiên dài theo ngưỡng trong `thresholds.md`.
- Đánh dấu nhiều phiên đồng thời.
- Ghi nhận tần suất kết nối.

## Bước 4 - Truy vấn phát hiện bất thường

```text
Xác thực thất bại: lọc auth-failed -> nhóm theo User, Remote IP -> so với ngưỡng >= 5 lần
Nhiều IP:         lấy toàn bộ sự kiện của user -> đếm Remote IP duy nhất -> so với ngưỡng >= 3 IP
Ngoài giờ:         lọc theo khung giờ trong ../../_shared/off-hours-and-baseline.md
Phiên dài/đồng thời: tính theo mục 3.3
```

## Bước 5 - Quy trình chi tiết cho kết nối ngoài giờ

1. Trích toàn bộ sự kiện tunnel-up kèm thời điểm.
2. Lọc sự kiện ngoài khung giờ làm việc, cuối tuần, ngày lễ.
3. Với mỗi sự kiện ngoài giờ, ghi bảng:

| Ngày | Giờ | Sự kiện | Remote IP | Thời lượng | Đánh giá |
|---|---|---|---|---|---|
| [date] | [time] | tunnel-up | [ip] | [duration] | Bình thường / Đáng ngờ |

4. Đối chiếu mẫu theo bảng "Đánh giá rủi ro kết nối ngoài giờ" trong `thresholds.md`.

Các mẫu đáng ngờ thường gặp:

- Kết nối rồi ngắt rất nhanh (< 5 phút) ngoài giờ - có thể là truy cập dữ liệu.
- Nhiều phiên ngắn liên tiếp ngoài giờ.
- Kết nối ngay trước khi tài khoản bị khoá.
- Truy cập rất sớm trong ngày.

## Bước 6 - Phân tích chi tiết sự kiện đáng ngờ

```text
Event:      [event type]
Timestamp:  [timestamp]
User:       [username]
Remote IP:  [ip]
Tunnel IP:  [ip]
Status:     [success/failure]
Details:    [additional info]
```

## Bước 7 - Ghi kết luận

Mỗi phát hiện ghi 4 trường:

```text
Bằng chứng: sự kiện, trường, giá trị quan sát được
Ngưỡng: dòng nào trong references/thresholds.md áp dụng
Kết luận: có bằng chứng | giả thuyết | cần thêm dữ liệu
Hậu quả: hành động đề xuất (không tự thực thi)
```
