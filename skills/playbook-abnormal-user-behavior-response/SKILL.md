---
name: playbook-abnormal-user-behavior-response
description: "Score and investigate insider-threat signals across AD, mail, and file activity with the shared 7-indicator matrix, then assign severity from the shared escalation scale. Use when a user profile deviates from its baseline or a watchlist alert needs a risk level. Not for daily batch watchlist monitoring, pre-departure reviews, or malware forensics."
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
mitre_attack: [T1074, T1114, T1098, T1526, T1048, T1530, T1115]
nist_csf: [DE.CM-3, DE.AE-1, DE.AE-3, PR.AA-05, RS.MI-1]
tags:
  - playbook
  - tier-2
  - UEBA
  - abnormal-behavior
  - insider-threat
  - Varonis
  - incident-response
  - DFIR
metadata:
  owner: security-skills-team
  tier: 2
  source: local
---

# Playbook: Abnormal User Behavior Response (Tier 2)

## Overview

Playbook Tier 2 xu ly su co hanh vi bat thuong cua nguoi dung phat hien boi UEBA
(Varonis, Microsoft Purview, SpectorSoft) hoac boi SOC. Noi dung gom: thu thap bang
chung, cham diem theo ma tran 7 chi bao, phan tich hoat dong tep tin, phat hien keo theo
(collusion), danh gia hieu luc DLP, va xep phan cap su co.

**Ranh gioi quan trong:** Jev (va ma tran 7 chi bao) chi **xep hang** rui ro de uu tien
dieu tra. Jev **khong bao gio nang muc phan cap**. Muc phan cap P1/P2/P3/P4 va muc phan
hoi lay tu `../_shared/severity-and-escalation.md`. Cac diem va nguong cua skill nay nam
o `references/thresholds.md`.

## When to Use

- UEBA alert (behavioral anomaly) dang cho review.
- Truy cap du lieu bat thuong hoac vuot pham vi cong viec.
- Ngh nho insider threat, lam dung quyen, hoac tai khoan bi chiem phat hien qua hanh vi.
- Da co bang chung du lieu bi rut boi nguoi dung da xac thuc.

Khong dung skill nay khi:

- Chay dot giam sat watchlist hang ngay theo lo - dung
  `../playbook-daily-watchlist-monitoring/SKILL.md`.
- Review truoc khi nhan vien nghi viep theo quy trinh HR - dung
  `../playbook-pre-departure-insider-review/SKILL.md`.
- Yeu cau la dinh ky malware, phan tich binary hoac dieu tra ky thuat sau pham vi
  (dung Tier 3 tuong ung).

## Prerequisites

- Chi tiet alert UEBA: alert id, ten rule, nguoi dung/ tai khoan, thoi diem phat hien.
- Ho so nguoi dung: ten day du, ma nhan vien, phong ban, chuc vu, thoi gian lam viec.
- Baseline cua nguoi dung (file baseline theo user) cho ky tuong du lieu.
- Log hoat dong: AD / Entra sign-in, file server, email audit, cloud storage, DLP, EDR.
- Ngữ kiện HR trong ky (nghi viep, ky luat, chuyen cong viec) va ngữ kiện peer group.
- Schema du lieu (ten cot) cua tung nguon log.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu thieu file baseline, van phan tich phan dinh tinh va ghi ro rang rang
`insufficient_evidence` cho phan mu/sigma; khong uoc luong mu/sigma
(`../_shared/off-hours-and-baseline.md`).

## Execution Steps

### Step 1: Thu thap alert va context

Ghi nhan alert id, rule, user, thoi diem phat hien, va nguon phat hien. Kiem tra ngay
các ngữ kiện co the giai thich hanh vi: nghi phep, du an co phep truy cap, script
tu dong hoa duoc duyet, PIP, don nghi viep, thay doi chuc vu.

### Step 2: Danh gia baseline

- Baseline lay theo `../_shared/off-hours-and-baseline.md` (chu ky ngay lam viec, so mau
  toi thieu, quy tac mu/sigma, quy tac so sanh ten).
- So sanh hanh vi hien tai voi baseline va voi peer group.
- Khong co baseline -> `insufficient_evidence`, khong uoc luong.

### Step 3: Cham diem theo ma tran 7 chi bao

- Ap dung ma tran 7 chi bao va huong dan calibration tai `references/indicators.md`.
- Diem chi co gia tri khi kem rationale tu log/bang chung cu the.
- Tong diem = tong truc tiep 7 chi bao, khong ap cap.
- Hop nhat voi hop dong cham diem tai `schemas/jev_contract.json`
  (`calibrated: false`, Jev chi xep hang).
- Context dinh tinh tach rieng, khong cong vao diem chinh thuc.

### Step 4: Phat hien hoat dong tep tin

Phan tich theo `references/file-activity-analysis.md`: thu thap log, dung baseline,
so sanh nguong, ghi nhan bang chung. Nguong: `references/thresholds.md`.

### Step 5: Cross-user va hieu luc DLP

Chi chay khi co tu 2 nguoi dung trong pham vi dieu tra. Quy trinh phat hien keo theo va
ma tran hieu luc DLP tai `references/collusion-and-dlp.md`.

### Step 6: Xep phan cap su co

- Xep phan cap theo `../_shared/severity-and-escalation.md`
  (P1 Critical / P2 High / P3 Medium / P4 Low).
- Phan cap = muc cao nhat trong cac dieu kien; khong ha cap; "chua xac nhan" khong duoc
  nang len muc cao khi chi co nghi ngo.
- Xep hang Jev khong quyet dinh phan cap.
- Ghi ro dieu kien nao da chung minh trong bao cao.

### Step 7: De xuat containment va truyen thong

Ke hoach truyen thong noi bo, khuyen nghia tam thoi, va hanh dong ngan han / hardening
tai `references/response-procedure.md`. Chi de xuat, khong chay tu dong (Rules dong 6).

### Step 8: Bao cao

Sinh bao cao theo mau `references/report-template.md`.

## Expected Output

1. **Bao cao** (markdown): thong tin nguoi dung, mo ta bat thuong, xep hang Jev,
   phan cap P1-P4 theo `../_shared/severity-and-escalation.md`, bang chung chinh,
   khuyen nghia, artefact con thieu. Mau: `references/report-template.md`.
2. **JSON** (fenced):

```json
{
  "skill": "playbook-abnormal-user-behavior-response",
  "status": "ok|insufficient_evidence",
  "scope": {
    "user": "[user]",
    "department": "[department]",
    "investigation_window": "[start] -> [end]"
  },
  "summary": {},
  "findings": [],
  "indicators": {
    "risk_indicators": {},
    "data_accessed": {},
    "exfiltration_indicators": {}
  },
  "jev_rank": "Critical|High|Medium|Low",
  "jev_rank_note": "Jev chi xep hang, khong phai phan cap su co",
  "severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

Vi du mau cua skill nay: `references/report-template.md`.

## References

- `schemas/jev_contract.json` - hop dong cham diem Jev (calibrated: false).
- `references/thresholds.md` - nguong cham diem, nguong hoat dong tep, kenh exfiltration.
- `references/indicators.md` - ma tran 7 chi bao, calibration, red flags, context dinh tinh.
- `references/file-activity-analysis.md` - phan tich hoat dong tep tin va vi du.
- `references/collusion-and-dlp.md` - cross-user correlation va danh gia hieu luc DLP.
- `references/response-procedure.md` - truyen thong, containment, hardening, false positive.
- `references/report-template.md` - mau bao cao.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 (nguon duy nhat).
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly (nguon duy nhat).
- `../_shared/off-hours-and-baseline.md` - gio lam viec, timezone, baseline mu/sigma.
- `../_shared/mitre-nist-mapping.md` - ID MITRE ATT&CK v18 va NIST CSF 2.0 hop le.
- `../playbook-master-incident-triage/SKILL.md` - entry point phan loai su co.
- `../analyzing-ueba-alerts-varonis/SKILL.md` - Tier 3: phan tich UEBA alert Varonis.
- `../analyzing-dlp-alerts/SKILL.md` - Tier 3: phan tich DLP alert.
- `../analyzing-email-audit-logs/SKILL.md` - Tier 3: phan tich email audit log.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
