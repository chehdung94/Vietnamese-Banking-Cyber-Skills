# Bảng ngưỡng - analyzing-ueba-alerts-varonis

Nguồn sự thật duy nhất cho ngưỡng của skill này. SKILL.md không nhắc lại số liệu.
Đổi model/dữ liệu -> chạm lại và ghi ngay thay đổi ở cuối file.

Mức ưu tiên (High / Medium / Critical) **KHÔNG phải** mức P1-P4. Phân cấp sự cố cuối cùng
luôn lấy từ `../../_shared/severity-and-escalation.md`.

## Công thức chấm điểm rủi ro

```text
RiskScore = (SeverityWeight × 0.4) + (BehavioralDeviation × 0.3) + (AssetSensitivity × 0.2) + (HistoricalContext × 0.1)
```

| Thành phần | Trọng số | Nguồn |
|---|---|---|
| SeverityWeight | × 0.4 | SKILL.md v2.0 (Step 3) |
| BehavioralDeviation | × 0.3 | SKILL.md v2.0 (Step 3) |
| AssetSensitivity | × 0.2 | SKILL.md v2.0 (Step 3) |
| HistoricalContext | × 0.1 | SKILL.md v2.0 (Step 3) |

## Trọng số theo mức độ cảnh báo

| Mức độ cảnh báo | SeverityWeight | Nguồn |
|---|---|---|
| Critical | 1.0 | SKILL.md v2.0 (Step 3) |
| High | 0.75 | SKILL.md v2.0 (Step 3) |
| Medium | 0.5 | SKILL.md v2.0 (Step 3) |
| Low | 0.25 | SKILL.md v2.0 (Step 3) |

## Trọng số theo độ nhạy cảm của asset

| Phân loại asset | AssetSensitivity | Nguồn |
|---|---|---|
| Confidential (dữ liệu khách hàng, PII) | 1.0 | SKILL.md v2.0 (Step 3) |
| Restricted (hồ sơ tài chính) | 0.75 | SKILL.md v2.0 (Step 3) |
| Internal (nghiệp vụ chung) | 0.5 | SKILL.md v2.0 (Step 3) |
| Public | 0.1 | SKILL.md v2.0 (Step 3) |

BehavioralDeviation và HistoricalContext lấy từ baseline của từng user (xem
`../../_shared/off-hours-and-baseline.md`). Không có baseline -> ghi `insufficient_evidence`
cho hai thành phần này, KHONG ước lượng.

## Tiêu chí chọn user rủi ro cao

| Tiêu chí | Ngưỡng | Nguồn |
|---|---|---|
| Điểm rủi ro | Risk score >= 70 | SKILL.md v2.0 (Step 4) |
| Đa dạng loại cảnh báo | >= 3 loại cảnh báo khác nhau | SKILL.md v2.0 (Step 4) |
| Hoạt động ngoài giờ trên asset nhạy cảm | Theo khung giờ trong `../../_shared/off-hours-and-baseline.md` | SKILL.md v2.0 (Step 4) |
| Truy cập từ vị trí / thiết bị lạ | Không có trong baseline của user | SKILL.md v2.0 (Step 4) |
| Tăng đột biến khối lượng hoạt động | So với baseline của user | SKILL.md v2.0 (Step 4) |
| Truy cập asset ngoài chức năng công việc | Không nằm trong danh sách asset theo vai trò | SKILL.md v2.0 (Step 4) |

## Phân loại cảnh báo theo hành vi

| Loại cảnh báo | ID MITRE dùng được | Ưu tiên | Nguồn |
|---|---|---|---|
| Truy cập file bất thường / gom dữ liệu tạm | T1074 | High | SKILL.md v2.0 (Step 2) |
| Tải hàng loạt email | T1114 | High | SKILL.md v2.0 (Step 2) |
| Nhiều lần đăng nhập thất bại | T1110 | Medium | SKILL.md v2.0 (Step 2) |
| Thay đổi quyền file / thư mục | T1222 | Critical | SKILL.md v2.0 (Step 2) |
| Dấu hiệu rò rỉ dữ liệu ra ngoài | T1048 | Critical | SKILL.md v2.0 (Step 2) |
| Dấu hiệu malware qua người dùng | T1204 | Critical | SKILL.md v2.0 (Step 2) |

Danh sách ID hợp lệ: `../../_shared/mitre-nist-mapping.md`.

## Ghi chú calibration

- Ngưỡng và trọng số kế thừa nguyên trạng từ SKILL.md v2.0; bản gốc chưa có tập nhãn đủ lớn
  để hiệu chỉnh độc lập.
- Điểm RiskScore là thang tương đối để xếp hạng, KHÔNG phải mức P1-P4.
- Bộ chấm Jev trong `schemas/jev_contract.json` có `calibrated: false`: chỉ xếp hạng, không nâng mức.
- Khi đổi mô hình phát hiện hoặc bổ sung nhãn điều tra, ghi lại: ngày, nguồn dữ liệu, số mẫu,
  ngưỡng cũ -> ngưỡng mới.
