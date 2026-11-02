---
name: playbook-master-incident-triage
description: "Classify a security incident into P1-P4 and route it to the matching Tier 2 playbook. Use as the entry point for any new incident report, alert, or request that names an incident type. Covers severity assignment and routing only, not deep technical analysis, forensics, or remediation execution."
allowed-tools:
  - Read
  - Grep
  - Glob
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: incident-response
mitre_attack: [T1048, T1567.002, T1074, T1114, T1530, T1110, T1078, T1021.001, T1071, T1098, T1204, T1486]
nist_csf: [DE.AE-1, DE.AE-3, DE.CM-3, RS.RP-1, RS.MI-1]
tags:
  - playbook
  - master
  - triage
  - severity-classification
  - routing
  - DFIR
metadata:
  owner: security-skills-team
  tier: 1
  source: local
---

# Playbook: Master Incident Triage

## Overview

Day la Tier 1 - entry point cua chuong trinh ung pho su co. Skill nay lam ba viec:
thu thap mo ta su co, xep phan cap P1-P4, va dieu huong sang dung Tier 2 playbook.

Skill khong phai hieu ke su co, khong chay lenh kham phuc, khong thu thap bang chung
sau nhieu, khong chan doan exfiltration. Nhiem vu cua no la **quyet dinh ai phuong tiep
theo, trong bao lau**.

Thang phan cap, muc phan hoi va danh sach nguoi duoc bao lay tu
`../_shared/severity-and-escalation.md` (ban duy nhat). Cau truc 3 tier xem
`../playbook-daily-watchlist-monitoring/references/tier_flow.md`.

## When to Use

- Co alert, bao cao su co, ticket hoac yeu cau moi chua duoc phan loai.
- Can xep phan cap P1-P4 va chon Tier 2 playbook xu ly.
- Nhieu su do chay song song, can uu tien thu tu xu ly.
- Can tao ban ghi triage ban dau cho su co.

Khong dung skill nay khi:

- Da bi route vao mot Tier 2 playbook cu the (chay Tier 2 do).
- Yeu cau la phan tich ky thuat cu the (UEBA, DLP, email audit, VPN, AD sign-in).
- Yeu cau la chay kham phuc truc tiep (thu thap memory, dong goi file, xoa file).

## Prerequisites

- Mo ta su do goc: nguon phat hien, thoi diem phat hien, thoi diem xay ra hanh vi.
- Pham vi he thong / tai khoan / du lieu lien quan.
- Bang chung hien co: log, alert, ticket, phong van nguoi dung, bao cao nhan vien.
- Thong tin HR lien quan neu co (ngay nghi viep, thay doi vai tro) cho su co insider.
- Khoi luong ngay lam viec va khung gio lam viec de xac dinh su co ngoai gio
  (`../_shared/off-hours-and-baseline.md`).

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu chi thieu mo ta su co toi thieu (khong ro su co xay ra khi nao, pham vi gi), dung
tai Step 1 va tra `insufficient_evidence` kem danh sach artefact can bo sung.

## Execution Steps

### Step 1: Initial identification

Thu thap va ghi lai (khong suy doan):

- He thong nao bi anh huong.
- Timeline: thoi diem xay ra hanh vi va thoi diem phat hien (hai moc khac nhau).
- Nguon phat hien (EDR / SIEM / mail gateway / UEBA / bao cao nguoi dung).
- Du lieu nao co the bi anh huong va co bang chung chua.

Bang ghi trieu chung: xem `references/report-template.md`.

### Step 2: Initial assessment

Kiem tra cac dau hieu quyet dinh muc do:

- Hanh vi con dang tiep tuc hay da dung.
- Co dau hieu lateral movement khong.
- Da co bang chung du lieu bi truy cap hoac bi rut khong.
- He thong backup co dung khong.
- Co ba hieu attacker da bi chan hay chua.

### Step 3: Severity assignment

- Xep phan cap theo bang trong `../_shared/severity-and-escalation.md`
  (P1 Critical / P2 High / P3 Medium / P4 Low).
- Phan cap = muc cao nhat trong cac dieu kien. Khong ha cap.
- Dieu kien "chua xac nhan" khong duoc nang len P1/P2 khi chi co nghi ngo.
- Review nguoi sap nghi viep co quyen du lieu nhay = **P2** (theo
  `../playbook-daily-watchlist-monitoring/references/tier_flow.md`).
- Ghi ro dieu kien nao da chung minh trong bao cao.
- Chi tiet nguong va muc phan hoi: `references/thresholds.md`.

### Step 4: Routing decision

Chon Tier 2 playbook theo bang dieu huong tai `references/routing-matrix.md`.
Neu khong co skill phu hop trong repo, ghi `NOT_AVAILABLE` va leo thang CIRT,
khong tu viet lai playbook.

### Step 5: Handoff to Tier 2

- Dung playbook nay. Load `SKILL.md` cua Tier 2 da chon va trinh bay checklist
  bang chung ma no can.
- Ban giao phai kem: phan cap, dieu kien da chung minh, artefact con thieu, moc thoi gian.
- Hanh dong kham phuc chi duoc **de xuat**, khong chay tu dong (Rules dong 6).

## Expected Output

1. **Bao cao** (markdown): tom tat su co, phan cap P1-P4 va dieu kien da chung minh,
   danh sach IOC, hanh dong de xuat, artefact con thieu. Mau: `references/report-template.md`.
2. **JSON** (fenced):

```json
{
  "skill": "playbook-master-incident-triage",
  "status": "ok|insufficient_evidence",
  "scope": {},
  "summary": {},
  "findings": [],
  "indicators": [],
  "severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": [],
  "routing": {
    "playbook": "[ten skill Tier 2 hoac NOT_AVAILABLE]",
    "escalation": "required|not_required"
  }
}
```

Vi du mau cua skill nay: `references/report-template.md`.

## References

- `references/thresholds.md` - nguong phan cap, muc phan hoi, uu tien tai san.
- `references/routing-matrix.md` - loai su co -> Tier 2 playbook.
- `references/report-template.md` - mau ban ghi triage va bao cao tom tat.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 (nguon duy nhat).
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly (nguon duy nhat).
- `../_shared/off-hours-and-baseline.md` - gio lam viec, timezone, baseline.
- `../_shared/mitre-nist-mapping.md` - ID MITRE ATT&CK v18 va NIST CSF 2.0 hop le.
- NIST CSF 2.0: DE.AE-1, DE.AE-3, DE.CM-3, RS.RP-1, RS.MI-1.
- `../playbook-daily-watchlist-monitoring/references/tier_flow.md` - cau truc 3 tier.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
