# Hypothesis Lifecycle

## States

```text
open
partially_resolved
resolved
blocked
rejected_with_evidence
```

## State Rules

### open

Use when the hypothesis is created but not sufficiently tested.

### partially_resolved

Use when some evidence supports or rejects part of the hypothesis, but key artifacts or cross-checks remain incomplete.

### resolved

Use only when evidence sufficiently answers the hypothesis and required cross-checks are complete.

### blocked

Use when investigation cannot continue due to missing evidence, unavailable parser, corrupted files, insufficient scope, or authorization constraints.

### rejected_with_evidence

Use when evidence actively contradicts the hypothesis. Do not use this for mere absence of evidence.

## Required Hypothesis Fields

```json
{
  "hypothesis_id": "H-001",
  "statement": "",
  "source": "triage|finding|artifact|ioc|analyst|supervisor",
  "status": "open",
  "related_scenarios": [],
  "evidence_supporting": [],
  "evidence_contradicting": [],
  "cross_checks_completed": [],
  "cross_checks_missing": [],
  "limitations": [],
  "follow_up_action_refs": [],
  "closure_impact": "blocks_closure|does_not_block_closure"
}
```

## Closure Rule

If unresolved and material to case outcome, create a follow-up action with:

```json
{
  "blocks_closure": true
}
```
