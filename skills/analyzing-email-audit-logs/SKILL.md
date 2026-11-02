---
name: analyzing-email-audit-logs
description: "Analyze Exchange Online message traces and M365 mailbox audit logs for exfiltration to personal mailboxes, auto-forwarding, and inbox rule abuse. Use when a message trace or mailbox audit export is supplied. Not for file server, endpoint, DLP console, or cloud storage audit analysis."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: email-security
mitre_attack: [T1114, T1048, T1078]
nist_csf: [DE.CM-1, DE.CM-3, DE.AE-1, RS.MI-1]
prerequisites:
  - Message trace / mailbox audit / unified audit log export in scope
  - Identity of the mailbox owner under investigation
  - Internal domain name and approved partner domain whitelist
  - Per-user email baseline, or an explicit statement that none exists
expected_output: JSON report with email exfiltration findings, indicators, severity, and recommended actions
tags:
  - email
  - Exchange
  - M365
  - audit-logs
  - exfiltration
  - BEC
  - phishing
  - data-leak
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Phan tich Email Audit Logs (Tier 3)

## Overview

Skill nay huong dan phan tich log kiem tra email (Exchange Online message trace, mailbox audit, unified audit log) de phat hien ro ri du lieu qua email, hop thu bi chiem doat va quy tac tu chuyen tiep bat thuong. Ket qua la bang chung email cho phong ro ri du lieu, dieu tra hanh vi bat thuong va dieu tra phishing.

Pham vi: huong email, dia chi nhan, quy tac hop thu, khoi luong tep dinh kem, baseline va IOC. Moi lenh khac phuc chi duoc DE XUAT, khong chay tu dong.

## When to Use

Dung khi:
- Phong ro ri du lieu can xac dinh email la duong ro ri.
- Dieu tra hanh vi bat thuong can doi chieu mau hanh vi gui/nhan mail theo tung nguoi dung.
- Dieu tra phishing can danh gia muc do lan toa va pham vi nhan vien bi anh huong.
- Nguoi dung cap file message trace, mailbox audit log hoac unified audit log.

Khong dung cho:
- Phan tich file server / endpoint (xem `../analyzing-ueba-alerts-varonis/SKILL.md`).
- Phan tich co canh bao DLP (xem `../analyzing-dlp-alerts/SKILL.md`).
- Phan tich log cloud storage (xem `../analyzing-cloud-storage-audit-logs/SKILL.md`).

## Prerequisites

- File export message trace / mailbox audit / unified audit log trong pham vi, co it nhat: thoi diem nhan, dia chi gui, dia chi nhan (To/CC/BCC), chu de, trang thai, kich thuoc tep dinh kem.
- Danh tinh hop thu chu so huu can dieu tra (dia chi day du) va ma nhan vien neu co.
- Danh sach domain noi bo + whitelist doi tac ngan hang do nguoi dung xac nhan.
- Baseline hanh vi gui mail cua tung nguoi dung, theo quy tac tai `../_shared/off-hours-and-baseline.md`.
- Quyen doc log hop thu (mailbox audit, inbox rule, forwarding config) khi can kiem tra quy tac chuyen tiep.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu thieu baseline: ghi ro ly do bo qua rule baseline, KHONG uoc luong gia tri baseline.

## Execution Steps

### Step 1 - Chuẩn hóa scope và dữ liệu
- Chốt khoang thoi gian, danh sách nguoi dung va nguon log trong pham vi.
- Chuan hoa ten theo quy tac so sanh ten tai `../_shared/off-hours-and-baseline.md`; trung ten -> ghi `ambiguous`, hoi nguoi dung.
- Chuan hoa timestamp: Exchange Online / Entra goc UTC -> cong 7 theo `../_shared/off-hours-and-baseline.md`.
- Cac buoc thu thap du lieu va cau lenh PowerShell: `references/collection-and-queries.md`.

### Step 2 - Phân loại hướng email (bắt buộc trước khi đánh giá rủi ro)
Khong gop chung inbound va outbound. Chi tiet dinh nghia va xu huong xem `references/analysis-playbook.md`.
- OUTBOUND: tu hop thu cong ty ra ngoai -> can dieu tra khan.
- INBOUND: tu ngoai vao hop thu cong ty -> tu chuyen tiep / staging, khong phai exfiltration.
- MIXED: thread co ca hai huong -> phan tich tung email trong thread.

### Step 3 - Phân loại domain đích
- Ap dung danh sach whitelist (domain noi bo, doi tac ngan hang, nha cung cap da duyet) do nguoi dung xac nhan.
- Domain ca nhan (freemail) va domain la khong -> gan nhan de danh gia, khong tu quyet dinh la hong.
- Danh sach phan loai domain mau: `references/analysis-playbook.md`.

### Step 4 - Phát hiện gửi standalone và hoạt động liên-mailbox
- Standalone: email chi gui ra dia chi ca nhan, khong co dia chi noi bo/doi tac trong To/CC/BCC.
- Dieu kien kich hoat phan tich lien-mailbox va quy tac so sanh: `references/thresholds.md`.
- Cross-user: dia chi ca nhan cua nguoi dung A xuat hien trong nguoi nhan cua nguoi dung B -> nghi thao tac phoi hop.
- Phan biet email gui ra ngoai doc lap voi tu chuyen tiep chinh no, hai truong hop co phan cap khac nhau.

### Step 5 - So sánh baseline
- So sanh khoi luong gui ra ngoai, kich thuoc dinh kem, so nguoi nhan ngoai, hoat dong ngoai gio voi baseline tung nguoi dung.
- Bieu mau va cach tinh delta: `references/analysis-playbook.md`.
- Rule mu + sigma chi ap dung khi du dieu kien mau theo `../_shared/off-hours-and-baseline.md`.

### Step 6 - Trích xuất IOC
- Dia chi nhan, dia chi gui, ten tep dinh kem, domain dich, quy tac hop thu, khoa du lieu.
- Chi ghi nhung gi co trong bang chung; truong khong co du lieu ghi `[NOT_AVAILABLE]` (Rules dong 1-3).

### Step 7 - Phân cấp và đề xuất hành động
- Xac dinh muc P1-P4 theo `../_shared/severity-and-escalation.md`; khong tao bang phan cap rieng.
- Neu co du lieu bi rut co bang chung -> phan cap theo bang shared, khong ha cap.
- Moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`; khong ghi so gio/ngay cuc the trong body skill.
- Danh sach hanh dong de xuat (goi y, khong chay): `references/analysis-playbook.md`.

## Expected Output

1. **Bao cao** (markdown): tom tat, bang chi tiet theo tung email dang ngo, muc P1-P4 theo `../_shared/severity-and-escalation.md`, danh sach IOC, hanh dong de xuat.
2. **JSON** (fenced, dung schema trong `references/` neu co):

```json
{
  "skill": "analyzing-email-audit-logs",
  "status": "ok",
  "scope": {
    "source": "Exchange Online message trace",
    "time_range": "NOT_AVAILABLE",
    "users": []
  },
  "summary": {
    "emails_analyzed": 0,
    "outbound_external": 0,
    "standalone_outbound": 0,
    "forwarding_rules_found": 0
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
- Quy trinh phan tich chi tiet, mau bang va quy tac domain: `references/analysis-playbook.md`
- Buoc thu thap du lieu va cau lenh PowerShell: `references/collection-and-queries.md`
- Vi du bao cao day du: `references/report-example.md`
- Muc thoi gian phap ly: `../_shared/compliance-vietnam.md`
- Bang phan cap P1-P4 va ma leo thang: `../_shared/severity-and-escalation.md`
- Gio lam viec, timezone, baseline: `../_shared/off-hours-and-baseline.md`
- Mapping MITRE ATT&CK / NIST CSF hop le: `../_shared/mitre-nist-mapping.md`
- Lien quan: `../playbook-abnormal-user-behavior-response/SKILL.md`, `../playbook-phishing-response/SKILL.md`, `../analyzing-dlp-alerts/SKILL.md`

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
