# Thu thập và xác thực log VPN - analyzing-vpn-logs

## Vị trí file log thường gặp

```text
Logs/*VPN*.csv
Logs/*vpn*.csv
Logs/*ssl*.csv
Logs/*FortiGate*.csv
Logs/*ASA*.csv
Logs/*PaloAlto*.csv
```

Liệt kê bằng `Glob`:

```text
Glob("**/Logs/*.csv")
```

## Cột dữ liệu thường gặp

| Cột | Mô tả |
|---|---|
| Date/Time | Thời điểm sự kiện (chú ý múi giờ gốc của từng hãng) |
| Level | Mức log (information, warning, error) |
| Action | Loại sự kiện (tunnel-up, tunnel-down, auth-failed...) |
| Status | Trạng thái kết nối |
| Message | Chi tiết sự kiện |
| VPN Tunnel | Định danh tunnel |
| User | Tên đăng nhập |
| Remote IP | Địa chỉ IP bên ngoài (nguồn kết nối) |
| Tunnel IP | Địa chỉ IP nội bộ được cấp trong tunnel |
| Source Port | Cổng nguồn |
| Protocol | Giao thức (SSL, IPSec) |

Múi giờ gốc theo từng nguồn log: xem `../../_shared/off-hours-and-baseline.md`.

## Quy trình xác thực

- [ ] File đọc được, không rỗng.
- [ ] Ghi tổng số dòng.
- [ ] Xác định khoảng thời gian log (đầu - cuối).
- [ ] Kiểm tra chất lượng dữ liệu: giá trị thiếu, encoding, cột không nhất quán giữa các file cùng nguồn.
- [ ] Chuẩn hoá tên cột giữa các nguồn (ASA / FortiGate / Palo Alto khác nhau) trước khi gộp.
- [ ] Kiểm tra có bản ghi nào bị trùng (cùng user + thời điểm + IP).

## Định dạng hỗ trợ

| Định dạng | Ghi chú |
|---|---|
| CSV | Phổ biến nhất; kiểm tra delimiter và encoding |
| CEF | Kiểm tra trường extension (`src`, `dst`, `suser`, `act`) |
| Syslog | Cần parse theo facility và severity |
| JSON | Một sự kiện một object hoặc mảng sự kiện |
