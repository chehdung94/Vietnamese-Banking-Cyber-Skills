# Checklist tự kiểm trước khi bàn giao - analyzing-ueba-alerts-varonis

- [ ] Đã phân loại toàn bộ cảnh báo theo hành vi và chiến lược MITRE hợp lệ.
- [ ] Đã tính điểm rủi ro cho toàn bộ user rủi ro cao, kèm từng thành phần điểm.
- [ ] Đã chạy bộ chấm Jev theo `schemas/jev_contract.json` và ghi rõ Jev chỉ xếp hạng.
- [ ] Đã dựng timeline điều tra.
- [ ] Đã trích xuất IOC và định dạng kèm AlertID.
- [ ] Đã tách rõ kết luận có bằng chứng, giả thuyết, và cần thêm dữ liệu.
- [ ] Hành động đề xuất có chủ sở hữu; mọi hành động khắc phục chỉ ở dạng đề xuất.
- [ ] Phân cấp P1-P4 theo `../../_shared/severity-and-escalation.md`, nêu rõ điều kiện đã chứng minh.
- [ ] Artefact còn thiếu đã được liệt kê (trả `insufficient_evidence` nếu thiếu điều kiện bắt buộc).
- [ ] Báo cáo đã được rà lại về tính chính xác của số liệu và thời gian.
