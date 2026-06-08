# Mandatory Cross-Check Rules

## Universal Rule

Do not accept or reject a scenario using only one evidence source unless the case explicitly has no other available sources and that limitation is recorded.

## RDP / Login Cross-Check

Security.evtx alone is insufficient.

Check:

```text
TerminalServices-RemoteConnectionManager/Admin
TerminalServices-RemoteConnectionManager/Operational
TerminalServices-LocalSessionManager/Admin
TerminalServices-LocalSessionManager/Operational
Security.evtx
PsLoggedon
netstat / TCPView
PsList / Running processes
user registry hives
Prefetch / Amcache / SRUM / MFT / USN
```

If Security.evtx lacks 4624/4625, do not conclude no RDP occurred unless retention and relevant TerminalServices logs support that conclusion.

## Malware / Execution Cross-Check

Running processes alone are insufficient.

Check:

```text
Prefetch
Amcache
Shimcache if available
MFT / USN
Autoruns
Services
Scheduled Tasks
PowerShell logs
Browser downloads
File hashes
```

## Persistence Cross-Check

Autorunsc alone is insufficient.

Check:

```text
Run keys
Services
Scheduled Tasks
WMI
Startup folders
Loaded DLLs
Registry hives
```

## Exfiltration Cross-Check

Netstat at collection time is insufficient.

Check:

```text
SRUM
Browser history/downloads/uploads if available
MFT / USN staging activity
Archive tool execution
Cloud client traces
Proxy / firewall / VPN logs if available
```

## Reconciliation Rule

If Cedarpelta findings contradict Log Analysis, Timeline, or Report outputs, create a reconciliation item:

```json
{
  "conflict_id": "RC-001",
  "source_a": "cedarpelta_findings.json",
  "source_b": "timeline.json",
  "description": "",
  "evidence_refs": [],
  "resolution_required": true
}
```
