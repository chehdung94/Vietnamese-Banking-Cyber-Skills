---
name: collecting-volatile-evidence-from-compromised-host
description: "Collect volatile evidence from a live compromised host in volatility order per RFC 3227, then hash and log it for chain of custody. Use at the start of live-response triage before a host is shut down, isolated or reimaged. Not for offline disk imaging and not for post-hoc analysis of an existing memory dump."
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
mitre_attack:
  - T1059.001
  - T1053.005
  - T1547.001
  - T1112
nist_csf:
  - DE.CM-9
  - DE.AE-1
  - RS.RP-1
  - RS.MI-1
tags:
  - volatile-evidence
  - live-forensics
  - memory-acquisition
  - DFIR
  - incident-response
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Collecting Volatile Evidence from Compromised Host

## Overview

Bang chung volatile chi ton tai trong RAM va mat ngay khi may tat. Skill nay cung cap quy trinh
thu thap theo thu tu bien dong (RFC 3227), bao dam artifact quan trong duoc bao ton truoc khi
buoc kiem soat (containment) va dong may.

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).
> Phan cap P1-P4: xem `../_shared/severity-and-escalation.md`.

## When to Use

- Danh ung mot su co dang dien ra tren he thong dang chay.
- Can bao ton bang chung truoc khi chuyen sang bien phap kiem soat hoac cach ly endpoint.
- Can giu memory image cho buoc phan tich RAM sau.
- Dieu tra trong khi ke tu van co the van hoat dong.
- Thu thap bang chung live-response co chu ky luu tru chuan.

**Khong dung cho skill nay:** chup dia offline (dung phan tinh dia bang KAPE/EZ Tools), phan tich
mot memory dump da co (dung `../conducting-memory-forensics-with-volatility/SKILL.md`), hoac phan tich
registry hive (dung `../analyzing-windows-registry-for-artifacts/SKILL.md`).

## Prerequisites

- Bo cong cu phap y tin cay (KAPE, Velociraptor, hoac Sysinternals) chay tu dia ngoai.
- Write-blocker hoac co che bao ve ghi read-only cho dia dich.
- Dia luu tru bang chung (o ngoai) voi du dung chua dung.
- Cong mang toi may da co (neu thu thap tu xa).
- Mau bien chain of custody va thong tin case.
- Memory image neu can: WinPmem, Magnet RAM Capture, DumpIt (Windows) hoac AVML (Linux).

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu khong co gia de luu bang chung hoac khong co quy trinh chain of custody, dung truoc khi
cham vao may - thu thap khong co ghi nhan la thu thap khong hop le.

## Execution Steps

### Step 1: Chuan bi thu thap

Tao thu muc bang chung tren dia ngoai, gan ten theo hostname va moc thoi gian thu thap.

### Step 2: Ghi nhan trang thai he thong

Hostname, ngay gio he thong (UTC va gio dia phuong), user dang chay, cau hinh mang. Moc thoi gian
nay la moc de doi chieu, khong phai moc phap ly.

### Step 3: Capture memory

Chup memory image bang WinPmem/DumpIt (Windows) hoac AVML (Linux). Day la buoc quan trong nhat
vi RAM mat truoc moi thu thap khac. Thoi gian chup anh huong ke qua phan tich sau.

### Step 4: Thu thap ket noi mang

`netstat -anob` (Windows) hoac `ss -tunap` (Linux). Giu nguyen ket qua thu thap va luu thoi diem
lay mau de dung cho phan tich.

### Step 5: Capture thong tin tien trinh

Danh sach tien trinh, dong DLL da nang tai (`listdlls`), dong lenh day du. Day la co so de doi chieu
voi memory image.

### Step 6: Capture phien dang dang nhap

Cac tai khoan dang dang nhap, phien dang mo, phien mang (`net session`). Ghi nhan user lao bien.

### Step 7: Capture cau hinh he thong

Scheduled task, service, Run key. Chi doc va export, khong sua registry.

### Step 8: Ghi nhan chain of custody

Tao manifest SHA-256 cho tat ca file da thu thap, ghi lai nguoi thu thap, thoi diem, phuong thuc
va danh sach file. Ghi ro rang trong bao cao rang viec thu thap tren may dang chay da lam thay doi
bang chung RAM.

Chi tiet command va mau bieu mau: `references/volatile-collection-sop.md`.

## Expected Output

1. **Bao cao** (markdown): danh sach bang chung da thu thap kem hash SHA-256, thu tu thu thap theo
   RFC 3227, moc thoi gian phat hien va moc phap ly theo `../_shared/compliance-vietnam.md`, gioi han
   cua bang chung live, phan cap P1-P4 theo `../_shared/severity-and-escalation.md`.
2. **JSON** (mau):

```json
{
  "skill": "collecting-volatile-evidence-from-compromised-host",
  "status": "ok",
  "scope": {
    "hostname": "",
    "os_version": "",
    "collection_start_utc": "",
    "collection_end_utc": ""
  },
  "summary": {},
  "findings": [],
  "indicators": [],
  "severity": "P3",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

## References

- `references/thresholds.md` - bang thu tu bien dong va tieu chi chat duoc cho buoc ban giao.
- `references/volatile-collection-sop.md` - command thu thap, khai niem then chot, bang cong cu.
- `../_shared/mitre-nist-mapping.md` - danh sach ID MITRE ATT&CK v18 va NIST CSF 2.0 duy nhat.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 va ma leo thang.
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly.
- `../_shared/off-hours-and-baseline.md` - khung gio lam viec khi chuan hoa moc thoi gian.
- RFC 3227 - Guidelines for Evidence Collection and Archiving.
- NIST SP 800-61r3 - Incident Handling Recommendations.
- NIST SP 800-86 - Guide to Integrating Forensic Techniques into Incident Response.

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
