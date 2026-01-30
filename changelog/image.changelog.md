# 📜 Changelog - Image Generation Design Document

> **Parent Document:** [image.design.md](../design/image.design.md)
> **Current Version:** 1.5

---

## Version 1.5: Added Planned Features Section

**Date:** 2026-01-22
**Session:** LEGACY-000

### Changes
- Added Section 11 "Planned Features"
- Added 11.1 Template System (Planned) - `--template closing-frame` feature
- This replaces standalone `create_last_frame.py` (deleted - was redundant)
- Cross-references with video.design.md Section 5.16.3

### Summary
Added planned template system for video closing frames, replacing redundant standalone tool.

---

## Version 1.4: Imagen Status & Config Alignment

**Date:** 2026-01-21
**Session:** deef7b2b-ff58-4e70-9e3c-257ea53cc609

### Changes
- Marked Imagen 3/4 as "🔲 Not Implemented" (requires Vertex AI endpoint, not yet in image_gen.py)
- Removed deprecated `gemini-2.0-flash` references from CLI examples
- Removed "Free Tier (Gemini 2.0)" row from features table
- Fixed `config.example.json`: model changed from `imagen-3.0-generate-002` to `gemini-3-pro-image-preview`
- Fixed `config.example.json`: resolution format changed from `1024x1024` to `1K`

### Summary
Aligned documentation and config with actual implementation status.

---

## Version 1.3: Model Update & Timeout Improvement

**Date:** 2026-01-21
**Session:** deef7b2b-ff58-4e70-9e3c-257ea53cc609

### Changes
- Removed deprecated `gemini-2.5-flash-image` model (error: unknown provider)
- Set `gemini-3-pro-image-preview` as the only default model
- Increased API request timeout from 120s to 300s (5 minutes)
- Increased URL download timeout from 30s to 60s
- Updated pricing table to remove deprecated models
- Removed free tier references (no free tier currently available)

### Summary
Model consolidation and timeout improvements for better reliability.

---

## Version 1.2: API Key Compatibility

**Date:** 2026-01-19
**Session:** LEGACY-001

### Changes
- Added Vertex AI API Key (`AQ...`) and OAuth2 Token to auth comparison table
- Added note clarifying `AQ...` keys work with `predictLongRunning` only
- Added link to check.design.md

### Summary
Clarified API key compatibility for different operations.

---

## Version 1.1: Cost Tracking

**Date:** 2026-01-18
**Session:** LEGACY-002

### Changes
- Added Section 10 Cost Tracking & Display

### Summary
Added cost estimation and display functionality.

---

## Version 1.0: Initial Version

**Date:** 2026-01-18
**Session:** LEGACY-003

### Changes
- Initial version - extracted from consolidated design

### Summary
Initial extraction of image generation design.

---

## Version History (Unified)

| Version | Date | Changes | Session ID |
|---------|------|---------|------------|
| 1.5 | 2026-01-22 | **[Added Planned Features Section](#version-15-added-planned-features-section)** | LEGACY-000 |
| | | Summary: Template system for closing frames, replaces create_last_frame.py | |
| 1.4 | 2026-01-21 | **[Imagen Status & Config Alignment](#version-14-imagen-status--config-alignment)** | deef7b2b... |
| | | Summary: Aligned documentation and config with implementation | |
| 1.3 | 2026-01-21 | **[Model Update & Timeout Improvement](#version-13-model-update--timeout-improvement)** | deef7b2b... |
| | | Summary: Model consolidation and timeout improvements | |
| 1.2 | 2026-01-19 | **[API Key Compatibility](#version-12-api-key-compatibility)** | LEGACY-001 |
| | | Summary: Clarified API key compatibility | |
| 1.1 | 2026-01-18 | **[Cost Tracking](#version-11-cost-tracking)** | LEGACY-002 |
| | | Summary: Added cost estimation functionality | |
| 1.0 | 2026-01-18 | **[Initial Version](#version-10-initial-version)** | LEGACY-003 |
| | | Summary: Initial extraction from consolidated design | |
