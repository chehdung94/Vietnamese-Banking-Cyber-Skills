---
name: conducting-memory-forensics-with-volatility
description: "Run Volatility 3 against a memory dump to recover process, injection, network, and credential artifacts. Use when a memory image in .raw, .mem, or .dmp format is available. Requires Volatility 3 and symbol tables."
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
license: Apache-2.0
compatibility: droid
version: 2.0.0
domain: cybersecurity
subdomain: incident-response
tags:
  - [memory-forensics
  - volatility
  - RAM-analysis
  - process-injection
  - DFIR]
metadata:
  owner: security-skills-team
  tier: 3
  source: local
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

## Rules (bat buoc)

1. Khong duoc xuat gia tri khong co trong bang chung duoc cung cap. Truong khong co du lieu ghi `[NOT_AVAILABLE]` va liet ke artefact con thieu.
2. Thieu log, truong du lieu, baseline hoac scope -> tra `insufficient_evidence` kem danh sach artefact can bo sung. Khong suy doan.
3. Khong bia: hash, IP, ten nguoi dung, ma ticket, mu/sigma, ID su co, timestamp, so luong. Khong co du lieu thi noi ro la chua co.
4. Moi moc thoi gian phap ly lay tu `../_shared/compliance-vietnam.md`. Khong dung tri nho.
5. Nguong phat hien lay tu bang nguong trong skill hoac `references/thresholds.md`. Khong tu dat nguong moi.
6. Lenh khac phuc (xoa, khoa, vo hieu key, tat tinh nang) chi duoc DE XUAT. Khong chay khi chua co xac nhan ro rang cua nguoi dung.
7. Phan biet ro ba ket luan co bang chung, gia thuyet, va can them du lieu.

## References

- Volatility 3 Documentation
- MITRE ATT&CK Framework
- NIST SP 800-86 (Forensics Integration)
