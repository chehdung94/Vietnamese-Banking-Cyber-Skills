# Parser Policy

## Parser Matrix Contract

Use the case-local parser matrix as the source of truth for parser selection:

```text
cases/<CASE_ID>/shared/parser_recommendation_matrix.json
```

Validate the matrix against the bundled schema when the case repository schema is unavailable:

```text
schemas/parser_recommendation_matrix.schema.json
```

A valid parser matrix contains `parser_matrix.<evidence_group>` entries with:

```text
evidence_group
raw_format
preferred_parser
alternative_parsers
output_format
parse_complexity
consumer_modes
notes
```

`parse_complexity` must use only:

```text
direct_ingest
light
specialized
skip
```

If deriving a matrix from `evidence_type_classification.json`, normalize parser requirements before using the matrix:

```text
false -> direct_ingest
light -> light
true -> specialized
skip -> skip
```

## Parser Decision Rules

Only run or recommend parsers listed in `parser_recommendation_matrix.json` unless the user or Supervisor explicitly approves a new parser.

Use `preferred_parser` first. Use `alternative_parsers` only when the preferred parser is unavailable, fails, or cannot answer the current hypothesis.

Do not parse the entire evidence set by default.

Parse only by a defensible scope:

- case time window;
- pivot window;
- host;
- user;
- Event ID;
- artifact group;
- IOC;
- process name;
- file path;
- registry key;
- hypothesis.

Use `parse_complexity` as the execution gate:

- `direct_ingest`: read already structured/plain evidence directly and normalize if useful.
- `light`: use lightweight text, CSV, HTML, or table extraction scoped to the task.
- `specialized`: use the recommended forensic parser, scoped by hypothesis or pivot.
- `skip`: do not parse unless Supervisor explicitly changes scope or classification.

## Query Logging

Record parser command, filter, source file, output path, and rationale in:

```text
cases/<CASE_ID>/agents/cedarpelta_analysis/queries/
```

## Parsed Subset Output

Write unstable or exploratory parse output to:

```text
cases/<CASE_ID>/agents/cedarpelta_analysis/parsed_subset/
```

## Normalized Output

Only publish stable reusable output to:

```text
cases/<CASE_ID>/processed/windows/
```

Naming convention:

```text
cedarpelta_<artifact>_<scope>.normalized.v001.jsonl
```

Examples:

```text
cedarpelta_rdp_user011.normalized.v001.jsonl
cedarpelta_prefetch_suspicious_execution.normalized.v001.jsonl
cedarpelta_registry_persistence.normalized.v001.jsonl
```