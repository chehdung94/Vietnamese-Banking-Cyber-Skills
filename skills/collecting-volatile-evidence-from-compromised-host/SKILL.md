---
name: collecting-volatile-evidence-from-compromised-host
description: Collect volatile forensic evidence from a compromised system following order of volatility, preserving memory, network connections, processes, and system state before they are lost.
domain: cybersecurity
subdomain: incident-response
tags: [volatile-evidence, live-forensics, memory-acquisition, DFIR, incident-response]
version: "1.0"
author: mahipal
license: Apache-2.0
---

# Collecting Volatile Evidence from Compromised Host

## Overview

Volatile evidence exists only in RAM and is lost upon system shutdown. This skill provides a systematic procedure for collecting volatile evidence in order of volatility (per RFC 3227), ensuring critical artifacts are preserved before containment.

## When to Use

- Responding to an active incident on a running system
- Containing a compromised endpoint before isolation
- Preserving memory for later forensics
- Collecting live forensic evidence
- Investigating while attacker may still be active

## Order of Volatility (RFC 3227)

| Priority | Evidence | Collection Method |
|----------|----------|-------------------|
| 1 | CPU registers, cache | Hardware-level collection |
| 2 | RAM (Memory) | WinPmem, DumpIt, AVML |
| 3 | Network connections | netstat, ss, lsof |
| 4 | Running processes | ps, top, tasklist |
| 5 | Kernel tables | OS-specific commands |
| 6 | Disk files (modified) | File system parsing |
| 7 | Physical disk | Full image acquisition |

## Prerequisites

- Trusted forensic USB toolkit (KAPE, Velociraptor, or Sysinternals)
- Write-blocker or forensic write protection
- Storage for evidence (external drive)
- Network access to system
- Chain of custody forms

## Steps

### Step 1: Prepare Evidence Collection

**Create evidence directory:**
```bash
EVIDENCE_DIR="/mnt/forensics/$(hostname)_$(date +%Y%m%d_%H%M%S)"
mkdir -p "$EVIDENCE_DIR"
cd "$EVIDENCE_DIR"
```

### Step 2: Document System State

```bash
# System info
hostname > system_info.txt
ifconfig -a >> system_info.txt 2>/dev/null
ipconfig /all >> system_info.txt 2>$null # Windows

# Current user
whoami > current_user.txt
query user > logged_in_users.txt 2>$null # Windows
w > w_output.txt 2>/dev/null # Linux

# System date/time
date -u > system_time_utc.txt
wmic os get localdatetime > system_time.txt 2>$null # Windows
```

### Step 3: Capture Memory (Most Critical!)

**Windows:**
```bash
# WinPmem
winpmem_mini_x64.exe memory.raw

# DumpIt
DumpIt.exe
# Creates .raw file in current directory
```

**Linux:**
```bash
# AVML
./avml output.lime

# LiME
insmod lime.ko "path=output.lime format=lime"
```

### Step 4: Collect Network Connections

```bash
# Windows
netstat -anob > network_connections.txt
netstat -ano > netstat_ano.txt

# Linux
ss -tunap > ss_output.txt
netstat -tunap > netstat_output.txt
cat /proc/net/tcp > proc_net_tcp.txt
```

### Step 5: Capture Process Information

```bash
# Windows
tasklist /v > tasklist.txt
wmic process list brief > process_list.txt

# Linux
ps auxwwf > ps_output.txt
cat /proc/*/cmdline 2>/dev/null | tr '\0' ' ' > proc_cmdline.txt

# DLLs loaded
listdlls.exe -accepteula > loaded_dlls.txt 2>$null # Windows
```

### Step 6: Capture User Sessions

```bash
# Windows
query user > logged_in_users.txt
query session > active_sessions.txt
net session > net_sessions.txt 2>&1

# Linux
who > who_output.txt
last -50 > last_logins.txt
```

### Step 7: System Configuration

```bash
# Scheduled tasks
schtasks /query /fo CSV /v > scheduled_tasks.csv 2>$null # Windows
crontab -l > crontab.txt 2>/dev/null # Linux

# Services
sc queryex type=service state=all > services.txt 2>$null # Windows
systemctl list-units --type=service --all > services.txt 2>/dev/null # Linux

# Registry autoruns
reg export "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" autorun_hklm.reg /y 2>$null
```

### Step 8: Document Chain of Custody

```bash
# Hash all evidence
sha256sum * > evidence_manifest.sha256

# Create chain of custody record
cat > chain_of_custody.txt << EOF
CHAIN OF CUSTODY RECORD
========================
Case ID: IR-YYYY-NNN
Collection Date: $(date -u)
Collected By: $(whoami)
System: $(hostname)
Collection Method: Live forensic collection

Evidence Items:
$(ls -la)

SHA256 Manifest: evidence_manifest.sha256
EOF
```

## Key Concepts

| Concept | Description |
|---------|-------------|
| Order of Volatility | Collect most volatile first |
| Live Forensics | Evidence from running system |
| Chain of Custody | Documentation of handling |
| Forensic Soundness | Not altering evidence |
| Trusted Tools | From external media |
| Evidence Integrity | SHA256 hashing |

## Output Format

```json
{
  "collection_summary": {
    "hostname": "WKSTN-042",
    "collection_date": "YYYY-MM-DD HH:MM:SS",
    "collector": "analyst-name",
    "os_version": "Windows 10 21H2"
  },
  "evidence_collected": {
    "memory_image": { "file": "memory.raw", "size": 0, "sha256": "" },
    "network_connections": { "file": "network_connections.txt" },
    "process_list": { "file": "process_list.txt" },
    "user_sessions": { "file": "logged_in_users.txt" },
    "system_config": { "file": "system_info.txt" }
  },
  "chain_of_custody": {
    "manifest": "evidence_manifest.sha256",
    "custody_record": "chain_of_custody.txt"
  },
  "initial_findings": {
    "suspicious_connections": [],
    "unusual_processes": [],
    "rogue_users": []
  }
}
```

## Tools Reference

| Tool | Purpose |
|------|---------|
| WinPmem | Windows memory acquisition |
| AVML | Linux memory acquisition |
| KAPE | Automated artifact collection |
| Velociraptor | Remote forensic collection |
| CyLR | Cross-platform live response |
| Sysinternals | Process/handle analysis |

## References

- RFC 3227 (Order of Volatility)
- NIST SP 800-86 (Forensics Integration)
- SANS DFIR (Live Forensics)
