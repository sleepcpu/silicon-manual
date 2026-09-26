# ADR-002: Rendering Standard

**Status:** Accepted  
**Scope:** Phase 1 PDF Page Normalization

## Decision

The canonical Phase 1 rendering configuration is:

```text
DPI         = 300
Color       = RGB
Format      = PNG
```

One rendered image is generated for each PDF page.

The rendering configuration MUST be recorded in the manifest and page metadata.

## Rationale

Phase 1 prioritizes deterministic, sufficiently high-quality visual input over maximum resolution.

300 DPI is the initial baseline for:

- body text
- tables
- register bitmaps
- footnotes
- diagrams
- timing/state figures

Future changes to the rendering profile require an explicit architecture decision.

## Non-Goals

Phase 1 does not implement:

- adaptive DPI
- multi-resolution images
- thumbnails
- OCR
- AI-driven image optimization
