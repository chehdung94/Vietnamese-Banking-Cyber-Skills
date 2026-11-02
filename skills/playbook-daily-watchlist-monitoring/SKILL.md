---
name: playbook-daily-watchlist-monitoring
description: "Run the daily insider-threat watchlist assessment over exported Varonis AD, email, and fileserver logs. Use for the morning SOC review of the employee watchlist or when a new batch folder has been exported. Not for single-alert triage, non-watchlist populations, or mailbox-only reviews."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: insider-threat
mitre_attack: [T1074, T1114, T1048, T1530]
nist_csf: [DE.CM-3, DE.AE-3, PR.AA-05, RS.RP-1]
tags:
  - playbook
  - tier-2
  - watchlist
  - daily-monitoring
  - insider-risk
  - Varonis
  - UEBA
  - correlation
metadata:
  owner: security-skills-team
  tier: 2
  source: local
---

# Playbook: Daily Watchlist Monitoring (Tier 2)

## Overview

Playbook huong dan giam sat hang ngay nhan vien trong watchlist noi bo (insider threat).
Pham vi gom thu thap log, lam sach du lieu, tuong quan da nguon, sinh canh bao, phan loai
rui ro va leo thang. Playbook duoc xay dung tren phan tich thuc te 334 nhan su nghi viep tai
mot ngan hang thuong mai Viet Nam, voi tuong quan log AD (36.964 su kien), email (3.675 su kien)
va fileserver (12.077 su kien).

Nguon su that duy nhat cho cac moc va nguong cua playbook:

- Nguong phat hien: `references/thresholds.md`.
- Khung gio lam viec, ngoai gio va baseline: `../_shared/off-hours-and-baseline.md`.
- Phan cap P1-P4 va cua so phan hoi: `../_shared/severity-and-escalation.md`.
- Moc thoi gian phap ly va bien mau: `../_shared/compliance-vietnam.md`.

## When to Use

- Giam sat buoi sang nhan vien watchlist trong khu vuc ngan hang.
- Xu ly log Varonis da export tu AD, Email va Fileserver.
- Tuong quan hoat dong tren nhieu nguon du lieu cho cung mot nhan vien.
- Sinh bao cao tong hop hang ngay cho lanh dao SOC.
- Tim nhan vien can leo thang sang dieu tra chuyen sau.

Khong dung playbook nay cho triage mot canh bao don le, cho danh sach nhan vien khong
nam trong watchlist, hoac cho viec phan tich chi rieng mailbox.

## Prerequisites

- File watchlist (Excel/CSV) co ten nhan vien va ma nhan vien, do HR bo sung.
- Log Varonis AD (CSV) cho phan tich xac thuc.
- Log Varonis Email (CSV) cho phan tich hoat dong email.
- Log Varonis Fileserver (CSV) cho phan tich hoat dong file.
- Danh sach domain ben ngoai duoc phep dung de danh gia rui ro email.
- Baseline theo nhan vien de so sanh mu/sigma theo `../_shared/off-hours-and-baseline.md`.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2. Neu thieu
baseline de tinh mu/sigma, ghi ro ly do va bo qua rule baseline thay vi uoc luong.

## Execution Steps

### Buoc 1: Thu thap va tien xu ly log

Loc log theo watchlist, chuan hoa thoi gian theo timezone cua nguon log, phan loai ngoai gio
va loai noise. Thuc hien theo `references/log-preprocessing.md`.

### Buoc 2: Tinh chi so theo tung nguon

Tinh chi so AD, Email va Fileserver theo danh sach chi so tai `references/log-preprocessing.md`.
So lieu nguong phat hien lay tu `references/thresholds.md`. Khong tu dat nguong moi.

### Buoc 3: Tao ho so hoat dong va tuong quan da nguon

Tao ho so hop nhat cho moi nhan vien watchlist (mau tai `references/report-templates.md`) va
ap dung cac quy tac tuong quan da nguon trong `references/thresholds.md`.

### Buoc 4: Phan loai rui ro va muc phan cap

Muc phan cap P1-P4 lay theo bang trong `../_shared/severity-and-escalation.md`. Ghi ro dieu kien
nao da duoc chung minh trong bao cao. Muc alert cua playbook duoc quy doi sang P1-P4 theo file do.

### Buoc 5: Sinh bao cao ngay

Dung mau bao cao va mau JSON alert tai `references/report-templates.md`. Bao cao phai co
tom tat, bang chi tiet theo nguon, danh sach IOC (neu co) va danh sach hanh dong de xuat.

### Buoc 6: Kiem tra dieu kien leo thang va chuyen tiep

Kiem tra dieu kien leo thang trong `references/thresholds.md`. Neu thoa man, chuan bi day du
nguon du lieu bo sung theo `references/escalation-and-compliance.md`.

### Buoc 7: Chay automation (neu co batch export hop le)

Chay cac script theo thu tu trong `references/escalation-and-compliance.md`, roi doi chieu
ket qua voi phan tich cua nhan vien SOC. Ket qua script khong thay the phan tich va khong
tu quyet dinh muc phan cap.

## Expected Output

1. **Bao cao** (markdown): tom tat, bang chi tiet theo nguon, muc phan cap P1-P4 theo
   `../_shared/severity-and-escalation.md`, danh sach IOC, hanh dong de xuat.
2. **JSON** (fenced, mau minh hoa, theo mau day du tai `references/report-templates.md`):

```json
{
  "skill": "playbook-daily-watchlist-monitoring",
  "status": "ok",
  "scope": {
    "batch": "YYYY-MM-DD",
    "watchlist_users": 0,
    "sources": ["AD", "Email", "Fileserver"]
  },
  "summary": {
    "users_with_activity": 0,
    "alerts_total": 0,
    "highest_alert_level": "P4"
  },
  "findings": [],
  "indicators": [],
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

- `status` la `ok` hoac `insufficient_evidence`; khi `insufficient_evidence`, dien
  `missing_artifacts` va khong dua ra ket luan ve hanh vi.
- `severity` lay theo `../_shared/severity-and-escalation.md`.
- `recommended_actions` chi de xuat; lenh khoa tai khoan, xoa du lieu, vo hieu key hoac
  tat tinh nang chi chay khi co xac nhan ro rang cua nguoi dung (Rules dong 6).

## References

- `references/thresholds.md` - bang nguong phat hien, quy tac tuong quan va dieu kien leo thang.
- `references/log-preprocessing.md` - quy trinh lam sach, chuan hoa thoi gian, khop ten.
- `references/report-templates.md` - mau bao cao ngay, ho so hoat dong va mau JSON alert.
- `references/escalation-and-compliance.md` - leo thang, checklist cong bo viec, script.
- `references/tier_flow.md` - kien truc 3 tier va investigation window.
- `../_shared/off-hours-and-baseline.md` - gio lam viec, ngoai gio, baseline, quy tac khop ten.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 va ma leo thang.
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly va bien mau.
- `../_shared/mitre-nist-mapping.md` - danh muc MITRE ATT&CK v18 va NIST CSF 2.0 hop le.
- [`playbook-master-incident-triage`](../playbook-master-incident-triage/SKILL.md) - Tier 1, phan loai va dinh huong.
- [`playbook-pre-departure-insider-review`](../playbook-pre-departure-insider-review/SKILL.md) - review truoc khi nhan su nghi viep.
- [`playbook-abnormal-user-behavior-response`](../playbook-abnormal-user-behavior-response/SKILL.md) - dieu tra hanh vi bat thuong.
- [`analyzing-ueba-alerts-varonis`](../analyzing-ueba-alerts-varonis/SKILL.md) - phan tich UEBA Varonis.
- [`analyzing-dlp-alerts`](../analyzing-dlp-alerts/SKILL.md) - phan tich canh bao DLP.
- [`analyzing-email-audit-logs`](../analyzing-email-audit-logs/SKILL.md) - phan tich log email.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
