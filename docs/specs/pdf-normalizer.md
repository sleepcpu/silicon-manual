# PDF Normalizer — Phase 1 Implementation Specification

## 1. Purpose

Implement the first executable component of Silicon Manual:

```text
PDF
 ↓
Standardized Page Artifacts
```

The component prepares deterministic, traceable visual page inputs for downstream AI extraction.

## 2. Scope

### In Scope

- PDF input
- multi-page rendering
- page identity
- PDF page labels
- page dimensions
- page rotation
- fixed rendering configuration
- PDF-native canonical coordinates
- PDF/image coordinate transformation
- manifest generation
- per-page metadata
- source SHA-256
- CLI `silicon-manual render`

### Out of Scope

- OCR
- AI inference
- SMIR/RI extraction
- translation
- RAG
- code generation
- GUI
- adaptive rendering
- semantic document understanding

## 3. Canonical Rendering Configuration

```yaml
dpi: 300
color_space: RGB
format: PNG
```

## 4. Coordinate Contract

PDF:

```yaml
origin: bottom-left
axis_x: right
axis_y: up
unit: pt
```

Image:

```yaml
origin: top-left
axis_x: right
axis_y: down
unit: px
```

The PDF coordinate system is canonical.

## 5. Output Layout

```text
output/
├── manifest.json
├── pages/
│   ├── page-000001.png
│   └── ...
└── metadata/
    ├── page-000001.json
    └── ...
```

## 6. Page Metadata

A page metadata file MUST contain:

```json
{
  "document_id": "string",
  "page_id": "page-000001",

  "pdf": {
    "page_index": 0,
    "page_number": 1,
    "page_label": null,
    "width_pt": 0.0,
    "height_pt": 0.0,
    "rotation": 0
  },

  "render": {
    "dpi": 300,
    "format": "png",
    "color_space": "RGB"
  },

  "image": {
    "width": 0,
    "height": 0
  },

  "coordinates": {
    "pdf": {
      "origin": "bottom-left",
      "axis_x": "right",
      "axis_y": "up",
      "unit": "pt"
    },
    "image": {
      "origin": "top-left",
      "axis_x": "right",
      "axis_y": "down",
      "unit": "px"
    }
  }
}
```

Additional implementation metadata may be added when useful.

Existing field semantics MUST NOT be changed.

## 7. Manifest

`manifest.json` MUST identify:

- artifact format/schema version;
- source PDF SHA-256;
- document ID;
- rendering configuration;
- page count;
- page entries;
- relative paths to page image and metadata.

The manifest must allow a consumer to resolve:

```text
document
  ↓
page
  ↓
image
  ↓
metadata
```

## 8. Document Identity

The source PDF content hash is the primary provenance identity.

Filename alone MUST NOT be treated as sufficient source identity.

The implementation may choose the exact public `document_id` derivation, provided it is deterministic and documented.

## 9. Rotation

Phase 1 MUST support:

```text
0°
90°
180°
270°
```

The implementation must preserve original PDF rotation metadata while rendering the page in its effective visual orientation.

Coordinate conversion must remain correct under all supported rotations.

## 10. Coordinate Transformation

The implementation MUST provide a deterministic transformation from:

```text
PDF native rectangle
```

to:

```text
rendered image rectangle
```

The transformation must account for:

- page dimensions;
- 300 DPI;
- PDF/image origin difference;
- page rotation.

The implementation must test the transformation rather than merely storing coordinate-system metadata.

## 11. Error Handling

The CLI MUST fail with a non-zero exit code for:

- missing input;
- unreadable input;
- invalid/corrupt PDF;
- unwritable output.

Individual page rendering failures MUST NOT be silently ignored.

## 12. Determinism

For identical:

```text
PDF bytes
+
rendering configuration
```

repeated runs MUST produce semantically equivalent artifacts.

Artifact identity MUST NOT depend on:

- machine-specific absolute paths;
- current timestamps;
- random identifiers.

## 13. Testing

Automated tests MUST cover:

- multi-page PDF;
- page index/number separation;
- PDF page labels;
- 0° rotation;
- 90° rotation;
- 180° rotation;
- 270° rotation;
- coordinate conversion;
- manifest completeness;
- metadata completeness;
- SHA-256;
- successful CLI invocation;
- invalid input handling.

Prefer small synthetic test PDFs rather than copyrighted vendor manuals.

## 14. Implementation Constraints

Phase 1 implementation language:

```text
Python
```

Preferred PDF engine:

```text
PyMuPDF
```

Pydantic may be used for structured models.

Keep the implementation minimal.

Do not introduce Phase 2/3 abstractions unless required by Phase 1.

## 15. Debug vs Formal AI Artifacts

Formal AI input images MUST NOT contain:

- watermarks;
- coordinate grids;
- debug bounding boxes;
- artificial page numbers;
- debug labels.

Debug/inspection overlays may exist as separate artifacts, but must never replace or modify the canonical AI input images.

## 16. Acceptance

Acceptance is defined by:

```text
docs/adr/ADR-006-phase1-acceptance.md
```

No Phase 2 functionality is required.