# Cedarpelta Collection Artifact Map

## Purpose

Use this map immediately after a Cedarpelta Build / Live Response Collection is received.

This is a collection map, not an investigation checklist. Its purpose is to identify what data types the tool collected, where they are stored, what each folder usually means, and what may be missing before any forensic analysis begins.

Do not perform threat hunting, IOC searching, suspicious-event analysis, attribution, compromise assessment, or incident conclusions while using this map.

## Root Layout

| Collection area | Path pattern | Data type | Common examples | Use before analysis | Notes |
|---|---|---|---|---|---|
| Collection metadata | `<collection_root>\*_File_Hashes.txt` | File hash manifest for collected outputs | `KTHT-LOCPV_20260409_134403_File_Hashes.txt` | Verify collection inventory and integrity context | Metadata only; not a finding. |
| Collection metadata | `<collection_root>\*_Processing_Details.txt` | Collection or processing details | `KTHT-LOCPV_20260409_134403_Processing_Details.txt` | Identify collection time, tool behavior, scope, and errors | Review early for missing or failed copy operations. |
| Forensic images | `ForensicImages\DiskImage` | Disk image output when collected | Raw/E01/VHD-style images, if present | Determine whether full disk analysis is available | May be empty in live-response-only collections. |
| Memory image | `ForensicImages\Memory` | Memory image output when collected | Raw memory image, crash dump, tool output | Determine whether memory analysis is available | May be empty in many collections. |
| Live response data | `LiveResponseData` | Main live response output tree | `BasicInfo`, `CopiedFiles`, `NetworkInfo`, `PersistenceMechanisms`, `UserInfo` | Primary map for endpoint baseline and artifact availability | Do not parse everything by default. |

## LiveResponseData Map

| Data type | Path pattern | Common files or subfolders | What it represents | Baseline use | Parser class |
|---|---|---|---|---|---|
| Basic host overview | `LiveResponseData\BasicInfo` | System info, process lists, disk info, installed software, file listings, hashes | Text/HTML snapshots of host state and broad file inventory | Build pre-analysis host baseline and high-level coverage overview | Direct ingest or light parse; large listings are parser-heavy. |
| Copied artifact files | `LiveResponseData\CopiedFiles` | `registry`, `prefetch`, `eventlogs`, `mft`, `usnjrnl`, browser folders, `amcache`, `SRUMDB`, `hosts` | Raw or near-raw forensic artifacts copied from the endpoint | Determine which artifact families are available | Specialized parse by artifact type. |
| Network snapshot | `LiveResponseData\NetworkInfo` | `netstat_anb_results.txt`, `TCPView.txt`, `cports.html`, ARP, NetBIOS | Network state at collection time | Record current network identity and active connection sources | Light parse; not enough alone for exfiltration conclusions. |
| Persistence inventory | `LiveResponseData\PersistenceMechanisms` | Autoruns, services, scheduled tasks, startup, loaded DLLs | Persistence-related live response text exports | Inventory persistence surfaces before scenario selection | Light parse or scoped review. |
| User and session info | `LiveResponseData\UserInfo` | `whoami.txt`, `All_logons_wmic.txt` | Current user and logon/account context | Identify user context and local/session information | Direct ingest or light parse. |

## BasicInfo Detailed Map

| Data type | Path pattern / files | What it represents | Baseline fields supported | Parser class | Coverage notes |
|---|---|---|---|---|---|
| Host OS and system metadata | `system_info.txt`, `system_info_wmic.txt`, `Windows_Version.txt`, `Windows_codepage.txt`, `system_date_time_tz.txt`, `psinfo.txt` | OS, version, system configuration, time, timezone, locale/codepage, psinfo summary | Computer name, OS version/build, architecture, timezone, codepage, collection-time context | Direct ingest | Required for interpreting timestamps and host identity. |
| Disk and volume inventory | `DiskDriveList_wmic.txt`, `LogicalDisk_name_wmic.txt`, `LogicalDisk_size_caption_wmic.txt` | Physical disks and logical volumes | Disk model/count, drive letters, size/free-space context | Direct ingest | Useful before filesystem parsing. |
| Installed software | `Installed_software_wmic.txt` | Installed application inventory from WMIC | Application baseline | Light parse | Inventory only at baseline stage; do not label suspicious here. |
| Loaded drivers | `Loaded_system_drivers_wmic.txt` | Loaded driver inventory | Driver baseline | Light parse | Can support later driver/rootkit questions but not analyzed at baseline stage. |
| Running process snapshot | `Running_processes.txt`, `PsList.txt`, `PrcView_extended.txt`, `PrcView_extended_long.txt` | Processes running at collection time | Runtime process baseline | Light parse | Single point-in-time view; not proof of execution history by itself. |
| Logged-on users and open files | `PsLoggedon.txt`, `psfile.txt` | Logged-on account and remote/open file snapshot | Current or recent session indicators | Direct ingest or light parse | Availability depends on live collection state. |
| Windows activity export | `LastActivityView.html`, `PsLoglist.txt` | Activity summary and event log export generated by tools | Broad activity coverage indicator | Parser-heavy | Do not parse full output unless scoped by hypothesis, user, time window, event ID, or artifact group. |
| Filesystem full listing | `Full_file_listing.txt` | Host-wide file listing | Filesystem coverage indicator; path inventory | Parser-heavy | Usually very large. Use only for scoped path/name/time pivots. |
| Hidden directories | `List_hidden_directories.txt` | Hidden directory listing | Filesystem coverage indicator | Light parse | Baseline inventory only unless later scoped. |
| Alternate data streams | `Alternate_data_streams.txt` | ADS enumeration | Filesystem coverage indicator | Light parse | Inventory only at baseline stage. |
| Unicode paths | `Possible_unicode_files_and_directories.txt` | Paths containing unusual/non-ASCII names | Filesystem coverage indicator | Light parse | Helps avoid missing renamed or locale-specific paths. |
| File hash inventory | `Hashes_md5_*`, `Hashes_sha256_*` | Hashes and timestamps for selected locations | Hash coverage for Startup, System32, System TEMP, User TEMP | Light parse | Empty hash files must be recorded as coverage limitations. |

## CopiedFiles Detailed Map

| Data type | Path pattern | Common files | What it represents | Parser class | Coverage notes |
|---|---|---|---|---|---|
| Registry hives | `LiveResponseData\CopiedFiles\registry` | `SYSTEM`, `SOFTWARE`, `SAM`, `SECURITY`, `DEFAULT`, `COMPONENTS`, `*_NTUSER.DAT`, `*_USRCLASS.DAT` | Machine and user registry hives | Specialized parse | Critical for users, services, persistence, execution traces, user activity, and configuration. |
| Prefetch | `LiveResponseData\CopiedFiles\prefetch` | `*.pf`, `ReadyBoot` | Windows application execution traces | Specialized parse | Presence indicates Prefetch collection; parse only when execution questions exist. |
| Amcache | `LiveResponseData\CopiedFiles\amcache` | `Amcache.hve` | Program execution/install metadata | Specialized parse | Important execution artifact; cross-check with Prefetch, Shimcache, logs, and hashes. |
| SRUM | `LiveResponseData\CopiedFiles\SRUMDB` | `SRUDB.dat`, `SRU*.log`, `SRU*.jrs`, `SRU.chk`, `SRUDB.jfm` | Network/application resource usage database | Specialized parse | Useful later for network/application usage timelines. |
| MFT | `LiveResponseData\CopiedFiles\mft` | `$MFT` | NTFS master file table | Specialized parse | Usually large; parser-heavy and should be scoped. |
| USN journal | `LiveResponseData\CopiedFiles\usnjrnl`, `LiveResponseData\CopiedFiles\D_usnjrnl` | `$UsnJrnl_$J.bin` | NTFS change journal | Specialized parse | Use for file creation/modification/deletion pivots. |
| Windows event logs | `LiveResponseData\CopiedFiles\eventlogs\Logs` | `*.evtx`, `*.etl` | Windows event channels | Specialized parse | Must record channels present/missing; do not rely on one log source alone. |
| Hosts file | `LiveResponseData\CopiedFiles\hosts` | `hosts` | Local hostname override file | Direct ingest | Baseline network/config artifact. |
| Chrome artifacts | `LiveResponseData\CopiedFiles\chrome\<profile>\history`, `cache`, `cookies` | `History`, cache/cookie DB files if present | Chromium browser activity | Specialized parse | Respect scope and privacy constraints. Empty profile subfolders should be recorded. |
| Firefox artifacts | `LiveResponseData\CopiedFiles\firefox\<profile>\history`, `download`, `cache`, `cookies` | Firefox DBs/files if present | Firefox browser activity | Specialized parse | Record empty folders as coverage limitations, not absence of activity. |
| Internet Explorer / Edge legacy artifacts | `LiveResponseData\CopiedFiles\ie` | Browser cache/history artifacts if present | Legacy browser activity | Specialized parse | May be empty on modern hosts. |
| Copy tool log | `LiveResponseData\CopiedFiles\forecopy_handy.log` | Copy operation log | Evidence copy status and errors | Direct ingest | Review for failed, skipped, or inaccessible artifact copies. |

## NetworkInfo Detailed Map

| Data type | Path pattern / files | What it represents | Baseline use | Parser class | Coverage notes |
|---|---|---|---|---|---|
| Active network connections | `netstat_anb_results.txt`, `TCPView.txt`, `cports.html` | Connections/process-network association at collection time | Current network state snapshot | Light parse | Point-in-time only; do not conclude exfiltration from this alone. |
| ARP and gateway lookup | `Gateway_ARP_Lookup.txt` | Local gateway/ARP context | Network identity and local network context | Direct ingest | May help identify local segment. |
| NetBIOS information | `nbtstat.txt`, `NetBIOS_sessions.txt` | NetBIOS names/sessions | Account/session/network context | Direct ingest or light parse | Empty files should be recorded. |

## PersistenceMechanisms Detailed Map

| Data type | Path pattern / files | What it represents | Baseline use | Parser class | Coverage notes |
|---|---|---|---|---|---|
| Autoruns inventory | `autorunsc.csv`, `autorunsc.txt` | Autorun locations from Sysinternals Autoruns | Persistence surface inventory | Light parse | Do not treat autorun presence as malicious at baseline stage. |
| Services | `services_aw_processes.txt` | Service inventory with process context | Service baseline | Light parse | Cross-check later with registry/services and event logs if needed. |
| Scheduled tasks | `scheduled_tasks.txt` | Scheduled task inventory | Persistence surface inventory | Light parse | Later analysis may require task XML or event logs. |
| Startup entries | `Startup_wmic.txt` | Startup command inventory | Persistence surface inventory | Light parse | Cross-check later with Startup folders and registry run keys. |
| Loaded DLLs | `Loaded_dlls.txt` | DLLs loaded in processes | Runtime module baseline | Parser-heavy or scoped light parse | Large and noisy; use scoped process/path review. |
| Driver load order | `Driver_group_load_order_wmic.txt` | Driver group/load order configuration | Driver baseline | Direct ingest or light parse | Configuration context, not evidence of maliciousness by itself. |

## UserInfo Detailed Map

| Data type | Path pattern / files | What it represents | Baseline use | Parser class | Coverage notes |
|---|---|---|---|---|---|
| Current user context | `whoami.txt` | User context used during collection | Current account identity | Direct ingest | Very small baseline artifact. |
| Logon inventory | `All_logons_wmic.txt` | WMIC logon/session/account output | User/session baseline | Light parse | Complements `PsLoggedon.txt` and event logs. |

## Coverage Review Rules

1. Record every mapped path as `present`, `missing`, `empty`, `partial`, `blocked`, or `not_applicable`.
2. Record file counts and total size by mapped folder before parsing.
3. Record large files as `parser-heavy`; do not parse them fully by default.
4. Treat empty files and empty folders as coverage facts, not findings.
5. Use this map to decide what is available before building hypotheses or selecting hunting scenarios.
6. If a mapped artifact is expected but missing, record the limitation and whether follow-up collection is needed.

## CASE-010 Sample Coverage Notes

The sample collection `HOSTNAME_20260409_134403` shows these notable coverage examples:

| Artifact family | Sample state |
|---|---|
| BasicInfo | Present with 31 files. |
| Registry hives | Present with machine hives and multiple user hives. |
| Prefetch | Present with many `.pf` files. |
| Windows event logs | Present with hundreds of `.evtx` and some `.etl` files. |
| MFT | Present as `$MFT`, large/parser-heavy. |
| USN journal | Present in `usnjrnl` and `D_usnjrnl`. |
| Amcache | Present as `Amcache.hve`. |
| SRUM | Present as `SRUDB.dat` and related logs. |
| Browser artifacts | Chrome history present for one profile; several Firefox/Chrome subfolders are empty. |
| NetworkInfo | Present; some NetBIOS files are empty. |
| PersistenceMechanisms | Present with autoruns, services, scheduled tasks, startup, loaded DLLs. |
| ForensicImages | DiskImage and Memory folders present but empty in this sample. |
