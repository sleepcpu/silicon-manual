# TASK-001: Implement Phase 1 PDF Normalizer

## Status

READY

## Role

You are the Implementation Agent.

You are responsible for implementation and testing.

You are NOT the architecture decision maker.

## Read First

Before modifying the repository, read:

```text
README.md

docs/adr/ADR-001-page-identity.md
docs/adr/ADR-002-rendering-standard.md
docs/adr/ADR-003-coordinate-system.md
docs/adr/ADR-004-page-artifact.md
docs/adr/ADR-005-cli-contract.md
docs/adr/ADR-006-phase1-acceptance.md

docs/specs/pdf-normalizer.md
```

## Goal

Implement the smallest complete Phase 1 PDF Normalizer satisfying the approved specification.

Input:

```text
manual.pdf
```

Output:

```text
output/
├── manifest.json
├── pages/
└── metadata/
```

## Required Deliverables

1. Python project/package structure.
2. `silicon-manual render` CLI.
3. PyMuPDF-based PDF rendering.
4. 300 DPI RGB PNG output.
5. Page identity and metadata.
6. PDF page label preservation.
7. PDF rotation handling.
8. PDF-native coordinate transformation.
9. Manifest generation.
10. Per-page metadata generation.
11. Source SHA-256.
12. Automated tests.
13. Minimal documentation required to install and run the tool.

## Fixed Requirements

```text
Language        = Python
PDF engine      = PyMuPDF
DPI             = 300
Color           = RGB
Format          = PNG

Page ID         = page-000001 style
Page numbering  = 1-based
PDF index       = 0-based

Canonical PDF coordinate:
    origin = bottom-left
    x      = right
    y      = up
    unit   = pt

Image coordinate:
    origin = top-left
    x      = right
    y      = down
    unit   = px
```

## CLI

Required:

```bash
silicon-manual render manual.pdf
```

Output selection SHOULD support:

```bash
silicon-manual render manual.pdf --output ./output
```

Do not add unrelated commands.

## Required Rotation Support

```text
0°
90°
180°
270°
```

## Required Tests

Tests must cover at least:

```text
multi-page rendering
page identity
page index vs page number
PDF page labels
0° rotation
90° rotation
180° rotation
270° rotation
PDF → image coordinate transformation
manifest completeness
metadata completeness
SHA-256
successful CLI execution
invalid input failure
```

## Test Fixtures

Prefer synthetic/minimal PDFs.

Do not make the test suite depend on proprietary semiconductor manuals.

## Non-Goals

Do NOT implement:

- OCR
- Vision AI
- SMIR/RI
- translation
- RAG
- code generation
- IDE integration
- GUI
- adaptive DPI
- additional semantic extraction

## Architecture Boundary

If a detail is not explicitly specified:

### Low-impact implementation detail

Use the simplest conventional implementation and document the choice.

### High-impact architectural ambiguity

If the decision changes any of the following:

- page identity;
- artifact semantics;
- coordinate semantics;
- rendering contract;
- public CLI behavior;
- evidence model;

DO NOT silently decide.

Instead report:

```text
Ambiguity
Options
Recommended option
Impact
```

and stop for Architect/Owner review.

## Completion Report

When implementation is finished, report:

1. Files created/modified.
2. Implementation summary.
3. Test command.
4. Test result.
5. Example CLI invocation.
6. Example generated artifact tree.
7. Assumptions made.
8. Unresolved questions.
9. Any deviation from the SPEC.

Do not claim completion if acceptance criteria are not satisfied.