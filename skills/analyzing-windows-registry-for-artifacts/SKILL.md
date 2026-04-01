---
name: analyzing-windows-registry-for-artifacts
description: Extract and analyze Windows Registry hives to uncover user activity, installed software, autostart entries, and evidence of system compromise.
domain: cybersecurity
subdomain: digital-forensics
tags: [windows-registry, registry-hives, forensics, autostart, user-activity, DFIR]
version: "1.0"
author: mahipal
license: Apache-2.0
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
| HKCU\Environment | T1003.004 (LSASRV) |
| UserAssist | T1053.005 (Scheduled Task) |

## References

- Registry Decoder: https://github.com/keydet89/RegRipper3
- Windows Registry Forensics: SANS DFIR
- MITRE ATT&CK: T1547 (Boot/Logon Autostart Execution)
