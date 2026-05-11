---
name: analyzing-vpn-logs
description: Analyzes VPN connection logs from firewall, SSL VPN, or network access control systems to detect unauthorized access, anomalous connection patterns, and potential security incidents involving remote access.
domain: cybersecurity
subdomain: network-security
tags: [vpn, ssl-vpn, network-access, remote-access, connection-analysis, firewall-logs, incident-response, insider-threat]
version: "1.1"
author: cybersecurity-skills-mode
license: Apache-2.0
mitre_attack: [T1133, T1021, T1078, T1086]
nist_csf: [DE.CM-1, DE.AE-1, PR.AC-3, PR.AC-7]
---

# Analyzing VPN Logs

## Overview

VPN log analysis is critical for detecting unauthorized remote access, compromised credentials, and anomalous connection patterns that may indicate insider threats or external attacks. This skill provides a structured procedure for analyzing VPN logs from various vendors (Cisco ASA, Fortinet FortiGate, Palo Alto, Pulse Secure, Microsoft Azure VPN Gateway) to identify suspicious activities and generate actionable investigation reports.

## When to Use

- When investigating remote access incidents
- When responding to unauthorized access alerts
- When conducting proactive monitoring for suspicious VPN activity
- When analyzing connections from external IPs to corporate network
- When investigating departing employee cases (insider risk)
- When correlating VPN logs with other security events (device control, endpoint)
- When preparing incident investigation reports for compliance

## Pre-Analysis Value Assessment 🆕

> **⚠️ BẮT BUỘC:** Trả lời các câu hỏi sau TRƯỚC KHI phân tích VPN logs. Nếu ≥2 câu trả lời là "Không" → cân nhắc SKIP và ghi rõ lý do.

| # | Câu hỏi | Yes/No |
|---|---|---|
| 1 | VPN logs có sẵn cho TẤT CẢ users trong scope không? | |
| 2 | Có policy VPN off-hours chính thức không? | |
| 3 | Có thể đối chiếu VPN IP với IP nội bộ của user không? | |
| 4 | Case có liên quan đến remote access hoặc location-based anomaly không? | |
| 5 | Có timeline correlation giữa VPN events và events từ nguồn log khác không? | |

**Nếu SKIP:** Ghi vào báo cáo: "VPN analysis was skipped because [lý do]. VPN logs available at [path] for future reference if needed."

## Prerequisites

- Access to VPN logs (firewall, SSL VPN, NAC logs)
- Log data in supported formats: CSV, CEF, Syslog, JSON
- Knowledge of VPN infrastructure and expected connection patterns
- List of target users/IPs to investigate
- Understanding of corporate network topology (internal IP ranges)

## Steps

### Step 1 – Identify and Locate VPN Log Sources

First, identify available VPN log files in the workspace:

**Common VPN log locations:**
```
Logs/*VPN*.csv
Logs/*vpn*.csv
Logs/*ssl*.csv
Logs/*FortiGate*.csv
Logs/*ASA*.csv
Logs/*PaloAlto*.csv
```

**Use list_files tool to explore Logs directory:**
```bash
list_files(path="Logs", recursive=false)
```

### Step 2 – Parse and Validate VPN Log Structure

Read the VPN log file and identify column structure:

**Common VPN log columns:**
| Column | Description |
|--------|-------------|
| Date/Time | Timestamp of event |
| Level | Log level (information, warning, error) |
| Action | Event type (tunnel-up, tunnel-down, auth-failed, etc.) |
| Status | Connection status |
| Message | Detailed message |
| VPN Tunnel | Tunnel identifier |
| User | Username |
| Remote IP | External IP address |
| Tunnel IP | Internal VPN IP address |
| Source Port | Source port for connection |
| Protocol | Protocol used (SSL, IPSec) |

**Validate file:**
- [ ] Confirm file is readable
- [ ] Note total number of rows
- [ ] Identify date range of logs
- [ ] Check for data quality issues (missing values, encoding)

### Step 3 – Calculate Basic Statistics

Calculate key metrics from VPN logs:

#### 3.1 Total Connection Statistics

```
Total Rows: [number]
Unique Users: [number]
Unique Remote IPs: [number]
Unique Tunnel IPs: [number]
Date Range: [start_date] to [end_date]
```

#### 3.2 Connection Events Summary

| Event Type | Count | Description |
|------------|-------|-------------|
| tunnel-up | X | Successful connection established |
| tunnel-down | X | Connection terminated |
| tunnel-stats | X | Tunnel statistics/keepalive |
| auth-success | X | Authentication successful |
| auth-failed | X | Authentication failed |
| disconnect | X | Forced disconnection |

#### 3.3 User Connection Summary

| User | Total Events | Unique IPs | First Connection | Last Connection |
|------|--------------|------------|-----------------|-----------------|
| user1 | X | X | datetime | datetime |
| user2 | X | X | datetime | datetime |

### Step 4 – Filter and Focus Analysis

Filter logs based on investigation context:

**For specific IP investigation:**
```
Filter: Remote IP = [target_ip]
Example: Remote IP = 116.111.184.100
```

**For specific user investigation:**
```
Filter: User = [target_username]
Example: User = luannt0500
```

**For specific date range:**
```
Filter: Date between [start_date] and [end_date]
Example: 2026-04-20 to 2026-04-29
```

### Step 5 – Analyze Connection Patterns

For each target user/IP, analyze:

#### 5.1 Connection Timing

| Metric | Value |
|--------|-------|
| Earliest connection | yyyy-mm-dd HH:MM |
| Latest connection | yyyy-mm-dd HH:MM |
| Average session duration | X hours |
| Connections during off-hours | Y |
| Weekend/holiday connections | Z |

**Off-hours definition:**
- Before 07:00 or after 18:00 on weekdays
- Any time on weekends/holidays

#### 5.2 Geographic/Network Analysis

- [ ] Identify all unique source IPs
- [ ] Check IP reputation (if available)
- [ ] Identify IP ranges (domestic vs. foreign)
- [ ] Note any IP changes during session

#### 5.3 Session Analysis

- [ ] Calculate session duration per connection
- [ ] Identify long-lived sessions (>8 hours)
- [ ] Identify multiple concurrent sessions
- [ ] Note connection frequency patterns

### Step 6 – Identify Anomalies and Suspicious Events

Flag suspicious patterns:

#### 6.1 High-Risk Indicators

| Indicator | Threshold | Severity |
|-----------|-----------|----------|
| Failed authentication attempts | ≥5 failures | High |
| Multiple IP addresses | ≥3 different IPs | Medium |
| Off-hours connections | Any | Medium |
| Long sessions | >8 hours | Medium |
| Multiple concurrent sessions | ≥2 | High |
| Impossible travel | Different IPs <1 hour | Critical |
| First connection after termination | Any | Critical |

#### 6.2 Anomaly Detection Queries

**Failed Auth Analysis:**
```
Search: auth-failed
Group by: User, Remote IP
Flag: ≥5 failures per user/IP
```

**Multiple IP Analysis:**
```
Search: All events for user
Unique: Remote IP
Flag: ≥3 unique IPs
```

**Off-Hours Analysis:**
```
Filter: Hour < 7 OR Hour > 18 OR DayOfWeek in (0,6)
Flag: Any off-hours connection
```

#### 6.3 Detailed Off-Hours Analysis Procedure

**Off-Hours Definition:**
- Before 08:00 or after 18:00 on weekdays
- Any time on weekends/holidays

**Analysis Steps:**

1. **Extract ALL tunnel-up events with timestamps:**
   ```
   Search pattern: tunnel-up
   Extract: Date/Time, User, Remote IP, Tunnel IP
   ```

2. **Identify off-hours connections:**
   - Filter where Hour < 8 OR Hour >= 18
   - Check DayOfWeek (0=Sunday, 6=Saturday)

3. **For each off-hours event, record:**
   | Date | Time | Event | Remote IP | Duration | Assessment |
   |------|------|-------|-----------|----------|------------|
   | yyyy/mm/dd | HH:MM | tunnel-up | x.x.x.x | X min | Normal/Suspicious |

4. **Risk Assessment for Off-Hours:**
   | Pattern | Risk Level | Action Required |
   |---------|------------|-----------------|
   | Occasional late night (1-2x/month) | Low | Document |
   | Regular after-hours | Medium | Investigate pattern |
   | Short sessions (<5 min) off-hours | Medium-High | High priority investigate |
   | Just before/after resignation date | Critical | Immediate investigation |

5. **Common Suspicious Off-Hours Patterns:**
   - Quick connect/disconnect (<5 min) - possible data access
   - Multiple short sessions in off-hours
   - Connection right before termination
   - Early morning access (before 6 AM)

### Step 7 – Detailed Event Analysis

For suspicious events, extract detailed information:

```
Event: tunnel-up
Timestamp: YYYY-MM-DD HH:MM:SS
User: [username]
Remote IP: [external_ip]
Tunnel IP: [internal_ip]
Status: [success/failure]
Details: [any additional info]
```

### Step 8 – Generate Investigation Report

Structure findings as JSON:

```json
{
  "investigation_id": "VPN-YYYYMMDD-XXX",
  "analyst": "analyst-name",
  "date": "YYYY-MM-DD",
  "target": {
    "type": "ip|user|both",
    "ip": "target_ip",
    "user": "target_username"
  },
  "log_summary": {
    "total_rows": 0,
    "date_range": {
      "start": "YYYY-MM-DD",
      "end": "YYYY-MM-DD"
    },
    "unique_users": 0,
    "unique_remote_ips": 0,
    "unique_tunnel_ips": 0
  },
  "connection_events": {
    "tunnel_up": 0,
    "tunnel_down": 0,
    "auth_success": 0,
    "auth_failed": 0,
    "disconnect": 0
  },
  "user_analysis": [
    {
      "user": "username",
      "total_connections": 0,
      "unique_remote_ips": 0,
      "first_connection": "YYYY-MM-DD HH:MM",
      "last_connection": "YYYY-MM-DD HH:MM",
      "total_session_time": "X hours",
      "off_hours_connections": 0,
      "failed_auth_attempts": 0,
      "risk_level": "Low|Medium|High|Critical"
    }
  ],
  "anomalies_detected": [
    {
      "type": "anomaly_type",
      "severity": "Low|Medium|High|Critical",
      "description": "Description",
      "timestamp": "YYYY-MM-DD HH:MM",
      "user": "username",
      "details": {}
    }
  ],
  "ioc_list": [
    {
      "type": "ip|user|session",
      "value": "indicator",
      "context": "related event"
    }
  ],
  "recommended_actions": [
    {
      "action": "Description",
      "owner": "Role/Team",
      "priority": "P1|P2|P3",
      "due_date": "YYYY-MM-DD"
    }
  ]
}
```

## Expected Output

1. **Executive Summary** – High-priority findings for management
2. **Connection Statistics Table** – All users/IPs with connection metrics
3. **Anomaly Summary** – List of suspicious events ranked by severity
4. **IOC List** – Indicators for blocking/monitoring
5. **Timeline of Events** – Chronological connection map
6. **Recommended Actions** – Prioritized remediation steps

## Vietnamese Banking Context

When analyzing VPN logs in Vietnamese banking environment:

- **Data Classification**: Customer PII, Financial records, Account data
- **Regulatory Reference**: 
  - Nghị định 356/2025/NĐ-CP (PDP) for personal data
  - Luật An ninh mạng 2018
  - NHNN Circular on IT Security for financial data
- **Common VPN Scenarios**:
  - Remote access to core banking systems
  - Third-party vendor access
  - Employee remote work connections
  - Departing employee access after notice period

### Internal IP Ranges (RFC 1918)

| Range | Description |
|-------|-------------|
| 10.0.0.0/8 | Class A private |
| 172.16.0.0/12 | Class B private |
| 192.168.0.0/16 | Class C private |

### Common VPN Tunnel IP Ranges

```
172.16.0.0/12   # Common VPN pool
10.10.0.0/16    # SSL VPN pool
10.100.0.0/16   # Remote access
```

## Validation Checklist

- [ ] All target users/IPs analyzed
- [ ] Connection statistics calculated
- [ ] Session duration analyzed
- [ ] Off-hours connections identified
- [ ] Failed authentication analyzed
- [ ] Multiple IP addresses flagged
- [ ] Anomalies categorized by severity
- [ ] IOC list formatted correctly
- [ ] Recommended actions have owners and due dates
- [ ] Report reviewed for accuracy

## References

- VPN vendor documentation (Cisco, Fortinet, Palo Alto)
- MITRE ATT&CK Framework v14 - T1133 (External Remote Services)
- NIST Cybersecurity Framework 2.0
- RFC 1918 - Address Allocation for Private Internets
- Vietnamese banking security regulations (NHNN)
- Nghị định 356/2025/NĐ-CP về bảo vệ dữ liệu cá nhân
