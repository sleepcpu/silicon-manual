
# ADR-003: Canonical Coordinate System

**Status:** Accepted  
**Scope:** Phase 1 PDF Page Normalization

## Decision

The canonical evidence coordinate system is the native PDF page coordinate system.

### PDF coordinates

```text
origin = bottom-left
x      = right
y      = up
unit   = pt
```

where:

```text
1 pt = 1/72 inch
```

### Image coordinates

```text
origin = top-left
x      = right
y      = down
unit   = px
```

The implementation MUST provide a deterministic transform between the two systems, including page rotation.

`region_pdf` is the canonical evidence representation.

`region_image` is a derived representation.

## Rationale

Image coordinates depend on:

- DPI
- renderer
- image dimensions
- rendering implementation

PDF-native coordinates are therefore more suitable as long-term evidence coordinates.

## Consequences

Evidence should conceptually follow:

```text
PDF Region
    ↓
Rendering Transform
    ↓
Image Region
```

rather than:

```text
Image Region
    ↓
permanently stored as canonical evidence
```
