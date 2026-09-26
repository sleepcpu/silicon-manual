# ADR-005: CLI Contract

**Status:** Accepted  
**Scope:** Phase 1 PDF Page Normalization

## Decision

The Phase 1 CLI MUST support:

```bash
silicon-manual render manual.pdf
```

An output directory option SHOULD be supported:

```bash
silicon-manual render manual.pdf --output ./output
```

Phase 1 does not require:

```text
inspect
validate
extract
convert
```

or other additional subcommands.

## Rationale

The first executable interface should remain minimal while the Page Artifact contract is being validated.
