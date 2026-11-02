---
name: playbook-after-hours-internal-access-investigation
description: "Investigate remote access to a bank endpoint outside business hours, including RDP lateral movement and file access after the account disable date. Use when an RDP cache artifact, an off-hours logon, or a disabled account with activity is reported. Not for business-hours access or general malware triage."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: incident-response
mitre_attack: [T1021.001, T1078, T1098, T1071, T1048]
nist_csf: [DE.CM-3, DE.AE-3, PR.AA-05, RS.MI-1]
tags:
  - playbook
  - tier-2
  - rdp
  - lateral-movement
  - after-hours
  - compromised-account
  - insider-threat
  - banking
  - vietnam
metadata:
  owner: security-skills-team
  tier: 2
  source: local
---

# Playbook: After-Hours Internal Access Investigation (Tier 2)

## Overview

Playbook Tier 2 dieu tra truong hop **nguoi dung A truy cap vao may tinh cua nguoi dung B
qua RDP ngoai gio lam viec** (bao gom ca truong hop tai khoan da bi vo hieu hoa nhung van
co hoat dong).

Boc kinh phu vi du:
- User A (nguon) -> RDP -> may cua User B (dich).
- Thoi gian: ngoai khung gio lam viec (khung gio lay tu `../_shared/off-hours-and-baseline.md`).
- Phuong thuc: Remote Desktop Protocol.

Cac kha nang phai xac minh, khong mac dinh:
1. **Tai khoan bi chiem** - User A bi chiem doat, attacker chuyen tiep can ngang.
2. **Insider threat** - User A co tinh truy cap trai phep may cua User B.
3. **Lam dung quyen** - User A co quyen nhung dung sai muc dich.
4. **Hop le** - Ly do chinh dang (IT support, du an cap tach).

Muc phan cap P1-P4 va muc phan hoi lay tu `../_shared/severity-and-escalation.md`.
Moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`.

## When to Use

- SIEM alert: ket noi RDP ngoai gio lam viec.
- Windows Event 4624 (Logon) + 4648 (explicit credential) tu may User A den may User B.
- EDR alert: lateral movement.
- User B bao cao "co ai dang dung may tinh cua toi".
- Varonis alert: truy cap tu nguoi dung khong thuong.
- Hoat dong cua tai khoan sau ngay vo hieu hoa.

Khong dung skill nay khi:

- Truy cap xay ra trong gio lam viec va khong co dau hieu trai phep.
- Su co khong lien quan truy cap tu may nay sang may khac (chay playbook chung cho
  hanh vi bat thuong: `../playbook-abnormal-user-behavior-response/SKILL.md`).
- Yeu cau la phan tich malware.

## Prerequisites

- Bang chung RDP: Event 4624 / 4625 (may dich), Event 4648 (may nguon), 4778 / 4779.
- Log xac thuc cua User A (Azure AD / Entra sign-in, VPN) va thiet bi, dia diem.
- Log truy cap tep / EDR tren may cua User B.
- Ticket IT support, phong van User B, thong tin quan ly / HR.
- Ngay vo hieu hoa tai khoan (neu ap dung) va nhat ky dang nhap.
- Schema du lieu cua tung nguon log.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu chi thieu Event 4624, van dung duoc timeline phien RDP va phan loai pham vi, nhung
phai ghi `insufficient_evidence` cho phan xac thuc nguon va boi canh.

## Execution Steps

### Step 1: Thu thap bang chung

Trinh bay checklist RFI tai `references/rfi-checklist.md` cho nguoi dung / SOC va cap nhat
trang thai tung muc.

### Step 2: Phan tich ket noi RDP

- Lay su kien 4624 (Logon Type 10), 4778, 4779 tren may dich; 4648 tren may nguon.
- Trich xuat source IP, thoi gian, thoi luong phien, workstation nguon, tien trinh.
- Quy trinh va mau lenh PowerShell: `references/investigation-procedure.md`.

### Step 3: Phan tich xac thuc cua User A

- Dia diem, thiet bi, hoat dong truoc khi RDP, dau hieu bi chiem.
- Tier 3: `../analyzing-azure-ad-signin-logs/SKILL.md`.

### Step 4: Phan tich hoat dong tren may dich

- File nao duoc truy cap, un dung nao chay, ket noi mang, USB, lenh da chay.
- Tier 3: `../analyzing-ueba-alerts-varonis/SKILL.md`,
  `../collecting-volatile-evidence-from-compromised-host/SKILL.md`.

### Step 5: Do pham vi lateral movement

- Kiem tra User A co RDP den may khac khong; phan tich RDP cache.
- Tier 3: `../performing-windows-artifact-analysis-with-eric-zimmerman-tools/SKILL.md`,
  `../analyzing-windows-registry-for-artifacts/SKILL.md`.

### Step 6: Kiem tra boi canh va phan loai

- Business justification, ticket, quan ly, quyen cua User B, mau thuan HR.
- Ma tran quyet dinh va phan loai tinh huong: `references/context-validation.md`.

### Step 7: Xep phan cap

- Xep phan cap theo `../_shared/severity-and-escalation.md`
  (P1 Critical / P2 High / P3 Medium / P4 Low).
- Phan cap = muc cao nhat trong cac dieu kien; khong ha cap; "chua xac nhan" khong duoc
  nang len muc cao khi chi co nghi ngo.
- Ghi ro dieu kien da chung minh.
- Dieu kien kich hoat va nguong: `references/thresholds.md`.

### Step 8: Bao ton bang chung va de xuat containment

- Thu thap theo uu tien va lap chain of custody: `references/context-validation.md`.
- Ke hoach truyen thong noi bo va mau bao cao: `references/report-template.md`.
- Chi de xuat hanh dong kham phuc, khong chay tu dong (Rules dong 6).

## Expected Output

1. **Bao cao** (markdown): dien bien su co, phan cap P1-P4 theo
   `../_shared/severity-and-escalation.md`, bang chung, danh sach hanh dong de xuat,
   artefact con thieu, danh gia thong bao cho nguoi bi pham vuot theo
   `../_shared/compliance-vietnam.md`. Mau: `references/report-template.md`.
2. **JSON** (fenced):

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
    "rdp_session": {},
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

Vi du mau cua skill nay: `references/report-template.md`.

## References

- `references/thresholds.md` - dieu kien kich hoat, nguong, nguon phan cap.
- `references/rfi-checklist.md` - checklist bang chung theo nhom.
- `references/investigation-procedure.md` - quy trinh phan tich 4 phase, mau lenh PowerShell.
- `references/context-validation.md` - business justification, ma tran quyet dinh, chain of custody.
- `references/report-template.md` - ke hoach truyen thong va mau bao cao.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 (nguon duy nhat).
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly (nguon duy nhat).
- `../_shared/off-hours-and-baseline.md` - gio lam viec, timezone, baseline.
- `../_shared/mitre-nist-mapping.md` - ID MITRE ATT&CK v18 va NIST CSF 2.0 hop le.
- `../playbook-master-incident-triage/SKILL.md` - entry point phan loai su co.
- NIST SP 800-61r3 - khuyen nghia xu ly su co.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
