---
name: analyzing-vpn-logs
description: "Analyze VPN concentrator logs from Cisco ASA, FortiGate, Palo Alto, Pulse Secure, or Azure VPN Gateway for credential abuse, brute force, off-hours access, and anomalous sessions. Use when a VPN export (CSV, CEF, syslog, JSON) is provided for an investigation scope. Not for full-tunnel traffic inspection, firewall rule review, or endpoint forensics."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: network-security
mitre_attack: [T1133, T1021.001, T1078, T1110]
nist_csf: [DE.CM-1, DE.CM-3, DE.AE-1, PR.AA-01, PR.AA-06]
tags:
  - vpn
  - ssl-vpn
  - remote-access
  - network-security
  - connection-analysis
  - insider-threat
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Phân tích VPN Logs

## Overview

Phân tích log VPN concentrator (Cisco ASA, FortiGate, Palo Alto, Pulse Secure, Azure VPN Gateway) để phát hiện truy cập trái phép, lạm dụng thông tin đăng nhập, hành vi ngoài giờ và phiên bất thường.

Ngưỡng phát hiện, SOP chi tiết và mẫu báo cáo nằm ở `references/`. SKILL.md chỉ giữ phần điều hướng.

> Mốc thời gian pháp lý: xem `../_shared/compliance-vietnam.md` (nguồn sự thật duy nhất).

## When to Use

- Điều tra sự cố truy cập từ xa: mất quyền truy cập, tài khoản bị khóa, kết nối lạ.
- Ứng cứu cảnh báo truy cập không được phép.
- Theo dõi định kỳ hoạt động VPN để tìm mẫu bất thường.
- Rà soát nhân sự sắp nghỉ việc, điều tra truy cập ngoài giờ, chuẩn bị hồ sơ tuân thủ (xem các playbook liên kết bên dưới).

## Prerequisites

- File log VPN (firewall / SSL VPN / NAC) dạng CSV, CEF, Syslog hoặc JSON trong phạm vi điều tra.
- Danh sách user hoặc IP mục tiêu, kèm phạm vi thời gian.
- Hiểu biết hạ tầng VPN và dải IP nội bộ của tổ chức (`references/banking-context.md`).
- Baseline kết nối của từng user hoặc file baseline đã export.
- Quy tắc giờ làm việc, ngoài giờ, múi giờ theo nguồn log: `../_shared/off-hours-and-baseline.md`.
- Danh sách phê duyệt làm việc ngoài giờ và dịch vụ đồng bộ đã xác nhận (nếu có).

**STOP rule:** Nếu thiếu một điều kiện bắt buộc ở trên, dừng tại bước đó, không phân tích tiếp. Trả `insufficient_evidence` và liệt kê artefact còn thiếu theo Rules dòng 1-2.

## Execution Steps

### Step 0 - Kiểm tra phạm vi

Trả lời 5 câu hỏi đánh giá phạm vi trong `references/scope-assessment.md`. Nếu 2/5 câu trả lời là "không" -> cân nhắc SKIP và ghi rõ lý do; nếu vẫn phân tích được thì ghi rõ phạm vi phân tích được.

### Step 1 - Tìm và xác thực nguồn log

Dùng `Glob` liệt kê file log, sau đó kiểm tra: file đọc được, tổng số dòng, khoảng thời gian, lỗi chất lượng dữ liệu. Cột dữ liệu và cách export: `references/collection-and-sources.md`.

### Step 2 - Tính thống kê cơ bản

Tổng số dòng, số user, số IP nguồn, số IP tunnel, khoảng thời gian, và phân bố theo loại sự kiện (tunnel-up, tunnel-down, auth-success, auth-failed, disconnect). Chi tiết: `references/analysis-sop.md`.

### Step 3 - Lọc theo phạm vi điều tra

Lọc theo IP mục tiêu, user mục tiêu, khoảng thời gian. Ghi rõ bộ lọc đã dùng để mọi kết luận có thể tái lập.

### Step 4 - Phân tích mẫu kết nối

- Thời gian: kết nối sớm nhất, muộn nhất, thời lượng phiên trung bình, số kết nối ngoài giờ, cuối tuần, ngày lễ.
- Mạng và địa lý: toàn bộ IP nguồn, IP trùng lặp, dải trong nước/ngoài nước, thay đổi IP giữa phiên.
- Phiên: thời lượng từng phiên, phiên dài, nhiều phiên đồng thời, tần suất kết nối.

### Step 5 - Phát hiện bất thường

Áp dụng **đúng** bảng ngưỡng `references/thresholds.md`: xác thực thất bại, nhiều IP, kết nối ngoài giờ, phiên dài, phiên đồng thời, impossible travel, kết nối ngay sau khi khoá tài khoản. Không tự đặt thêm ngưỡng.

### Step 6 - Phân tích chi tiết sự kiện đáng ngờ

Trích xuất: loại sự kiện, thời điểm, user, IP nguồn, IP tunnel, trạng thái. Phân biệt rõ kết luận có bằng chứng, giả thuyết, và cần thêm dữ liệu.

### Step 7 - Trích xuất IOC và dựng timeline

IOC: IP nguồn, IP tunnel, user, dải mạng, địa chỉ endpoint. Timeline sắp theo thời gian đã chuẩn hoá. Mẫu báo cáo: `references/output-example.md`.

### Step 8 - Phân cấp và đề xuất hành động

Phân cấp P1-P4 theo `../_shared/severity-and-escalation.md`, nêu rõ điều kiện đã chứng minh. Hành động khắc phục **chỉ được đề xuất** (Rules dòng 6).

## Expected Output

1. **Báo cáo** (markdown): tóm tắt, thống kê kết nối, bất thường xếp theo mức, IOC, timeline, hành động đề xuất, giới hạn dữ liệu.
2. **JSON** (fenced, mẫu minh hoạ):

```json
{
  "skill": "analyzing-vpn-logs",
  "status": "ok",
  "scope": {
    "target_type": "ip|user|both",
    "targets": [],
    "time_range": {"start": "YYYY-MM-DD", "end": "YYYY-MM-DD"},
    "log_source": ""
  },
  "summary": {"total_rows": 0, "unique_users": 0, "unique_remote_ips": 0, "unique_tunnel_ips": 0},
  "connection_events": {"tunnel_up": 0, "tunnel_down": 0, "auth_success": 0, "auth_failed": 0},
  "findings": [
    {
      "type": "",
      "evidence": "",
      "threshold_applied": "",
      "conclusion": "evidence|assumption|need_more_data"
    }
  ],
  "indicators": {"ips": [], "tunnel_ips": [], "users": []},
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

Ví dụ: `references/output-example.md`.

## References

- `references/thresholds.md` - bảng ngưỡng phát hiện (nguồn duy nhất).
- `references/collection-and-sources.md` - tìm, export và xác thực log VPN.
- `references/analysis-sop.md` - SOP thống kê, lọc, phân tích mẫu kết nối.
- `references/scope-assessment.md` - 5 câu hỏi đánh giá phạm vi.
- `references/output-example.md` - mẫu báo cáo và JSON đầy đủ.
- `references/banking-context.md` - bối cảnh ngân hàng, dải IP nội bộ.
- `references/checklist.md` - checklist tự kiểm trước khi bàn giao.
- `../_shared/compliance-vietnam.md` - mốc thời gian pháp lý.
- `../_shared/severity-and-escalation.md` - thang P1-P4 và mã leo thang.
- `../_shared/off-hours-and-baseline.md` - giờ làm việc, múi giờ, cách tính baseline.
- `../_shared/mitre-nist-mapping.md` - ID MITRE/NIST hợp lệ.
- [`playbook-pre-departure-insider-review`](../playbook-pre-departure-insider-review/SKILL.md)
- [`playbook-after-hours-internal-access-investigation`](../playbook-after-hours-internal-access-investigation/SKILL.md)
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md)

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
