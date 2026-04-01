# Kiến trúc Phân tầng Kỹ năng DFIR (3-Tier DFIR Architecture)

Tài liệu này quy định cấu trúc phân tầng cho hệ thống kỹ năng Phản ứng sự cố (Incident Response - IR) và Điều tra số (Digital Forensics - DFIR) sử dụng chuẩn agentskills.io.

Mục tiêu của kiến trúc này là tách biệt rõ ràng giữa **Quy trình điều phối sự cố (Playbooks)** và **Thao tác kỹ thuật (Technical Skills)**, giúp hệ thống hoạt động giống như một trung tâm SOC thực thụ.

---

## 1. Cấu trúc 3 Tầng (3-Tier Architecture)

### 🎯 Tầng 1: Master Playbook (Tổng đài Phân loại & Điều hướng)
- **Vai trò:** Điểm tiếp nhận đầu tiên (Entry Point) của mọi báo cáo sự cố.
- **Nhiệm vụ:** 
  1. Thu thập thông tin ban đầu.
  2. Đánh giá mức độ nghiêm trọng (Severity: P1, P2, P3, P4).
  3. Phân loại sự cố (Categorization).
  4. Điều hướng (Route) luồng xử lý sang Playbook tương ứng ở Tầng 2.

### 🗂️ Tầng 2: Incident Playbooks (Kịch bản Phản ứng Sự cố Cụ thể)
- **Vai trò:** "Chỉ huy sự cố" (Incident Commander) cho từng loại tấn công.
- **Nhiệm vụ:**
  1. Đưa ra **Checklist Bằng chứng (Evidence)** cần thu thập (Log, File, PCAP...).
  2. Kích hoạt **Kế hoạch Trao đổi nội bộ (Communication Plan)** (Báo cáo ai, khi nào).
  3. Cung cấp **Khuyến nghị Ngăn chặn Tạm thời (Interim Containment)**.
  4. Gọi các kỹ năng Tầng 3 để phân tích sâu bằng chứng thu thập được.

### 🛠️ Tầng 3: Technical Skills (Kỹ năng Kỹ thuật & Công cụ)
- **Vai trò:** Công cụ thực thi kỹ thuật chuyên sâu (DFIR Tools).
- **Nhiệm vụ:**
  1. Tiếp nhận tệp tin, logs, hoặc bằng chứng từ Tầng 2.
  2. Chạy công cụ/script phân tích (Volatility, Wireshark, KAPE...).
  3. Trích xuất IOCs (IP, Domain, Hash) và trả kết quả dạng JSON về cho Tầng 2.

---

## 2. Quy ước Đặt tên (Naming Convention)

Để hệ thống phẳng (flat folder structure) của `agentskills.io` vẫn đảm bảo tính phân tầng rõ ràng, chúng ta sử dụng tiền tố (prefix) cho tên thư mục và tên skill:

| Tầng | Tiền tố (Prefix) | Ví dụ Tên Thư mục / Skill |
|---|---|---|
| **Tier 1** | `playbook-master-` | `playbook-master-incident-triage` |
| **Tier 2** | `playbook-[loại_sự_cố]-` | `playbook-phishing-response`<br>`playbook-ransomware-response`<br>`playbook-insider-threat-response` |
| **Tier 3** | `[động_từ]-` *(chuẩn gốc)* | `analyzing-windows-registry`<br>`collecting-volatile-evidence`<br>`performing-memory-forensics` |

---

## 3. Đề xuất Cấu trúc Thư mục Hệ thống

Dựa trên quy ước trên, thư mục `skills/` sẽ được tổ chức như sau:

```text
skills/
├── ARCHITECTURE.md                                 # File giải thích kiến trúc này
├── README.md                                       # Hướng dẫn sử dụng chung
│
├── playbook-master-incident-triage/                # [TIER 1] Master Triage
│   └── SKILL.md
│
├── playbook-phishing-response/                     # [TIER 2] Phishing Playbook
│   └── SKILL.md
├── playbook-malware-response/                      # [TIER 2] Malware Playbook
│   └── SKILL.md
│
├── analyzing-ueba-alerts-varonis/                  # [TIER 3] Phân tích UEBA
│   └── SKILL.md
├── analyzing-windows-registry-for-artifacts/       # [TIER 3] Phân tích Registry
│   └── SKILL.md
├── collecting-volatile-evidence-from-host/         # [TIER 3] Thu thập RAM/Network
│   └── SKILL.md
├── conducting-memory-forensics-with-volatility/    # [TIER 3] Phân tích Memory
│   └── SKILL.md
└── performing-network-forensics-with-wireshark/    # [TIER 3] Phân tích PCAP
    └── SKILL.md
```

---

## 4. Luồng Phối hợp (Workflow Example)

**Sự cố:** Một nhân viên (Phòng Kế toán) báo cáo máy tính chạy chậm và có file bị đổi đuôi lạ sau khi click vào link email.

1. **User (Admin)**: Kích hoạt Agent xử lý sự cố máy tính kế toán.
2. **Agent -> Tier 1 (`playbook-master-incident-triage`)**:
   - Đánh giá: Có dấu hiệu Malware/Ransomware + Nguồn lây từ Email. Severity: P2 (High).
   - Quyết định: Gọi Tier 2 Playbook Malware.
3. **Agent -> Tier 2 (`playbook-malware-response`)**:
   - Đưa Checklist cho Admin: "Cần RAM dump (ưu tiên 1), cách ly máy khỏi mạng (ưu tiên 2), báo cáo CISO (ưu tiên 3)".
   - Admin cách ly máy và lấy được file `memory.raw`, gửi lại cho Agent.
4. **Agent -> Tier 3 (`conducting-memory-forensics`)**:
   - Agent phân tích `memory.raw`, tìm ra process độc hại và IP máy chủ C2.
   - Trả kết quả (IOCs) về cho Tier 2.
5. **Agent -> Tier 2 (Tổng hợp)**:
   - Lập báo cáo cuối cùng: "Ransomware lây qua Phishing. Đã có IP C2. Đề nghị chặn IP này trên toàn hệ thống Firewall."