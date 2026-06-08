# TIER-BASED FLOW: Daily Watchlist Monitoring Program
## Case Study: 334 Departing Employees - Vietnamese Commercial Bank

---

## KIẾN TRÚC 3-TIER (THEO CHUẨN AGENTSKILLS.IO)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  TIER 1: MASTER PLAYBOOK (Tổng đài Phân loại & Điều hướng)                  │
│  Skill: playbook-master-incident-triage                                     │
├─────────────────────────────────────────────────────────────────────────────┤
│  Vai trò: Entry Point - Tiếp nhận yêu cầu giám sát                          │
│  Input: Danh sách watchlist từ HR (334 nhân sự nghỉ việc)                   │
│  Output: Phân loại → Route sang Tier 2 Playbook                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  TIER 2: INCIDENT PLAYBOOKS (Kịch bản Phản ứng Sự cố)                       │
│  Skill: playbook-daily-watchlist-monitoring                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│  Vai trò: Incident Commander cho Insider Threat                             │
│  Nhiệm vụ:                                                                  │
│  ├── 1. Xác định investigation window từ HR data                            │
│  ├── 2. Đưa ra checklist bằng chứng cần thu thập                            │
│  ├── 3. Gọi Tier 3 skills để phân tích logs                                 │
│  ├── 4. Tổng hợp kết quả, đánh giá rủi ro                                   │
│  └── 5. Khuyến nghị containment/escalation                                  │
└─────────────────────────────────────────────────────────────────────────────┘
                                      │
                                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  TIER 3: TECHNICAL SKILLS (Kỹ năng Kỹ thuật & Công cụ)                      │
│  Skills: analyzing-ueba-alerts-varonis, analyzing-email-audit-logs, ...     │
├─────────────────────────────────────────────────────────────────────────────┤
│  Vai trò: Công cụ phân tích chuyên sâu                                      │
│  Nhiệm vụ:                                                                  │
│  ├── analyzing-ueba-alerts-varonis → Phân tích UEBA alerts                  │
│  ├── analyzing-email-audit-logs → Phân tích email logs                      │
│  ├── analyzing-dlp-alerts → Phân tích DLP alerts                            │
│  └── analyzing-vpn-logs → Phân tích VPN logs (khi cần)                      │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## INVESTIGATION WINDOW: KHOANH VÙNG THỜI GIAN ĐIỀU TRA

### Nguyên tắc xác định Investigation Window

```
INVESTIGATION WINDOW = {
  start_date: Ngày nộp đơn nghỉ việc (Ngày nộp đơn),
  end_date: Ngày nghỉ thực tế (Ngày nghỉ dự kiến theo Đơn),
  buffer: +30 ngày sau ngày nghỉ thực tế (để phát hiện late-stage exfiltration)
}
```

### Cột quan trọng từ Watchlist

| Cột | Ý nghĩa | Ứng dụng |
|-----|---------|----------|
| **Ngày nộp đơn** | Thời điểm nhân sự chính thức thông báo nghỉ việc | Start của investigation window |
| **Ngày nghỉ dự kiến theo Đơn** | Ngày làm việc cuối cùng | End của investigation window |
| **Ngày hủy user đợt 1** | Thời điểm account bị vô hiệu hóa | Deadline cho containment |

### Phân loại theo Investigation Window

| Loại | Thời gian giữa nộp đơn và nghỉ | Mức độ rủi ro | Action |
|------|--------------------------------|---------------|--------|
| **Ngắn hạn** | < 7 ngày | 🔴 Cao nhất | Giám sát ngay, escalate nhanh |
| **Trung hạn** | 7-30 ngày | 🟠 Cao | Giám sát hàng ngày |
| **Dài hạn** | > 30 ngày | 🟡 Trung bình | Giám sát hàng tuần |

### Áp dụng cho Case Study

| User | Ngày nộp đơn | Ngày nghỉ | Window (ngày) | Loại | Ưu tiên |
|------|-------------|-----------|---------------|------|---------|
| Nguyễn Thị Diễm Hằng | 2025-12-29 | 2026-01-01 | 3 | Ngắn hạn | 🔴 P1 |
| Trần Thị Ngọc Châu | 2025-12-30 | 2026-01-02 | 3 | Ngắn hạn | 🔴 P1 |
| Lê Đình Văn | 2025-12-31 | 2026-01-09 | 9 | Trung hạn | 🟠 P2 |
| Trần Quang Quốc | 2026-01-05 | 2026-01-05 | 0 | Ngay lập tức | 🔴 P1 |
| Phan Minh Luân | 2026-01-05 | 2026-02-01 | 27 | Trung hạn | 🟠 P2 |

---

## LUỒNG PHỐI HỢP THEO TIER (WORKFLOW EXAMPLE)

### Scenario: Giám sát nhân sự "Ngan, Le Thi Kim"

**Thông tin từ HR:**
- Ngày nộp đơn: 2026-01-06
- Ngày nghỉ dự kiến: 2026-01-31
- Investigation window: 2026-01-06 → 2026-01-31 (+30 ngày buffer)

### Bước 1: Tier 1 - Master Triage

```
INPUT: Watchlist từ HR
PROCESS:
  ├── Đánh giá: Insider Threat - Pre-departure monitoring
  ├── Severity: P2 (High) - Nhân sự có quyền truy cập dữ liệu nhạy cảm
  ├── Investigation window: 2026-01-06 → 2026-02-28 (30 ngày buffer)
  └── Route: playbook-daily-watchlist-monitoring (Tier 2)
```

### Bước 2: Tier 2 - Daily Watchlist Monitoring

```
INPUT: 
  ├── User: Ngan, Le Thi Kim
  ├── Investigation window: 2026-01-06 → 2026-02-28
  └── Varonis logs trong window

PROCESS:
  ├── Checklist bằng chứng cần thu thập:
  │   ├── [ ] AD authentication logs trong window
  │   ├── [ ] Email sent/received/deleted logs
  │   ├── [ ] Fileserver access logs
  │   └── [ ] DLP alerts (nếu có)
  │
  ├── Gọi Tier 3 skills:
  │   ├── analyzing-ueba-alerts-varonis → Phân tích AD logs
  │   ├── analyzing-email-audit-logs → Phân tích email logs
  │   └── analyzing-dlp-alerts → Phân tích fileserver logs
  │
  └── Tổng hợp kết quả:
      ├── AD: 4,791 events (72% off-hours)
      ├── Email: 1,093 events (142 deleted)
      └── Fileserver: 658 events (folder delete detected)

OUTPUT:
  ├── Risk level: 🔴 CRITICAL
  ├── Red flags: Mass file delete, folder delete, off-hours activity
  └── Recommendation: Escalate to Tier 3 deep investigation
```

### Bước 3: Tier 3 - Technical Analysis

```
INPUT: Filtered logs từ Tier 2

PROCESS (analyzing-ueba-alerts-varonis):
  ├── Phân tích baseline behavior
  ├── So sánh với peer group
  ├── Tính risk score theo matrix
  └── Extract IOCs

OUTPUT (JSON):
{
  "user": "Ngan, Le Thi Kim",
  "risk_score": 24,
  "risk_level": "CRITICAL",
  "indicators": {
    "recent_hr_event": 8,
    "access_to_sensitive_data": 7,
    "unusual_data_volume": 4,
    "off_hours_activity": 5
  },
  "iocs": ["folder_delete", "mass_file_delete", "off_hours_access"]
}
```

### Bước 4: Tier 2 - Tổng hợp & Escalation

```
INPUT: Kết quả từ Tier 3

PROCESS:
  ├── Đánh giá: CRITICAL (risk score 24/28)
  ├── Communication plan:
  │   ├── T+0: Notify SOC Lead
  │   ├── T+15 min: Notify CISO
  │   └── T+4 hours: HR + Legal meeting
  │
  └── Containment recommendations:
      ├── Suspend user account
      ├── Revoke external email access
      └── Enhanced monitoring on fileserver

OUTPUT: Escalation report to CISO
```

---

## SKILL MAPPING THEO TIER CHUẨN

| Tier | Prefix | Skill | Vai trò |
|------|--------|-------|---------|
| **Tier 1** | `playbook-master-` | `playbook-master-incident-triage` | Tiếp nhận watchlist, phân loại, route |
| **Tier 2** | `playbook-[loại]-` | `playbook-daily-watchlist-monitoring` | Giám sát hàng ngày, correlation, báo cáo |
| **Tier 2** | `playbook-[loại]-` | `playbook-pre-departure-insider-review` | Review trước khi nhân sự nghỉ |
| **Tier 2** | `playbook-[loại]-` | `playbook-abnormal-user-behavior-response` | Phản ứng khi phát hiện bất thường |
| **Tier 3** | `[động_từ]-` | `analyzing-ueba-alerts-varonis` | Phân tích UEBA alerts |
| **Tier 3** | `[động_từ]-` | `analyzing-email-audit-logs` | Phân tích email logs |
| **Tier 3** | `[động_từ]-` | `analyzing-dlp-alerts` | Phân tích DLP alerts |
| **Tier 3** | `[động_từ]-` | `analyzing-vpn-logs` | Phân tích VPN logs |

---

## AUTOMATION PIPELINE (DAILY)

```
08:00 - Nhận logs từ Varonis (AD, Email, Fileserver)
        │
        ▼
08:05 - Tier 1: Master Triage
        ├── Nhận watchlist update từ HR
        ├── Xác định investigation window cho mỗi user
        └── Route sang Tier 2
        │
        ▼
08:10 - Tier 2: Daily Watchlist Monitoring
        ├── Chạy Process_AD_Log.py
        ├── Chạy Process_Email_Log.py
        ├── Chạy Process_Fileserver_Log.py
        └── Merge outputs
        │
        ▼
08:20 - Tier 3: Technical Analysis (gọi skills)
        ├── analyzing-ueba-alerts-varonis
        ├── analyzing-email-audit-logs
        └── analyzing-dlp-alerts
        │
        ▼
08:30 - Tier 2: Tổng hợp & Báo cáo
        ├── Cross-source correlation
        ├── Risk classification
        ├── Generate daily report
        └── Escalate nếu có CRITICAL
        │
        ▼
09:00 - Gửi báo cáo cho SOC Lead
```

---

## COMPLIANCE MAPPING

| Requirement | Regulation | How We Address |
|-------------|------------|----------------|
| Log retention | Luật An ninh mạng | Varonis logs retained 90 days |
| PII protection | NĐ 13/2023, NĐ 356/2025 | Sensitive file keyword monitoring |
| Breach notification | NĐ 356/2025 (36 hours) | Tier 2 escalation within 36 hours |
| Financial data protection | NHNN Circulars | Fileserver monitoring for financial folders |
| Access monitoring | NHNN Circulars | AD authentication monitoring |

---

## INVESTIGATION WINDOW VISUALIZATION

```
Timeline cho User "Ngan, Le Thi Kim":

2026-01-06          2026-01-31          2026-02-28
    │                   │                   │
    ├───── Window ──────┤──── Buffer ───────┤
    │  (Investigation)  │   (Monitoring)    │
    │                   │                   │
  Nộp đơn           Nghỉ việc         Hết buffer
                    (Last day)
                    
Focus: Giám sát chặt trong window
       Tiếp tục monitor trong buffer
       Escalate nếu phát hiện bất thường
```
