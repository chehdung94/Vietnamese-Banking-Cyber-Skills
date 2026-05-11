---
name: playbook-abnormal-user-behavior-response
description: Tier 2 Playbook to respond to abnormal user behavior incidents detected by UEBA systems. Provides checklists for evidence collection, behavioral analysis, insider threat assessment, and remediation steps for anomalous activities detected by Varonis or similar UEBA platforms.
domain: cybersecurity
subdomain: incident-response
tags: [playbook, tier-2, UEBA, abnormal-behavior, insider-threat, Varonis, incident-response, DFIR]
version: "1.2"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1074, T1114, T1098, T1526]
---

# Playbook: Abnormal User Behavior Response (Tier 2)

## Overview
This playbook guides the response to abnormal user behavior incidents detected by User and Entity Behavior Analytics (UEBA) systems such as Varonis, Microsoft Purview, or SpectorSoft. It covers evidence collection, behavioral analysis, insider threat assessment, and remediation steps. Designed specifically for Vietnamese banking environments where sensitive financial data requires enhanced monitoring.

## When to Use
Triggered by the **Master Incident Triage** playbook when an incident is classified as:
- UEBA Alert (Behavioral Anomaly)
- Abnormal Data Access Patterns
- Potential Insider Threat
- Compromised Account (Behavioral Detection)
- Privilege Abuse
- Data Exfiltration by Insider

---

## 1. UEBA Alert Analysis

### 1.1 Alert Information Collection
*Collect the following from your UEBA platform before starting investigation.*

| Check | Information | Source | Status |
|---|---|---|:---:|
| [ ] | **Alert ID / Rule Name** | UEBA Platform (Varonis, Purview) | Pending |
| [ ] | **Triggered User / Account** | Username, Employee ID, Department | Pending |
| [ ] | **Alert Severity** | Critical / High / Medium / Low | Pending |
| [ ] | **Detection Time** | When alert was triggered | Pending |
| [ ] | **Baseline Behavior** | Normal activity pattern for this user | Pending |
| [ ] | **Peer Group** | Users with similar roles/permissions | Pending |

### 1.2 Behavioral Baseline Assessment
*Compare current behavior against established baseline.*

| Metric | Baseline (Normal) | Observed (Anomaly) | Delta |
|--------|------------------|-------------------|-------|
| **Login Hours** | 08:00-18:00 | After hours access | +4h |
| **Data Access Volume** | ~50 files/day | 5000+ files accessed | +100x |
| **Unique Locations** | Office IP only | Multiple VPN locations | +3 locations |
| **Sensitive Data Access** | Rarely | Daily, large queries | New pattern |
| **Export Activity** | None | CSV/Excel exports | New activity |

---

## 2. Evidence Request Checklist (RFI)
*Agent Action: Present this checklist to collect necessary information for behavioral analysis.*

| Check | Evidence Type | Purpose | Source | Status |
|---|---|---|---|:---:|
| [ ] | **UEBA Alert Details** | Full alert context, risk score breakdown | Varonis/WebGUI, Purview Alerts | Pending |
| [ ] | **User Activity Logs** | Detailed activity timeline | Varonis DatAdvantage, Windows Security Logs | Pending |
| [ ] | **Authentication Logs** | Login/logout history, MFA events | Azure AD, AD FS, VPN Logs | Pending |
| [ ] | **Data Access Logs** | Files/folders accessed, permissions changes | Varonis, File Server Audit Logs | Pending |
| [ ] | **Email Activity** | Internal/external email patterns | Exchange, Email Gateway | Pending |
| [ ] | **Cloud Activity** | OneDrive, SharePoint, Azure Blob access | Cloud Audit Logs, CASB | Pending |
| [ ] | **Privileged Account Logs** | Admin actions, privilege escalations | PAM, Azure AD Privileged Identity Management | Pending |
| [ ] | **HR Records** | Employment status, recent HR events | HR System (offline) | Pending |
| [ ] | **Physical Access Logs** | Badge access, building entry | Physical Security System | Pending |

---

## 3. Insider Threat Risk Assessment

### 3.1 Risk Indicators Matrix
*Evaluate each indicator to determine insider threat probability.*

| Indicator | Weight | User Shows This? | Score |
|-----------|--------|------------------|-------|
| **Recent HR Event** | High | Yes/No/Unknown | 0-10 |
| **Access to Sensitive Data** | High | Yes/No/Unknown | 0-10 |
| **Unusual Data Volume** | Medium | Yes/No/Unknown | 0-5 |
| **Off-Hours Activity** | Medium | Yes/No/Unknown | 0-5 |
| **Personal Device Usage** | Medium | Yes/No/Unknown | 0-5 |
| **External Communication** | Low | Yes/No/Unknown | 0-3 |
| **Job Performance Issues** | Low | Yes/No/Unknown | 0-3 |

**Risk Classification:**
- **Critical (21-28):** Immediate investigation, consider suspension
- **High (14-20):** Priority investigation, enhanced monitoring
- **Medium (7-13):** Standard investigation
- **Low (0-6):** Monitor, potential false positive

### 3.1.1 Scoring Calibration Guide

> **⚠️ BẮT BUỘC:** Mỗi điểm phải có rationale từ log/evidence cụ thể. Không gán điểm dựa trên "cảm nhận".

| Indicator | 0-3 (Low Evidence) | 4-6 (Moderate) | 7-10 (Strong Evidence) |
|---|---|---|---|
| **Recent HR Event** | No HR event | Resignation submitted, standard offboarding | Resignation + disciplinary issues + sensitive data access |
| **Access to Sensitive Data** | No sensitive access | Indirect access (reports only) | Direct CRUD access to customer PII/financial data |
| **Unusual Data Volume** | No anomaly | Minor deviation (<2x baseline) | Major deviation (>5x baseline); multi-channel pattern |
| **Off-Hours Activity** | None | Occasional (<5/month) | Regular pattern; critical-timing events around resignation date |
| **Personal Device Usage** | None | Blocked attempts (Evidence=0) | Successful data copy; user ignores DLP block; multiple device types |
| **External Communication** | Business partners only | Unusual volume/timing to partners | Personal Gmail/Yahoo with attachments; standalone (no internal recipients) |
| **Job Performance Issues** | No info; satisfactory | Verbal warning | PIP or documented disciplinary action |

### 3.1.2 Scoring Rules (BẮT BUỘC)

- [ ] Mỗi điểm có rationale cụ thể (dòng log, timestamp, event ID)
- [ ] Điểm nằm trong phạm vi cho phép (không vượt max: 10/10/5/5/5/3/3)
- [ ] **Tổng = tổng trực tiếp 7 indicators. Không áp dụng "cap".** Phân loại dựa trên ngưỡng.
- [ ] Đã phân biệt "DLP BLOCKED (không mất dữ liệu)" vs "SUCCESSFUL exfiltration"
- [ ] Cross-user correlation đã thực hiện nếu ≥2 users
- [ ] Qualitative context được tách riêng (Section 3.5), không cộng vào official score

### 3.2 Behavioral Red Flags Checklist
- [ ] User download lượng lớn dữ liệu khách hàng (hàng ngàn records)
- [ ] Truy cập dữ liệu ngoài phạm vi công việc (không có business justification)
- [ ] Sửa đổi quyền truy cập (permission changes) không được authorize
- [ ] Sử dụng USB cá nhân hoặc cloud storage cá nhân
- [ ] In/copy tài liệu nhạy cảm (sensitive)
- [ ] Gửi email với file đính kèm lớn đến external addresses
- [ ] Đăng nhập từ location không quen thuộc / country khác
- [ ] Thay đổi hành vi đột ngột (ví dụ: 6 tháng đầu bình thường, 1 tháng gần đây bất thường)
- [ ] Copy dữ liệu vào thư mục ít người dùng hoặc thư mục cá nhân
- [ ] Sử dụng service accounts hoặc shared accounts

---

## 3B. File Activity Anomaly Detection (Phát hiện Bất thường Hành vi Tệp)

*Phương pháp phát hiện dựa trên kinh nghiệm thực tế xử lý sự vụ an ninh mạng, tập trung vào 2 nhóm dữ liệu chính.*

### 3B.1 Nhóm 1: Giám sát Hoạt động Tệp tin (File Activity Monitoring)

*Theo dõi các thao tác liên quan đến tệp tin với ngưỡng cảnh báo cụ thể.*

| Thao tác | Ngưỡng Cảnh báo | Mức độ | Mô tả |
|----------|------------------|--------|-------|
| **Mở tệp hàng loạt** | ≥ 5,000 tệp trong ≤ 4 phút | 🔴 Cao | Phản ánh hoạt động sao chép hoặc truy xuất dữ liệu hàng loạt |
| **Xóa tệp** | Vượt ngưỡng baseline + 3σ | 🟡 Trung bình | Có thể là cố gắng che giấu dấu vết |
| **Cắt (Move) tệp** | Vượt ngưỡng baseline + 3σ | 🟡 Trung bình | Di chuyển dữ liệu ra khỏi vị trí thông thường |
| **Tải tệp xuống** | Vượt ngưỡng baseline + 3σ | 🔴 Cao | Khối lượng tải vượt mức bình thường |
| **Tạo file nén** | > 500MB, file .zip/.rar/.7z | 🔴 Cao | Chuẩn bị dữ liệu để exfiltrate |

### 3B.2 Nhóm 2: Phân tích So sánh Hành vi (Behavioral Comparison Analysis)

*So sánh hành vi người dùng qua các khoảng thời gian để xác định deviant behavior.*

#### Công thức Tính Baseline

```
Hành vi Trung bình = Σ(μ) / n
Trong đó n = số ngày quan sát (1, 3, hoặc 7 ngày)

Ngưỡng Cảnh báo = μ + 3σ (3 lần độ lệch chuẩn)
```

#### Các Chỉ số Thu thập

| Chỉ số | Mô tả | Đơn vị |
|--------|-------|---------|
| **Số tệp truy cập** | Số lượng file mở/đọc | files/day |
| **Số lần đăng nhập** | Login count | logins/day |
| **Khối lượng dữ liệu** | Data transmitted | MB/day |
| **Thời gian hoạt động** | Business hours | hours |
| **Thời gian ngoài giờ** | After hours | hours |
| **Số thiết bị** | Unique devices used | devices |

#### Quy tắc Kích hoạt Cảnh báo

```
IF (current_activity > baseline + 3σ) THEN
    severity = "HIGH"
    trigger_investigation = TRUE
END IF
```

### 3B.3 Phân tích Log để Phát hiện Bất thường

*Ngưỡng cảnh báo được xác định dựa trên việc phân tích file log thực tế, không phải giá trị cố định.*

#### Phương pháp Phân tích Log

1. **Thu thập Log từ các nguồn:**
   - Windows Event Logs (Security 4656, 4663, 4670)
   - File Server Audit Logs (Varonis, NTFS Auditing)
   - EDR Process Events (CrowdStrike, Defender ATP)
   - Cloud Storage Sync Logs (OneDrive, SharePoint)

2. **Xây dựng Baseline từ dữ liệu thực tế:**
   ```
   Baseline = μ (trung bình) của hoạt động trong 7-30 ngày
   Độ lệch chuẩn = σ (standard deviation)
   Ngưỡng = μ + 3σ (tự động tính toán từ log)
   ```

3. **So sánh Hoạt động Hiện tại với Baseline:**
   - Nếu hoạt động hiện tại vượt ngưỡng μ + 3σ → Cảnh báo
   - Nếu hoạt động hiện tại vượt ngưỡng μ + 5σ → Cảnh báo ngay lập tức

#### Các Chỉ số Cần Phân tích từ Log

| Chỉ số | Nguồn Log | Cách phát hiện |
|---------|-----------|----------------|
| **Tần suất truy cập file** | Windows Security, Varonis | Đếm số lượng event ID 4656/4663 trên thư mục |
| **Khối lượng dữ liệu** | File Server, Cloud Storage | Tổng hợp size của files accessed |
| **Thời gian truy cập** | Security Event Logs | Kiểm tra timestamp có nằm ngoài giờ làm việc |
| **Loại file truy cập** | File extension logging | Kiểm tra có truy cập .pst, .bak, .csv, .xlsx |
| **Vị trí truy cập** | IP, hostname | So sánh với danh sách thiết bị thông thường |
| **Tốc độ truy cập** | Timestamp analysis | Tính khoảng cách thời gian giữa các event |

#### Ví dụ Phân tích Log Thực tế

```
Nguồn: Windows Security Log (Event ID 4663)
Mẫu log:
  "TimeCreated: 2024-01-15T14:35:00Z
   User: DOMAIN\huyhq
   Object: \FileServer\HR\Salary_2024.xlsx
   AccessMask: READ_DATA"

Phân tích:
  - Số lượng file accessed trong 1 phút: 150 files
  - So sánh với baseline của user này: 5-10 files/phút
  - Tỷ lệ: 150/10 = 15x ngưỡng bình thường
  - Kết luận: BẤT THƯỜNG → Cảnh báo được kích hoạt
```

### 3B.4 Ví dụ Phát hiện Thực tế

```json
{
  "user": "huyhq@domain.com",
  "timestamp": "2024-01-15T14:35:00Z",
  "detection": {
    "pattern": "MassFileAccess",
    "files_opened": 5420,
    "time_window_minutes": 3.5,
    "files_per_minute": 1548,
    "threshold_applied": "> 500 files/4 minutes",
    "result": "EXCEEDED",
    "severity": "CRITICAL"
  },
  "risk_indicators": {
    "unusual_volume": true,
    "rapid_succession": true,
    "after_hours": false,
    "new_device": false
  },
  "recommended_action": "Immediate investigation required"
}
```

### 3B.5 Evidence Collection cho File Activity

| Check | Nguồn Log | Mục đích |
|-------|-----------|----------|
| [ ] | **EDR Solution** (CrowdStrike, Defender) | Process execution, file access |
| [ ] | **Varonis DatAdvantage** | File server activity, permission changes |
| [ ] | **Windows Event Logs** (Security 4656, 4663) | File open/modify operations |
| [ ] | **DLP Alerts** | Sensitive data access triggers |
| [ ] | **Cloud Storage Logs** | OneDrive/SharePoint sync activity |

---

## 3C. Cross-User Correlation (COLLUSION DETECTION) 🆕

> **Kích hoạt:** Khi có ≥2 users trong cùng scope điều tra. Nếu chỉ có 1 user, bỏ qua section này.

### 3C.1 Collusion Detection Procedure

1. **Lập intersection matrix:** Xác định mối quan hệ giữa các user (cùng phòng ban? Cùng dự án? Cùng manager?)
2. **Communication pattern:** Tìm email giữa các user (nội bộ và ra ngoài)
3. **Cross-mailbox check (CRITICAL):** Tìm Gmail cá nhân của User A xuất hiện trong recipients của User B (và ngược lại)
4. **Timeline correlation:** Đối chiếu timeline hoạt động giữa các user – có coordinated activity?
5. **Shared resources:** Kiểm tra file/folder/application được cả 2 user truy cập

### 3C.2 Collusion Indicators

| Indicator | Severity | Action |
|---|---|---|
| User A gửi email standalone đến Gmail cá nhân của User B | 🔴 CRITICAL | Escalate immediately |
| User A và B có shared external communication pattern (cùng gửi đến 1 Gmail) | 🟠 HIGH | Expand investigation |
| User A và B hoạt động cùng thời điểm bất thường (off-hours pattern giống nhau) | 🟡 MEDIUM | Flag for review |
| User A xuất hiện trong mailbox forwarding rules của User B | 🔴 CRITICAL | Escalate immediately |

### 3C.3 Output Example

```json
{
  "cross_user_analysis": {
    "users_in_scope": ["luannt0500", "tanvn"],
    "relationship": "Same department, both department heads, both departing",
    "collusion_detected": true,
    "collusion_events": [{
      "date": "2026-04-21",
      "from": "tanvn",
      "to": "thanhluan22022@gmail.com",
      "type": "standalone_outbound",
      "severity": "CRITICAL",
      "note": "tanvn sent standalone email to luannt0500's personal Gmail"
    }]
  }
}
```

---

## 3D. DLP Effectiveness Assessment 🆕

> **Áp dụng:** Khi có DLP/Device Control events trong scope.

### 3D.1 DLP Event Classification

| Scenario | DLP Status | Behavioral Risk | Ví dụ từ Case Study |
|---|---|---|---|
| **BLOCKED + Evidence=0** | ✅ DLP hoạt động | 🔴 Hành vi cố tình | ADATA HV320 28 phút bị block |
| **BLOCKED + Evidence>0** | ⚠️ Có lọt một phần | 🔴 CRITICAL | DLP block nhưng data vẫn thoát |
| **ALLOWED + No policy** | ❌ Lỗ hổng DLP | 🔴 CRITICAL | OneDrive cá nhân không bị chặn |
| **NOT DETECTED** | ❌ Blind spot | 🔴 CRITICAL | OUTGOING_HTTP bypass proxy |

### 3D.2 DLP Effectiveness Matrix (Output)

| Protection Layer | Total Events | Blocked | Allowed (Gap) | Effectiveness |
|---|---|---|---|---|
| Device Control (USB/HDD) | X | X (100%) | 0 | ✅ Effective |
| Device Control (MTP) | X | X (100%) | 0 | ✅ Effective |
| Web Filter (mail.google.com) | X | X (100%) | 0 | ✅ Effective |
| Web Filter (onedrive.live.com) | X | 0 | X (100%) | ❌ Gap: add to block list |
| Email DLP (SMTP→Gmail) | X | 0 | X (100%) | ❌ Gap: deploy DLP rule |
| Endpoint (OUTGOING_HTTP) | X | 0 (detected only) | X | ⚠️ Partial: detect but not block |

---

## 3E. Expanded Risk Context (Qualitative) 🆕

> **⚠️ QUAN TRỌNG:** Phần này chứa các phát hiện bổ sung từ multi-source analysis **KHÔNG được cộng vào điểm chính thức** ở Section 3.1.

Các phát hiện dưới đây nằm ngoài phạm vi 7 indicators của Risk Indicators Matrix nhưng hỗ trợ cho kết luận:

| # | Phát hiện | Mức độ | Nguồn | Ghi chú |
|---|---|---|---|---|
| E1 | Virus/Malware detected on endpoint | 🔴 CRITICAL | Endpoint Security | Đặc biệt nếu từ USB cá nhân |
| E2 | Access Protection rule violation by user | 🔴 CRITICAL | Endpoint Security | Hành vi truy cập trái phép |
| E3 | HTTP traffic bypassing web proxy | 🔴 HIGH | Endpoint + Proxy | Automated/scripted pattern |
| E4 | Webmail upload attempts (blocked) | 🟠 MEDIUM | Web Gateway | Cho thấy ý định dùng webmail |
| E5 | Cross-mailbox Gmail appearance (collusion) | 🔴 CRITICAL | Email | Cần investigation mở rộng |

---

## 4. Investigation Steps

### Step 4.1: Initial Alert Triage
*Review the UEBA alert and determine if it's a true positive or false positive.*

1. **Verify Alert Authenticity:**
   - Check if user was on vacation (explains off-hours access)
   - Verify if user had legitimate project requiring data access
   - Confirm if user was using company-approved automation/script

2. **Check for Known Context:**
   - Is user undergoing performance improvement plan (PIP)?
   - Did user recently submit resignation?
   - Did user receive promotion/new role (legitimate new access)?

3. **Calculate Risk Score:**
   - Apply Risk Indicators Matrix above
   - Document decision rationale

### Step 4.2: Data Access Pattern Analysis
*Analyze what data the user accessed and if it aligns with job responsibilities.*

```powershell
# Example: Export user's file access from Varonis
# (Requires Varonis Export Reports or API access)
# Reference: analyzing-ueba-alerts-varonis skill

# Data to analyze:
# - Top 10 folders accessed
# - Sensitive data exposure count
# - Unusual file types accessed (.pst, .bak, .csv)
# - After-hours access percentage
```

| Question | Answer |
|-----------|--------|
| User có quyền truy cập data accessed không? | Yes/No |
| Access pattern có phù hợp với job role không? | Yes/No |
| Có access vào sensitive folders không? | Yes/No |
| Volume có vượt ngưỡng bình thường không? | Yes/No |
| Có pattern of "low and slow" exfiltration không? | Yes/No |

### Step 4.3: Network and Communication Analysis
*Check for data exfiltration indicators.*

| Channel | What to Check | Red Flag Threshold |
|---------|--------------|-------------------|
| **Email** | External sends, attachments, BCC rules | >5 external emails with attachments |
| **OneDrive/SharePoint** | Uploads, sharing links | Any personal cloud storage |
| **USB** | File copies, device connections | Any USB activity with sensitive data |
| **VPN** | Connection duration, data volume | >10GB transferred |
| **Print** | Print jobs with sensitive documents | Any sensitive document printed |

### Step 4.4: Timeline Reconstruction
*Build a comprehensive timeline of user activities.*

```
Timeline Format:
[YYYY-MM-DD HH:MM] - Event Description - Source
─────────────────────────────────────────────────
2024-01-15 08:30 - Login to workstation - AD
2024-01-15 08:35 - Access shared HR folder - Varonis
2024-01-15 08:40 - Download 2500 employee records - Varonis
2024-01-15 08:55 - Upload to personal OneDrive - Cloud Audit
2024-01-15 09:00 - Send email to personal Gmail - Exchange
... (continue for full day)
```

---

## 5. Internal Communication Plan

*   **T+15 Phút (Alert triaged):**
    *   Thông báo Security Lead: *"UEBA alert triggered for [User]. Đang điều tra."*
    *   Xác định initial risk level

*   **T+1 Giờ (Preliminary investigation):**
    *   Nếu High/Critical: Notify CISO
    *   IT Ops: Enable enhanced monitoring on user account
    *   HR: Check for recent HR events (offline, discreet)

*   **T+4 Giờ (Risk assessment complete):**
    *   CISO + Legal + HR meeting (if High/Critical)
    *   Quyết định có suspend user không
    *   Prepare communication plan

*   **T+24 Giờ (Investigation complete):**
    *   Final report to CISO
    *   Quyết định disciplinary/action steps
    *   Update detection rules if false positive

---

## 6. Interim Recommendations (Khuyến nghị Tạm thời)

### For Suspected Malicious Insider
1. **Enhanced Monitoring** (không block):
   - Enable real-time alerting on user's account
   - Monitor email and file transfers
   - Review badge access logs

2. **Access Restrictions** (if evidence strong):
   - Remove access to sensitive data temporarily
   - Disable external email forwarding rules
   - Block USB device usage (if DLP supports)

3. **If Confirmed Malicious:**
   - Coordinate with HR and Legal
   - Preserve evidence (do NOT notify user)
   - Plan for account termination (if termination decided)

### For Compromised Account (not malicious insider)
1. **Immediate Containment:**
   - Force password reset
   - Revoke all active sessions
   - Enable MFA if not already

2. **Investigation Priority:**
   - Determine attack vector (phishing, credential theft)
   - Check for other compromised accounts
   - Review lateral movement indicators

---

## 7. Remediation and Hardening

### 7.1 Short-term Actions
| Action | Description | Priority |
|--------|-------------|----------|
| Remove sensitive access | Temporarily revoke access until investigation complete | P1 |
| Force password reset | If compromised account suspected | P1 |
| Enable MFA | If not already enabled | P1 |
| Communication with user | Only after HR/Legal approval | P2 |

### 7.2 Long-term Controls
| Control | Purpose | Implementation |
|---------|---------|----------------|
| **Data Access Governance** | Limit access to need-to-know | Varonis DataPrivilege |
| **UEBA Alert Tuning** | Reduce false positives | Review and tune rules |
| **DLP Policies** | Detect future exfiltration attempts | Microsoft Purview/Varonis |
| **Security Awareness** | Reduce phishing/ransomware risk | Training program |
| **Privileged Access Monitoring** | Detect privilege abuse | PAM solution |

---

## 8. Expected Output / Reporting

### 8.1 Behavioral Analysis Report (JSON)
```json
{
  "incident_id": "UEBA-YYYYMMDD-XXX",
  "status": "InvestigationComplete/FalsePositive/Confirmed",
  "alert_info": {
    "alert_id": "Varonis-XXXXX",
    "rule_name": "Excessive Data Access",
    "severity": "High",
    "triggered_at": "YYYY-MM-DDTHH:MM:SSZ"
  },
  "user_profile": {
    "username": "huyhq",
    "department": "IT",
    "role": "Developer",
    "tenure_years": 3,
    "risk_indicators": {
      "recent_hr_event": false,
      "access_outside_scope": true,
      "unusual_volume": true,
      "off_hours_activity": true
    }
  },
  "analysis": {
    "risk_score": 18,
    "risk_level": "High",
    "data_accessed": {
      "total_files": 5420,
      "sensitive_files": 342,
      "data_types": ["PII", "Financial"]
    },
    "exfiltration_indicators": {
      "personal_cloud": true,
      "external_email": false,
      "usb_activity": false
    }
  },
  "recommendation": "InvestigateAsInsiderThreat",
  "actions_taken": ["EnhancedMonitoring", "HRConsultation"],
  "next_steps": ["HRMeeting", "UserInterview"]
}
```

### 8.2 Executive Summary Template
```markdown
## Báo cáo UEBA - Hành vi bất thường người dùng

**Thông tin người dùng:**
- Username: [Name]
- Phòng ban: [Department]
- Chức vụ: [Position]
- Thời gian làm việc: [Tenure]

**Mô tả bất thường:**
[Description of detected anomaly]

**Mức độ rủi ro:**
- Điểm rủi ro: [X]/28
- Mức độ: [Critical/High/Medium/Low]

**Bằng chứng chính:**
1. [Evidence point 1]
2. [Evidence point 2]
3. [Evidence point 3]

**Khuyến nghị:**
- [ ] Bước 1
- [ ] Bước 2

**Quyết định của Ban Giám đốc:**
[Decision box]
```

---

## 9. False Positive Handling
*If investigation determines this was a false positive:*

- [ ] Document why alert was false positive
- [ ] Note legitimate business reason
- [ ] Update UEBA baseline for this user/peer group
- [ ] Submit rule tuning request to security team
- [ ] Monitor for similar alerts in next 30 days

---

## Related Skills
- [`analyzing-ueba-alerts-varonis`](analyzing-ueba-alerts-varonis/SKILL.md) - Detailed Varonis UEBA alert analysis
- [`playbook-data-leak-response`](playbook-data-leak-response/SKILL.md) - Data breach response (if exfiltration confirmed)
- [`playbook-master-incident-triage`](../playbook-master-incident-triage/SKILL.md) - Entry point for incident classification
