# Hành động đề xuất và đối chiếu chéo - analyzing-azure-ad-signin-logs

> Rules dòng 6: mọi lệnh dưới đây **chỉ được đề xuất** trong báo cáo. Không chạy khi chưa
> có xác nhận rõ ràng của người dùng.

## Hành động cho tài khoản bị chiếm đoạt (nghi ngờ có bằng chứng)

| Hành động | Lệnh minh hoạ | Mức đề xuất |
|---|---|---|
| Đặt lại mật khẩu | `Set-AzureADUserPassword -ObjectId <id> <SECRET>` | P1 |
| Thu hồi toàn bộ refresh token / phiên đăng nhập | `Revoke-AzureADUserAllRefreshToken -ObjectId <id>` | P1 |
| Vô hiệu hoá tài khoản | `Set-AzureADUser -ObjectId <id> -AccountEnabled $false` | P1 |
| Yêu cầu đăng ký MFA | Bật cấu hình MFA bắt buộc cho user | P1 |

Mức đề xuất chỉ là điểm khởi đầu; phải đối chiếu `../../_shared/severity-and-escalation.md` trước khi ghi.

## Hành động cho nghi ngờ nội giới (chưa xác nhận chiếm đoạt)

| Hành động | Mục đích | Mức đề xuất |
|---|---|---|
| Tăng cường giám sát | Theo dõi thêm anomaly cho cùng user | P2 |
| Tạm thu hồi quyền nhạy cảm | Cho đến khi điều tra xong | P2 |
| Phối hợp với HR | Chuẩn bị buổi làm việc với nhân sự | P2 |

## Đối chiếu chéo nguồn khác

| Nguồn | Đối chiếu gì | Dấu hiệu cần tìm |
|---|---|---|
| Varonis UEBA | Thời điểm truy cập file sau đăng nhập bất thường | Truy cập file dữ liệu khách hàng ngay sau khung giờ lạ |
| Log email (Exchange Online) | Mail gửi đi trong hoặc sau khung đăng nhập đáng ngờ | Gửi mail hàng loạt, gửi kèm tệp, gửi tới hộp thư cá nhân |
| OneDrive / SharePoint | Đọc dữ liệu sau đăng nhập bất thường | Tải hàng loạt, chia sẻ ra ngoài |
| Log VPN | Kết nối VPN trước khi có đăng nhập Entra ID | Cùng IP, cùng thời điểm, phiên bất thường |
| Endpoint / EDR | Tiến trình và hành vi máy sau đăng nhập | Script, tải công cụ, tiến trình ẩn |

Ghi rõ nguồn nào có dữ liệu, nguồn nào không có, để tránh kết luận thiếu căn cứ.

## Ghi chu pháp lý

Mọi mốc thời gian báo cáo, thông báo và hồ sơ lấy từ `../../_shared/compliance-vietnam.md`.
Skill này không tự đặt hạn chót.
