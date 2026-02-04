# 📜 Changelog - Smart Skill Design Document

> **Parent Document:** [skill.design.md](../design/skill.design.md)
> **Current Version:** 3.2.1

---

## Version 3.2.1: gsutil Documentation

**Date:** 2026-02-04
**Session:** (current session)

### Changes
- Added Section 15.10: gsutil Documentation to skill.design.md
- Clarified gcloud vs gsutil requirements:
  - `gcloud` = REQUIRED for Vertex AI authentication
  - `gsutil` = OPTIONAL for manual GCS management
  - `--storage-uri` = Works via REST API directly (no gsutil needed)
- Updated SKILL.md in 3 locations:
  - Config Show Output: Added gsutil check
  - `/generative config gcloud`: Added gsutil clarification
  - `/generative help video`: Added GCS OUTPUT section

### Key Clarification
```
gcloud SDK includes:
  - gcloud  → Authentication (REQUIRED for Vertex AI)
  - gsutil  → GCS CLI (OPTIONAL)

--storage-uri works via REST API directly.
No gsutil installation required for video generation.
```

### Files Modified
- `design/skill.design.md` → v3.2.1 (Added Section 15.10)
- `changelog/skill.changelog.md` → v3.2.1 (this entry)
- `.claude/skills/generative/SKILL.md` → Updated (~1,520 lines)

### Summary
gsutil documentation v3.2.1 - clarified that gsutil is OPTIONAL, --storage-uri works via REST API.

---

## Version 3.2: Enhanced Config Mode

**Date:** 2026-02-04
**Session:** (current session)

### Changes
- **IMPLEMENTED** Enhanced Config Mode v3.2
- Added Section 15: Enhanced Config Mode in skill.design.md
- Expanded `/generative config` to full Setup Wizard
- Added dual API support (Gemini API + Vertex AI)
- Added config sub-commands: setup, show, gemini, vertex, gcloud, reset
- Added Welcome Start auto-detect for first-time users
- Added step-by-step guides with real console URLs
- Added ~200 lines to SKILL.md

### New Commands
```bash
/generative config           # Show current config + options menu
/generative config setup     # Start guided setup wizard
/generative config show      # Show all current settings
/generative config gemini    # Setup Gemini API Key only
/generative config vertex    # Setup Vertex AI only
/generative config gcloud    # Setup gcloud CLI
/generative config reset     # Reset config to defaults
```

### API Configuration
- **Gemini API**: `GOOGLE_API_KEY` environment variable
  - URL: https://console.cloud.google.com/apis/credentials?project=gen-lang-client-0344941103
- **Vertex AI**: gcloud auth or `GOOGLE_APPLICATION_CREDENTIALS`
  - URL: https://console.cloud.google.com/vertex-ai/studio/settings/api-keys?project=gen-lang-client-0344941103

### Setup Wizard Features
- Step-by-step guided setup with skip option
- Real console URLs for getting API keys
- gcloud CLI installation guide
- Auto-detect missing config on first use
- Config file: `./config.json`

### Files Modified
- `design/skill.design.md` → v3.2 (Added Section 15)
- `changelog/skill.changelog.md` → v3.2 (this entry)
- `.claude/skills/generative/SKILL.md` → Updated (~1,300 lines)
- `TODO.md` → Updated

### Summary
Enhanced Config Mode v3.2 - full Setup Wizard with dual API support, step-by-step guides, and auto-detect.

---

## Version 3.1: Enhanced Help System

**Date:** 2026-02-04
**Session:** (current session)

### Changes
- **IMPLEMENTED** Enhanced Help System v3.1
- Added Section 14: Enhanced Help System in skill.design.md
- Updated Mode Detection to include `help` mode
- Added 5 help sub-topics: main, image, video, examples, modes
- Added ~290 lines to SKILL.md

### New Commands
```bash
/generative help              # Help overview + available topics
/generative help image        # Detailed image generation guide
/generative help video        # Detailed video generation guide
/generative help examples     # 10+ real usage examples
/generative help modes        # 8 video generation modes explained
```

### Help Content
- **help** (main): Overview, topics, shortcuts (~30 lines)
- **help image**: CLI args, aspect ratios, sizes, models (~50 lines)
- **help video**: CLI args, presets, audio tips, models (~60 lines)
- **help examples**: 10+ real examples for image/video/workflow (~80 lines)
- **help modes**: All 8 video modes with syntax and examples (~70 lines)

### Files Modified
- `design/skill.design.md` → v3.1 (Added Section 14)
- `changelog/skill.changelog.md` → v3.1 (this entry)
- `.claude/skills/generative/SKILL.md` → Updated (~1,100 lines)
- `TODO.md` → Updated

### Summary
Enhanced Help System v3.1 - adds `/generative help [topic]` with 5 detailed help topics.

---

## Version 3.0: Unified Generative Skill Implementation

**Date:** 2026-02-04
**Session:** (current session)

### Changes
- **IMPLEMENTED** Unified Generative Skill v3.0
- Created `.claude/skills/generative/SKILL.md` (798 lines merged from video + image)
- Deleted `.claude/skills/generate-video/` folder
- Deleted `.claude/skills/generate-image/` folder
- Updated Section 13 status from "Planned" to "Implemented"
- Updated Migration Plan status (all steps complete)

### Implementation Details
- Single SKILL.md with 4 modes: `image`, `video`, `info`, `config`
- Mode detection via `$ARGUMENTS[0]`
- Integrated workflows: video mode can auto-call image generation
- Trigger keywords for auto image: "closing credits", "with avatar", "thumbnail"
- Full CLI reference for both image_gen.py and video_gen.py

### Files Modified
- `design/skill.design.md` → v3.0 (Section 13 marked Implemented)
- `changelog/skill.changelog.md` → v3.0 (this entry)
- `.claude/skills/generative/SKILL.md` → Created (798 lines)
- `.claude/skills/generate-video/` → Deleted
- `.claude/skills/generate-image/` → Deleted

### Summary
Unified Generative Skill v3.0 implemented - consolidates video + image into single skill with 4 modes.

---

## Version 2.5: Unified Generative Skill Design (Planned v3.0)

**Date:** 2026-02-04
**Session:** (current session)

### Changes
- Added Section 13: Unified Generative Skill (Planned v3.0)
- Designed consolidated `/generative` skill with 4 modes:
  - `/generative image` - Image generation
  - `/generative video` - Video generation (with integrated image workflows)
  - `/generative info` - Show capabilities and help
  - `/generative config` - Show configuration
- Documented command structure using `$ARGUMENTS[0]` for mode detection
- Added integrated workflow design (video auto-detects when image is needed)
- Added migration plan (5 steps)
- Added benefits comparison table

### Design Decisions
- **Consolidate** separate `generate-video` and `generate-image` into single `generative` skill
- **Video mode** can internally call image generation when needed (closing credits, thumbnails)
- **No separate workflow command** - video handles everything
- **Trigger keywords** for auto image integration: "closing credits", "with avatar", etc.

### Migration Plan (Pending Implementation)
1. ✅ Design unified skill structure (this version)
2. 🔲 Create `.claude/skills/generative/SKILL.md`
3. 🔲 Test all 4 modes
4. 🔲 Delete old skill folders
5. 🔲 Update documentation

### Summary
Unified Generative Skill v3.0 designed - consolidates video + image into single skill with 4 modes.

---

## Version 2.4: Multi-Tool Workflow Orchestration

**Date:** 2026-02-04
**Session:** (current session)

### Changes
- Added Section 12: Multi-Tool Workflow Orchestration
- Design decision: Skills orchestrate multi-tool sequences (not CLI flags)
- Documented 3 supported workflows: Closing Credits, Video + Thumbnail, Style Transfer
- Added workflow execution pattern with user confirmation steps
- Updated generate-video/SKILL.md with Multi-Tool Workflows section

### Design Decisions
- **Removed** `--template closing-frame` from planned features
- **Instead** use Skill orchestration for image_gen → video_gen workflows
- No code changes to image_gen.py or video_gen.py needed
- Leverages existing `--image` flag in image_gen.py

### Related Updates
- image.design.md v1.9: Section 11.1 changed from Template System to Multi-Tool Workflows
- generate-video/SKILL.md: Added Multi-Tool Workflows section

### Summary
Multi-Tool Workflow Orchestration via Skills - replaces planned Template System CLI flags.

---

## Version 2.3: Audio Prompting Support

**Date:** 2026-02-03
**Session:** (current session)

### Changes
- Added Section 3.3: Audio Prompting (Veo 3.x Only)
- Added Audio element to Prompt Enhancement Rules table
- Updated Prompt Structure Template to include [Audio]
- Added Audio Type control techniques (Dialogue, Sound Effects, Ambient, Music, Silence)
- Added Audio-Enhanced Prompt Examples
- Added Tips for Better Audio
- Updated SKILL.md with Audio Prompting section
- Updated Cost Reference table with Audio column
- Marked Audio Support as ✅ Implemented in Gap Analysis

### Summary
Audio prompting support via prompt engineering (not CLI flag - audio is model-level feature).

---

## Version 2.2: Gap Analysis & Future Features

**Date:** 2026-02-03
**Session:** (current session)

### Changes
- Added Section 11: Gap Analysis & Future Features
- Documented 40+ implemented features across 7 categories
- Added Gap Analysis by Video Mode (8 modes coverage)
- Added Missing Features Analysis with 3 priority levels
- Added Feature Decision Matrix (Effort/Value/Frequency)
- Added Recommended Next Steps
- Added Implementation Notes for top features

### Summary
Comprehensive gap analysis documenting what's implemented and what's missing.

---

## Version 2.1: Smart Skill System v2.1 (English + Enhanced Discovery)

**Date:** 2026-02-03
**Session:** (current session)

### Changes
- Complete English conversion of SKILL.md files
- Added Question 5: Quality Settings (Video) with Custom Duration/Resolution
- Added Question 4: Aspect Ratio (Image)
- Added Question 5: Quality/Size (Image)
- Added Image Attachment Flow (search, select, verify)
- Added Video Source Flow (for extension mode)
- Added Reference Image Flow (Image)
- Added Document Context Flow (for Diagram/Illustration)

### Summary
Smart Skill v2.1 with English-only content, enhanced discovery questions.

---

## Version 2.0: Smart Skill System Full Implementation

**Date:** 2026-02-03
**Session:** (current session)

### Changes
- Complete 6-Phase Standard Workflow
- 4 Discovery Questions for Video
- 3 Discovery Questions for Image
- Prompt Engineering templates and enhancement rules
- Pre-flight checks (API key, config, resources)
- Preview & Confirm with cost estimation
- Execute & Report with error handling
- Complete CLI arguments reference
- 8 Video Generation Modes documentation

### Summary
Full Smart Skill implementation with all phases completed.

---

## Version 1.0: Initial Design

**Date:** 2026-02-03
**Session:** (current session)

### Changes
- Initial design document draft
- Problem statement and vision
- Skill architecture layers
- Basic workflow outline

### Summary
Initial draft of Smart Skill design.

---

## Version History (Unified)

| Version | Date | Changes | Session ID |
|---------|------|---------|------------|
| 3.2.1 | 2026-02-04 | **[gsutil Documentation](#version-321-gsutil-documentation)** | (current) |
| | | Summary: Clarified gsutil is OPTIONAL, --storage-uri via REST API | |
| 3.2 | 2026-02-04 | **[Enhanced Config Mode](#version-32-enhanced-config-mode)** | (current) |
| | | Summary: Full Setup Wizard with dual API support | |
| 3.1 | 2026-02-04 | **[Enhanced Help System](#version-31-enhanced-help-system)** | (current) |
| | | Summary: 5 detailed help topics for /generative help | |
| 3.0 | 2026-02-04 | **[Unified Generative Skill](#version-30-unified-generative-skill-implementation)** | (current) |
| | | Summary: Consolidated video + image into single skill | |
| 2.5 | 2026-02-04 | **[Unified Skill Design](#version-25-unified-generative-skill-design-planned-v30)** | (current) |
| | | Summary: Designed unified skill with 4 modes | |
| 2.4 | 2026-02-04 | **[Multi-Tool Workflow](#version-24-multi-tool-workflow-orchestration)** | (current) |
| | | Summary: Skill orchestration for multi-tool sequences | |
| 2.3 | 2026-02-03 | **[Audio Prompting Support](#version-23-audio-prompting-support)** | (current) |
| | | Summary: Audio via prompt engineering for Veo 3.x | |
| 2.2 | 2026-02-03 | **[Gap Analysis & Future Features](#version-22-gap-analysis--future-features)** | (current) |
| | | Summary: Comprehensive gap analysis with missing features | |
| 2.1 | 2026-02-03 | **[Smart Skill v2.1 (English)](#version-21-smart-skill-system-v21-english--enhanced-discovery)** | (current) |
| | | Summary: English conversion + Enhanced discovery questions | |
| 2.0 | 2026-02-03 | **[Full Implementation](#version-20-smart-skill-system-full-implementation)** | (current) |
| | | Summary: Complete Smart Skill with all 6 phases | |
| 1.0 | 2026-02-03 | **[Initial Design](#version-10-initial-design)** | (current) |
| | | Summary: Initial draft of Smart Skill design | |
