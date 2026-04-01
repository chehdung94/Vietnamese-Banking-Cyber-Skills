# Cybersecurity Skills Library

Bộ thư viện skill bảo mật theo chuẩn [agentskills.io](https://agentskills.io) open standard.

## Cấu trúc

```
skills/
├── [skill-name]/
│   ├── SKILL.md           # File skill chính (YAML + markdown)
│   ├── references/        # Tài liệu tham khảo
│   └── scripts/          # Script hỗ trợ (nếu có)
└── README.md             # File này
```

## Cách sử dụng

### 1. Tìm skill phù hợp
- Browse theo domain folder
- Kiểm tra tags và description
- Đọc SKILL.md để hiểu procedure

### 2. Thực thi skill
1. Đọc **Prerequisites** – xác nhận đủ điều kiện
2. Làm theo **Steps** từ 1→n
3. So sánh output với **Expected Output**

### 3. Tích hợp với Vietnamese Banking
- Tham khảo **Vietnamese Banking Context** section
- Áp dụng quy định: Luật ANM, NĐ 13/2023, NHNN

---

## Danh mục Skills

### DFIR (Digital Forensics & Incident Response)

| Skill | Domain | Mô tả |
|-------|--------|--------|
| `analyzing-ueba-alerts-varonis` | Threat Detection | Phân tích alert UEBA từ Varonis |
| `conducting-memory-forensics-with-volatility` | Memory Forensics | Phân tích memory dump với Volatility |
| `performing-windows-artifact-analysis-with-eric-zimmerman-tools` | Windows Forensics | Phân tích artifact Windows với EZ Tools |
| `analyzing-windows-registry-for-artifacts` | Registry Forensics | Phân tích Windows Registry |
| `conducting-phishing-incident-response` | Incident Response | IR cho sự cố phishing |
| `triaging-security-incident-with-ir-playbook` | Incident Response | Phân loại và ưu tiên incident |
| `performing-network-forensics-with-wireshark` | Network Forensics | Phân tích network traffic với Wireshark |
| `collecting-volatile-evidence-from-compromised-host` | Live Forensics | Thu thập volatile evidence từ host bị compromise |

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
   - Markdown body (Overview, When to Use, Steps, Expected Output...)
3. Thêm references/ và scripts/ nếu cần

### YAML Frontmatter Template

```yaml
---
name: skill-name
description: Brief description of what this skill does
domain: cybersecurity
subdomain: specific-subdomain
tags: [tag1, tag2, tag3]
version: "1.0"
author: author-name
license: Apache-2.0
mitre_attack: [T1234, T5678]
nist_csf: [DE.CM-1, PR.AC-4]
---
```

---

## Liên kết

- [agentskills.io](https://agentskills.io) - Open standard
- [Anthropic Cybersecurity Skills](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) - 753 skills gốc
- [MITRE ATT&CK](https://attack.mitre.org/)
- [NIST CSF 2.0](https://csrc.nist.gov/publications/detail/nist-csf-2/final)
