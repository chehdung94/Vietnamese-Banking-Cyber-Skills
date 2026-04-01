---
name: conducting-memory-forensics-with-volatility
description: >
  Performs memory forensics analysis using Volatility 3 to extract evidence of
  malware execution, process injection, network connections, and credential theft
  from RAM dumps captured during incident response. Covers memory acquisition,
  process analysis, DLL inspection, and malware detection.
domain: cybersecurity
subdomain: incident-response
tags: [memory-forensics, volatility, RAM-analysis, process-injection, DFIR]
version: "1.0.0"
author: mahipal
license: Apache-2.0
---

# Conducting Memory Forensics with Volatility

## When to Use

- An endpoint has been contained during an active incident and volatile evidence must be preserved
- EDR alerts suggest process injection or fileless malware that only exists in memory
- Encryption keys need to be recovered from a ransomware-infected system before shutdown
- Credential theft (Mimikatz, LSASS dumping) is suspected and evidence must be confirmed
- A rootkit or kernel-level compromise is suspected and disk-based analysis is insufficient

**Do not use** for analyzing disk images or file system artifacts; use disk forensics tools (Autopsy, FTK) for those tasks.

## Prerequisites

- Memory acquisition tool deployed or available: WinPmem, Magnet RAM Capture, DumpIt, or AVML (Linux)
- Volatility 3 installed with Python 3.8+ and required symbol tables
- Sufficient storage for memory dumps (equal to system RAM size, typically 8-64 GB)
- YARA rules for malware detection in memory
- Reference baseline of normal processes and DLLs for the OS version being analyzed
- Chain of custody documentation for evidence handling

## Workflow

### Step 1: Acquire Memory Image

**Windows (WinPmem):**
```
winpmem_mini_x64.exe output.raw
```

**Linux (AVML):**
```
./avml output.lime
```

### Step 2: Identify Operating System and Profile

```bash
vol -f memory.raw windows.info
```

### Step 3: Analyze Running Processes

```bash
# List all running processes
vol -f memory.raw windows.pslist

# Show process tree (parent-child relationships)
vol -f memory.raw windows.pstree

# Scan for hidden/unlinked processes (rootkit detection)
vol -f memory.raw windows.psscan
```

### Step 4: Investigate Network Connections

```bash
vol -f memory.raw windows.netscan
```

### Step 5: Detect Process Injection and Malware

```bash
vol -f memory.raw windows.malfind
```

### Step 6: Extract Credentials

```bash
# Extract from LSASS
vol -f memory.raw windows.lsass_dump

# Parse cached credentials
vol -f memory.raw windows.hashdump
```

### Step 7: Analyze Timeline

```bash
vol -f memory.raw windows.timeliner
```

## Output Format

```json
{
  "analysis_summary": {
    "memory_image": "filename.raw",
    "os_version": "Windows 10 22H2",
    "analysis_date": "YYYY-MM-DD"
  },
  "process_findings": {
    "total_processes": 0,
    "suspicious": []
  },
  "network_connections": {
    "total": 0,
    "suspicious_connections": []
  },
  "injection_indicators": [],
  "credentials_recovered": [],
  "ioc_list": []
}
```

## Key Indicators of Compromise

| Indicator | MITRE ATT&CK | Severity |
|-----------|--------------|----------|
| Suspicious process parent | T1055.001 | High |
| Hidden process (psscan) | T1014 | Critical |
| Malicious network connection | T1071 | High |
| LSASS access | T1003.001 | Critical |
| Injected code (malfind) | T1055 | Critical |

## References

- Volatility 3 Documentation
- MITRE ATT&CK Framework
- NIST SP 800-86 (Forensics Integration)
