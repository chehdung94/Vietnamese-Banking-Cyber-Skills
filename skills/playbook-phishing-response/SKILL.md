---
name: playbook-phishing-response
description: Tier 2 Playbook to respond to phishing attacks. Provides checklists for evidence collection, internal communication plans, and technical steps to analyze email headers, content, and remediate the incident.
domain: cybersecurity
subdomain: incident-response
tags: [playbook, tier-2, phishing, incident-response, checklist, DFIR]
version: "1.1"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1566]
---

# Playbook: Phishing Incident Response (Tier 2)

## Overview
This playbook guides the response to suspected or confirmed phishing incidents. It outlines the specific evidence required, internal communication steps, and the execution of technical skills to contain and eradicate the threat.

## When to Use
Triggered by the **Master Incident Triage** playbook when an incident is classified as Phishing (Credential Harvesting, Malware Delivery via Email, BEC).

---

## 1. Evidence Request Checklist (RFI)
*Agent Action: Present this checklist to the user/SOC analyst to gather necessary information before deep analysis.*

| Check | Evidence Type | Purpose | Source | Status |
|---|---|---|---|:---:|
| [ ] | **Full Email Source (EML/MSG)** | Trích xuất Headers, URLs, Payload HTML. | Exchange/O365, User Mailbox | Pending |
| [ ] | **Attachments (Mã hóa ZIP)** | Phân tích file đính kèm độc hại (nếu có). | Extract từ EML | Pending |
| [ ] | **Mail Gateway / Trace Logs** | Xác định bao nhiêu user đã nhận email này. | Mail Gateway / O365 Trace | Pending |
| [ ] | **Web Proxy / Firewall Logs** | Kiểm tra user nào đã Click/Truy cập link độc hại. | Proxy/NGFW Logs | Pending |
| [ ] | **User Context / Interview** | User có nhập mật khẩu hay tải file không? | Helpdesk / Phỏng vấn | Pending |

---

## 2. Internal Communication Plan
*Quy trình báo cáo và liên lạc nội bộ khi xử lý sự cố.*

*   **T+15 Phút (Phát hiện):**
    *   Thông báo cho Helpdesk: *"Đang có chiến dịch phishing, tạm thời không reset password hàng loạt cho đến khi có danh sách user bị ảnh hưởng."*
*   **T+1 Giờ (Phân tích & Ngăn chặn ban đầu):**
    *   Báo cáo IT Ops: Cung cấp danh sách IP/Domain/Sender Email để đưa vào Blacklist.
*   **T+4 Giờ (Đánh giá mức độ):**
    *   Báo cáo CISO / Ban Giám đốc: Nếu có nhân viên đã nhập mật khẩu (Compromised) và có nguy cơ lộ lọt dữ liệu nội bộ/khách hàng.

---

## 3. Interim Recommendations (Khuyến nghị Tạm thời)
*Thực hiện ngay trong khi thu thập bằng chứng.*

1. **Cô lập User:** Tạm thời vô hiệu hóa (Disable) tài khoản Active Directory / O365 của user đã báo cáo hoặc user nghi ngờ click link.
2. **Cảnh báo toàn công ty (Tùy chọn):** Gửi email cảnh báo khẩn cấp không mở email có tiêu đề `[Subject của Phishing]`.

---

## 4. Execution Steps (Các bước Phân tích Kỹ thuật)
*Agent Action: Execute these steps once evidence from Section 1 is provided.*

### Step 4.1: Analyze Email Headers & Body (Identification)
*Sử dụng kỹ năng trích xuất thông tin tĩnh từ file EML.*
*   Trích xuất `Sender IP`, kiểm tra SPF/DKIM/DMARC.
*   Trích xuất tất cả URLs trong Body.
*   Trích xuất Hash của tệp đính kèm.

### Step 4.2: Scope Determination
Dựa vào Mail Trace Logs và Proxy Logs:
*   [ ] Tổng số email phát tán?
*   [ ] Số lượng user đã Click?
*   [ ] Số lượng user đã Submit thông tin (POST request)?

### Step 4.3: Containment (Ngăn chặn)
Cung cấp lệnh hoặc danh sách IOCs cho Network Team:
| Lớp | Hành động (Action) | Priority |
|---|---|---|
| **Email Gateway** | Block Sender Domain/IP, Block Subject. | P1 |
| **Network (Proxy/FW)** | Block Malicious URLs/Domains. | P1 |
| **Identity (AD)** | Force Password Reset, Revoke Sessions cho compromised users. | P1 |

### Step 4.4: Eradication (Loại bỏ)
```powershell
# Ví dụ lệnh xóa email trên Exchange/O365 (Search & Purge)
Search-Mailbox -SearchQuery "Subject:'[Tiêu đề Phishing]'" -DeleteContent
```

---

## 5. Expected Output / Reporting
*Agent Action: After executing steps, generate a structured JSON report.*

```json
{
  "incident_id": "PHISH-YYYYMMDD",
  "status": "Contained/Investigating",
  "metrics": {
    "total_received": 0,
    "total_clicked": 0,
    "compromised_accounts": 0
  },
  "extracted_iocs": {
    "sender_ips": [],
    "domains": [],
    "urls": [],
    "file_hashes": []
  },
  "remediation_status": {
    "gateway_blocked": false,
    "emails_purged": false,
    "passwords_reset": false
  }
}
```
