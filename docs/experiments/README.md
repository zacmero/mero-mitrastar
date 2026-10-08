# Experiment records

Use IDs starting with `MITRA-` so predecessor experiment IDs remain unambiguous.
Write one reviewed Markdown record per bounded experiment.

```markdown
# MITRA-NET-001 — Ethernet identity and management baseline

Date/time: UTC timestamp
Device: DSL-100HN-T1-NV / BR_SA_113WUK0b15
Question:
Host and interfaces:
Control route:
Target IP and observed MAC:
Preconditions:

## Procedure
Exact commands and UI paths; duration and scope.

## Observations
What the device actually returned. Separate interpretations from output.

## Artifacts
Ignored local paths, sizes, SHA-256 values, and redaction status.

## Result and limits
Positive, negative, or inconclusive; what has not been established.

## Changes and cleanup
Settings changed, restoration steps, and verified control connectivity.

## Next decision
The smallest next experiment justified by this result.
```

Store raw files under `.local/captures/<UTC timestamp>/` or
`.local/exports/<UTC timestamp>/`. HTTP bodies, headers, packet captures, and
configuration exports can contain credentials; review them before quoting any
content in tracked records. A capture hash identifies evidence without
publishing its sensitive contents.
