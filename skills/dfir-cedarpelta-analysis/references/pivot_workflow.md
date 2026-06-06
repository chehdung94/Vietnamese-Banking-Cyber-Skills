# Pivot Workflow

Every seed event or finding must create a pivot chain.

## Seed Types

A seed can be:

- timestamp;
- user;
- source IP;
- destination IP;
- host;
- process;
- file path;
- command line;
- Event ID;
- registry key;
- service;
- scheduled task;
- IOC.

## Default Pivot Windows

```text
Login / RDP seed: 24 hours before and 24 hours after
Process / malware seed: 4 hours before and 24 hours after
Persistence seed: 7 days before and 24 hours after
Data staging / exfil seed: 24 hours before and 24 hours after
```

## Pre-Event Pivot

Look for:

- failed logons;
- password spraying;
- network scan;
- previous account use;
- first-seen source IP;
- suspicious script or process;
- privilege or group changes;
- prior RDP / SMB / WinRM activity.

## During-Event Pivot

Look for:

- session connect and shell start;
- process tree;
- command/script execution;
- network connections;
- file writes;
- registry changes;
- archive creation;
- credential access indicators.

## Post-Event Pivot

Look for:

- persistence;
- cleanup/log clear;
- repeated login;
- lateral movement;
- data staging;
- exfiltration;
- account reuse;
- source IP reuse.

## Pivot Chain Output

```json
{
  "pivot_chain_id": "PC-001",
  "seed": {},
  "window_start": "",
  "window_end": "",
  "pre_event_observations": [],
  "during_event_observations": [],
  "post_event_observations": [],
  "evidence_refs": [],
  "limitations": [],
  "result": "finding|no_finding|inconclusive"
}
```
