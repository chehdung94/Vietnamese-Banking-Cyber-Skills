# Hunting Scenarios

Each scenario must contain status, evidence checked, evidence missing, supporting evidence, contradictory evidence, confidence, and follow-up actions.

Valid status values:

```text
reviewed
finding
not_applicable
blocked
deferred
```

## Scenario Catalog

### 1. Suspicious Logon / RDP Activity

Review:

- Security 4624, 4625, 4634, 4647, 4776, 4672;
- Logon Type 2, 3, 7, 10, 11;
- TerminalServices RemoteConnectionManager;
- TerminalServices LocalSessionManager;
- PsLoggedon;
- user registry hives;
- Prefetch / Amcache / SRUM / MFT / USN around session time.

Map to ATT&CK where applicable:

- T1021.001 Remote Services: RDP
- T1078 Valid Accounts

### 2. Credential Access / Privilege Abuse

Review:

- 4672 special privileges;
- 4648 explicit credentials;
- local administrator usage;
- SAM / SECURITY access indicators;
- suspicious admin tools.

ATT&CK examples:

- T1003 OS Credential Dumping
- T1078 Valid Accounts

### 3. Suspicious PowerShell / Script Execution

Review:

- PowerShell Operational;
- ScriptBlock if present;
- EncodedCommand;
- IEX;
- DownloadString;
- bypass flags;
- wscript, cscript, mshta, rundll32, regsvr32.

ATT&CK examples:

- T1059.001 PowerShell
- T1218 Signed Binary Proxy Execution

### 4. Malware Execution / Suspicious Binary

Review:

- Prefetch;
- Amcache;
- Shimcache;
- Temp/user profile execution;
- unusual filename/path;
- hash reputation if available;
- file creation around execution time.

ATT&CK examples:

- T1204 User Execution
- T1059 Command and Scripting Interpreter

### 5. Persistence

Review:

- Run keys;
- services;
- scheduled tasks;
- WMI persistence;
- startup folders;
- suspicious DLL loading.

ATT&CK examples:

- T1053 Scheduled Task/Job
- T1543 Create or Modify System Process
- T1547 Boot or Logon Autostart Execution

### 6. Lateral Movement

Review:

- RDP;
- SMB / admin shares;
- WinRM;
- PsExec-like traces;
- remote service creation;
- network logon patterns.

ATT&CK examples:

- T1021 Remote Services
- T1570 Lateral Tool Transfer

### 7. Data Staging / Exfiltration Preparation

Review:

- archive tool execution;
- unusual file staging;
- browser or cloud upload traces;
- SRUM network usage;
- MFT / USN bursts;
- outbound network patterns.

ATT&CK examples:

- T1074 Data Staged
- T1560 Archive Collected Data

### 8. Defense Evasion

Review:

- log clearing;
- AV/security service stop;
- tool tampering;
- suspicious deletion;
- event log service changes.

ATT&CK examples:

- T1070 Indicator Removal
- T1562 Impair Defenses

### 9. Reconnaissance

Review:

- whoami;
- net user / net group;
- nltest;
- ipconfig;
- route;
- arp;
- query session.

ATT&CK examples:

- T1087 Account Discovery
- T1016 System Network Configuration Discovery

### 10. Suspicious Tools

Review:

- nmap;
- winrar / 7zip;
- putty / winscp;
- AnyDesk / TeamViewer / remote admin tools;
- living-off-the-land binaries.
