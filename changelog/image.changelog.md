# 📜 Changelog - Image Generation Design Document

> **Parent Document:** [image.design.md](../design/image.design.md)
> **Current Version:** 1.9

---

## Version 1.9: Template System → Multi-Tool Workflow

**Date:** 2026-02-04
**Session:** (current session)

### Changes
- **Removed** Section 11.1 Template System (Planned)
  - `--template closing-frame` flag is NOT implemented
  - `--avatars`, `--names` flags are NOT implemented
- **Added** Section 11.1 Multi-Tool Workflows (via Skill Orchestration)
  - Design decision: Skill manages multi-tool workflows instead of CLI flags
  - image_gen.py `--image` flag already supports reference images
  - No code changes needed

### Design Decision
Instead of implementing `--template` CLI flag, use Smart Skill to orchestrate:
1. image_gen.py (generate closing frame)
2. video_gen.py (extend video with closing frame)

This approach:
- Keeps both tools simple and single-purpose
- Leverages existing capabilities (`--image` flag)
- Avoids code duplication and coupling

### Summary
Template System removed - Multi-Tool Workflow handled by Smart Skill orchestration.

---

## Version 1.8: Smart Skill System v2.1 (English + Enhanced Discovery)

**Date:** 2026-02-03
**Session:** (current session)

### Changes
- Complete English conversion of SKILL.md
- Added Question 4: Aspect Ratio (1:1, 16:9, 9:16, 4:3/3:4)
- Added Question 5: Quality/Size (1K, 2K, 4K)
- Added Reference Image Flow (search, select, verify)
- Added Document Context Flow (for Diagram/Illustration)
- All examples and documentation now in English only

### Files Modified
- `.claude/skills/generate-image/SKILL.md` - Rewritten in English (484 lines)
- `design/skill.design.md` - Updated to v2.1

### Summary
Smart Skill v2.1 with English-only content, enhanced aspect ratio and quality settings discovery.

---

## Version 1.7: Smart Skill System v2.0

**Date:** 2026-02-03
**Session:** (current session)

### Changes
- Complete redesign of `/generate-image` skill as Smart Skill
- Added 6-Phase Standard Workflow
- Added Requirement Discovery System (3 question sets)
- Added Prompt Engineering templates and enhancement rules
- Added complete CLI arguments reference
- Added aspect ratio and image size reference tables
- Added example interactions

### Files Modified
- `.claude/skills/generate-image/SKILL.md` - Completely rewritten (397 lines)
- `design/skill.design.md` - Updated to v2.0

### Summary
Smart Skill implementation with intelligent prompt assistance for image generation.

---

## Version 1.6: Updated for Skill Support

**Date:** 2026-02-03
**Session:** (current session)

### Changes
- Updated version to 1.6 for Claude Code Skill integration
- Added cross-reference to skill.design.md

### Summary
Version alignment with skill system implementation.

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
| 1.8 | 2026-02-03 | **[Smart Skill v2.1 (English)](#version-18-smart-skill-system-v21-english--enhanced-discovery)** | (current) |
| | | Summary: English conversion + Aspect Ratio + Quality/Size discovery | |
| 1.7 | 2026-02-03 | **[Smart Skill System v2.0](#version-17-smart-skill-system-v20)** | (current) |
| | | Summary: 6-Phase workflow, Requirement Discovery, Prompt Engineering | |
| 1.6 | 2026-02-03 | **[Updated for Skill Support](#version-16-updated-for-skill-support)** | (current) |
| | | Summary: Version alignment with skill system implementation | |
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
