# Artifact Groups

For each group, record status: `reviewed`, `finding`, `not_available`, `not_applicable`, `blocked`, or `deferred`.

## Required Groups

1. Windows Event Logs
   - Security
   - System
   - Application
   - PowerShell
   - TerminalServices RemoteConnectionManager
   - TerminalServices LocalSessionManager
   - TaskScheduler
   - Sysmon if available

2. Live Response Text
   - PsList / process list
   - PsLoggedon
   - netstat
   - TCPView
   - whoami
   - system info
   - installed software
   - loaded drivers
   - full file listing

3. Persistence
   - autorunsc
   - services
   - scheduled tasks
   - startup entries
   - WMI indicators
   - loaded DLLs

4. Execution Artifacts
   - Prefetch
   - Amcache
   - Shimcache if available
   - UserAssist
   - BAM / DAM
   - SRUM

5. Registry Hives
   - SYSTEM
   - SOFTWARE
   - SAM
   - SECURITY
   - DEFAULT
   - NTUSER.DAT
   - USRCLASS.DAT

6. File System And Timeline Artifacts
   - MFT
   - USN journal
   - LNK
   - Jump Lists
   - recent files
   - recycle bin
   - full file listing

7. Browser Artifacts
   - Chrome / Edge / Firefox history
   - downloads
   - cookies if allowed
   - cache metadata if relevant

8. Network Artifacts
   - netstat
   - TCPView
   - hosts
   - ARP
   - NetBIOS sessions
   - DNS / proxy / firewall if collected

9. Account And Session Artifacts
   - logon / logoff
   - RDP connect / reconnect / disconnect
   - local users and groups
   - privilege assignment
   - admin logon

10. Collection Metadata
   - hashes
   - collection timestamp
   - timezone
   - hostname
   - collector version / output details
