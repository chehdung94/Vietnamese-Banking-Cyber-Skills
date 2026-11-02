---
name: performing-windows-artifact-analysis-with-eric-zimmerman-tools
description: "Run KAPE and Eric Zimmerman tools (PECmd, MFTECmd, LECmd, JLECmd) to collect and triage Windows artifacts, then build a normalized timeline. Use when a live Windows host or a Windows artifact set (prefetch, LNK, jump lists, MFT, registry hives) must be parsed into events. Not for memory analysis and not for network capture analysis."
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
  - T1053.005
  - T1059.001
  - T1003.001
nist_csf:
  - DE.CM-9
  - DE.AE-1
  - DE.AE-3
  - RS.RP-1
tags:
  - windows-forensics
  - eric-zimmerman
  - registry
  - prefetch
  - artifact-analysis
  - DFIR
metadata:
  owner: security-skills-team
  tier: 3
  source: upstream:mukul975/Anthropic-Cybersecurity-Skills
---

# Performing Windows Artifact Analysis with Eric Zimmerman Tools

## Overview

Bo cong cu Eric Zimmerman (EZ Tools) cung cap tien ich command-line de parse artifact Windows:
KAPE (thu thap), MFTECmd (MFT), PECmd (Prefetch), LECmd (LNK), JLECmd (Jump List) va Timeline
Explorer (gop su kien). Skill nay bao quat quy trinh thu thap, parse va gom timeline chuan hoa.

> Muc thoi gian phap ly: xem `../_shared/compliance-vietnam.md` (nguon su fact duy nhat).
> Phan cap P1-P4: xem `../_shared/severity-and-escalation.md`.

## When to Use

- Phan tich registry hive de tim hoat dong nguoi dung va cau hinh he thong.
- Parse prefetch de xac dinh lich su chay ung dung.
- Xem LNK va Jump List de truy vei tep nguoi dung vua mo.
- Tao timeline forensics tu nhieu nguon artifact.
- Thu thap artifact Windows tu dong bang KAPE.

**Khong dung cho skill nay:** phan tich RAM (dung `../conducting-memory-forensics-with-volatility/SKILL.md`),
phan tich capture mang (dung `../performing-network-forensics-with-wireshark/SKILL.md`), phan tich
registry chuyen sau (dung `../analyzing-windows-registry-for-artifacts/SKILL.md`), hoac thu thap
volatile tren host dang chay (dung `../collecting-volatile-evidence-from-compromised-host/SKILL.md`).

## Prerequisites

- Bo EZ Tools tai tu kho Eric Zimmerman, kem ban ghi chung.
- Tap artifact Windows: registry hives (SYSTEM, SOFTWARE, NTUSER.DAT, SAM), prefetch, LNK, Jump Lists.
- Target va `--tdest` cho KAPE (dia ngoai, dung chua).
- Timeline Explorer de gop su kien.
- Moc thoi gian su co de khoang loc timeline (tham chieu `../_shared/off-hours-and-baseline.md`).

**STOP rule:** Neu thieu mot dieu kien bat buoc o tren, dung tai buoc do, khong phan tich tiep.
Tra `insufficient_evidence` va liet ke artefact con thieu theo Rules dong 1-2.
Neu khong co tap artifact (khong co target nao de thu thap), dung - khong tao timeline rong.

## Execution Steps

### Step 1: Thu thap bang KAPE

Chay `--tlist` de ghi nhan danh sach target thuc te, roi thu thap theo target trong pham vi
(phe, tai khoan, file system, event log). Luu output `--tlist` kem bao cao de chung minh pham vi.

### Step 2: Parse Prefetch (PECmd)

Parse file prefetch de lay lich su chay ung dung. Luu y prefetch chi cho so lan chay va moc thoi
gian gan nhat, khong cho biet lan chay dau tien. Ap dung dieu kien phat hien tai
`references/thresholds.md`.

### Step 3: Parse MFT (MFTECmd)

Parse `$MFT` de lay danh sach file, thoi gian tao/sua va kich thuoc. Dung de tim file bi xoa
hoac bien doi ngoai doi so voi duong dan mong doi.

### Step 4: Parse LNK (LECmd)

Parse LNK trong thu muc Recent de truy vei tep nguoi dung vua mo theo user.

### Step 5: Parse Jump List (JLECmd)

Parse Jump Lists de bo sung danh sach tep va ung dung gan nhat theo user.

### Step 6: Gom timeline va phan cap

Dung Timeline Explorer gom cac su kien, chuan hoa moc thoi gian, roi phan cap theo
`../_shared/severity-and-escalation.md`. Chi de xuat hanh dong khac phuc (Rules dong 6).

Chi tiet command va quy tac: `references/ez-tools-sop.md`.

## Expected Output

1. **Bao cao** (markdown): pham vi thu thap (kem output `--tlist`), ket qua parse tung loai artifact,
   timeline chuan hoa, phan cap P1-P4, danh sach IOC, hanh dong de xuat, gioi han phan tich.
2. **JSON** (mau):

```json
{
  "skill": "performing-windows-artifact-analysis-with-eric-zimmerman-tools",
  "status": "ok",
  "scope": {
    "collection_targets": [],
    "artifacts_parsed": [],
    "users_covered": []
  },
  "summary": {},
  "findings": [
    {
      "artifact": "",
      "source_tool": "",
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

- `references/thresholds.md` - bang dieu kien phat hien theo loai artifact.
- `references/artifact-location-map.md` - bang cong cu, artifact cho dieu tra ngan hang, ban do MITRE.
- `references/ez-tools-sop.md` - command KAPE/EZ Tools theo buoc va quy tac phan tich.
- `../_shared/mitre-nist-mapping.md` - danh sach ID MITRE ATT&CK v18 va NIST CSF 2.0 duy nhat.
- `../_shared/severity-and-escalation.md` - thang phan cap P1-P4 va ma leo thang.
- `../_shared/compliance-vietnam.md` - moc thoi gian phap ly.
- `../_shared/off-hours-and-baseline.md` - chuan hoa moc thoi gian.
- Eric Zimmerman's Tools: https://github.com/EricZimmerman
- KAPE Documentation: https://www.kape.dev/
- Timeline Explorer: https://github.com/EricZimmerman/TimelineExplorer

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.
