---
name: analyzing-cloud-storage-audit-logs
description: "Analyze OneDrive, SharePoint, Azure Blob, AWS S3, and Google Drive audit logs for mass download, external sharing, personal cloud sync, and credential exposure. Use when a cloud storage audit export or CloudTrail log set is supplied. Not for endpoint forensics, mailbox audit, or DLP console triage."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: cloud-security
mitre_attack: [T1530, T1567.002, T1074, T1078]
nist_csf: [DE.CM-1, DE.CM-9, DE.AE-3, RS.MI-1]
prerequisites:
  - Cloud storage audit export (M365 Unified Audit, CloudTrail, Azure Storage logs, Google Cloud Audit) in scope
  - Identity of the account or user under investigation
  - Approved cloud services and external sharing policy for the organization
expected_output: JSON report with cloud activity findings, indicators, severity, and recommended actions
tags:
  - cloud
  - OneDrive
  - SharePoint
  - Azure
  - Blob
  - AWS
  - S3
  - exfiltration
  - DLP
  - CASB
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Phan tich Cloud Storage Audit Logs (Tier 3)

## Overview

Skill nay huong dan phan tich log kiem tra cloud storage (OneDrive, SharePoint, Azure Blob, AWS S3,
Google Drive) de phat hien ro ri du lieu qua dich vu cloud: tai ve hang loat, chia se ngoai,
dong bo sang cloud ca nhan va su dung khoa cong dung bi lo. Ket qua la bang chung cloud cho
phong ro ri du lieu va dieu tra hanh vi bat thuong.

Moi lenh thu hoi token, xoa share, vo hieu key hoac khoa tai khoan chi duoc **DE XUAT**,
khong chay tu dong.

## When to Use

Dung khi:
- Phong ro ri du lieu can xac dinh cloud storage la duong ro ri.
- Dieu tra hanh vi bat thuong can kiem tra thoi gian tai ve, chia se, dong bo cua nguoi dung.
- Can phat hien upload / download hang loat, chia se "anyone with link", hoac su dung access key bi lo.
- Nguoi dung cap file export log cloud (CSV/JSON) hoac CloudTrail.

Khong dung cho:
- Phan tich mailbox audit / message trace (xem `../analyzing-email-audit-logs/SKILL.md`).
- Phan tich co canh bao DLP console (xem `../analyzing-dlp-alerts/SKILL.md`).
- Phan tich file server / endpoint (xem `../analyzing-ueba-alerts-varonis/SKILL.md`).

## Prerequisites

- File export log cloud trong pham vi, co it nhat: thoi diem, nguoi dung hoac access key, hanh dong (download/upload/share/delete), ten tep hoac doi tuong, kich thuoc, dia chi IP.
- Danh tinh tai khoan hoac nguoi dung can dieu tra.
- Danh sach dich vu cloud duoc duyet va chinh sach chia se ngoai cua to chuc.
- Khi can phoi hop voi su co khac: log identity (sign-in, token) va log endpoint neu co.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu thieu danh sach dich vu duyet: ghi `NOT_AVAILABLE` cho truong nay, khong tu ket luan la duong
ro ri duy nhat.

## Execution Steps

### Step 1 - Thu thap va chuan hoa log
- Chot nen tang, pham vi thoi gian, tai khoan/nguoi dung va loai su kien trong export.
- Chuan hoa timestamp: CloudTrail, Google Cloud Audit va M365 Unified Audit goc UTC -> cong 7 theo `../_shared/off-hours-and-baseline.md`.
- Cac buoc thu thap va cau lenh theo nen tang: `references/collection-and-queries.md`.

### Step 2 - Tong hop hoat dong theo nguoi dung / tai khoan
- Tong hop so tep truy cap, dung luong tai ve, dung luong upload, so lan chia se ngoai, so tep nhay cam.
- Bieu mau tong hop: `references/analysis-playbook.md`.

### Step 3 - Phat hien tai ve hang loat (mass download)
- So sanh so tep, dung luong tai ve va khoang thoi gian voi bang nguong tai `references/thresholds.md`.
- File co phu lieu nhay cam (du phoi duong dan) deu la dau hieu can xem xet: `references/analysis-playbook.md`.

### Step 4 - Phat hien chia se ngoai
- Phan biet loai link (chi to chuc / bat ky ai co link), quyen (doc / sua / toan quyen) va nguoi nhan ngoai.
- Nguong so lan chia se lay tu `references/thresholds.md`.
- Share "anyone with link" tren tep nhay cam la dau hieu rui ro cao, nhung van phai doi chieu bang chung.

### Step 5 - Phat hien dong bo / upload sang cloud khac
- Upload sang OneDrive ca nhan, Google Drive ca nhan, Dropbox, Box khong duyet.
- Doi chieu voi danh sach dich vu duyet (dang ngoai) va voi quy tac T1567.002 trong `../_shared/mitre-nist-mapping.md`.

### Step 6 - Phat hien truy cap tu dia diem la
- IP/ISP la, quoc gia khac, thiet bi khong quan tri, truy cap ngoai gio.
- Khung gio lam viec va quy tac ngoai gio lay tu `../_shared/off-hours-and-baseline.md`.

### Step 7 - Kiem tra credential exposure
- Phat hien dang nhap bang access key/token, dung chung key nhieu nguon, key bi rot trong log.
- Khong tu thu hoi token hoac xoa key: chi de xuat, theo Rules dong 6.

### Step 8 - Phan cap, xep hang va de xuat
- Muc P1-P4 lay tu `../_shared/severity-and-escalation.md`; khong tao bang phan cap rieng.
- Xep hang uu tien xu ly (khong phai muc phan cap) theo `schemas/jev_contract.json`.
- Moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`; khong ghi so gio/ngay cuc the trong body skill.
- Hanh dong de xuat: `references/analysis-playbook.md`.

## Expected Output

1. **Bao cao** (markdown): tom tat, bang chi tiet hoat dong cloud, muc P1-P4 theo `../_shared/severity-and-escalation.md`, danh sach IOC, hanh dong de xuat.
2. **JSON** (fenced, dung schema trong `references/` neu co):

```json
{
  "skill": "analyzing-cloud-storage-audit-logs",
  "status": "ok",
  "scope": {
    "source": "M365 Unified Audit (SharePointFileOperation)",
    "time_range": "NOT_AVAILABLE",
    "users": []
  },
  "summary": {
    "files_accessed": 0,
    "files_downloaded": 0,
    "files_uploaded": 0,
    "external_shares": 0,
    "total_size_mb": 0
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

- Bang nguong phat hien: `references/thresholds.md`
- Quy trinh phan tich chi tiet, mau tong hop va hanh dong de xuat: `references/analysis-playbook.md`
- Buoc thu thap du lieu va cau lenh theo nen tang: `references/collection-and-queries.md`
- Vi du bao cao day du: `references/report-example.md`
- Hop dong xep hang Jev (chi xep hang, khong nang muc): `schemas/jev_contract.json`
- Muc thoi gian phap ly: `../_shared/compliance-vietnam.md`
- Bang phan cap P1-P4 va ma leo thang: `../_shared/severity-and-escalation.md`
- Gio lam viec, timezone, baseline: `../_shared/off-hours-and-baseline.md`
- Mapping MITRE ATT&CK / NIST CSF hop le: `../_shared/mitre-nist-mapping.md`
- Quy tac bat buoc cua tat ca skill: `../_shared/rules-block.md`
- Lien quan: `../playbook-abnormal-user-behavior-response/SKILL.md`, `../analyzing-dlp-alerts/SKILL.md`, `../analyzing-email-audit-logs/SKILL.md`

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
