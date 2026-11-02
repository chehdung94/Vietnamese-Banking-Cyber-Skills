# Cybersecurity Skills Library

Bộ thư viện skill bảo mật theo chuẩn [agentskills.io](https://agentskills.io) open standard.

## Cấu trúc

```
skills/
├── [skill-name]/
│   ├── SKILL.md              # File skill chính (YAML frontmatter + markdown), <= 8 KB
│   ├── references/           # Tài liệu tham khảo
│   │   └── thresholds.md     # Bảng ngưỡng - nguồn sự thật duy nhất cho ngưỡng
│   ├── schemas/              # JSON Schema + jev_contract.json
│   └── scripts/              # Script hỗ trợ (nếu có)
├── _shared/                  # Nguồn sự thật dùng chung (chỉ đọc)
└── README.md                 # File này
```

Mỗi skill theo chuẩn v2.1 có cấu trúc SKILL.md cố định:
`Overview` → `When to Use` → `Prerequisites` → `Execution Steps` → `Expected Output` → `References` → `Rules (bat buoc)`.

## Cách sử dụng

### 1. Tìm skill phù hợp
- Browse theo danh mục bên dưới
- Kiểm tra tags và `description` trong frontmatter
- Đọc SKILL.md để hiểu procedure

### 2. Thực thi skill
1. Đọc **Prerequisites** – xác nhận đủ điều kiện. Thiếu điều kiện bắt buộc thì dừng và trả `insufficient_evidence`.
2. Làm theo **Execution Steps** từ 1→n
3. So sánh output với **Expected Output**
4. Lệnh khắc phục chỉ được **đề xuất**, không tự chạy

### 3. Tích hợp với ngân hàng Việt Nam
- Mốc thời gian pháp lý: `_shared/compliance-vietnam.md` (nguồn duy nhất, không trích dẫn từ trí nhớ)
- Phân cấp P1–P4 và mã leo thang: `_shared/severity-and-escalation.md`
- Khung giờ làm việc, timezone, cách tính baseline: `_shared/off-hours-and-baseline.md`
- Mapping MITRE ATT&CK / NIST CSF: `_shared/mitre-nist-mapping.md`

---

## Tài liệu dùng chung

Năm tài liệu trong `_shared/` là nguồn sự thật duy nhất cho pháp lý, thang phân cấp, khung giờ làm việc,
mapping MITRE/NIST và khối `## Rules`. Mọi skill tham chiếu chúng thay vì lặp lại.

| File | Nguồn sự thật cho |
|---|---|
| `compliance-vietnam.md` | Văn bản pháp lý, mốc thời gian, mẫu biểu |
| `severity-and-escalation.md` | Thang P1–P4, quy tắc leo thang, mã leo thang |
| `off-hours-and-baseline.md` | Khung giờ làm việc, timezone theo nguồn log, quy tắc baseline |
| `mitre-nist-mapping.md` | ID MITRE ATT&CK và NIST CSF 2.0 hợp lệ |
| `rules-block.md` | Khối `## Rules (bat buoc)` – copy nguyên văn |

---

## Danh mục Skills

18 kỹ năng, chia 3 tầng theo [ARCHITECTURE.md](ARCHITECTURE.md).

### Tier 1 – Master Playbook

| Skill | Domain | Mô tả |
|-------|--------|--------|
| [`playbook-master-incident-triage`](playbook-master-incident-triage/SKILL.md) | Incident Response | Phân loại sự cố P1–P4 và định tuyến sang playbook Tier 2 |

### Tier 2 – Incident Playbooks

| Skill | Domain | Mô tả |
|-------|--------|--------|
| [`playbook-phishing-response`](playbook-phishing-response/SKILL.md) | Incident Response | Xử lý sự cố phishing: thu thập bằng chứng mail, định phạm vi, đề xuất cách ly |
| [`playbook-abnormal-user-behavior-response`](playbook-abnormal-user-behavior-response/SKILL.md) | Incident Response | Chấm điểm và điều tra hành vi bất thường (insider threat) trên AD, mail, file |
| [`playbook-after-hours-internal-access-investigation`](playbook-after-hours-internal-access-investigation/SKILL.md) | Incident Response | Điều tra truy cập ngoài giờ vào endpoint ngân hàng, lateral movement RDP |
| [`playbook-pre-departure-insider-review`](playbook-pre-departure-insider-review/SKILL.md) | Insider Threat | Rà soát access/mail/file của nhân viên sắp nghỉ việc trước ngày khoá tài khoản |
| [`playbook-daily-watchlist-monitoring`](playbook-daily-watchlist-monitoring/SKILL.md) | Insider Threat | Đánh giá watchlist hằng ngày trên log Varonis AD, email, fileserver |

### Tier 3 – Technical Skills

| Skill | Domain | Mô tả |
|-------|--------|--------|
| [`analyzing-azure-ad-signin-logs`](analyzing-azure-ad-signin-logs/SKILL.md) | Identity Security | Phân tích log đăng nhập Entra ID: chiếm đoạt tài khoản, password spraying, impossible travel |
| [`analyzing-vpn-logs`](analyzing-vpn-logs/SKILL.md) | Network Security | Phân tích log VPN (ASA, FortiGate, Palo Alto, Pulse, Azure VPN Gateway) |
| [`analyzing-ueba-alerts-varonis`](analyzing-ueba-alerts-varonis/SKILL.md) | Threat Detection (UEBA) | Chấm điểm và phân loại alert UEBA Varonis Data Advantage |
| [`analyzing-email-audit-logs`](analyzing-email-audit-logs/SKILL.md) | Email Security | Phân tích message trace và mailbox audit: rò rỉ ra hộp cá nhân, auto-forward, abuse rule |
| [`analyzing-dlp-alerts`](analyzing-dlp-alerts/SKILL.md) | Data Loss Prevention | Phân loại cảnh báo DLP (Purview, Varonis, Symantec): true positive, policy error, cố ý |
| [`analyzing-cloud-storage-audit-logs`](analyzing-cloud-storage-audit-logs/SKILL.md) | Cloud Security | Phân tích audit log OneDrive, SharePoint, Blob, S3, Google Drive: mass download, chia sẻ ngoài |
| [`analyzing-windows-registry-for-artifacts`](analyzing-windows-registry-for-artifacts/SKILL.md) | Digital Forensics | Trích xuất artifact Windows Registry: Run key, service, UserAssist, persistence |
| [`collecting-volatile-evidence-from-compromised-host`](collecting-volatile-evidence-from-compromised-host/SKILL.md) | Incident Response | Thu thập bằng chứng volatile theo thứ tự bay hơi, chuẩn RFC 3227 |
| [`conducting-memory-forensics-with-volatility`](conducting-memory-forensics-with-volatility/SKILL.md) | Incident Response | Phân tích memory dump bằng Volatility 3: process, injection, network, credential |
| [`performing-network-forensics-with-wireshark`](performing-network-forensics-with-wireshark/SKILL.md) | Network Forensics | Phân tích PCAP bằng tshark: dựng session, trích object, nhận diện C2 và exfiltration |
| [`performing-windows-artifact-analysis-with-eric-zimmerman-tools`](performing-windows-artifact-analysis-with-eric-zimmerman-tools/SKILL.md) | Digital Forensics | Thu thập và phân tích artifact Windows bằng KAPE và Eric Zimmerman tools |
| [`dfir-cedarpelta-analysis`](dfir-cedarpelta-analysis/SKILL.md) | DFIR | Phân tích bằng chứng live-response Cedarpelta theo vòng lặp giả thuyết DFIR |

### Draft (chưa đưa vào `skills/`)

| Skill | Trạng thái | Mô tả |
|-------|-----------|--------|
| `playbook-data-leak-response` | 🟢 Draft | Xử lý vi phạm dữ liệu cá nhân, chuẩn bị hồ sơ thông báo theo pháp lý |
| `playbook-malware-response` | 🟡 Planned | Xử lý malware/ransomware với chain of custody |
| `playbook-account-takeover-response` | 🟡 Planned | Account takeover, brute force |
| `playbook-insider-threat-response` | 🟡 Planned | Điều tra insider threat tổng quát |

Thư mục `drafts/` không được push lên GitHub.

### Sắp có

- [ ] `auditing-active-directory-acl` - Kiểm tra ACL AD
- [ ] `detecting-insider-threats` - Phát hiện threat insider
- [ ] `incident-response-bank` - IR cho ngân hàng VN
- [ ] `cloud-security-assessment-m365` - Đánh giá M365
- [ ] `data-exfiltration-detection` - Phát hiện exfiltration
- [ ] `privilege-escalation-hunting` - Hunt privilege escalation

---

## Thêm skill mới

1. Tạo folder: `skills/[skill-name]/`
2. Tạo file `SKILL.md` theo format:
   - YAML frontmatter (name, description, domain, tags...)
   - Markdown body (Overview, When to Use, Prerequisites, Execution Steps, Expected Output, References, Rules)
3. Thêm `references/`, `schemas/`, `scripts/` nếu cần

### YAML Frontmatter Template

```yaml
---
name: skill-name
description: Brief description of what this skill does
domain: cybersecurity
subdomain: specific-subdomain
tags: [tag1, tag2, tag3]
version: "2.1.0"
license: Apache-2.0
compatibility: droid
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
mitre_attack: [T1048, T1078]
nist_csf: [DE.CM-1, PR.AA-05]
---
```

`mitre_attack` va `nist_csf` chi duoc dung ID co trong `_shared/mitre-nist-mapping.md`.
Moc thoi gian phap ly lay tu `_shared/compliance-vietnam.md`, phan cap P1-P4 tu `_shared/severity-and-escalation.md`,
khung gio lam viec tu `_shared/off-hours-and-baseline.md`.

### Checklist v2.1 trước khi commit

- [ ] `name` khớp tên thư mục, `compatibility: droid`, `version: 2.1.0`
- [ ] `description` tiếng Anh (hành động + khi nào dùng + ranh giới), thân skill tiếng Việt
- [ ] SKILL.md <= 8 KB
- [ ] Có `## Prerequisites` kèm quy tắc STOP và `insufficient_evidence`
- [ ] Có `## Expected Output` kèm mẫu JSON fenced
- [ ] Mọi ngưỡng nằm trong `references/thresholds.md`, không lặp lại trong thân
- [ ] Khối `## Rules (bat buoc)` copy nguyên văn `_shared/rules-block.md` và là heading cuối cùng
- [ ] Mọi ID MITRE/NIST trong frontmatter và thân đều có trong `_shared/mitre-nist-mapping.md`
- [ ] Mọi link tương đối `../<skill>/SKILL.md` và `../_shared/...` đều resolve

---

## Liên kết

- [agentskills.io](https://agentskills.io) - Open standard
- [Anthropic Cybersecurity Skills](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) - 753 skills gốc
- [MITRE ATT&CK](https://attack.mitre.org/)
- [NIST CSF 2.0](https://csrc.nist.gov/publications/detail/nist-csf-2/final)
