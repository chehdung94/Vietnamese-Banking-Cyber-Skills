---
name: playbook-pre-departure-insider-review
description: "Review a departing employee's access, mail, and file activity before the account is disabled and assess data-exfiltration risk before cutoff. Use when HR provides a departure date and the team must review sensitive-data access. Not for daily watchlist monitoring or active incident response."
allowed-tools:
  - Read
  - Grep
  - Glob
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: insider-threat
mitre_attack: [T1048, T1074, T1114, T1530, T1567.002]
nist_csf: [DE.CM-3, DE.AE-1, PR.AA-05, RS.MI-1]
tags:
  - playbook
  - tier-2
  - pre-departure
  - insider-risk
  - offboarding
  - compliance
  - collusion
metadata:
  owner: security-skills-team
  tier: 2
  source: local
---

# Playbook: Pre-Departure Insider Risk Review (Tier 2)

## Overview

Playbook Tier 2 phuc vu review phong ngan phap cho nhan vien sap nghi viec, nhung nhan vien
duoc cap quyen truy cap du lieu nhay hoac he thong quan trong. Muc tieu: phat hien dau hieu
rut du lieu truoc khi account bi vo hieu hoa.

Noi dung dung de tham chu (case study): PREDEP-20260429-001 - hai truong phong Card
Operations / Digital Banking cua ngan hang Vietbank.

Nguyen tac chinh:
- Review nhan vien sap nghi viep co quyen du lieu nhay = **P2**
  (`../_shared/severity-and-escalation.md`).
- Ma tran 7 chi bao chi **xep hang** rui ro, khong phai phan cap su co.
- Khung dieu tra lay tu
  `../playbook-daily-watchlist-monitoring/references/tier_flow.md`.

## When to Use

- HR thong bao nhan vien nghi viep va nhan vien do co quyen truy cap du lieu khach hang hoac
  he thong nhay.
- Compliance / Legal yeu cau review phong ngan phap truoc khi offboarding.
- Nhieu nhan vien cung phong ban nghi viep dong thoi (rui ro keo theo).
- Can danh gia rui ro rut du lieu truoc moc cutoff cua account.

Khong dung skill nay khi:

- Chay dot giam sat watchlist hang ngay - dung
  `../playbook-daily-watchlist-monitoring/SKILL.md`.
- Da co su co dang dien ra (du lieu da bi rut, tai khoan bi chiem) - dung
  `../playbook-abnormal-user-behavior-response/SKILL.md`.
- Su co khong lien quan hanh vi nhan vien (vi du phishing hang loat).

## Prerequisites

- Danh tinh nguoi dung that: email, ten dang nhap, ma nhan vien (HR / IAM).
- Ngay nop don nghi viep (HR).
- Ngay nghi thuc te duoc duyet (HR).
- Ho so quyen truy cap: nhom AD, vai tro ung dung, quyen du lieu (IAM).
- Lich su hieu nang va ky luat (HR) cho chi bao Job Performance Issues.
- Schema du lieu (ten cot) cua tung nguon log.
- Baseline de so sanh hanh voi nguoi dung (neu co).

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu thieu baseline, van phan tich phan dinh tinh va ghi `insufficient_evidence` cho phan
mu/sigma; khong uoc luong (`../_shared/off-hours-and-baseline.md`).

## Execution Steps

### Step 1: Chuan bi pham vi

- Chot khung dieu tra: ngay nop don -> ngay nghi thuc te, kem buffer theo doi.
- Chot domain whitelist cho email.
- Dinh nghia ngoai gio theo `../_shared/off-hours-and-baseline.md`.
- Chi tiet va nguong: `references/thresholds.md`.

### Step 2: Phan tich da kenh

Chay cac kenh bat buoc (email, web gateway / DLP, device control, endpoint security) va
kenh VPN co dieu kien. Nguong Value Assessment cua kenh VPN do bang nguong cua skill
analyzing-vpn-logs quy dinh (`../analyzing-vpn-logs/SKILL.md`, muc "Value Assessment");
neu so cau tra loi "Khong" vuot nguong do -> can nhac SKIP va ghi ro ly do. Quy trinh chi
tiet tai `references/analysis-procedure.md`.

Tier 3: `../analyzing-email-audit-logs/SKILL.md`, `../analyzing-dlp-alerts/SKILL.md`,
`../analyzing-vpn-logs/SKILL.md`.

### Step 3: Phat hien keo theo

Khi co tu 2 nhan vien sap nghi viep: quy trinh cross-mailbox, timeline correlation,
shared resources. Chi bao: `../playbook-abnormal-user-behavior-response/references/collusion-and-dlp.md`.

### Step 4: Danh gia rui ro

- Ap dung ma tran 7 chi bao tai
  `../playbook-abnormal-user-behavior-response/references/indicators.md`, kem calibration
  rieng cho boi canh nghi viep.
- Xep hang Jev chi la xep hang.

### Step 5: Xep phan cap su co

- Xep phan cap theo `../_shared/severity-and-escalation.md`
  (P1 Critical / P2 High / P3 Medium / P4 Low).
- Review nhan vien sap nghi viep co quyen du lieu nhay = **P2**.
- Phan cap = muc cao nhat trong cac dieu kien; khong ha cap; "chua xac nhan" khong duoc
  nang len muc cao khi chi co nghi ngo.
- Ghi ro dieu kien da chung minh trong bao cao.

### Step 6: De xuat hanh dong

De xuat hanh dong kiem so (go quyen, tang cuong giam sat, chuyen quyen so huu) va hanh
dong phoi hop voi HR / Legal. Chi de xuat, khong chay tu dong (Rules dong 6).

### Step 7: Bao cao

Sinh bao cao theo mau `references/report-template.md`.

## Expected Output

1. **Bao cao** (markdown): executive summary, ho so nguoi dung, pham vi log, findings
   theo tung nguon, timeline, cross-user correlation, risk scoring, danh gia hieu luc DLP,
   ket luan va khuyen nghia. Phan cap P1-P4 theo
   `../_shared/severity-and-escalation.md`. Mau: `references/report-template.md`.
2. **JSON** (fenced):

```json
{
  "skill": "playbook-pre-departure-insider-review",
  "status": "ok|insufficient_evidence",
  "scope": {
    "user": "[user]",
    "department": "[department]",
    "resignation_date": "[date]",
    "last_working_date": "[date]",
    "investigation_window": "[start] -> [end]"
  },
  "summary": {},
  "findings": [],
  "indicators": {
    "risk_indicators": {},
    "exfiltration_indicators": {},
    "dlp_gaps": []
  },
  "jev_rank": "Critical|High|Medium|Low",
  "jev_rank_note": "Jev chi xep hang, khong phai phan cap su co",
  "severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": [],
  "skipped_analyses": []
}
```

Vi du mau cua skill nay: `references/report-template.md`.

## References

- `references/thresholds.md` - khung dieu tra, nguong kenh phan tich, whitelist, nguon phan cap.
- `references/analysis-procedure.md` - quy trinh phan tich theo kenh va calibration rieng.
- `references/report-template.md` - mau bao cao.
- `../playbook-abnormal-user-behavior-response/SKILL.md` - ma tran 7 chi bao, chung thuc.
- `../playbook-master-incident-triage/SKILL.md` - Tier 1 phan loai su co.
- `../playbook-daily-watchlist-monitoring/SKILL.md` - chay dot giam sat watchlist.
- `../playbook-daily-watchlist-monitoring/references/tier_flow.md` - cau truc 3 tier, khung dieu tra.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 (nguon duy nhat).
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly (nguon duy nhat).
- `../_shared/off-hours-and-baseline.md` - gio lam viec, timezone, baseline mu/sigma.
- `../_shared/mitre-nist-mapping.md` - ID MITRE ATT&CK v18 va NIST CSF 2.0 hop le.
- `../analyzing-email-audit-logs/SKILL.md` - Tier 3 phan tich email.
- `../analyzing-dlp-alerts/SKILL.md` - Tier 3 phan tich DLP.
- `../analyzing-vpn-logs/SKILL.md` - Tier 3 phan tich VPN va nguong Value Assessment.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
