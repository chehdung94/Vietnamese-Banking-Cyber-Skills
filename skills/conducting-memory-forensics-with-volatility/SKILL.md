---
name: conducting-memory-forensics-with-volatility
description: "Run Volatility 3 against a memory dump to recover process, injection, network and credential artifacts. Use when a memory image in .raw, .mem or .dmp format is available for a DFIR case. Requires Volatility 3 and matching symbol tables. Not for disk image forensics and not for live memory acquisition on a running host."
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
  - T1003.001
  - T1071
  - T1021.001
  - T1112
nist_csf:
  - DE.CM-9
  - DE.AE-1
  - DE.AE-3
  - RS.RP-1
tags:
  - memory-forensics
  - volatility
  - RAM-analysis
  - process-injection
  - DFIR
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Conducting Memory Forensics with Volatility

## Overview

RAM giu trang thai chay cua he thong: tien trinh, ket noi mang, vung bo nho cua malware
fileless, key giai ma va credential. Skill nay huong dan phan tich memory dump bang Volatility 3
de xac nhung bang chung khong con tren dia.

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).
> Phan cap P1-P4: xem `../_shared/severity-and-escalation.md`.

## When to Use

- Endpoint da duoc kiem soat trong su co dang dien ra va can bao ton bang chung volatile.
- EDR bao cao co process injection hoac malware chi ton tai trong RAM.
- Can khoi phuc khoa giai ma tren may bi ransomware truoc khi tat may.
- Nghi nghap credential theft (Mimikatz, dump LSASS) va can xac nhan bang bang chung.
- Nghi nghap rootkit muc kernel, phan tinh dia khong du.

**Khong dung cho skill nay:** phan tich dia (dung `../performing-windows-artifact-analysis-with-eric-zimmerman-tools/SKILL.md`),
thu thap memory tren host dang chay (dung `../collecting-volatile-evidence-from-compromised-host/SKILL.md`),
hoac phan tich registry hive (dung `../analyzing-windows-registry-for-artifacts/SKILL.md`).

## Prerequisites

- Memory image: file `.raw`, `.mem` hoac `.dmp` (Windows) / `.lime` (Linux), kem hash SHA-256.
- Volatility 3 cai dat, Python 3.8+ va symbol tables tuong ung phien ban Windows cua image.
- Dung luong dia de luu output plugin.
- YARA rules cho phat hien malware trong RAM (neu dung).
- Baseline tien trinh va DLL binh thuong cho phien ban OS dang phan tich.
- Ghi nhan chain of custody cho memory image.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu thieu symbol table phu hop phien ban OS, hoac memory image khong doc duoc, dung ngay - khong
duoc doan bang bang chung khac.

## Execution Steps

### Step 1: Chot danh tinh memory image

Ghi ten file, kich thuoc, hash SHA-256 va nguon thu thap. Khong chay phan tich tren file khong
xac dinh duoc.

### Step 2: Xac dinh OS va profile

Chay `vol -f <image> windows.info`. Profile phai khop phien ban OS cua image; neu khong co profile
phu hop, dung theo STOP rule.

### Step 3: Phan tich tien trinh

Chay `windows.pslist` (danh sach tien trinh), `windows.pstree` (cay cha-con) va `windows.psscan`
(tim tien trinh khong lien ket). Luon so sanh `pslist` voi `psscan` truoc khi ket luan co tien
trinh bi giau.

### Step 4: Kiem tra ket noi mang

Chay `windows.netscan`. Ket noi ngoai khong doi chieu voi danh sach dich vu hop le la dau hieu
can dien tra. Doi chieu them voi proxy/DNS log de xac nhan.

### Step 5: Phat hien injection va malware

Chay `windows.malfind`. Chi bao chi la gia thuyet cho den khi doi chieu voi DLL list, EDR log va
hash cua file tren dia.

### Step 6: Truy xuat credential

Chay `windows.lsass_dump` va `windows.hashdump` de xac nhan credential theft. TUYET DOI khong ghi
hash hay credential vao bao cao va khong gui ra ngoai - chi ghi nhan loai credential bi lo.

### Step 7: Lap timeline va phan cap

Chay `windows.timeliner`, chuan hoa moc thoi gian theo `../_shared/off-hours-and-baseline.md`,
roi phan cap theo `../_shared/severity-and-escalation.md`. Chi de xuat hanh dong khac phuc
(Rules dong 6).

Chi tiet command va quy tac phan tich: `references/memory-analysis-sop.md`.

## Expected Output

1. **Bao cao** (markdown): thong tin image va OS, ket qua tung plugin voi dong du lieu, bang chi
   bao, phan cap P1-P4, danh sach IOC, hanh dong de xuat, gioi han phan tich RAM.
2. **JSON** (mau):

```json
{
  "skill": "conducting-memory-forensics-with-volatility",
  "status": "ok",
  "scope": {
    "memory_image": "",
    "sha256": "",
    "os_version": "",
    "profile": ""
  },
  "summary": {},
  "findings": [
    {
      "plugin": "",
      "indicator": "",
      "assessment": "",
      "evidence": "",
      "hypothesis": ""
    }
  ],
  "indicators": [],
  "severity": "P2",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

## References

- `references/thresholds.md` - bang chi bao theo plugin va quy tac phan cap cho tung chi bao.
- `references/memory-analysis-sop.md` - command Volatility 3 va quy tac phan tich theo buoc.
- `../_shared/mitre-nist-mapping.md` - danh sach ID MITRE ATT&CK v18 va NIST CSF 2.0 duy nhat.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 va ma leo thang.
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly.
- `../_shared/off-hours-and-baseline.md` - chuan hoa moc thoi gian va baseline.
- Volatility 3 Documentation: https://volatility3.readthedocs.io/
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
