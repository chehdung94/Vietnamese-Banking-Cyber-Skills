---
name: dfir-cedarpelta-analysis
description: Investigation methodology for Cedarpelta Build / Live Response Collection evidence from Windows endpoints in DFIR Hospital cases. Use when a Cedarpelta Analysis Agent or DFIR Supervisor needs the reasoning playbook for artifact coverage review, threat hunting scenarios, hypothesis lifecycle, mandatory cross-checking, pivot analysis, parser-scoped processing, confidence assessment, follow-up actions, and closure control.
---

# DFIR Cedarpelta Analysis

## Purpose

Use this skill as the investigation brain for Cedarpelta Build / Live Response Collection evidence.

This skill defines how the agent thinks, investigates, pivots, cross-checks, assesses confidence, and controls closure.
## When to Use

Use this skill when at least one condition is true:

- Cedarpelta Build or Live Response Collection evidence is in scope.
- Evidence classification or inventory identifies one or more items collected by Cedarpelta or an equivalent live response collector.
- The investigation needs Cedarpelta-specific artifact coverage review, hypothesis-driven hunting, pivoting, mandatory cross-checking, parser-scoped processing, confidence assessment, follow-up actions, or closure control.
- A downstream consumer needs Cedarpelta findings, normalized events, unresolved limitations, parser recommendations, or closure signals.
- A conclusion cannot be trusted until Cedarpelta evidence has been reconciled with other findings, timeline events, report conclusions, or human investigator notes.

Do not use this skill as the primary methodology for SIEM-only, firewall-only, VPN-only, email-only, malware reverse engineering, or generic report-writing tasks unless Cedarpelta evidence is part of the scope. Use it only for the Cedarpelta-specific analysis or reconciliation portion.

## Prerequisites

Required inputs, independent of folder structure:

```text
analysis request or tasking instruction
Cedarpelta evidence inventory or evidence list
Cedarpelta raw evidence location
artifact classification or collection manifest
parser recommendation matrix or parser plan
approved investigation scope
```

Required conditions:

- Cedarpelta evidence is identified by inventory, classification, collection metadata, file naming, or investigator decision.
- Original evidence is read-only.
- A private working area exists for notes, queries, parser commands, and exploratory parsed subsets.
- A stable output area exists for findings, summaries, normalized events, and downstream handoff artifacts.
- Parser actions are scoped by hypothesis, artifact group, user, host, time window, Event ID, IOC, or pivot seed.
- Parser recommendations use normalized complexity values: `direct_ingest`, `light`, `specialized`, or `skip`.
- Output validation schemas are available from the active repository, bundled skill schemas, or the receiving system contract.

Useful but optional reconciliation inputs:

```text
intake notes
initial scope
triage result
previous findings
timeline artifacts
preliminary or final report drafts
known benign assets or allowlists
organization capability or logging profile
```

Return blocked instead of guessing when the analysis request, evidence inventory, parser plan, raw evidence location, or approved scope is missing.

## Operating Principle

Do not behave like a simple artifact parser. Use a hypothesis-driven DFIR loop:

```text
Evidence -> Hypothesis -> Cross-check -> Pivot -> Finding -> Limitation -> Follow-up -> Closure decision
```

Never conclude that a case is clean only because one artifact source is clean. Missing evidence reduces confidence unless it is explicitly not applicable.

## Methodology References

Load `references/cedarpelta_analysis_sop.md` before deep analysis. It defines the standard operating procedure for investigating Cedarpelta evidence, including investigation flow, artifact coverage review, hunting scenarios, hypothesis lifecycle, mandatory cross-checks, pivot workflow, parser decision logic, finding and confidence criteria, follow-up action requirements, and closure control rules.

Load focused references as needed:

- `references/cedarpelta_collection_artifact_map.md`: map Cedarpelta collection output paths to artifact families, baseline meaning, parser class, and coverage notes. Load this before artifact coverage review to avoid missing collected data types.
- `references/cedarpelta_stepwise_report_template.md`: working report template for recording each completed SOP step or task flow as it finishes.
- `references/investigator_tool_readiness.md`: workstation readiness guide mapping artifact types to required parsers/tools and fallback actions. Load this after artifact coverage review and before parser-heavy processing.
- `references/artifact_groups.md`: artifact coverage groups and coverage notes.
- `references/hunting_scenarios.md`: scenario IDs, statuses, and evidence expectations.
- `references/hypothesis_lifecycle.md`: hypothesis states and transition rules.
- `references/cross_check_rules.md`: mandatory independent artifact checks.
- `references/pivot_workflow.md`: seed types, pivot windows, and pivot-chain documentation.
- `references/parser_policy.md`: scoped parser selection and parser output handling.

Use bundled schemas in `schemas/` when validating local Cedarpelta outputs or when the case repository schema is unavailable.

## SOP Authority

Always load and follow `references/cedarpelta_analysis_sop.md` for investigation flow and step order.

Use focused reference documents only for supporting details such as artifact expectations, scenarios, hypotheses, cross-checks, pivots, parser policy, and output contracts.

When in doubt, follow the SOP first.
