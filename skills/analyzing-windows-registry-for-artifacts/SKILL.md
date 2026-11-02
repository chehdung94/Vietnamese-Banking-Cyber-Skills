---
name: analyzing-windows-registry-for-artifacts
description: "Extract and triage Windows registry artifacts (Run keys, services, UserAssist, persistence, user activity) from forensic hive images. Use when SYSTEM, SOFTWARE, SAM, SECURITY, NTUSER.DAT or USRCLASS.DAT hives, or registry exports, are provided for a DFIR case. Not for live volatile collection on a running host and not for memory forensics."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.1.0
domain: cybersecurity
subdomain: digital-forensics
mitre_attack:
  - T1112
  - T1547.001
  - T1059.001
  - T1003.001
  - T1021.001
nist_csf:
  - DE.CM-9
  - DE.AE-1
  - DE.AE-3
  - RS.RP-1
tags:
  - windows-registry
  - registry-hives
  - forensics
  - autostart
  - user-activity
  - DFIR
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Analyzing Windows Registry for Artifacts

## Overview

Windows Registry chua nhieu tang chung minh ve hanh dong nguoi dung, cau hinh he thong va co che
gia dau (persistence). Skill nay trich xuat va phan tich cac hive registry de ho tro dieu tra,
phat hien persistence va dung timeline hanh dong.

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).
> Phan cap P1-P4: xem `../_shared/severity-and-escalation.md`.

## When to Use

- Dieu tra hoat dong nguoi dung tren may bi xam nhap.
- Xac nhan co che persistence (Run key, Service, Winlogon, AppInit, IFEO).
- Khoi phuc bang chung da bi xoa hoac hong tren dia.
- Xay dung timeline hanh dong nguoi dung.
- Phat hien dau hieu credential theft hoac escalation dac quyen.

**Khong dung cho skill nay:** thu thap volatile tren host dang chay (dung
`../collecting-volatile-evidence-from-compromised-host/SKILL.md`), phan tich file Windows tren dia
bang KAPE/EZ Tools (dung `../performing-windows-artifact-analysis-with-eric-zimmerman-tools/SKILL.md`),
hoac phan tich RAM (dung `../conducting-memory-forensics-with-volatility/SKILL.md`).

## Prerequisites

- Hive registry can phan tich: `SYSTEM`, `SOFTWARE`, `SAM`, `SECURITY`, `NTUSER.DAT`, `USRCLASS.DAT`.
- Cong cu doc hive: Registry Viewer, RegRipper, hoac `python-registry`; phai giu nguyen nguyen ban goc.
- Ban do cac duong dan registry can phan tich: `references/registry-artifact-locations.md`.
- Pham vi su co va moc thoi gian lien quan (tham chieu `../_shared/off-hours-and-baseline.md` khi chuan hoa
  moc thoi gian).
- Hash SHA-256 cua tung file hive de dinh danh bang chung.

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu thieu hash cua hive hoac khong xac dinh duoc hive nao thuoc case, dung truoc khi trich xuat.

## Execution Steps

### Step 1: Chot danh tinh bang chung

Ghi lai ten file, kich thuoc, hash SHA-256 va nguon cua tung hive. Ghi lai phien ban Windows va
timezone he thong tu hive `SYSTEM` de chuan hoa moc thoi gian su co.

### Step 2: Parse hive va trich xuat khoa

Dung cong cu doc hive de lay gia tri cac duong dan trong `references/registry-artifact-locations.md`.
Chi doc, khong ghi lai vao file hive goc.

### Step 3: Xac dinh persistence

Kiem tra Run/RunOnce, Services, Winlogon, AppInit_DLLs va IFEO theo bang dieu kien phat hien tai
`references/thresholds.md`. Moi muc dang ngo phai doi chieu duong dan tai co that, hash file, log EDR
truoc khi ket luan. Doi muc phan loai: T1547.001 cho Run key, T1112 cho
thay doi registry, T1059.001 khi khoa tro toi shell/cmd.

### Step 4: Phan tich hoat dong nguoi dung

Doc UserAssist, RunMRU, RecentApps, ShimCache, BAM/DAM va Office UserMRU de dung chay chuong trinh
va tep gan nhat theo user. UserAssist luu gia tri ROT13.

### Step 5: Trich xuat thong tin he thong

Lay OS build, install time, danh sach tai khoan tu `SAM` (khong dich hash SAM thanh mat khau),
cau hinh RDP va timezone. Chuyen tiep can qua RDP duoc gan voi T1021.001.

### Step 6: Lien ket va phan cap

Ghep mo dong thoi gian voi log tai khoan, EDR va log mang. Phan cap theo
`../_shared/severity-and-escalation.md`, chi de xuat hanh dong khac phuc (Rules dong 6).

Chi tiet command va quy trinh phan tich: `references/registry-analysis-sop.md`.

## Expected Output

1. **Bao cao** (markdown): tom tat, bang chi tiet khoa registry, phan cap P1-P4, danh sach IOC,
   hanh dong de xuat, muc hien chong. Mau: `references/registry-report-example.md`.
2. **JSON** (mau):

```json
{
  "skill": "analyzing-windows-registry-for-artifacts",
  "status": "ok",
  "scope": {
    "hives_analyzed": [],
    "os_version": "",
    "timezone": ""
  },
  "summary": {},
  "findings": [
    {
      "artifact": "",
      "value": "",
      "assessment": "",
      "evidence": "",
      "hypothesis": ""
    }
  ],
  "indicators": [],
  "severity": "P3",
  "severity_evidence": [],
  "recommended_actions": [],
  "missing_artifacts": []
}
```

## References

- `references/thresholds.md` - bang dieu kien phat hien persistence va hoat dong nguoi dung.
- `references/registry-artifact-locations.md` - bang duong dan registry va ban do MITRE ATT&CK.
- `references/registry-analysis-sop.md` - command thu thap, phan tich va lien ket bang chung.
- `references/registry-report-example.md` - mau bao cao markdown.
- `../_shared/mitre-nist-mapping.md` - danh sach ID MITRE ATT&CK v18 va NIST CSF 2.0 duy nhat.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 va ma leo thang.
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly.
- Registry Decoder: https://github.com/keydet89/RegRipper3
- Windows Registry Forensics: SANS DFIR

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
