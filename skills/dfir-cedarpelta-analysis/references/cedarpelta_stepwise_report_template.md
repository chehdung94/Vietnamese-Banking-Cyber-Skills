# Cedarpelta Stepwise Investigation Report Template

## Purpose

Use this template as the working report for Cedarpelta evidence review and investigation.

Complete each section immediately after the corresponding SOP step or task flow is finished. Do not wait until the end of the investigation to reconstruct results from memory or chat history.

If a section is skipped, blocked, deferred, or not applicable, still fill in the status, reason, limitations, and recommended next step.

## Report Metadata

| Field | Value |
|---|---|
| Case ID |  |
| Host / Evidence Package |  |
| Analyst / Agent |  |
| Report File |  |
| Report Created |  |
| Last Updated |  |
| Current Status | `in_progress / completed / blocked / failed` |
| Scope |  |

## 1. Evidence Scope Identification

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Evidence Root Reviewed |  |
| Cedarpelta Package Name |  |
| Host Identified |  |
| Collection Type | `LiveResponse / ForensicImages / Mixed / Unknown` |
| Inputs Reviewed |  |
| Output File(s) |  |

### Summary

```text

```

### Limitations / Open Questions

```text

```

### Recommended Next Step

```text

```

## 2. Pre-Analysis Host Baseline

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Inputs Reviewed |  |
| Output File(s) |  |

### Basic Host Context

| Baseline Item | Value | Evidence Ref | Notes |
|---|---|---|---|
| Computer name |  |  |  |
| Domain / workgroup |  |  |  |
| Host role / owner if known |  |  |  |
| OS version / build / edition |  |  |  |
| Architecture |  |  |  |
| System time / timezone / codepage |  |  |  |
| Local users |  |  |  |
| Logged-on users |  |  |  |
| User profiles |  |  |  |
| Installed applications |  |  |  |
| Security tools / EDR / AV visible |  |  |  |
| Loaded drivers |  |  |  |
| Running processes |  |  |  |
| Physical disks |  |  |  |
| Logical volumes |  |  |  |
| Network identity |  |  |  |
| Collection time |  |  |  |
| Collection scope |  |  |  |

### Baseline Summary

```text

```

### Baseline Limitations

```text

```

## 3. Artifact Coverage Review

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Collection Map Used | `references/cedarpelta_collection_artifact_map.md` |
| Inputs Reviewed |  |
| Output File(s) |  |

### Coverage Summary

| Artifact Family | Expected Path | State | File Count | Total Size | Parser Class | Notes |
|---|---|---:|---:|---:|---|---|
| Collection metadata |  | `present / missing / empty / partial / blocked / not_applicable` |  |  | direct ingest |  |
| Forensic images |  |  |  |  | specialized parse |  |
| Memory image |  |  |  |  | specialized parse |  |
| BasicInfo | `LiveResponseData\BasicInfo` |  |  |  | direct/light/parser-heavy |  |
| Registry hives | `LiveResponseData\CopiedFiles\registry` |  |  |  | specialized parse |  |
| Prefetch | `LiveResponseData\CopiedFiles\prefetch` |  |  |  | specialized parse |  |
| Amcache | `LiveResponseData\CopiedFiles\amcache` |  |  |  | specialized parse |  |
| SRUM | `LiveResponseData\CopiedFiles\SRUMDB` |  |  |  | specialized parse |  |
| MFT | `LiveResponseData\CopiedFiles\mft` |  |  |  | parser-heavy |  |
| USN journal | `LiveResponseData\CopiedFiles\usnjrnl` |  |  |  | specialized parse |  |
| Windows event logs | `LiveResponseData\CopiedFiles\eventlogs\Logs` |  |  |  | specialized parse |  |
| Browser artifacts | `LiveResponseData\CopiedFiles\chrome/firefox/ie` |  |  |  | specialized parse |  |
| NetworkInfo | `LiveResponseData\NetworkInfo` |  |  |  | light parse |  |
| PersistenceMechanisms | `LiveResponseData\PersistenceMechanisms` |  |  |  | light parse |  |
| UserInfo | `LiveResponseData\UserInfo` |  |  |  | direct/light parse |  |

### Empty / Missing / Parser-Heavy Items

| Item | State | Impact | Follow-Up Needed | Notes |
|---|---|---|---|---|
|  |  | `none / low / medium / high / unknown` | `yes / no` |  |

### Coverage Limitations

```text

```

## 4. Investigator Tool Readiness

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Readiness Guide Used | `references/investigator_tool_readiness.md` |
| Inputs Reviewed |  |
| Output File(s) |  |

### Tool Readiness Summary

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

### Blocked Or Limited Parser Tasks

| Artifact Type | Required Tool | Status | Fallback | Next Action |
|---|---|---|---|---|
|  |  |  |  |  |

### Tool Readiness Limitations

```text

```

## 5. Hypotheses

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Inputs Reviewed |  |
| Output File(s) |  |

| Hypothesis ID | Statement | Source | State | Evidence Needed | Notes |
|---|---|---|---|---|---|
| HYP-001 |  |  | `open / partially_resolved / resolved / blocked / rejected_with_evidence` |  |  |

### Limitations / Open Questions

```text

```

## 6. Hunting Scenario Selection

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Inputs Reviewed |  |
| Output File(s) |  |

| Scenario ID | Selected | Reason | Evidence Needed | Status |
|---|---|---|---|---|
| suspicious_logon_rdp | `yes / no / deferred` |  |  |  |
| credential_access_privilege_abuse |  |  |  |  |
| powershell_script_execution |  |  |  |  |
| malware_or_suspicious_binary_execution |  |  |  |  |
| persistence |  |  |  |  |
| lateral_movement |  |  |  |  |
| data_staging_or_exfiltration |  |  |  |  |
| defense_evasion |  |  |  |  |
| reconnaissance |  |  |  |  |
| suspicious_tools |  |  |  |  |

## 7. Parser Scope And Processing

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Inputs Reviewed |  |
| Output File(s) |  |

| Parser Task | Evidence Input | Scope / Filter | Reason | Output | Status |
|---|---|---|---|---|---|
|  |  |  |  |  |  |

### Processing Limitations

```text

```

## 8. Cross-Checks

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Inputs Reviewed |  |
| Output File(s) |  |

| Signal / Question | Primary Source | Independent Sources Checked | Result | Limitation |
|---|---|---|---|---|
|  |  |  |  |  |

## 9. Pivot Chains

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Inputs Reviewed |  |
| Output File(s) |  |

| Pivot ID | Seed Type | Seed Value | Time Window | Sources Checked | Result |
|---|---|---|---|---|---|
| PIV-001 |  |  |  |  |  |

## 10. Findings, Limitations, And Unresolved Questions

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Output File(s) |  |

### Findings

| Finding ID | Title | Severity | Confidence | Evidence Refs | Summary |
|---|---|---|---|---|---|
|  |  |  | `low / medium / high` |  |  |

### Limitations

| Limitation | Impact | Blocks Closure | Follow-Up |
|---|---|---|---|
|  | `none / low / medium / high / unknown` | `yes / no` |  |

### Unresolved Questions

| Question | Why It Matters | Evidence Needed | Blocks Closure |
|---|---|---|---|
|  |  |  | `yes / no` |

## 11. Follow-Up Action Items

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Output File(s) |  |

| Action ID | Priority | Question | Recommended Data Sources | Recommended Steps | Owner | Blocks Closure |
|---|---|---|---|---|---|---|
| ACT-001 | `low / medium / high` |  |  |  |  | `yes / no` |

## 12. Closure Decision

| Field | Result |
|---|---|
| Status | `completed / partial / blocked / skipped / not_applicable` |
| Completion Time |  |
| Output File(s) |  |
| Closure Allowed | `yes / no` |

### Closure Rationale

```text

```

### Closure Blockers

| Blocker | Related Evidence / Limitation | Required Action |
|---|---|---|
|  |  |  |

## Change Log

| Time | Section Updated | Summary | Updated By |
|---|---|---|---|
|  |  |  |  |
