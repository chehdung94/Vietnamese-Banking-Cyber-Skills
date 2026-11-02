---
name: performing-windows-artifact-analysis-with-eric-zimmerman-tools
description: "Run KAPE and Eric Zimmerman tools to collect and triage Windows artifacts, then build a normalized timeline. Use when a live or disk Windows artifact set must be parsed into events."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.0.0
domain: cybersecurity
subdomain: digital-forensics
tags:
  - [windows-forensics
  - eric-zimmerman
  - registry
  - prefetch
  - artifact-analysis
  - DFIR]
metadata:
  owner: security-skills-team
  tier: 3
  source: upstream:mukul975/Anthropic-Cybersecurity-Skills
---

# Performing Windows Artifact Analysis with Eric Zimmerman Tools

## Overview

Eric Zimmerman's EZ Tools suite provides powerful command-line utilities for parsing Windows forensic artifacts. This skill covers KAPE (artifact collector), MFTECmd (MFT parser), PECmd (Prefetch), LECmd (Lnk files), JLECmd (Jump Lists), and Timeline Explorer for comprehensive Windows forensics.

## When to Use

- Analyzing registry hives for user activity and system configuration
- Parsing prefetch files to identify application execution history
- Examining LNK files for recent document access
- Creating forensic timelines from multiple artifact sources
- Automated artifact collection with KAPE

## Prerequisites

- EZ Tools suite downloaded from Eric Zimmerman's GitHub
- Windows registry hives (SYSTEM, SOFTWARE, NTUSER.DAT, SAM)
- Prefetch files from C:\Windows\Prefetch
- LNK files from Recent folder
- Jump list files from AppData

## Tools Overview

| Tool | Purpose | Key Output |
|------|---------|------------|
| KAPE | Automated artifact collection | Parsed CSV/JSON |
| MFTECmd | Parse MFT files | Timeline, file details |
| PECmd | Parse Prefetch files | Execution history |
| LECmd | Parse LNK files | Access timestamps |
| JLECmd | Parse Jump Lists | Recent documents |
| Timeline Explorer | Visual timeline analysis | Correlated events |

## Steps

### Step 1: KAPE - Automated Collection

```bash
# List available targets
.\KAPE.exe --tlist

# Collect from target
.\KAPE.exe --tsource C: --tdest C:\KAPE\Output --tflush
```

### Step 2: Parse Prefetch with PECmd

```bash
PECmd.exe -f C:\Windows\Prefetch\APP.EXE-*.pf --json C:\Output\
```

### Step 3: Parse Registry with MFTECmd

```bash
MFTECmd.exe -f C:\$MFT --csv C:\Output\
```

### Step 4: Parse LNK Files with LECmd

```bash
LECmd.exe -d C:\Users\*\AppData\Roaming\Microsoft\Windows\Recent --json C:\Output\
```

### Step 5: Parse Jump Lists with JLECmd

```bash
JLECmd.exe -d C:\Users\*\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations --json C:\Output\
```

## Output Format

```json
{
  "prefetch_analysis": {
    "files_processed": 0,
    "execution_history": []
  },
  "registry_findings": {
    "user_activity": [],
    "autostart_entries": [],
    "usb_devices": []
  },
  "lnk_files": {
    "total": 0,
    "recent_documents": []
  },
  "timeline": {
    "events": [],
    "correlations": []
  }
}
```

## Key Artifacts for Banking Investigations

| Artifact | Location | Investigation Value |
|----------|----------|-------------------|
| Recent Docs | NTUSER.DAT | File access history |
| UserAssist | Registry | Program execution |
| BAM/DAM | SYSTEM | Background activity |
| Prefetch | Prefetch folder | Application usage |
| ShimCache | SYSTEM | Program execution |

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.

## References

- Eric Zimmerman's Tools: https://github.com/EricZimmerman
- KAPE Documentation: https://www.kape.dev/
- Timeline Explorer: https://github.com/EricZimmerman/TimelineExplorer
