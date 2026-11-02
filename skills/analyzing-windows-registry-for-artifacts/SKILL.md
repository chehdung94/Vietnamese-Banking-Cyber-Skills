---
name: analyzing-windows-registry-for-artifacts
description: "Extract and triage Windows registry artifacts such as Run keys, services, UserAssist, and persistence from forensic hive images. Use when SOFTWARE.hiv or ntuser.hiv images, or an exported registry, are provided."
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
  - [windows-registry
  - registry-hives
  - forensics
  - autostart
  - user-activity
  - DFIR]
metadata:
  owner: security-skills-team
  tier: 3
  source: local
---

# Analyzing Windows Registry for Artifacts

## Overview

The Windows Registry contains critical forensic artifacts including user activity, application execution, system configuration, and evidence of compromise. This skill covers extraction and analysis of key registry hives for incident response and forensic investigations.

## When to Use

- Investigating user activity on a compromised system
- Identifying persistence mechanisms (autoruns)
- Recovering deleted or damaged evidence
- Building timelines of user actions
- Detecting credential theft or privilege escalation

## Prerequisites

- Registry hives: SYSTEM, SOFTWARE, SAM, SECURITY, NTUSER.DAT, USRCLASS.DAT
- Tools: Registry Viewer, FTK Imager, or regedit export
- Timeline context (incident window)

## Key Registry Locations

### User Activity
| Hive | Path | Artifacts |
|------|------|-----------|
| NTUSER.DAT | HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer | RecentApps, RunMRU |
| NTUSER.DAT | HKCU\Software\Microsoft\Office\*\*\UserMRU | Recent documents |
| USRCLASS.DAT | Local Settings\Software\Microsoft\Windows\Shell | BagMRU |

### Persistence Mechanisms
| Hive | Path | Purpose |
|------|------|---------|
| NTUSER.DAT | HKCU\Software\Microsoft\Windows\CurrentVersion\Run | User autorun |
| SYSTEM | HKLM\SYSTEM\CurrentControlSet\Services | Service entries |
| SOFTWARE | HKLM\Software\Microsoft\Windows\CurrentVersion\Run | System autorun |

### System Information
| Hive | Path | Artifacts |
|------|------|-----------|
| SYSTEM | HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation | Timezone |
| SOFTWARE | HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion | OS Version |
| SAM | HKLM\SAM\SAM\Domains | User accounts |

## Steps

### Step 1: Collect Registry Hives

```bash
# Using FTK Imager
ftkimager.exe --acquire C: C:\Forensics\registry_images

# Using regedit export
reg export HKLM\SOFTWARE C:\Forensics\SOFTWARE.hiv /y
reg export HKCU\NTUSER.DAT C:\Forensics\ntuser.hiv /y
```

### Step 2: Parse with Registry Viewer

```bash
# Parse and extract specific keys
python regparse.py --hive SOFTWARE.hiv --keys "Microsoft\Windows\CurrentVersion\Run"
```

### Step 3: Identify Autostart Entries

```bash
# Check all autorun locations
python autoruns_parser.py --hives "NTUSER.DAT,SYSTEM,SOFTWARE"
```

### Step 4: Analyze User Activity

```bash
# Extract recent documents and applications
python user_activity.py --hive NTUSER.DAT.hiv
```

## Output Format

```json
{
  "registry_analysis": {
    "hives_analyzed": [],
    "autostart_entries": [],
    "user_activity": [],
    "system_info": {}
  },
  "findings": {
    "persistence_mechanisms": [],
    "suspicious_entries": [],
    "user_timeline": []
  },
  "ioc_list": []
}
```

## Key MITRE ATT&CK Mappings

| Registry Location | ATT&CK Technique |
|-----------------|------------------|
| HKCU\Run | T1547.001 (Boot/Logon Autostart Execution) |
| HKLM\Services | T1543.003 (Create/Modify System Process) |
| HKCU\Environment | T1112 (Modify Registry) |
| UserAssist | T1112 (Modify Registry) |

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.

## References

- Registry Decoder: https://github.com/keydet89/RegRipper3
- Windows Registry Forensics: SANS DFIR
- MITRE ATT&CK: T1547 (Boot/Logon Autostart Execution)
