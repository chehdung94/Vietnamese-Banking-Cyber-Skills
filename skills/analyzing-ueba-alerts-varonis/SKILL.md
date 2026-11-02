---
name: analyzing-ueba-alerts-varonis
description: "Score and triage Varonis Data Advantage UEBA alerts into a comparable risk level with supporting evidence. Use when a UEBA alert export (CSV, XLSX, JSON) is provided for a batch or a single user. Not for collecting raw audit logs, endpoint forensics, or deciding the final P1-P4 severity."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: threat-detection-ueba
mitre_attack: [T1074, T1114, T1110, T1222, T1048, T1204]
nist_csf: [DE.CM-3, DE.AE-1, DE.AE-3, PR.AA-05]
tags:
  - varonis
  - ueba
  - insider-threat
  - user-behavior
  - anomaly-detection
  - banking
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Phân tích UEBA Alerts từ Varonis

## Overview

Nền tảng Varonis Data Advantage giám sát hành vi người dùng trên hệ thống tệp, email, Teams, OneDrive và ứng dụng cloud. Skill này hướng dẫn phân tích các cảnh báo UEBA, ưu tiên bất thường rủi ro cao và sinh báo cáo điều tra dùng được cho vận hành an ninh ngân hàng.

Bộ chấm điểm và ngưỡng nằm ở `references/thresholds.md`. Hợp đồng chấm điểm hành vi nằm ở `schemas/jev_contract.json`. SKILL.md chỉ giữ phần điều hướng.

> Mốc thời gian pháp lý: xem `../_shared/compliance-vietnam.md` (nguồn sự thật duy nhất).

## When to Use

- Điều tra cảnh báo UEBA của một người dùng cụ thể từ bảng điều khiển Varonis.
- Phân loại một lô cảnh báo UEBA sau khi export.
- Ứng cứu dấu hiệu nội giới (insider threat).
- Phân tích hành vi người dùng chủ động theo yêu cầu của điều tra viên.
- Chuẩn bị báo cáo điều tra phục vụ tuân thủ.

## Prerequisites

- Bản export cảnh báo UEBA dạng CSV, XLSX hoặc JSON, tối thiểu có: AlertID, Timestamp, User, UserEmail, AlertType, Severity, RiskScore, Department, Asset, Action, SourceIP, Destination.
- Hợp đồng chấm điểm `schemas/jev_contract.json` (bắt buộc khi chạy bước chấm Jev).
- Baseline hành vi riêng cho từng user trong phạm vi (ngày làm việc gần nhất) hoặc file baseline đã export.
- Danh sách user hoặc nhóm trong phạm vi và câu hỏi điều tra.
- Phân loại mức độ nhạy cảm của dữ liệu / asset của tổ chức để đối chiếu trường "asset sensitivity".
- Quy tắc giờ làm việc, ngoài giờ và múi giờ: `../_shared/off-hours-and-baseline.md`.

**STOP rule:** Nếu thiếu một điều kiện bắt buộc ở trên, dừng tại bước đó, không phân tích tiếp. Trả `insufficient_evidence` và liệt kê artefact còn thiếu theo Rules dòng 1-2.

## Execution Steps

### Step 1 - Thu thập và xác thực dữ liệu cảnh báo

- Nếu dùng giao diện Varonis: export theo ngày, mức độ, loại cảnh báo, kèm các cột ngữ cảnh (User, Asset, Action, Risk score).
- Nếu dùng file export: kiểm tra cấu trúc, số dòng, khoảng thời gian, giá trị thiếu, encoding.
- Danh sách cột và kiểm tra chất lượng: `references/alert-catalog.md`.

### Step 2 - Chuẩn hoá và phân loại cảnh báo

- Chuẩn hoá tên người dùng theo quy tắc so sánh tên trong `../_shared/off-hours-and-baseline.md`; tên mơ hồ thì ghi `ambiguous`.
- Quy đổi thời gian sang giờ Việt Nam trước khi so sánh ngoài giờ.
- Gán mỗi cảnh báo vào loại hành vi và chiến lược MITRE theo `references/alert-catalog.md`. Chỉ dùng ID có trong `../_shared/mitre-nist-mapping.md`.

### Step 3 - Chấm điểm rủi ro

Áp dụng **đúng** công thức và trọng số trong `references/thresholds.md`. Ghi lại từng thành phần để kết quả có thể kiểm tra lại. Không tự đặt thêm trọng số.

### Step 4 - Xác định user rủi ro cao

Đối chiếu từng user với tiêu chí chọn lọc trong `references/thresholds.md` (ngưỡng điểm rủi ro, số loại cảnh báo khác nhau, hoạt động ngoài giờ trên asset nhạy cảm, vị trí/thiết bị lạ, tăng đột biến khối lượng, truy cập ngoài chức năng công việc).

### Step 5 - Điều tra bối cảnh

Với mỗi user rủi ro cao, thu thập: hồ sơ baseline (giờ làm việc, mẫu truy cập asset, phòng ban, chức danh), dãy thời gian sự kiện, và bối cảnh kinh doanh (thay đổi tổ chức, lý do nghiệp vụ của việc truy cập).

### Step 6 - Xếp hạng bằng hợp đồng Jev

Chạy bộ chấm điểm theo `schemas/jev_contract.json` để xếp hạng các profile. Jev **chỉ xếp hạng**, không nâng mức phân cấp (xem `threshold_note` trong contract).

### Step 7 - Phân cấp và báo cáo

- Phân cấp P1-P4 theo `../_shared/severity-and-escalation.md`; nêu rõ điều kiện nào đã chứng minh.
- Hành động khắc phục **chỉ được đề xuất** (Rules dòng 6).
- Mẫu báo cáo và JSON đầy đủ: `references/output-example.md`.

## Expected Output

1. **Báo cáo** (markdown): tóm tắt điều tra, bảng phân loại cảnh báo, bảng xếp hạng user rủi ro, danh sách IOC, timeline, hành động đề xuất, giới hạn dữ liệu.
2. **JSON** (fenced, mẫu minh hoạ):

```json
{
  "skill": "analyzing-ueba-alerts-varonis",
  "status": "ok",
  "scope": {
    "users": [],
    "time_range": {"start": "YYYY-MM-DD", "end": "YYYY-MM-DD"},
    "alert_source": ""
  },
  "summary": {
    "total_alerts": 0,
    "high_risk": 0,
    "medium_risk": 0,
    "low_risk": 0
  },
  "findings": [
    {
      "type": "",
      "user": "",
      "evidence": "",
      "threshold_applied": "",
      "jev_ranking": "",
      "conclusion": "evidence|assumption|need_more_data"
    }
  ],
  "indicators": {
    "ips": [],
    "files": [],
    "users": [],
    "assets": []
  },
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

Ví dụ: `references/output-example.md`.

## References

- `references/thresholds.md` - bảng ngưỡng và công thức chấm điểm (nguồn duy nhất).
- `references/alert-catalog.md` - cột dữ liệu, kiểm tra chất lượng, phân loại MITRE.
- `references/output-example.md` - mẫu báo cáo và JSON đầy đủ.
- `references/banking-context.md` - bối cảnh ngân hàng Việt Nam, kịch bản thường gặp.
- `references/checklist.md` - checklist tự kiểm trước khi bàn giao.
- `schemas/jev_contract.json` - hợp đồng chấm điểm (Jev chỉ xếp hạng, không nâng mức).
- `../_shared/compliance-vietnam.md` - mốc thời gian pháp lý.
- `../_shared/severity-and-escalation.md` - thang P1-P4 và mã leo thang.
- `../_shared/off-hours-and-baseline.md` - giờ làm việc, múi giờ, cách tính baseline.
- `../_shared/mitre-nist-mapping.md` - ID MITRE/NIST hợp lệ.
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) - điều tra hành vi bất thường.
- [`playbook-pre-departure-insider-review`](../playbook-pre-departure-insider-review/SKILL.md) - rà soát nhân sự sắp nghỉ việc.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
