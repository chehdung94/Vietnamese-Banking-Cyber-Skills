# Checklist tự kiểm trước khi bàn giao - analyzing-vpn-logs

- [ ] Đã kiểm tra đủ các user/IP trong phạm vi.
- [ ] Đã tính thống kê kết nối và ghi rõ tổng số dòng, khoảng thời gian log.
- [ ] Đã phân tích thời lượng phiên theo ngưỡng trong `thresholds.md`.
- [ ] Đã xác định và ghi nhận kết nối ngoài giờ theo khung giờ trong `../../_shared/off-hours-and-baseline.md`.
- [ ] Đã phân tích xác thực thất bại.
- [ ] Đã đánh dấu user có nhiều IP nguồn.
- [ ] Đã phân loại bất thường theo mức và nêu ngưỡng áp dụng.
- [ ] Đã tách rõ kết luận có bằng chứng, giả thuyết, và cần thêm dữ liệu.
- [ ] IOC đã định dạng và ghi rõ nguồn.
- [ ] Hành động đề xuất có chủ sở hữu; mọi hành động khắc phục chỉ ở dạng đề xuất.
- [ ] Phân cấp P1-P4 theo `../../_shared/severity-and-escalation.md`, nêu rõ điều kiện đã chứng minh.
- [ ] Artefact còn thiếu đã được liệt kê (trả `insufficient_evidence` nếu thiếu điều kiện bắt buộc).
- [ ] Báo cáo đã được rà lại về tính chính xác của số liệu và thời gian.
