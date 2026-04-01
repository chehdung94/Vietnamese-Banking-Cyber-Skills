---
name: performing-windows-artifact-analysis-with-eric-zimmerman-tools
description: Perform comprehensive Windows forensic artifact analysis using Eric Zimmerman's open-source EZ Tools suite including KAPE, MFTECmd, PECmd, LECmd, JLECmd, and Timeline Explorer for parsing registry hives, prefetch files, event logs, and file system metadata.
domain: cybersecurity
subdomain: digital-forensics
tags: [windows-forensics, eric-zimmerman, registry, prefetch, artifact-analysis, DFIR]
version: "1.0"
author: mahipal
license: Apache-2.0
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

## References

- Eric Zimmerman's Tools: https://github.com/EricZimmerman
- KAPE Documentation: https://www.kape.dev/
- Timeline Explorer: https://github.com/EricZimmerman/TimelineExplorer
