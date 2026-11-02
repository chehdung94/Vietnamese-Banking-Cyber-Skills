---
name: playbook-phishing-response
description: "Respond to a suspected or confirmed phishing incident by collecting email evidence, scoping delivery and clicks, and proposing containment. Use when a suspicious email, credential-harvesting page, or confirmed phishing delivery is reported. Not for malware reverse engineering, deep endpoint forensics, or non-email intrusion paths."
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
mitre_attack: [T1204, T1110, T1078, T1048, T1567.002]
nist_csf: [DE.CM-3, DE.AE-1, RS.MI-1, RS.RP-1]
tags:
  - playbook
  - tier-2
  - phishing
  - incident-response
  - checklist
  - DFIR
metadata:
  owner: security-skills-team
  tier: 2
  source: local
---

# Playbook: Phishing Incident Response (Tier 2)

## Overview

Playbook Tier 2 xu ly su co phishing: thu thap bang chung, xac dinh pham vi,
xep phan cap, va **de xuat** cac hanh dong ngan chặn cho den khi co xac nhan.

Noi dung ky thuat hien co duoc giu nguyen, chi tach phan tinh/the va tai
`references/response-procedure.md`.

## When to Use

- Nguoi dung bao cao email nghi ngo phishing, trang thu thong tin xac thuc gia, hoac
  tep dinh kem doc hai.
- Master triage da phan loai su co la phishing (credential harvesting, malware delivery
  qua email, BEC) - xem `../playbook-master-incident-triage/SKILL.md`.
- Mail gateway / SIEM / UEBA ghi nhan campaign phishing dang phat tan.

Khong dung skill nay khi:

- Da xac nhan mot vector khac phai phishing (vi du chi rom ransomware) - chay Tier 3
  phu hop.
- Yeu cau la reverse engineering malware hoac phan tich binary mau.
- Yeu cau la dieu tra loi vao khong qua email.

## Prerequisites

- Email goc (EML/MSG) cua thong bao, hoac ban trich xuat tu mail gateway.
- Mail gateway / O365 trace log de xac dinh so nguoi nhan.
- Web proxy / firewall log de xac dinh click va submit.
- Nguoi dung bi anh huong (hoac phong van) xac nhan co nhap mat khau / tai file khong.
- Truong du lieu (cot) cua tung nguon log de phan tich dung.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu chi thieu email goc, van phan tich phan tinh tach (header, URL, hash) va ghi
`insufficient_evidence` cho pham vi phat tan.

## Execution Steps

### Step 1: Thu thap bang chung

Trinh bay checklist RFI tai `references/rfi-checklist.md` cho nguoi dung / SOC.
Cap nhat trang thai tung muc (Pending / Done / NOT_AVAILABLE).

### Step 2: Phan tich email

Tach header, URL, hash tep dinh kem; kiem tra SPF / DKIM / DMARC. Chi tiet:
`references/response-procedure.md`.

### Step 3: Xac dinh pham vi

Do so email phat tan, so user click, so user submit thong tin, so tai khoan bi chiem.
Khong uoc luong: thieu log -> `[NOT_AVAILABLE]`.

### Step 4: Xep phan cap

- Xep phan cap theo bang trong `../_shared/severity-and-escalation.md`
  (P1 Critical / P2 High / P3 Medium / P4 Low).
- Phan cap = muc cao nhat trong cac dieu kien; khong ha cap; "chua xac nhan" khong duoc
  nang len muc cao khi chi co nghi ngo.
- Ghi ro dieu kien da chung minh.
- Nguong cua skill: `references/thresholds.md`.

### Step 5: De xuat ngan chặn

Dung bang lop ngan chặn (email gateway, mang, identity) trong
`references/response-procedure.md`. Chi de xuat, khong chay tu dong (Rules dong 6).

### Step 6: De xuat loai bo va khoi phuc

Mau lenh search-and-purge va ke hoach truyen thong noi bo trong
`references/response-procedure.md`. Can xac nhan cua nguoi dung truoc khi chay.

### Step 7: Bao cao

Sinh bao cao theo mau `references/report-template.md`.

## Expected Output

1. **Bao cao** (markdown): tom tat, pham vi, phan cap P1-P4 theo
   `../_shared/severity-and-escalation.md`, danh sach IOC, hanh dong de xuat,
   artefact con thieu. Mau: `references/report-template.md`.
2. **JSON** (fenced):

```json
{
  "skill": "playbook-phishing-response",
  "status": "ok|insufficient_evidence",
  "scope": {
    "total_received": null,
    "total_clicked": null,
    "total_submitted": null,
    "compromised_accounts": null
  },
  "summary": {},
  "findings": [],
  "indicators": {
    "sender_ips": [],
    "domains": [],
    "urls": [],
    "file_hashes": []
  },
  "severity": "P1|P2|P3|P4",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

Vi du mau cua skill nay: `references/report-template.md`.

## References

- `references/thresholds.md` - nguong phan cap va nguong phat hien so luong.
- `references/rfi-checklist.md` - checklist bang chung can thu thap.
- `references/response-procedure.md` - quy trinh phan tich, ngan chặn, loai bo, truyen thong.
- `references/report-template.md` - mau bao cao.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 (nguon duy nhat).
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly (nguon duy nhat).
- `../_shared/off-hours-and-baseline.md` - gio lam viec, timezone, baseline.
- `../_shared/mitre-nist-mapping.md` - ID MITRE ATT&CK v18 va NIST CSF 2.0 hop le.
- `../playbook-master-incident-triage/SKILL.md` - entry point phan loai su co.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
