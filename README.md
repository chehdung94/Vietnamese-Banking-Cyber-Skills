# 🛡️ Vietnamese Banking Cyber Skills

<p align="center">
  <img src="https://img.shields.io/badge/Skills-10-success?style=for-the-badge" alt="Skills Count" />
  <img src="https://img.shields.io/badge/Architecture-3--Tier-blue?style=for-the-badge" alt="Architecture" />
  <img src="https://img.shields.io/badge/MITRE%20ATT%26CK-Mapped-orange?style=for-the-badge" alt="MITRE ATT&CK" />
  <img src="https://img.shields.io/badge/License-Apache%202.0-green?style=for-the-badge" alt="License" />
  <img src="https://img.shields.io/badge/Compliance-Nghị%20định%2013%2F2023-purple?style=for-the-badge" alt="Vietnamese Compliance" />
</p>

<p align="center">
  <strong>Bộ thư viện kỹ năng An ninh mạng cho AI Agents</strong><br/>
  <em>Incident Response • Digital Forensics • Threat Hunting</em><br/>
  <a href="https://agentskills.io">agentskills.io</a> • <a href="https://github.com/mukul975/Anthropic-Cybersecurity-Skills">Reference: mukul975/Anthropic-Cybersecurity-Skills</a>
</p>

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Architecture](#-architecture)
- [Skill Catalog](#-skill-catalog)
- [Quick Start](#-quick-start)
- [Installation](#-installation)
- [Usage Examples](#-usage-examples)
- [Vietnamese Banking Compliance](#-vietnamese-banking-compliance)
- [Contributing](#-contributing)
- [License](#-license)

---

## 📖 Overview

**Vietnamese-Banking-Cyber-Skills** là bộ thư viện kỹ năng cybersecurity được thiết kế đặc thù cho hệ thống ngân hàng tại Việt Nam. Được xây dựng trên nền tảng [agentskills.io](https://agentskills.io) open standard, bộ kỹ năng này giúp AI Agents thực hiện các tác vụ phản ứng sự cố (Incident Response) và điều tra số (Digital Forensics) một cách có hệ thống và chuyên nghiệp.

### 🎯 Mục tiêu

| Mục tiêu | Mô tả |
|----------|-------|
| **Chuẩn hóa IR** | Cung cấp quy trình IR đồng nhất cho đội ngũ an ninh |
| **Tự động hóa** | Hỗ trợ AI Agent thực hiện triage, analysis, và containment |
| **Tuân thủ pháp luật** | Tích hợp Nghị định 13/2023/NĐ-CP và quy định NHNN |
| **MITRE ATT&CK** | Ánh xạ kỹ năng với framework ATT&CK phổ biến |

---

## ✨ Features

### 🏗️ 3-Tier Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         TIER 1: MASTER PLAYBOOK                      │
│              (Entry Point • Triage • Severity Classification)       │
│                    playbook-master-incident-triage                   │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    TIER 2: INCIDENT PLAYBOOKS                       │
│         (Checklist • Communication Plan • Interim Recommendations)   │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐              │
│   │  Phishing    │  │  Malware     │  │  Insider     │              │
│   │  Response    │  │  Response    │  │  Threat      │              │
│   └──────────────┘  └──────────────┘  └──────────────┘              │
└─────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    TIER 3: TECHNICAL SKILLS                          │
│              (Tool Execution • Artifact Analysis • DFIR)            │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐       │
│  │ Volatility │ │ Wireshark  │ │  Varonis   │ │ EZ Tools   │       │
│  │  Memory    │ │  Network   │ │   UEBA     │ │  Windows   │       │
│  └────────────┘ └────────────┘ └────────────┘ └────────────┘       │
└─────────────────────────────────────────────────────────────────────┘
```

### 🔑 Key Features

| Tính năng | Mô tả |
|-----------|-------|
| **Evidence Request Checklist** | Danh sách artifact cần thu thập theo từng loại sự cố |
| **Communication Plan** | Kế hoạch thông báo nội bộ theo timeline (T+15m, T+1h, T+4h) |
| **Interim Recommendations** | Hành động tạm thời để contain sự cố trước khi có kết luận |
| **Execution Commands** | Lệnh cụ thể cho từng công cụ DFIR |
| **MITRE ATT&CK Mapping** | Ánh xạ techniques với framework chuẩn quốc tế |
| **Vietnamese Context** | Tích hợp bối cảnh pháp luật và quy định ngân hàng VN |

---

## 📂 Architecture

### Directory Structure

```
Vietnamese-Banking-Cyber-Skills/
├── README.md                      # This file
├── LICENSE                        # Apache 2.0 License
├── .gitignore                     # Excludes _drafts/ from GitHub
├── system-prompt.md               # AI Agent system prompt
├── skills/
│   ├── ARCHITECTURE.md            # Architecture documentation
│   ├── README.md                  # Skills index
│   ├── _drafts/                  # ⚠️ NOT pushed to GitHub
│   │   └── README.md              # Draft skills documentation
│   │
│   ├── playbook-master-incident-triage/   # TIER 1
│   │   └── SKILL.md
│   │
│   ├── playbook-phishing-response/        # TIER 2
│   │   └── SKILL.md
│   │
│   ├── conducting-memory-forensics-with-volatility/     # TIER 3
│   │   └── SKILL.md
│   ├── performing-network-forensics-with-wireshark/
│   │   └── SKILL.md
│   ├── analyzing-ueba-alerts-varonis/
│   │   └── SKILL.md
│   ├── analyzing-windows-registry-for-artifacts/
│   │   └── SKILL.md
│   ├── collecting-volatile-evidence-from-compromised-host/
│   │   └── SKILL.md
│   └── performing-windows-artifact-analysis-with-eric-zimmerman-tools/
│       └── SKILL.md
```

### Naming Convention

| Tier | Prefix | Example |
|------|--------|---------|
| Master Playbook | `playbook-master-` | `playbook-master-incident-triage` |
| Incident Playbook | `playbook-` | `playbook-phishing-response` |
| Technical Skill | `[verb]-[target]-` | `conducting-memory-forensics-with-volatility` |

---

## 📋 Skill Catalog

### Tier 1: Master Triage

| Skill | Description | MITRE ATT&CK |
|-------|-------------|--------------|
| [`playbook-master-incident-triage`](skills/playbook-master-incident-triage/SKILL.md) | Entry point - phân loại và định hướng sự cố theo P1-P4 | Triage |

### Tier 2: Incident Playbooks

| Skill | Description | MITRE ATT&CK |
|-------|-------------|--------------|
| [`playbook-phishing-response`](skills/playbook-phishing-response/SKILL.md) | Xử lý email lừa đảo, credential harvesting | T1566 |
| [`playbook-abnormal-user-behavior-response`](skills/playbook-abnormal-user-behavior-response/SKILL.md) | Phản ứng hành vi bất thường UEBA, insider threat | T1074, T1114 |
| *(Xem draft)* | Data Leak Response | T1041, T1074 |
| *(Đang phát triển)* | Malware/Ransomware Response | T1486 |
| *(Đang phát triển)* | Account Takeover Response | T1078 |

### Tier 3: Technical Skills

| Skill | Description | MITRE ATT&CK |
|-------|-------------|--------------|
| [`conducting-memory-forensics-with-volatility`](skills/conducting-memory-forensics-with-volatility/SKILL.md) | Phân tích memory dump với Volatility 3 | T1055 |
| [`performing-network-forensics-with-wireshark`](skills/performing-network-forensics-with-wireshark/SKILL.md) | Phân tích PCAP files | T1040 |
| [`analyzing-ueba-alerts-varonis`](skills/analyzing-ueba-alerts-varonis/SKILL.md) | Phân tích UEBA alerts từ Varonis | T1074 |
| [`analyzing-windows-registry-for-artifacts`](skills/analyzing-windows-registry-for-artifacts/SKILL.md) | Trích xuất artifacts từ Windows Registry | T1012 |
| [`collecting-volatile-evidence-from-compromised-host`](skills/collecting-volatile-evidence-from-compromised-host/SKILL.md) | Thu thập volatile evidence theo RFC 3227 | T1054 |
| [`performing-windows-artifact-analysis-with-eric-zimmerman-tools`](skills/performing-windows-artifact-analysis-with-eric-zimmerman-tools/SKILL.md) | Windows forensics với KAPE, MFTECmd | T1005 |
| [`analyzing-dlp-alerts`](skills/analyzing-dlp-alerts/SKILL.md) | Phân tích DLP alerts (Purview, Varonis) | T1041, T1074 |
| [`analyzing-azure-ad-signin-logs`](skills/analyzing-azure-ad-signin-logs/SKILL.md) | Phân tích Azure AD sign-in logs | T1078, T1110 |
| [`analyzing-email-audit-logs`](skills/analyzing-email-audit-logs/SKILL.md) | Phân tích email audit logs (Exchange, M365) | T1114, T1566 |
| [`analyzing-cloud-storage-audit-logs`](skills/analyzing-cloud-storage-audit-logs/SKILL.md) | Phân tích cloud storage logs (OneDrive, S3) | T1041, T1074 |

---

## 🚀 Quick Start

### 1️⃣ Clone Repository

```bash
git clone https://github.com/your-username/Vietnamese-Banking-Cyber-Skills.git
cd Vietnamese-Banking-Cyber-Skills
```

### 2️⃣ Import to Your AI Agent

#### Roo Code (VS Code)
1. Mở Roo Code Settings → Modes
2. Tạo Mode mới: `cybersecurity-skills-mode`
3. Copy nội dung từ [`system-prompt.md`](system-prompt.md) vào **Role Definition**
4. Đặt đường dẫn thư mục `skills/` vào **Allowed Directories**

#### Claude Code
```bash
# Thêm vào claude_desktop_config.json
{
  "skills": {
    "cyber-skills": "/path/to/Vietnamese-Banking-Cyber-Skills/skills"
  }
}
```

#### Custom GPTs (ChatGPT)
1. Nén thư mục `skills/` thành ZIP
2. Upload vào **Knowledge** section
3. Copy nội dung `system-prompt.md` vào **Instructions**

### 3️⃣ Activate and Use

```markdown
# Gọi Master Playbook
"Kích hoạt playbook-master-incident-triage"

# Gọi Incident Playbook cụ thể
"Xử lý sự cố phishing - kích hoạt playbook-phishing-response"

# Gọi Technical Skill trực tiếp
"Phân tích memory dump với Volatility - conducting-memory-forensics-with-volatility"
```

---

## 📦 Installation

### Prerequisites

| Tool | Version | Purpose |
|------|---------|---------|
| Python | 3.8+ | Script execution |
| Volatility 3 | 3.x | Memory forensics |
| Wireshark | 4.x | Network analysis |
| Eric Zimmerman Tools | Latest | Windows artifact analysis |
| Varonis DataAdvantage | - | UEBA (optional) |

### Install All Dependencies

```bash
# Clone repository
git clone https://github.com/your-username/Vietnamese-Banking-Cyber-Skills.git
cd Vietnamese-Banking-Cyber-Skills

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows

# Install Python dependencies
pip install pyzipper pyyaml

# Verify installation
python -c "import pyzipper; print('pyzipper installed')"
```

### Platform-Specific Setup

#### Windows (PowerShell)
```powershell
# Install Chocolatey first (if not installed)
Set-ExecutionPolicy Bypass -Scope Process -Force
iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

# Install tools
choco install -y volatility wireshark
```

#### Linux (Ubuntu/Debian)
```bash
# Install Volatility
sudo apt install volatility3

# Install Wireshark
sudo apt install wireshark

# For EZ Tools, download from GitHub releases
wget https://github.com/EricZimmerman/NameBench/releases/latest/download/namebench.zip
```

#### macOS
```bash
# Using Homebrew
brew install volatility wireshark

# For EZ Tools
brew install --cask ega
```

---

## 💡 Usage Examples

### Example 1: Phishing Incident Response

```markdown
User: "Phát hiện email đáng ngờ từ người dùng huyhq@domain.com. Email có file đính kèm invoice.pdf"

Agent Response:
---
## 🔍 Kích hoạt: playbook-phishing-response

### Step 1: Thu thập Evidence
Yêu cầu người dùng cung cấp:
- [ ] Email source (file .eml)
- [ ] File đính kèm (invoice.pdf)
- [ ] Mail headers (X-MS-Exchange-MessageSentRepresentingType)
- [ ] Proxy logs cho IP sender
- [ ] Kết quả scan antivirus

### Step 2: Header Analysis
Đang phân tích email headers...
- Sender IP: 104.161.37.231
- SPF: ❌ FAIL
- DKIM: ❌ FAIL  
- DMARC: ✅ PASS

### Step 3: Interim Recommendation
⚠️ **Hành động tạm thời:**
1. Disable account huyhq@domain.com
2. Block IP 104.161.37.231 at firewall
3. Alert users trong department

### Step 4: Communication Plan
- [ ] T+15m: Notify SOC team
- [ ] T+1h: Notify CISO
- [ ] T+4h: Status report to management

### Step 5: Technical Analysis
Để phân tích sâu hơn, kích hoạt:
- `conducting-memory-forensics-with-volatility` (nếu có malware)
- `performing-network-forensics-with-wireshark` (nếu có PCAP)
```

### Example 2: Memory Forensics

```markdown
User: "Có suspicious process trên máy victim. File dump đã có tại C:\Forensics\mem.dmp"

Agent Response:
---
## 🔍 Kích hoạt: conducting-memory-forensics-with-volatility

### Prerequisites
- [x] Memory dump: C:\Forensics\mem.dmp
- [x] Volatility 3 installed
- [x] Profile identified: Win10x64_19041

### Step 1: Process Analysis
```bash
vol -f mem.dmp windows.pslist.PsList
```

### Step 2: Network Connections
```bash
vol -f mem.dmp windows.netscan.Netscan
```

### Step 3: DLL Injection Detection
```bash
vol -f mem.dmp windows.malfind.Malfind
```

### Findings
⚠️ **Suspicious Process Detected:**
- Process ID: 4532
- Name: update.exe
- Parent: notepad.exe (anomalous)
- MITRE ATT&CK: T1055 (Process Injection)
```

---

## 🇻🇳 Vietnamese Banking Compliance

### Supported Regulations

| Regulation | Description | Coverage |
|------------|-------------|----------|
| **Nghị định 356/2025/NĐ-CP** | Hướng dẫn Luật Bảo vệ Dữ liệu Cá nhân (có hiệu lực 01/07/2025) | PDP compliance |
| **Luật Bảo vệ Dữ liệu Cá nhân 2024** | Luật số 32/2024/QH15 | PDP compliance |
| **Luật An ninh mạng 2018** | Luật ATTT lĩnh vực ngân hàng | ANM compliance |
| **QĐ 18/2020/QĐ-TTg** | Tiêu chuẩn kỹ thuật ANTT | Technical standards |
| **Thông tư 13/2020/TT-NHNN** | ANTT cho thanh toán điện tử | Payment security |
| **Nghị định 13/2023/NĐ-CP** | Đã được thay thế bởi NĐ 356/2025/NĐ-CP | ⚠️ Deprecated |

### Key Changes in Nghị định 356/2025/NĐ-CP

Nghị định 356/2025/NĐ-CP có hiệu lực từ **01/07/2025**, thay thế hoàn toàn Nghị định 13/2023/NĐ-CP với các điểm mới quan trọng:

| Nội dung | NĐ 13/2023 (Đã thay thế) | NĐ 356/2025 (Hiệu lực) |
|----------|---------------------------|-------------------------|
| **Thời hạn thông báo vi phạm** | 24 giờ | 36 giờ |
| **Xử phạt vi phạm hành chính** | Tối đa 100 triệu VNĐ | Tối đa 200 triệu VNĐ |
| **Quyền của DPO** | Quy định chung | Chi tiết hơn về quyền và nhiệm vụ |
| **Hồ sơ xử lý dữ liệu** | Yêu cầu cơ bản | Mở rộng, bao gồm DPIA |
| **Chuyển dữ liệu ra nước ngoài** | Cần sự đồng ý | Quy trình phê duyệt bổ sung (Điều 54) |

### Compliance Checklist

```markdown
## Vietnamese Banking Compliance Checklist

### Nghị định 356/2025/NĐ-CP & Luật BVTTDL 2024
- [ ] Thông báo vi phạm trong vòng 36 giờ (trước đây là 24h)
- [ ] Đánh giá tác động bảo vệ dữ liệu (DPIA) được thực hiện
- [ ] Hồ sơ xử lý dữ liệu cá nhân được cập nhật
- [ ] Đơn vị phụ trách bảo vệ dữ liệu (DPO) được chỉ định
- [ ] Quy trình xin phê duyệt chuyển dữ liệu ra nước ngoài có sẵn

### Luật An ninh mạng 2018
- [ ] Báo cáo sự cố đến cơ quan chức năng (trong 24h cho sự cố nghiêm trọng)
- [ ] Bằng chứng được bảo quản phục vụ điều tra
- [ ] Điều tra nội bộ được ghi chép đầy đủ
- [ ] Kế hoạch khắc phục được phê duyệt

### Quy định NHNN
- [ ] SOC được thông báo
- [ ] Phân loại sự cố (P1-P4)
- [ ] Lãnh đạo được thông báo (cho P1-P2)
- [ ] Báo cáo cuối cùng được nộp
```

### Data Breach Response Timeline (NĐ 356/2025)

```
T+0 (Phát hiện)
    │
    ├─ T+36 giờ: Thông báo đến Cơ quan chức năng & Người dùng bị ảnh hưởng
    │
    ├─ T+72 giờ: Đánh giá tác động ban đầu (DPIA)
    │
    └─ T+30 ngày: Báo cáo điều tra chi tiết & Kế hoạch khắc phục
```

---

## 🤝 Contributing

### Adding New Skills

1. **Fork** repository
2. **Create** skill folder following naming convention:
   ```bash
   # For Tier 3 technical skills
   skills/[verb]-[target]-[tool]/
   
   # For Tier 2 incident playbooks  
   skills/playbook-[incident-type]-response/
   ```
3. **Create** `SKILL.md` with YAML frontmatter:
   ```yaml
   ---
   name: skill-name
   description: Brief description
   domain: cybersecurity
   subdomain: specific-area
   tags: [keyword1, keyword2]
   version: "1.0"
   author: Your Name
   license: Apache-2.0
   mitre_attack: Txxxx
   ---
   ```
4. **Follow** 3-tier architecture guidelines in [`skills/ARCHITECTURE.md`](skills/ARCHITECTURE.md)
5. **Submit** Pull Request

### Skill Contribution Template

```markdown
---
name: example-skill
description: Description of what this skill does
domain: cybersecurity
subdomain: incident-response
tags: [phishing, email, analysis]
version: "1.0"
author: Your Name
license: Apache-2.0
mitre_attack: T1566
prerequisites:
  - Python 3.8+
  - Tool name
expected_output: JSON format
---

# Skill Title

## Overview
Brief description of when to use this skill.

## Prerequisites
List of required tools and access.

## Steps
1. Step one
2. Step two

## Output Format
Expected output structure.
```

---

## 📄 License

This project is licensed under the **Apache License 2.0** - see the [LICENSE](LICENSE) file for details.

```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copies of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

---

## 🙏 Acknowledgments

- [agentskills.io](https://agentskills.io) - Open standard for AI agent skills
- [mukul975/Anthropic-Cybersecurity-Skills](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) - Reference repository
- [MITRE ATT&CK](https://attack.mitre.org/) - Framework chuẩn quốc tế
- [Volatility Foundation](https://www.volatilityfoundation.org/) - Memory forensics
- [Eric Zimmerman](https://github.com/EricZimmerman) - Windows forensics tools

---

<p align="center">
  <strong>Made for Vietnamese Banking Security Teams 🇻🇳</strong><br/>
  <em>Built with ❤️ for the cybersecurity community</em>
</p>
