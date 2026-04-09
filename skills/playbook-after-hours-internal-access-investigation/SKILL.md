---
name: playbook-rdp-lateral-movement-response
description: Tier 2 Playbook to investigate RDP lateral movement between internal users during after-hours. User A accessing User B's computer via RDP outside business hours. Covers compromised account detection, lateral movement analysis, and evidence preservation for banking environments.
domain: cybersecurity
subdomain: incident-response
tags: [playbook, tier-2, rdp, lateral-movement, after-hours, compromised-account, insider-threat, banking, vietnam]
version: "1.1"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1021.001, T1078, T1098, T1071, T1040, T1003]
nist_csf: [DE.CM-3, DE.AE-3, PR.AC-4, RS.MI-1]
---

# Playbook: RDP Lateral Movement Response (Tier 2)

> **Tier:** 2 - Incident Response Playbook
> **Triggered by:** [`playbook-master-incident-triage`](Vietnamese-Banking-Cyber-Skills/skills/playbook-master-incident-triage/SKILL.md)
> **Calls Tier 3:** `analyzing-windows-event-logs`, `collecting-volatile-evidence`, `analyzing-ueba-alerts-varonis`

## Overview

Playbook này hướng dẫn điều tra sự cố **User A truy cập vào máy tính của User B qua RDP vào ngoài giờ làm việc**.

**Scenario:**
- User A (Source) → RDP → User B's Computer (Target)
- Thời gian: Ngoài giờ làm việc (22:00-06:00, cuối tuần, ngày lễ)
- Phương thức: Remote Desktop Protocol (RDP)

**Các khả năng:**
1. **Compromised Account:** User A bị chiếm đoạt, attacker di chuyển ngang
2. **Insider Threat:** User A cố tình truy cập trái phép máy User B
3. **Privilege Abuse:** User A có quyền nhưng sử dụng sai mục đích
4. **Legitimate:** Có lý do chính đáng (IT support, dự án khẩn)

**Ngữ cảnh áp dụng:** Ngân hàng Việt Nam, tuân thủ Nghị định 13/2023/NĐ-CP và các quy định của NHNN.

## When to Use

Được kích hoạt bởi **Master Incident Triage** khi phát hiện:
- SIEM Alert: RDP connection outside business hours
- Windows Event 4624 (Logon) + 4648 (Explicit credential) từ User A đến máy User B
- EDR Alert: Lateral movement detected
- User B báo cáo: "Tôi thấy ai đó đang dùng máy tôi"
- Varonis Alert: Access from unusual source user

**Trigger Conditions:**
- [ ] User A RDP đến máy User B ngoài giờ làm việc
- [ ] User A không phải IT support hoặc không có ticket hỗ trợ
- [ ] User B không biết về việc truy cập này
- [ ] Có dấu hiệu data access hoặc file transfer sau khi RDP

---

## 1. Initial Triage & Classification

### 1.1 Quick Assessment Checklist

| Check | Question | Yes | No |
|-------|----------|-----|-----|
| ☐ | User A có quyền RDP đến máy User B không? | P3 | P2 |
| ☐ | User A là IT support/helpdesk? | P3 | P2 |
| ☐ | Có ticket hỗ trợ hợp lệ? | P4 | P2 |
| ☐ | User B xác nhận cho phép? | P4 | P2 |
| ☐ | Có dấu hiệu data exfiltration? | P1 | P2 |
| ☐ | User A có dấu hiệu compromised? | P1 | P2 |

### 1.2 Severity Classification

| Severity | Điều kiện | Thời gian phản hồi |
|----------|-----------|-------------------|
| **P1 - Critical** | Data exfiltration confirmed + Unauthorized RDP + Sensitive data | < 30 phút |
| **P2 - High** | Unauthorized RDP + After-hours + No business justification | < 1 giờ |
| **P3 - Medium** | Authorized RDP nhưng outside policy | < 4 giờ |
| **P4 - Low** | Legitimate IT support activity | < 24 giờ |

### 1.3 Scenario Classification

| Scenario | Indicators | Action |
|----------|------------|--------|
| **Compromised User A** | User A login từ unusual location, sau đó RDP đến nhiều máy | Isolate User A, investigate scope |
| **Insider Threat** | User A có history truy cập máy User B, có mâu thuẫn | HR + Legal involvement |
| **Privilege Abuse** | User A có quyền admin, sử dụng sai mục đích | Manager review, policy update |
| **Legitimate** | Có ticket, User B đồng ý, trong phạm vi công việc | Document, close incident |

---

## 2. Execution Steps (Các bước Thực thi)

> **Agent Action:** Sau khi thu thập evidence từ Section 3 (RFI), thực hiện các bước sau để phân tích.

### Step 2.1: RDP Connection Analysis (Tier 3 Skill Call)

**Skill:** `analyzing-windows-event-logs` (hoặc manual analysis nếu chưa có skill)

**Input:** Security.evtx từ User B's PC và User A's PC
**Output:** RDP session timeline, source IP, session duration

```powershell
# Agent sẽ chạy hoặc hướng dẫn analyst chạy:
# Event 4624 Type 10 - RDP Logon
# Event 4648 - Explicit Credentials
# Event 4778/4779 - Session reconnect/disconnect
```

### Step 2.2: User A Authentication Analysis (Tier 3 Skill Call)

**Skill:** [`analyzing-azure-ad-signin-logs`](Vietnamese-Banking-Cyber-Skills/skills/analyzing-azure-ad-signin-logs/SKILL.md)

**Input:** User A's UPN, time range
**Output:** Login location, device, impossible travel detection

### Step 2.3: Data Access Analysis (Tier 3 Skill Call)

**Skill:** [`analyzing-ueba-alerts-varonis`](Vietnamese-Banking-Cyber-Skills/skills/analyzing-ueba-alerts-varonis/SKILL.md)

**Input:** User A's username, User B's computer name, time range
**Output:** Files accessed, data volume, sensitivity classification

### Step 2.4: Forensic Collection (Tier 3 Skill Call - nếu cần)

**Skill:** [`collecting-volatile-evidence-from-compromised-host`](Vietnamese-Banking-Cyber-Skills/skills/collecting-volatile-evidence-from-compromised-host/SKILL.md)

**Input:** User B's computer (target)
**Output:** Memory dump, network connections, process list

---

## 3. Evidence Request Checklist (RFI)

### 3.1 RDP Connection Evidence (QUAN TRỌNG NHẤT)

| Check | Evidence Type | Purpose | Source | Status |
|-------|---------------|---------|--------|--------|
| ☐ | **Windows Event 4624 (Target - User B's PC)** | RDP logon success | User B's Computer | Pending |
| ☐ | **Windows Event 4625 (Target - User B's PC)** | RDP logon failure | User B's Computer | Pending |
| ☐ | **Windows Event 4648 (Source - User A's PC)** | Explicit credential use | User A's Computer | Pending |
| ☐ | **Windows Event 4778/4779** | Session reconnect/disconnect | User B's Computer | Pending |
| ☐ | **TerminalServices-RemoteConnectionManager** | RDP connection details | User B's Computer | Pending |
| ☐ | **Network Connection Logs** | RDP port 3389 traffic | Firewall, EDR | Pending |

### 2.2 User A (Source) Evidence

| Check | Evidence Type | Purpose | Source | Status |
|-------|---------------|---------|--------|--------|
| ☐ | **User A's Login History** | How did User A authenticate? | Azure AD, AD | Pending |
| ☐ | **User A's Location** | Where did User A login from? | VPN Logs, Azure AD | Pending |
| ☐ | **User A's Device** | What device did User A use? | Azure AD, EDR | Pending |
| ☐ | **User A's Recent Activity** | What did User A do before RDP? | Varonis, EDR | Pending |
| ☐ | **User A's Email/Chat** | Any communication về việc này? | Exchange, Teams | Pending |

### 2.3 User B (Target) Evidence

| Check | Evidence Type | Purpose | Source | Status |
|-------|---------------|---------|--------|--------|
| ☐ | **User B's Computer - Event Logs** | What happened sau khi RDP? | User B's Computer | Pending |
| ☐ | **User B's Computer - File Access** | Files nào được truy cập? | Varonis, Windows Events | Pending |
| ☐ | **User B's Computer - Process Execution** | Chương trình nào chạy? | EDR, Sysmon | Pending |
| ☐ | **User B's Computer - Network Activity** | Có kết nối ra ngoài? | EDR, Firewall | Pending |
| ☐ | **User B's Computer - USB Activity** | Có copy ra USB? | Registry, EDR | Pending |
| ☐ | **User B's Interview** | User B biết gì? | HR/Security Interview | Pending |

### 2.4 Lateral Movement Evidence

| Check | Evidence Type | Purpose | Source | Status |
|-------|---------------|---------|--------|--------|
| ☐ | **RDP Bitmap Cache** | Screenshots của RDP session | User B's Computer | Pending |
| ☐ | **RDP Clipboard History** | Copy/paste giữa các máy | Registry, Memory | Pending |
| ☐ | **Jump Lists** | Ứng dụng mở trong RDP | User B's Computer | Pending |
| ☐ | **Prefetch Files** | Ứng dụng chạy trong RDP | User B's Computer | Pending |
| ☐ | **Network Shares Accessed** | Shares nào được truy cập? | Windows Events | Pending |

### 3.5 Context Evidence

| Check | Evidence Type | Purpose | Source | Status |
|-------|---------------|---------|--------|--------|
| ☐ | **IT Support Ticket** | Có ticket hỗ trợ? | ITSM System | Pending |
| ☐ | **Manager Approval** | Manager có biết? | Email, Interview | Pending |
| ☐ | **User B's Permission** | User B có cho phép? | Interview | Pending |
| ☐ | **Business Justification** | Lý do chính đáng? | Interview, Email | Pending |
| ☐ | **HR Records** | Mâu thuẫn giữa A và B? | HR System | Pending |

---

## 4. Investigation Procedures (Chi tiết kỹ thuật)

> **Note:** Các bước chi tiết dưới đây được thực hiện thông qua Tier 3 Skills được gọi ở Section 2.

### 3.1 Phase 1: RDP Connection Analysis

#### Step 3.1.1: Collect RDP Events from Target (User B's Computer)

```powershell
# Event 4624: An account was successfully logged on
# Logon Type 10 = RemoteInteractive (RDP)
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4624
    StartTime = (Get-Date).AddDays(-1)
} | Where-Object { $_.Message -like "*Logon Type:.*10*" -and $_.Message -like "*User A*" } |
Select-Object TimeCreated, Id, LevelDisplayName, Message

# Event 4778: A session was reconnected to a Window Station
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4778
    StartTime = (Get-Date).AddDays(-1)
} | Select-Object TimeCreated, Message

# Event 4779: A session was disconnected from a Window Station
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4779
    StartTime = (Get-Date).AddDays(-1)
} | Select-Object TimeCreated, Message
```

**Key Information to Extract:**
- Source IP (IP của User A)
- Logon Time
- Logoff Time (nếu có)
- Session Duration
- Source Workstation Name

#### Step 3.1.2: Collect Explicit Credential Events from Source (User A's Computer)

```powershell
# Event 4648: A logon was attempted using explicit credentials
# This shows User A intentionally used credentials to connect to User B's PC
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4648
    StartTime = (Get-Date).AddDays(-1)
} | Where-Object { $_.Message -like "*Target Server:.*User B*" -or $_.Message -like "*Target Domain Name:.*User B*" } |
Select-Object TimeCreated, Message
```

**Key Information to Extract:**
- Time of credential use
- Target Server (User B's computer)
- Process name (mstsc.exe = RDP client)
- IP address of target

#### Step 3.1.3: RDP Connection Timeline

```
RDP Session Timeline:
═══════════════════════════════════════════════════════════

[Time]          [Event]                           [Source]
───────────────────────────────────────────────────────────
02:15:30        User A login to workstation       Azure AD
02:16:45        Event 4648 (Explicit creds)       User A's PC
02:17:00        Event 4624 Type 10 (RDP success)  User B's PC
02:17:30        Session established               User B's PC
02:45:00        File access: salary_data.xlsx     Varonis
03:00:00        Event 4779 (Session disconnect)   User B's PC
03:05:00        User A logout                     Azure AD

Session Duration: 43 minutes
Files Accessed: [List]
Data Transferred: [Volume]
```

### 3.2 Phase 2: User A (Source) Investigation

#### Step 3.2.1: User A Authentication Analysis

```powershell
# Check how User A authenticated before RDP
Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq 'userA@domain.com'" |
    Where-Object { $_.CreatedDateTime -gt (Get-Date).AddHours(-2) } |
    Select-Object CreatedDateTime, IPAddress, Location, DeviceDetail, Status
```

**Questions to Answer:**
- User A login từ đâu? (Office, VPN, External?)
- User A có mặt tại văn phòng không? (Badge logs)
- User A sử dụng device nào? (Company device, Personal?)
- User A có dấu hiệu compromised? (Impossible travel, new device)

#### Step 3.2.2: User A Behavior Analysis

| Check | Normal | Observed | Risk |
|-------|--------|----------|------|
| Login Location | Office/VPN | External IP | High |
| Login Time | 08:00-18:00 | 02:15 AM | High |
| Device | Company laptop | Unknown device | High |
| Activity Before RDP | Normal work | Suspicious activity | High |
| RDP Pattern | IT support only | Random user access | Critical |

**Escalation:** Nếu User A có dấu hiệu compromised → P1, isolate User A

### 3.3 Phase 3: User B (Target) Investigation

#### Step 3.3.1: Post-RDP Activity Analysis

```powershell
# Check what happened on User B's computer after RDP
# Event 4663: An attempt was made to access an object
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4663
    StartTime = (Get-Date).AddHours(-2)
} | Where-Object { $_.Message -like "*User A*" } |
Select-Object TimeCreated, Message

# Check process execution
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-Sysmon/Operational'
    ID = 1
    StartTime = (Get-Date).AddHours(-2)
} | Where-Object { $_.Message -like "*User A*" } |
Select-Object TimeCreated, Message
```

**Key Activities to Identify:**
- Files accessed (sensitive data?)
- Applications opened (email, browser, file manager?)
- Network connections (cloud upload?)
- USB devices connected
- Commands executed (PowerShell, CMD?)

#### Step 3.3.2: Data Access Pattern

```json
{
  "user_b_computer_analysis": {
    "rdp_session": {
      "start_time": "2024-01-15T02:17:00Z",
      "end_time": "2024-01-15T03:00:00Z",
      "duration_minutes": 43,
      "source_user": "User A"
    },
    "files_accessed": [
      {
        "path": "C:\\Users\\UserB\\Documents\\salary_data.xlsx",
        "time": "2024-01-15T02:45:00Z",
        "action": "Read",
        "sensitivity": "High"
      },
      {
        "path": "C:\\Users\\UserB\\Documents\\customer_list.xlsx",
        "time": "2024-01-15T02:50:00Z",
        "action": "Read",
        "sensitivity": "Critical"
      }
    ],
    "applications_opened": [
      "explorer.exe",
      "chrome.exe",
      "winzip.exe"
    ],
    "network_connections": [
      {
        "destination": "personal-onedrive.com",
        "port": 443,
        "bytes_sent": 52428800
      }
    ]
  }
}
```

### 3.4 Phase 4: Lateral Movement Scope

#### Step 3.4.1: Check for Additional Targets

```powershell
# Check if User A RDP to other computers
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    ID = 4648
    StartTime = (Get-Date).AddDays(-1)
} | Where-Object { $_.Message -like "*User A*" -and $_.Message -like "*mstsc.exe*" } |
Group-Object { $_.Message -match "Target Server: (.*)" } |
Select-Object Name, Count
```

**Scope Assessment:**
- User A chỉ RDP đến User B?
- User A RDP đến nhiều máy? (Widespread compromise)
- Có dấu hiệu worm-like behavior?

#### Step 3.4.2: RDP Cache Analysis

```powershell
# RDP Bitmap Cache location
# C:\Users\UserA\AppData\Local\Microsoft\Terminal Server Client\Cache

# List cache files
Get-ChildItem -Path "C:\Users\UserA\AppData\Local\Microsoft\Terminal Server Client\Cache" -Recurse

# Hash for evidence
Get-FileHash -Path "C:\Users\UserA\AppData\Local\Microsoft\Terminal Server Client\Cache\*" -Algorithm SHA256
```

**Note:** RDP cache có thể chứa screenshots của session, cần thu thập cho forensics.

---

## 5. Context Validation

### 4.1 Business Justification Checklist

| Check | Question | Evidence | Result |
|-------|----------|----------|--------|
| ☐ | User A có phải IT support? | AD Group, HR records | Yes/No |
| ☐ | Có ticket hỗ trợ từ User B? | ITSM system | Yes/No |
| ☐ | Ticket được approve? | ITSM workflow | Yes/No |
| ☐ | User B có yêu cầu hỗ trợ? | Email, chat, interview | Yes/No |
| ☐ | Manager có biết? | Email, interview | Yes/No |
| ☐ | Trong phạm vi công việc? | Job description | Yes/No |

### 4.2 Decision Matrix

| IT Support | Ticket | User B Knows | Manager Knows | Conclusion | Severity |
|------------|--------|--------------|---------------|------------|----------|
| Yes | Yes | Yes | Yes | Legitimate | P4 |
| Yes | No | Yes | No | Policy violation | P3 |
| No | No | No | No | Unauthorized access | P2/P1 |
| No | No | Yes | No | Possible insider threat | P2 |

---

## 6. Timeline Reconstruction

### 5.1 Complete Event Timeline

```
Timeline: RDP Lateral Movement Investigation
═══════════════════════════════════════════════════════════════════

Phase 1: Initial Access (User A)
─────────────────────────────────
[2024-01-15 02:15:30] User A login from external IP (VPN/Azure AD)
[2024-01-15 02:16:00] User A authenticates to domain
[2024-01-15 02:16:45] Event 4648: User A initiates RDP to User B's PC
                      Source: User A's PC
                      Target: User B-PC01.domain.com
                      Process: mstsc.exe

Phase 2: Lateral Movement (RDP Connection)
───────────────────────────────────────────
[2024-01-15 02:17:00] Event 4624 Type 10: RDP logon success
                      Source IP: 10.0.1.100 (User A's VPN IP)
                      Target: User B-PC01
[2024-01-15 02:17:30] Session established
[2024-01-15 02:18:00] User A gains desktop access

Phase 3: Reconnaissance & Data Access
─────────────────────────────────────
[2024-01-15 02:20:00] Explorer.exe opened
[2024-01-15 02:25:00] Browsing C:\Users\UserB\Documents
[2024-01-15 02:45:00] salary_data.xlsx opened (Event 4663)
[2024-01-15 02:50:00] customer_list.xlsx opened (Event 4663)
[2024-01-15 02:55:00] WinZip.exe opened (Archive creation)

Phase 4: Potential Exfiltration
────────────────────────────────
[2024-01-15 02:58:00] Chrome.exe opened
[2024-01-15 02:59:00] Connection to personal-onedrive.com:443
[2024-01-15 03:00:00] 50MB data transferred (EDR alert)

Phase 5: Session Termination
────────────────────────────
[2024-01-15 03:00:00] Event 4779: Session disconnected
[2024-01-15 03:05:00] User A logout from domain
[2024-01-15 03:10:00] VPN disconnected

═══════════════════════════════════════════════════════════════════
Session Duration: 43 minutes
Data Accessed: 2 sensitive files
Potential Exfiltration: 50MB to personal cloud
```

---

## 7. Internal Communication Plan

### 6.1 Communication Timeline

| Time | Audience | Message | Channel |
|------|----------|---------|---------|
| **T+15 min** | Security Lead | "Unauthorized RDP detected: User A → User B. Investigating." | Teams/Slack |
| **T+30 min** | User B (discreet) | "Có ai đó vừa RDP vào máy bạn. Bạn có biết không?" | Phone/Direct |
| **T+1 hour** | CISO (if P1/P2) | "Potential lateral movement. Preliminary findings attached." | Email |
| **T+2 hours** | IT Ops | "Enable enhanced monitoring on User A. Do NOT disable yet." | Ticket |
| **T+4 hours** | HR (if insider threat) | "Need employment records for User A. Confidential." | Phone |
| **T+24 hours** | Legal/Compliance | "Investigation complete. Evidence preserved." | Email |

### 6.2 User B Communication Script

```
"Chào [User B],

Chúng tôi phát hiện có người dùng [User A] vừa truy cập máy tính của bạn 
qua RDP vào lúc [thời gian]. 

Bạn có:
1. Yêu cầu [User A] hỗ trợ không?
2. Biết về việc này không?
3. Cho phép [User A] truy cập không?

Thông tin này đang được điều tra. Vui lòng giữ bí mật."
```

---

## 8. Legal Evidence Preservation

### 7.1 Evidence Collection Priority

| Priority | Evidence | Location | Method |
|----------|----------|----------|--------|
| 1 | Windows Event Logs (User B's PC) | C:\Windows\System32\winevt\Logs | Live export |
| 2 | Windows Event Logs (User A's PC) | C:\Windows\System32\winevt\Logs | Live export |
| 3 | RDP Bitmap Cache | User A's PC | File copy |
| 4 | Memory Dump (User B's PC) | RAM | WinPmem |
| 5 | Network Logs | Firewall/Switch | Log export |
| 6 | EDR Data | EDR Console | API export |

### 7.2 Chain of Custody

```
┌─────────────────────────────────────────────────────────────┐
│         CHAIN OF CUSTODY - RDP LATERAL MOVEMENT             │
├─────────────────────────────────────────────────────────────┤
│ Case ID: RDP-AH-YYYYMMDD-XXX                                │
│ Incident: Unauthorized RDP from User A to User B            │
│                                                             │
│ Evidence Items:                                             │
│ 1. Security.evtx (User B's PC) - RDP events                │
│ 2. Security.evtx (User A's PC) - Explicit creds            │
│ 3. RDP Bitmap Cache (User A's PC)                          │
│ 4. Memory dump (User B's PC)                               │
│ 5. Network logs (Firewall)                                 │
│                                                             │
│ Collection:                                                 │
│ - Date/Time: [YYYY-MM-DD HH:MM]                             │
│ - Collected By: [Analyst]                                   │
│ - Witness: [IT Staff]                                       │
│                                                             │
│ SHA-256 Hashes:                                             │
│ - [File1]: [Hash]                                           │
│ - [File2]: [Hash]                                           │
│                                                             │
│ Storage: [Secure location]                                  │
│ Access: [Restricted list]                                   │
└─────────────────────────────────────────────────────────────┘
```

---

## 9. Remediation Actions

### 8.1 Immediate Actions (P1/P2)

| Priority | Action | Command/Method | Owner |
|----------|--------|----------------|-------|
| P1 | Disable User A account | `Set-ADUser -Identity UserA -Enabled $false` | Identity Team |
| P1 | Revoke User A sessions | `Revoke-AzureADUserAllRefreshToken` | Identity Team |
| P1 | Isolate User B's PC | Network isolation | IT Ops |
| P1 | Preserve evidence | Chain of custody | Security Team |
| P2 | Check other RDP sessions | Query Event 4624 across domain | SOC |
| P2 | Review User A's permissions | AD Group audit | Identity Team |

### 8.2 Long-term Actions

| Action | Purpose | Timeline |
|--------|---------|----------|
| RDP policy review | Restrict RDP to authorized users only | 1 week |
| Just-in-time access | Implement PAM for RDP | 1 month |
| Session recording | Record all RDP sessions | 1 month |
| EDR rule tuning | Detect lateral movement | 1 week |
| Security awareness | Training on RDP security | Quarterly |

---

## 10. Expected Output

### 9.1 Investigation Report (JSON)

```json
{
  "investigation_id": "RDP-AH-20240115-001",
  "playbook_version": "1.1",
  "incident_type": "RDP Lateral Movement After-Hours",
  "severity": "High",
  "status": "InvestigationComplete",
  "subjects": {
    "user_a": {
      "username": "userA@domain.com",
      "department": "IT",
      "role": "System Administrator",
      "is_it_support": true
    },
    "user_b": {
      "username": "userB@domain.com",
      "department": "HR",
      "role": "HR Manager",
      "was_aware": false
    }
  },
  "rdp_session": {
    "start_time": "2024-01-15T02:17:00Z",
    "end_time": "2024-01-15T03:00:00Z",
    "duration_minutes": 43,
    "source_ip": "10.0.1.100",
    "target_computer": "UserB-PC01.domain.com"
  },
  "findings": {
    "business_justification": false,
    "ticket_exists": false,
    "user_b_aware": false,
    "manager_aware": false,
    "data_accessed": [
      "salary_data.xlsx",
      "customer_list.xlsx"
    ],
    "potential_exfiltration": true,
    "exfiltration_destination": "personal-onedrive.com"
  },
  "conclusion": "Unauthorized RDP access. User A accessed sensitive HR data without business justification.",
  "recommendations": [
    "Disciplinary action for User A (coordinate with HR)",
    "Review RDP access permissions",
    "Implement session recording",
    "User B notification assessment (DPO)"
  ],
  "evidence_collected": [
    "Security.evtx (User B's PC)",
    "Security.evtx (User A's PC)",
    "RDP Bitmap Cache",
    "Memory dump"
  ],
  "legal_status": "Evidence preserved. Ready for HR/Legal proceedings."
}
```

### 9.2 Executive Summary (Vietnamese)

```markdown
## Báo cáo Điều tra Truy cập RDP Trái phép Ngoài giờ

**Thông tin chung:**
- Mã sự cố: RDP-AH-20240115-001
- Loại: RDP Lateral Movement
- Mức độ: Cao

**Các bên liên quan:**
- Người truy cập (User A): [Tên], [Phòng ban], [Chức vụ]
- Người bị truy cập (User B): [Tên], [Phòng ban], [Chức vụ]

**Diễn biến sự cố:**
- Thời gian: 02:17 - 03:00 ngày 15/01/2024
- Phương thức: RDP từ máy User A đến máy User B
- Thời lượng: 43 phút

**Phát hiện:**
- ❌ Không có ticket hỗ trợ hợp lệ
- ❌ User B không biết về việc truy cập
- ❌ Manager không được thông báo
- ⚠️ Truy cập file nhạy cảm: salary_data.xlsx, customer_list.xlsx
- ⚠️ Dấu hiệu chuyển dữ liệu ra ngoài (50MB)

**Kết luận:** Truy cập RDP trái phép, vi phạm chính sách bảo mật.

**Khuyến nghị:**
1. Xử lý kỷ luật User A (phối hợp HR)
2. Rà soát lại quyền truy cập RDP
3. Triển khai ghi hình RDP session
4. Đánh giá thông báo User B (theo Nghị định 13/2023/NĐ-CP)

**Bằng chứng pháp lý:** Đã thu thập và bảo quản theo quy trình.
```

---

## 10. Related Skills

### Tier 1 (Entry Point)
- [`playbook-master-incident-triage`](Vietnamese-Banking-Cyber-Skills/skills/playbook-master-incident-triage/SKILL.md) - Phân loại và điều hướng sự cố

### Tier 2 (Other Playbooks)
- [`playbook-abnormal-user-behavior-response`](Vietnamese-Banking-Cyber-Skills/skills/playbook-abnormal-user-behavior-response/SKILL.md) - General UEBA response
- [`playbook-phishing-response`](Vietnamese-Banking-Cyber-Skills/skills/playbook-phishing-response/SKILL.md) - Nếu User A bị phishing

### Tier 3 (Technical Skills)
| Skill | Purpose | When to Call |
|-------|---------|------------|
| [`analyzing-ueba-alerts-varonis`](Vietnamese-Banking-Cyber-Skills/skills/analyzing-ueba-alerts-varonis/SKILL.md) | File access analysis | Step 2.3 - Data access |
| [`analyzing-azure-ad-signin-logs`](Vietnamese-Banking-Cyber-Skills/skills/analyzing-azure-ad-signin-logs/SKILL.md) | User A authentication | Step 2.2 - Source analysis |
| [`collecting-volatile-evidence-from-compromised-host`](Vietnamese-Banking-Cyber-Skills/skills/collecting-volatile-evidence-from-compromised-host/SKILL.md) | Memory/forensics | Step 2.4 - Deep forensics |
| [`performing-windows-artifact-analysis-with-eric-zimmerman-tools`](Vietnamese-Banking-Cyber-Skills/skills/performing-windows-artifact-analysis-with-eric-zimmerman-tools/SKILL.md) | RDP cache analysis | Step 2.4 - Artifact analysis |
| [`analyzing-windows-registry-for-artifacts`](Vietnamese-Banking-Cyber-Skills/skills/analyzing-windows-registry-for-artifacts/SKILL.md) | Registry analysis | Step 2.4 - USB/device history |

---

## 11. References

- MITRE ATT&CK T1021.001 - Remote Desktop Protocol
- Nghị định 13/2023/NĐ-CP - Bảo vệ dữ liệu cá nhân
- Windows Security Event Reference (4624, 4625, 4648, 4778, 4779)
- NIST SP 800-61 - Incident Handling Guide

---

*Document Version: 1.1*  
*Last Updated: 09/04/2026*  
*Classification: Internal Use - Banking Security*
