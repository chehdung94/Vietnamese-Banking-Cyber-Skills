---
name: analyzing-dlp-alerts
description: "Triage data-loss-prevention alerts from Microsoft Purview, Varonis, or Symantec into true positive, allowed, intentional, and policy-error cases with a P1-P4 severity. Use when a DLP alert export or a suspected data leak report is supplied. Not for raw mailbox, endpoint, or cloud storage audit log analysis."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: data-loss-prevention
mitre_attack: [T1048, T1074, T1567.002, T1115]
nist_csf: [DE.CM-3, DE.AE-1, DE.AE-3, RS.MI-1]
prerequisites:
  - DLP alert export (Purview, Varonis DLP, Symantec, or SIEM) in scope
  - Alert ID, policy name, user, timestamp, data classification, and action taken
  - DLP policy catalogue to tell correct rules from policy errors
expected_output: JSON report with alert classification, indicators, severity, and recommended actions
tags:
  - DLP
  - data-leak
  - exfiltration
  - Microsoft
  - Purview
  - Varonis
  - alerts
  - incident-response
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Phan tich DLP Alerts (Tier 3)

## Overview

Skill nay huong dan phan tich co canh bao Data Loss Prevention (DLP) de phan loai su co ro ri du
va dinh diem nguy co. Ket qua la bang chung cho phong ro ri du lieu va dieu tra hanh vi bat thuong.
Khi co nhieu nguon log (device control, endpoint, web gateway, mail), skill to hop cac su kien
than mot timeline de tim chuoi quan he nguyen nhan.

Moi hanh dong khoa, xoa, thu hoi hoac tat tinh nang chi duoc **DE XUAT**, khong chay tu dong.

## When to Use

Dung khi:
- Phong ro ri du lieu can phan tinh co canh bao DLP trong qua trinh dieu tra.
- Dieu tra hanh vi bat thuong can doi chieu canh bao DLP voi hoat dong dang nhap, file, web.
- Can phan loat canh bao thanh false positive / policy error truoc khi dua vao danh sach nguoi canh bao.
- Nguoi dung cap file export canh bao DLP hoac report tu man hinh DLP.

Khong dung cho:
- Phan tich message trace / mailbox audit (xem `../analyzing-email-audit-logs/SKILL.md`).
- Phan tich log cloud storage (xem `../analyzing-cloud-storage-audit-logs/SKILL.md`).
- Phan tich file server / endpoint (xem `../analyzing-ueba-alerts-varonis/SKILL.md`).

## Prerequisites

- File export canh bao DLP trong pham vi, co it nhat: Alert ID, loai canh bao, ten policy vi pham, nguoi dung, thoi diem, phan loai du lieu, hanh dong da thuc hien (Block/Warning/Allowed).
- Danh muc policy DLP dang ap dung de xac dinh canh bao dung rule hay sai rule.
- Pham vi thoi gian va danh sach nguoi dung can kiem tra.
- Khi canh bao lien quan den nguoi khac: log bo sung (sign-in, file activity, web gateway) neu co.
- Danh sach domain/to chuc duoc duyet de phan biet dien luong hop le.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu thieu danh muc policy: ghi `NOT_AVAILABLE` cho truong kiem tra policy, khong tu quyet dinh la policy error.

## Execution Steps

### Step 1 - Thu thap va chuan hoa alert
- Chot Alert ID, loai canh bao, policy, nguoi dung, thoi diem, phan loai du lieu, hanh dong da thuc hien.
- Chuan hoa timestamp: log Purview/Entra goc UTC -> cong 7 theo `../_shared/off-hours-and-baseline.md`.
- Cac buoc export va cau lenh truy van: `references/collection-and-queries.md`.

### Step 2 - Phan loai alert
- Phan loai: true positive / false positive / intentional (co bieu chung kinh doanh) / policy error.
- Tieu chi phan loai va bang mo ta: `references/analysis-playbook.md`.
- Khong gan nhan false positive chi vi co dau hieu giong mau - phai co bang chung.

### Step 3 - Xac dinh du lieu at risk
- Xac dinh loai du lieu (PII, tai chinh, suc khoe, thong tin dang nhap, IP/secret noi bo, noi bo).
- Bang loai du lieu va muc rui ro tham chieu: `references/analysis-playbook.md`.
- Neu khong xac dinh duoc loai du lieu -> ghi `NOT_AVAILABLE`, khong suy doan.

### Step 4 - Xac dinh duong ro ri (route)
- Phan loai duong di: email ngoai, cloud ca nhan, USB, in, cloud app, mxh.
- Bang duong va hinh thuc phat hien: `references/analysis-playbook.md`.

### Step 5 - Luong du lieu va tinh la duy lap
- Luu so ban ghi, kich thuoc tep, so nguoi nhan, so lan lap lai.
- Nguong so ban ghi cho phep phan loai xem `references/thresholds.md`.

### Step 6 - To hop timeline da nguon (khi co nhieu nguon log)
- Tao timeline thong nhat, sap xep theo thoi diem, danh dau su kien cach nhau trong khoang lien quan.
- Quy tac lien quan va cua so thoi gian lay tu `references/thresholds.md`; mau trong `references/analysis-playbook.md`.
- Danh gia hieu qua cua lop bao ve (device control, email DLP, web filter, endpoint) trong bao cao.

### Step 7 - Kiem tra hanh vi theo chuoi cua nguoi dung
- So sanh voi cac canh bao truoc do, dang nhap tu dia diem la, tai ve du lieu lon, hoat dong ngoai gio.
- Quy tac baseline: `../_shared/off-hours-and-baseline.md`. Khong co baseline -> ghi ro ly do bo qua.

### Step 8 - Phan cap, xep hang va de xuat
- Muc P1-P4 lay tu `../_shared/severity-and-escalation.md`; khong tao bang phan cap rieng.
- Xep hang uu tien xu ly (khong phai muc phan cap) theo `schemas/jev_contract.json`.
- Moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`; khong ghi so gio/ngay cuc the trong body skill.
- Hanh dong de xuat: `references/analysis-playbook.md`.

## Expected Output

1. **Bao cao** (markdown): tom tat, bang phan loai alert, muc P1-P4 theo `../_shared/severity-and-escalation.md`, danh sach IOC, hanh dong de xuat.
2. **JSON** (fenced, dung schema trong `references/` neu co):

```json
{
  "skill": "analyzing-dlp-alerts",
  "status": "ok",
  "scope": {
    "source": "Microsoft Purview DLP alert export",
    "time_range": "NOT_AVAILABLE",
    "alerts_analyzed": 0
  },
  "summary": {
    "true_positive": 0,
    "false_positive": 0,
    "intentional": 0,
    "policy_error": 0
  },
  "findings": [],
  "indicators": [],
  "severity": "P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

Vi du mau cua skill nay: `references/report-example.md`.

## References

- Bang nguong phat hien va cua so lien quan: `references/thresholds.md`
- Quy trinh phan tich, bang phan loai, duong ro ri, hanh dong de xuat: `references/analysis-playbook.md`
- Buoc export du lieu va cau lenh truy van: `references/collection-and-queries.md`
- Vi du bao cao day du: `references/report-example.md`
- Hop dong xep hang Jev (chi xep hang, khong nang muc): `schemas/jev_contract.json`
- Muc thoi gian phap ly: `../_shared/compliance-vietnam.md`
- Bang phan cap P1-P4 va ma leo thang: `../_shared/severity-and-escalation.md`
- Gio lam viec, timezone, baseline: `../_shared/off-hours-and-baseline.md`
- Mapping MITRE ATT&CK / NIST CSF hop le: `../_shared/mitre-nist-mapping.md`
- Quy tac bat buoc cua tat ca skill: `../_shared/rules-block.md`
- Lien quan: `../playbook-abnormal-user-behavior-response/SKILL.md`, `../analyzing-ueba-alerts-varonis/SKILL.md`, `../analyzing-email-audit-logs/SKILL.md`

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
