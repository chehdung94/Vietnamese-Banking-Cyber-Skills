# Ke hoach truyen thong va bao cao - playbook-after-hours-internal-access-investigation

## 1. Ke hoach truyen thong noi bo

Cac moc T+ la SLA phong giao noi bo, khong phai moc thoi gian phap ly.
Moc thoi gian phap ly: xem `../_shared/compliance-vietnam.md`.
Muc phan hoi theo phan cap su co: `../_shared/severity-and-escalation.md`.

| Moc | Doi tieu | Noi dung | Kenh |
|---|---|---|---|
| T+15 phut | Security Lead | Truy cap RDP khong duoc phep: User A -> User B. Dang dieu tra. | Teams/Slack |
| T+30 phut | User B (khuem the) | Co nguoi vua truy cap may tinh cua ban qua RDP. | Dien thoai / truc tiep |
| T+1 gio | CISO (neu phan cap cao) | Ngh chuyen tiep can ngang. Kem bao cao so bo. | Email |
| T+2 gio | IT Ops | Bat giam sat nang cao cho User A. KHONG vo hieu hoa tai khoan. | Ticket |
| T+4 gio | HR (neu nghi nho insider) | Can ho so nhan vien cua User A. Bao mat. | Dien thoai |
| T+24 gio | Legal / Compliance | Dieu tra hoan tat. Bang chung da bao ton. | Email |

## 2. Timeline tong hop (mau)

```
Timeline: dieu tra truy cap RDP ngoai gio
═══════════════════════════════════════════════════════════════════

Phase 1: Initial access (User A)
[YYYY-MM-DD HH:MM:SS] User A dang nhap tu IP ben ngoai
[YYYY-MM-DD HH:MM:SS] User A xac thuc vao domain
[YYYY-MM-DD HH:MM:SS] Event 4648: User A mo RDP den may User B
                      Source: may cua User A
                      Target: [hostname dich]
                      Process: mstsc.exe

Phase 2: Chuyen tiep can (RDP)
[YYYY-MM-DD HH:MM:SS] Event 4624 Type 10: RDP logon thanh cong
                      Source IP: [IP]
                      Target: [may dich]
[YYYY-MM-DD HH:MM:SS] Session established
[YYYY-MM-DD HH:MM:SS] User A co desktop access

Phase 3: Doi tham va truy cap du lieu
[YYYY-MM-DD HH:MM:SS] explorer.exe duoc mo
[YYYY-MM-DD HH:MM:SS] Duyet [thu muc cua User B]
[YYYY-MM-DD HH:MM:SS] [tep nhay] duoc mo (Event 4663)

Phase 4: Nghi exfiltration
[YYYY-MM-DD HH:MM:SS] chrome.exe duoc mo
[YYYY-MM-DD HH:MM:SS] Ket noi toi [dich ben ngoai]
[YYYY-MM-DD HH:MM:SS] [khoi luong] du lieu duoc truyen (EDR alert)

Phase 5: Ket thuc phien
[YYYY-MM-DD HH:MM:SS] Event 4779: Session disconnected
[YYYY-MM-DD HH:MM:SS] User A logout khoi domain
[YYYY-MM-DD HH:MM:SS] VPN disconnected
═══════════════════════════════════════════════════════════════════
Session duration: [duration]
Data accessed: [so tep]
Potential exfiltration: [khoi luong] toi [dich]
```

## 3. Bao cao dieu tra (JSON)

```json
{
  "skill": "playbook-after-hours-internal-access-investigation",
  "status": "ok|insufficient_evidence",
  "investigation_id": "RDP-AH-YYYYMMDD-XXX",
  "scope": {
    "user_a": "[user]",
    "user_b": "[user]",
    "investigation_window": "[start] -> [end]"
  },
  "summary": {},
  "findings": [],
  "indicators": {
    "rdp_session": {
      "start_time": "[timestamp]",
      "end_time": "[timestamp]",
      "duration_minutes": null,
      "source_ip": null,
      "target_computer": "[hostname]"
    },
    "data_accessed": [],
    "potential_exfiltration": null,
    "exfiltration_destination": null
  },
  "severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": [],
  "evidence_collected": [],
  "legal_status": "[trang thai bao ton bang chung]"
}
```

## 4. Bao cao tom tat cho lanh dao (markdown)

```markdown
## Bao cao dieu tra truy cap RDP trai phep ngoai gio

**Thong tin chung:**
- Ma su co: RDP-AH-YYYYMMDD-XXX
- Loai: Truy cap RDP khong duoc phep ngoai gio
- Phan cap: [P1 Critical | P2 High | P3 Medium | P4 Low]
- Dieu kien da chung minh: [ghi ro dieu kien nao co bang chung]

**Cac ben lien quan:**
- Nguoi truy cap (User A): [ten], [phong ban], [chuc vu]
- Nguoi bi truy cap (User B): [ten], [phong ban], [chuc vu]

**Dien bien su co:**
- Thoi gian: [khoang thoi gian]
- Phuong thuc: RDP tu may User A den may User B
- Thoi luong phien: [duration]

**Phat hien:**
- [x] Khong co ticket ho tro hop le
- [x] User B khong biet ve viec truy cap
- [x] Quan ly khong duoc thong bao
- [ ] Truy cap tep nhay: [danh sach tep]
- [ ] Dau hieu chuyen du lieu ra ngoai: [mo ta]

**Ket luan:** [ket luan, tach bang chung / gia thuyet]

**Khuyen nghia (de xuat, khong tu thuc thi):**
1. [de xuat]

**Danh gia thong bao cho nguoi bi pham vuot:** theo
`../_shared/compliance-vietnam.md`

**Bang chung phap ly:** Da thu thap va bao quan theo quy trinh.
```

## 5. Quy tac

- `severity` lay tu `../_shared/severity-and-escalation.md`.
- Truong khong co du lieu ghi `[NOT_AVAILABLE]` va day vao `missing_artifacts`.
- Hanh dong kham phuc chi de xuat.
- Khong trich dan moc thoi gian phap ly tu tri nho; luon tro toi
  `../_shared/compliance-vietnam.md`.
