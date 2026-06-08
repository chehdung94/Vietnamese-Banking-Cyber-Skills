# Investigator Tool Readiness Guide

## Purpose

Use this guide before parsing Cedarpelta evidence on an investigator workstation.

The goal is to determine which artifact types can be processed with the tools currently available, which parsing tasks are blocked, and what should be installed or configured before deeper investigation.

This is an environment readiness check only. Do not analyze case evidence while performing this check.

## Readiness Status

Use one of these statuses for each tool or capability:

```text
ready
present_but_unverified
missing
version_risk
blocked
not_required_for_scope
```

## Core Tool Categories

| Capability | Recommended tools | Used for | Check command / method | If missing |
|---|---|---|---|---|
| Windows artifact parsing | Eric Zimmerman's Tools | Registry, Amcache, Shimcache, Prefetch, SRUM, LNK, JumpLists, MFT, Timeline Explorer workflows | Check EZ Tools folder and run selected executables with `--help` or `-h` | Install/download ZimmermanTools package or copy approved internal toolkit. |
| Event log parsing | EvtxECmd, Chainsaw, Hayabusa, Windows Event Viewer, PowerShell `Get-WinEvent` | `.evtx` and event timeline extraction | `EvtxECmd.exe -h`, `hayabusa.exe --help`, or PowerShell availability | Install approved parser; use PowerShell as limited fallback. |
| Registry hive inspection | Registry Explorer, RECmd, RegRipper | `SYSTEM`, `SOFTWARE`, `SAM`, `SECURITY`, `NTUSER.DAT`, `USRCLASS.DAT` | `RECmd.exe -h`, Registry Explorer launch check | Install EZ Tools and RegRipper if plugin-based review is needed. |
| Filesystem timeline parsing | MFTECmd, MFTECmd with `$MFT`, USN tools, Timeline Explorer | `$MFT`, `$UsnJrnl`, file timeline pivots | `MFTECmd.exe -h` | Install EZ Tools; avoid manual parsing for large NTFS artifacts. |
| Prefetch parsing | PECmd | `*.pf` execution traces | `PECmd.exe -h` | Install EZ Tools. |
| Amcache parsing | AmcacheParser or AppCompatCacheParser/RECmd plugins where applicable | `Amcache.hve` | `AmcacheParser.exe -h` | Install EZ Tools. |
| SRUM parsing | SrumECmd | `SRUDB.dat` | `SrumECmd.exe -h` | Install EZ Tools. |
| Browser artifacts | BrowserHistoryView, BrowsingHistoryView, Hindsight, DB Browser for SQLite, sqlite3 | Chrome/Edge/Firefox history, downloads, cookies when in scope | `sqlite3 --version`; tool launch/version check | Install SQLite tooling and approved browser parsers. |
| CSV/table review | Timeline Explorer, Excel, LibreOffice, PowerShell `Import-Csv`, Python pandas | Autoruns CSV, parser outputs, tabular review | Check app availability and PowerShell modules | Use PowerShell as fallback; install Timeline Explorer for large parser output review. |
| Hashing and integrity | PowerShell `Get-FileHash`, `certutil`, HashMyFiles | Validate copied files and manifests | `Get-Command Get-FileHash`; `certutil -hashfile` | Use built-in PowerShell/certutil; install GUI hash tool only if needed. |
| Archive handling | 7-Zip | Evidence packages and compressed outputs | `7z.exe` availability | Install 7-Zip or approved archive tool. |
| Text search | ripgrep, PowerShell `Select-String` | Fast review of text exports and parser outputs | `rg --version` | Install ripgrep; use `Select-String` as fallback. |
| Scripting/runtime | PowerShell, Python 3 | Automation, conversion, light parsers | `pwsh -v` or `$PSVersionTable`; `python --version` | Install approved Python; confirm no untrusted package downloads during case work. |
| Reporting | Markdown editor, Word/LibreOffice, PDF export if required | Final report and stepwise report artifacts | Application check | Use Markdown report first; export later if needed. |

## Artifact-To-Tool Mapping

| Cedarpelta data type | Common path | Primary tools | Fallback tools | Readiness impact if missing |
|---|---|---|---|---|
| Registry hives | `LiveResponseData\CopiedFiles\registry` | Registry Explorer, RECmd, RegRipper | PowerShell/offline registry APIs for limited checks | Blocks reliable registry-based persistence, user activity, services, and execution artifact review. |
| Prefetch | `LiveResponseData\CopiedFiles\prefetch` | PECmd | Manual filename review only | Blocks reliable execution timeline extraction from Prefetch. |
| Amcache | `LiveResponseData\CopiedFiles\amcache\Amcache.hve` | AmcacheParser | Registry Explorer limited review | Blocks reliable Amcache execution/install metadata extraction. |
| SRUM | `LiveResponseData\CopiedFiles\SRUMDB\SRUDB.dat` | SrumECmd | None practical | Blocks SRUM network/application usage analysis. |
| MFT | `LiveResponseData\CopiedFiles\mft\$MFT` | MFTECmd | None practical for large MFT | Blocks scalable filesystem timeline analysis. |
| USN journal | `LiveResponseData\CopiedFiles\usnjrnl`, `D_usnjrnl` | MFTECmd / USN parser | None practical for large journal | Blocks file change timeline pivots. |
| Windows event logs | `LiveResponseData\CopiedFiles\eventlogs\Logs\*.evtx` | EvtxECmd, Hayabusa, Chainsaw | PowerShell `Get-WinEvent` | Limits event search, normalization, and rule-based detection. |
| Autoruns | `LiveResponseData\PersistenceMechanisms\autorunsc.csv` | Timeline Explorer, Excel, PowerShell `Import-Csv` | Text editor for small files | Reduces review efficiency, usually not fully blocking. |
| Services / scheduled tasks / startup text | `LiveResponseData\PersistenceMechanisms` | PowerShell, text search, Timeline Explorer | Text editor | Usually light-parse capable without specialized tools. |
| Running processes | `LiveResponseData\BasicInfo`, `PersistenceMechanisms` | PowerShell, text search, spreadsheet viewer | Text editor | Usually not blocking. |
| Network snapshots | `LiveResponseData\NetworkInfo` | PowerShell, text search, browser for HTML | Text editor | Usually not blocking. |
| Browser history | `LiveResponseData\CopiedFiles\chrome`, `firefox`, `ie` | sqlite3, DB Browser for SQLite, Hindsight, NirSoft tools | Manual SQLite file copy review is not recommended | Blocks reliable browser timeline extraction. |
| File hashes | `*_File_Hashes.txt`, `Hashes_*` | PowerShell, text search, spreadsheet viewer | Text editor | Usually not blocking. |
| BasicInfo text and HTML | `LiveResponseData\BasicInfo` | PowerShell, ripgrep, browser, spreadsheet viewer | Text editor | Usually direct-ingest/light-parse capable. |
| Memory image | `ForensicImages\Memory` | Volatility 3, MemProcFS | None practical | Blocks memory analysis if memory is in scope. |
| Disk image | `ForensicImages\DiskImage` | FTK Imager, Arsenal Image Mounter, Autopsy, X-Ways, Magnet AXIOM if licensed | 7-Zip only for some image containers, limited | Blocks full disk image mounting and broad filesystem review. |

## Minimum Workstation Check

Record the result of these checks before parsing:

```text
operating_system
available_disk_space
case_working_directory
tool_root_paths
zimmerman_tools_available
event_log_parser_available
registry_parser_available
filesystem_timeline_parser_available
browser_parser_available
memory_parser_available_if_memory_in_scope
disk_image_tool_available_if_disk_image_in_scope
python_available
powershell_available
ripgrep_or_text_search_available
spreadsheet_or_timeline_viewer_available
archive_tool_available
```

## Suggested PowerShell Checks

Adjust paths to the local approved toolkit location.

```powershell
$toolRoots = @(
  "C:\Tools",
  "C:\Tools\EZTools",
  "C:\ZimmermanTools",
  "D:\Tools",
  "D:\Cybersecurity\Tools"
)

$executables = @(
  "PECmd.exe",
  "RECmd.exe",
  "RegistryExplorer.exe",
  "AmcacheParser.exe",
  "SrumECmd.exe",
  "MFTECmd.exe",
  "EvtxECmd.exe",
  "TimelineExplorer.exe",
  "Hayabusa.exe",
  "chainsaw.exe",
  "sqlite3.exe",
  "7z.exe",
  "rg.exe",
  "python.exe",
  "vol.exe"
)

foreach ($exe in $executables) {
  $found = Get-Command $exe -ErrorAction SilentlyContinue
  if (-not $found) {
    $found = Get-ChildItem -Path $toolRoots -Filter $exe -Recurse -ErrorAction SilentlyContinue | Select-Object -First 1
  }
  [PSCustomObject]@{
    Tool = $exe
    Status = if ($found) { "present_but_unverified" } else { "missing" }
    Path = if ($found) { $found.Source ?? $found.FullName } else { "" }
  }
}
```

## Readiness Report Template

| Tool / Capability | Status | Path / Version | Required For Current Scope | Missing Impact | Required Action |
|---|---|---|---|---|---|
| ZimmermanTools package |  |  |  |  |  |
| PECmd |  |  | Prefetch |  |  |
| RECmd / Registry Explorer |  |  | Registry hives |  |  |
| AmcacheParser |  |  | Amcache |  |  |
| SrumECmd |  |  | SRUM |  |  |
| MFTECmd |  |  | MFT / USN |  |  |
| EvtxECmd / Hayabusa / Chainsaw |  |  | Event logs |  |  |
| sqlite3 / browser parser |  |  | Browser artifacts |  |  |
| Volatility 3 / MemProcFS |  |  | Memory image if present |  |  |
| FTK Imager / image mounter |  |  | Disk image if present |  |  |
| 7-Zip |  |  | Archives |  |  |
| ripgrep / text search |  |  | Text exports |  |  |
| Python |  |  | Automation/light parsing |  |  |
| PowerShell |  |  | Baseline checks/light parsing |  |  |
| Timeline Explorer / spreadsheet viewer |  |  | CSV/timeline review |  |  |

## Missing Tool Handling

When a required tool is missing:

1. Record the missing tool and affected artifact type.
2. Decide whether the current task can proceed using a safe fallback.
3. If fallback is insufficient, mark the parser task `blocked`.
4. Recommend installation from an approved source or use of an approved internal toolkit.
5. Do not download tools from the internet during case work unless explicitly approved by the case lead and allowed by workstation policy.
6. Re-run the readiness check after installation or toolkit path changes.

## Installation Guidance

Use organization-approved sources and hashes where available. At minimum:

| Tool family | Install / obtain from | Notes |
|---|---|---|
| Eric Zimmerman's Tools | Approved internal DFIR toolkit or official EZ Tools release mirror | Keep the package together; many tools are used as a set. |
| Hayabusa / Chainsaw | Approved internal toolkit or official project releases | Rule sets must be versioned and recorded. |
| Volatility 3 / MemProcFS | Approved internal toolkit or official releases | Required only if memory is in scope. |
| SQLite tooling | Approved package manager or internal toolkit | Useful for browser artifacts. |
| 7-Zip / ripgrep / Python | Approved package manager or internal software repository | Record versions in readiness report. |

## Completion Criteria

The workstation readiness step is complete when:

```text
required_tools_for_available_artifacts_are_checked
missing_tools_are_recorded
fallbacks_are_identified
blocked_parser_tasks_are_marked
installation_or_toolkit_actions_are_recommended
readiness_result_is_written_to_the_case_report
```
