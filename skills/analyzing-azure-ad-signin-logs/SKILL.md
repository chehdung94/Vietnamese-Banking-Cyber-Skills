---
name: analyzing-azure-ad-signin-logs
description: "Analyze Entra ID (Azure AD) sign-in logs for account compromise, password spraying, impossible travel, and off-hours access. Use when an interactive, non-interactive, or risky sign-in export (CSV/JSON) is provided for one or more identities. Not for mailbox auditing, file-exfiltration analysis, or UEBA alert ranking."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: identity-security
mitre_attack: [T1078, T1110, T1098]
nist_csf: [DE.CM-1, DE.CM-3, DE.AE-1, PR.AA-06]
tags:
  - azure-ad
  - entra-id
  - signin-logs
  - identity-security
  - account-takeover
  - impossible-travel
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Phân tích Azure AD / Entra ID Sign-in Logs (Tier 3)

## Overview

Hướng dẫn phân tích log đăng nhập Azure Active Directory (Microsoft Entra ID) để phát hiện chiếm đoạt tài khoản, mẫu đăng nhập bất thường (brute force, password spray, impossible travel, truy cập ngoài giờ, vị trí/thiết bị mới) và phục vụ hồ sơ điều tra dựa trên bằng chứng danh tính.

Ngưỡng phát hiện, quy trình chi tiết và mẫu báo cáo nằm ở `references/`. SKILL.md chỉ giữ phần điều hướng.

> Mốc thời gian pháp lý: xem `../_shared/compliance-vietnam.md` (nguồn sự thật duy nhất).

## When to Use

- `playbook-abnormal-user-behavior-response`: phân tích bất thường đăng nhập theo hồ sơ người dùng.
- `playbook-phishing-response`: kiểm tra tài khoản bị chiếm sau sự cố phishing.
- `playbook-pre-departure-insider-review`: rà soát truy cập của nhân sự sắp nghỉ việc.
- `playbook-after-hours-internal-access-investigation`: điều tra truy cập nội bộ ngoài giờ.
- Điều tra trực tiếp: đăng nhập lạ, tài khoản bị khóa, hoặc cần dựng timeline.

## Prerequisites

- Export log đăng nhập (interactive, non-interactive, risky sign-in) dạng CSV/JSON, tối thiểu có: timestamp, user/UPN, IP, location, status/error code, device detail, application, correlation id.
- Baseline đăng nhập lịch sử **riêng cho từng user** trong phạm vi, hoặc file baseline đã export.
- Danh sách user/IP trong phạm vi và câu hỏi điều tra cụ thể.
- Quyền truy cập nguồn log: Azure Portal, Microsoft Graph API (`Get-MgAuditSignInLog`) hoặc module AzureAD (`Get-AzureADAuditSignInLogs`, `Get-AzureADMSriskySignInActivity`). Xem `references/collection-and-sources.md`.
- Quy tắc chuyển múi giờ và định nghĩa ngoài giờ: `../_shared/off-hours-and-baseline.md`.

**STOP rule:** Nếu thiếu một điều kiện bắt buộc ở trên, dừng tại bước đó, không phân tích tiếp. Trả `insufficient_evidence` và liệt kê artefact còn thiếu theo Rules dòng 1-2.

## Execution Steps

### Step 1 - Thu thập và xác thực dữ liệu

Theo `references/collection-and-sources.md`: chọn loại log, export bằng Azure Portal / Graph / AzureAD PowerShell, xác nhận file đọc được, đếm số dòng, xác định khoảng thời gian, ghi nhận lỗi chất lượng dữ liệu.

### Step 2 - Chuẩn hoá thời gian và định danh

- Chuyển timestamp Entra ID (gốc UTC) sang giờ Việt Nam theo `../_shared/off-hours-and-baseline.md` trước khi so sánh ngoài giờ.
- Khớp tên người dùng theo quy tắc so sánh tên trong file shared; tên mơ hồ ghi `ambiguous`, không tự chọn.
- Không có baseline riêng cho user -> ghi `insufficient_evidence` cho hạng mục baseline, KHONG ước lượng mu/sigma.

### Step 3 - Chấm điểm rủi ro theo bảng ngưỡng

Nạp `references/thresholds.md` và áp dụng **đúng** các ngưỡng trong đó cho 5 trục: thất bại đăng nhập, impossible travel, ngoài giờ, vị trí/thiết bị mới, risky sign-in. Không tự đặt thêm ngưỡng.

### Step 4 - Phân tích theo 5 trục

Thực hiện chi tiết theo `references/analysis-sop.md`: (1) thất bại đăng nhập, (2) impossible travel, (3) ngoài giờ, (4) vị trí/thiết bị/ASN mới, (5) risky sign-in của Identity Protection.

Mỗi trục ghi: bằng chứng, giá trị quan sát được, ngưỡng đã áp dụng, kết luận (có bằng chứng / giả thuyết / cần thêm dữ liệu).

### Step 5 - Trích xuất IOC và dựng timeline

- IOC: IP, vị trí, thiết bị, user, error code, correlation id - chỉ ghi giá trị thực sự có trong log.
- Timeline: sắp theo thời gian đã chuẩn hoá, gắn nhãn bình thường / bất thường.
- Mẫu báo cáo và JSON đầy đủ: `references/output-example.md`.

### Step 6 - Phân cấp và đề xuất hành động

- Phân cấp P1-P4 theo `../_shared/severity-and-escalation.md`; nêu rõ điều kiện nào đã chứng minh.
- Hành động khắc phục **chỉ được đề xuất**, không thực thi (Rules dòng 6). Danh sách: `references/remediation-and-correlation.md`.

### Step 7 - Đối chiếu chéo nguồn khác

Đối chiếu timeline đăng nhập với Varonis UEBA, log email, OneDrive/SharePoint, log VPN. Chi tiết: `references/remediation-and-correlation.md`.

## Expected Output

1. **Báo cáo** (markdown): tóm tắt điều tra, bảng chi tiết theo từng trục, phân cấp P1-P4, danh sách IOC, hành động đề xuất.
2. **JSON** (fenced, mẫu minh hoạ):

```json
{
  "skill": "analyzing-azure-ad-signin-logs",
  "status": "ok",
  "scope": {
    "users": ["user@example.com"],
    "time_range": {"start": "YYYY-MM-DDTHH:MM:SSZ", "end": "YYYY-MM-DDTHH:MM:SSZ"},
    "log_types": ["interactive"]
  },
  "summary": {"total_signins": 0, "failed_signins": 0, "unique_locations": 0, "risky_signins": 0},
  "findings": [
    {
      "type": "ImpossibleTravel",
      "evidence": "",
      "threshold_applied": "",
      "conclusion": "evidence|assumption|need_more_data"
    }
  ],
  "indicators": {"ips": [], "locations": [], "devices": [], "users": []},
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

Ví dụ: `references/output-example.md`.

## References

- `references/thresholds.md` - bảng ngưỡng phát hiện (nguồn duy nhất).
- `references/collection-and-sources.md` - loại log, cách export, kiểm tra dữ liệu.
- `references/analysis-sop.md` - SOP chi tiết cho 5 trục phân tích.
- `references/output-example.md` - mẫu báo cáo và JSON đầy đủ.
- `references/remediation-and-correlation.md` - hành động đề xuất, đối chiếu chéo.
- `../_shared/compliance-vietnam.md` - mốc thời gian pháp lý.
- `../_shared/severity-and-escalation.md` - thang P1-P4 và mã leo thang.
- `../_shared/off-hours-and-baseline.md` - giờ làm việc, múi giờ, cách tính baseline.
- `../_shared/mitre-nist-mapping.md` - ID MITRE/NIST hợp lệ.
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md)
- [`playbook-phishing-response`](../playbook-phishing-response/SKILL.md)
- [`playbook-pre-departure-insider-review`](../playbook-pre-departure-insider-review/SKILL.md)

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
