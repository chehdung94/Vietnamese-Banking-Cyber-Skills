# Cedarpelta Analysis SOP

## Purpose

Use this skill to perform deep investigation on Windows endpoint evidence collected by Cedarpelta Build / Live Response Collection.

This skill defines how to investigate.

## Operating Principle

Do not behave like a simple artifact parser.

Behave like a DFIR investigation specialist:

```text
Evidence → Hypothesis → Cross-check → Pivot → Finding → Limitation → Follow-up → Closure decision
```

Never conclude that a case is clean only because one artifact source is clean.

## Investigation Flow

For every task:

1. Identify Cedarpelta evidence in scope.
2. Build the pre-analysis host baseline.
3. Review artifact coverage.
4. Check investigator workstation tool readiness for available artifact types.
5. Build or inherit investigation hypotheses.
6. Select relevant hunting scenarios.
7. Parse only the evidence subset required by scope or hypothesis.
8. Cross-check findings across independent artifact sources.
9. Pivot around seed events.
10. Record findings, limitations, and unresolved questions.
11. Generate follow-up action items.
12. Decide whether closure is allowed.

## Step Output Persistence

After each investigation step, task flow, or assigned subtask is completed, write the result to an appropriate case file before moving to the next step.

The written output should capture:

```text
step_or_task_name
status
scope
inputs_reviewed
methods_used
summary_result
evidence_refs
limitations
open_questions
recommended_next_step
completion_time
```

Use stable case artifacts for handoff-ready outputs and the agent private workspace for draft, temporary, or parser-working outputs. Do not rely on chat-only summaries as the system of record.

Use **`references/cedarpelta_stepwise_report_template.md`** as the default report structure when the case does not already provide a stricter report template.

If a step is skipped, blocked, deferred, or not applicable, still write a short record explaining the reason and whether it affects confidence, follow-up actions, or closure.

## 1. Evidence Scope Identification

Identify the Cedarpelta evidence package and confirm what is in scope before reviewing host context or artifact coverage.

Record:

```text
case_id
evidence_root
collection_package_name
source_host_or_folder_identity
collection_type
assigned_task_scope
included_evidence_areas
excluded_evidence_areas
initial_scope_limitations
```

Do not infer incident conclusions from scope identification. This step only defines what evidence is available for the current task.

## 2. Pre-Analysis Host Baseline

Before any forensic analysis, threat hunting, IOC search, suspicious-event review, attribution, compromise assessment, or incident conclusion, collect a basic host baseline and evidence coverage overview.

This is an orientation step only. The purpose is to understand the investigated machine and available evidence before analysis begins.

The baseline should answer:

```text
what machine is being investigated
who uses or has used the machine
what operating system and locale context exists
what applications, services, drivers, and processes are present
what disks, volumes, and basic storage layout exist
what network identity is visible
when and how the evidence was collected
what evidence groups are available, missing, empty, or parser-heavy
```

Collect and report only basic context fields when available:

```text
computer_name
domain_or_workgroup
host_role_or_owner_when_known
ip_addresses_mac_addresses_and_network_adapters
operating_system_version_build_edition_and_architecture
system_time_timezone_codepage_and_locale
uptime_or_last_boot_when_available
local_users
logged_on_users
user_profiles
basic_user_privilege_context_when_available
installed_applications
security_tools_edr_av_when_visible
loaded_drivers
running_processes
services_when_available
physical_disks
logical_volumes_drive_letters_size_and_free_space
filesystem_type_when_available
collection_tool
collection_time
collection_scope
source_host_or_evidence_folder_identity
empty_truncated_unusually_large_or_parser_heavy_files
missing_artifact_groups
```

Do not assess whether an application, user, process, file, connection, or event is malicious in this step. Unknown or unusual items may be recorded as inventory only; they become analysis targets only after hypotheses, scenario selection, or scoped parser decisions are made.

## 3. Artifact Coverage Review

Before parsing or interpreting specific signals, create a lightweight host/evidence coverage overview. Use directory inventory, file names, extensions, sizes, timestamps, and small format-identification samples only. Do not read or parse entire large files at this stage.

This step supports the Pre-Analysis Host Baseline. It remains baseline collection only. Do not perform forensic investigation, threat hunting, IOC searching, suspicious-event analysis, attribution, compromise assessment, or incident conclusions in this step.

Use `references/cedarpelta_collection_artifact_map.md` as the collection map for this step. Check each mapped Cedarpelta output path and record whether each artifact family is present, missing, empty, partial, blocked, or not applicable before moving to hypotheses or parsing.

At minimum, classify available evidence into host context groups such as:

```text
host_system_metadata
disk_storage_inventory
installed_software_and_drivers
process_snapshot
account_session_activity
windows_event_or_activity_logs
filesystem_enumeration
hash_inventory
collection_metadata
```

The overview should answer:

```text
what evidence groups are present
what evidence groups are absent or not visible in the scoped path
which files are empty, truncated, unusually large, or parser-heavy
which artifacts describe host baseline context before incident analysis
which artifacts are likely direct-ingest, light-parse, specialized-parse, or skip
which missing artifacts reduce confidence or require follow-up collection
```

Record the overview as preliminary context, not as findings. It is used to understand the investigated host before deep analysis, choose relevant hypotheses and hunting scenarios, scope parsers, and document limitations.

Large host-wide listings and event exports should normally be marked parser-heavy until a hypothesis, time window, IOC, user, process, path, event ID, or artifact group justifies scoped parsing.

For each artifact group, mark one status:

```text
reviewed
finding
deferred
blocked
not_available
not_applicable
```

Minimum artifact groups:

```text
windows_event_logs
live_response_text
persistence
execution_artifacts
registry_hives
file_system_artifacts
browser_artifacts
network_artifacts
account_session_artifacts
collection_metadata
```

For every group, record:

```text
artifact_group
status
evidence_refs
coverage_notes
limitations
```

## 4. Investigator Tool Readiness

After artifact coverage is reviewed and before parser-heavy processing begins, check whether the investigator workstation has the tools required for the artifact families that are present.

Use `references/investigator_tool_readiness.md` for artifact-to-tool mapping, readiness statuses, check commands, missing-tool handling, and installation guidance.

Record:

```text
available_artifact_types
required_tools
tool_status
tool_path_or_version
missing_tools
fallback_options
blocked_parser_tasks
recommended_install_or_toolkit_actions
```

Do not parse evidence as part of the readiness check. This step only determines whether the workstation can support the next parsing or analysis tasks.

## 6. Hunting Scenarios

Evaluate scenarios relevant to the incident type:

```text
suspicious_logon_rdp
credential_access_privilege_abuse
powershell_script_execution
malware_or_suspicious_binary_execution
persistence
lateral_movement
data_staging_or_exfiltration
defense_evasion
reconnaissance
suspicious_tools
```

Each scenario must include:

```text
scenario_id
status
hypotheses_tested
evidence_checked
supporting_evidence
contradicting_evidence
missing_evidence
assessment
confidence
```

Valid scenario status:

```text
reviewed
finding
not_applicable
blocked
deferred
```

## 5. Hypothesis Lifecycle

Each hypothesis must have one state:

```text
open
partially_resolved
resolved
blocked
rejected_with_evidence
```

Do not move a hypothesis to `resolved` unless:

* relevant artifact groups were checked;
* mandatory cross-check was performed;
* limitations were recorded;
* unresolved items were converted into follow-up actions.

A benign explanation for one user, host, source IP, process, or timestamp does not close unrelated hypotheses.

## 8. Mandatory Cross-Check Rules

Never close a scenario based on a single artifact source.

### RDP / Logon

Security.evtx alone is insufficient.

Cross-check with:

```text
TerminalServices-RemoteConnectionManager
TerminalServices-LocalSessionManager
Security.evtx
PsLoggedon
netstat
TCPView
PsList
Prefetch
Amcache
SRUM
MFT
USN
user_registry_hives
```

Absence of Event ID 4624 or 4625 is not proof of no RDP if retention, collection scope, or TerminalServices logs are incomplete.

### Malware / Execution

Running processes alone are insufficient.

Cross-check with:

```text
Prefetch
Amcache
Shimcache
UserAssist
BAM_DAM
PowerShell logs
scheduled tasks
services
autoruns
browser downloads
MFT
USN
file hashes
```

### Persistence

Autoruns alone is insufficient.

Cross-check with:

```text
registry run keys
services
scheduled tasks
startup folders
WMI
loaded DLLs
user hives
```

### Exfiltration

Netstat at collection time is insufficient.

Cross-check with:

```text
SRUM
browser history
browser downloads
cloud client traces
archive tool execution
MFT
USN
proxy logs
firewall logs
```

## 9. Pivot Workflow

Every seed event or finding must generate a pivot chain.

Seed types:

```text
timestamp
user
source_ip
host
process
file_path
command_line
event_id
registry_key
hash
domain
url
```

Default pivot windows:

```text
login_rdp: -24h to +24h
process_execution: -4h to +24h
persistence: -7d to +24h
data_staging_exfiltration: -24h to +24h
```

For each pivot chain, evaluate:

```text
pre_event_activity
during_event_activity
post_event_activity
```

## 7. Parser Decision Logic

Do not parse full evidence by default.

Parse only when needed for:

```text
scope
hypothesis
user
host
timestamp_window
event_id
ioc
artifact_group
```

Parser selection must follow `parser_recommendation_matrix.json`.

Record parser commands, filters, and assumptions in the agent private workspace.

Reusable normalized outputs should be written only when they are stable enough for other agents.

## 10. Finding Standard

Each finding must include:

```text
finding_id
title
severity
confidence
summary
evidence_refs
affected_entities
timeline_refs
attack_mapping
hypotheses_supported
hypotheses_rejected
limitations
recommended_actions
```

Do not write findings that exceed the available evidence.

## Confidence Assessment

Use:

```text
high
medium
low
```

Guidance:

```text
high = multiple independent artifacts support the same conclusion, with low ambiguity
medium = evidence supports the conclusion but some relevant sources are missing or incomplete
low = weak, indirect, partial, or single-source evidence
```

Missing evidence must reduce confidence unless explicitly not applicable.

## 11. Follow-Up Action Rules

Create follow-up actions for every unresolved or blocked investigation item.

Each action item must include:

```text
action_id
priority
question
rationale
evidence_refs
recommended_data_sources
recommended_steps
recommended_owner
blocks_closure
```

If an unresolved issue could materially change the case conclusion, set:

```text
blocks_closure = true
```

## 12. Closure Control

Closure is not allowed when:

* any material hypothesis remains open without follow-up;
* required cross-check was not performed;
* key evidence is missing and blocks conclusion;
* there is a contradiction between Cedarpelta findings and other agent outputs;
* any follow-up action has `blocks_closure = true`.

When closure is not allowed, explain why in the case notes and recommend next steps.

When closure is allowed, summarize the investigation process, findings, limitations, and rationale for closure in the case report.
## Final Quality Gate

Before completing, verify:

```text
Cedarpelta-only scope enforced
artifact coverage recorded
relevant scenarios reviewed
hypotheses have lifecycle states
mandatory cross-check performed or limitation recorded
pivot chains created for seed events
findings include evidence references
unresolved items became follow-up actions
closure_allowed is justified
```
