# 📜 Changelog - Master Design Document

> **Parent Document:** [design.md](../design/design.md)
> **Current Version:** 4.8

---

## Version History

### Version 4.8 (2026-02-10)

**Global Installation Refactor - Single Install Architecture:**

**Goal:** เปลี่ยนจาก per-project copy (ต้อง copy 5 ไฟล์ .py ไปทุก project) เป็น single global install (ทุกอย่างอยู่ที่ `~/.claude/skills/generative/`)

**Architecture Change:**
| Before (per-project) | After (global install) |
|------|------|
| Scripts copy ไปทุก project | Scripts อยู่ที่ `~/.claude/skills/generative/` ที่เดียว |
| Output ที่ `Path(__file__).parent` | Output ที่ `Path.cwd()` (user's CWD) |
| `generated_images/`, `generated_videos/` | `+generated_images/`, `+generated_videos/` |
| Config search: CWD + Home | Config search: CWD + Skill dir + Home |
| SKILL.md ใช้ relative path | SKILL.md ใช้ absolute path |

**Target Installed Structure:**
```
~/.claude/
├── agents/
│   └── generative-media-navigator.md
└── skills/
    └── generative/
        ├── SKILL.md
        ├── image_gen.py
        ├── video_gen.py
        ├── config.py
        ├── video_utils.py
        ├── check_api.py
        ├── config.example.json
        └── config.json

{user's CWD}/
├── +generated_images/    ← Output สร้างอัตโนมัติ
└── +generated_videos/    ← Output สร้างอัตโนมัติ
```

**Files to Modify:**

| # | File | Change | Reason |
|---|------|--------|--------|
| 1 | SKILL.md:6 | `python image_gen.py` → `python ~/.claude/skills/generative/image_gen.py` | Script อยู่กับ skill |
| 2 | image_gen.py:274-276 | `Path(__file__).parent / "generated_images"` → `Path.cwd() / "+generated_images"` | Output ที่ CWD |
| 3 | video_gen.py:270 | `Path(__file__).parent / "generated_videos"` → `Path.cwd() / "+generated_videos"` | Output ที่ CWD |
| 4 | config.py:17-20 | เพิ่ม `Path(__file__).parent / "config.json"` ลำดับที่ 2 | Config หาเจอที่ skill dir |
| 5 | config.example.json:43-44 | `generated_*` → `+generated_*` | ให้ตรงกับ code |
| 6 | SKILL.md:145,378,412,435 | `generated_images/` → `+generated_images/` ใน ls commands | ให้ตรงกับ output dir |

**`+` Prefix Rationale:**
- แยกความแตกต่างระหว่าง user's files กับ generated output
- ✅ Compatible: Linux, macOS, Git, Python, Bash
- ⚠️ Windows: ใช้ได้แต่บาง tools อาจมีปัญหา

**Deferred (ไม่แก้ในรอบนี้):**
- design/*.md, wiki/*.md, pages/*.md → อัพเดท documentation ภายหลัง
- README.md installation section → อัพเดทหลังแก้ code เสร็จ

---

### Version 4.7 (2026-02-09)

**Testing Phases Restructure:**

**Major Changes to testing.design.md (v1.1 → v1.2):**
- Restructured entire testing into 4 distinct phases
- Phase dependencies enforce correct testing order
- Cannot test skill/agent without installation

**Testing Phases:**
| Phase | Name | Tests | Prerequisites |
|-------|------|-------|---------------|
| 1 | Environment & Scripts | 30 | None - ทำได้เลย |
| 2 | Installation | 6 | Phase 1 ✅ |
| 3 | Skill & Agent | 17 | Phase 2 ✅ |
| 4 | Integration | 4 | Phase 3 ✅ |

**New Test Category:**
- Added ENV (Environment) with 4 tests
- Total tests: 57 (was 48)

**Rationale:**
- Direct script testing (video_gen.py, image_gen.py) can run without installation
- Skill/Agent tests REQUIRE installation first
- Clear phase dependencies prevent testing errors

**Files Modified:**
- `design/testing.design.md` - Updated to v1.2
- `changelog/testing.changelog.md` - Added v1.2 entry
- `changelog/changelog.md` - This file (v4.7)

---

### Version 4.6 (2026-02-09)

**Testing Design Enhancement - Usage Pattern Recording:**

**Updates to testing.design.md (v1.0 → v1.1):**
- Added Section 11: Usage Pattern Recording
- Recording Goals: Command examples, error patterns, best practices, edge cases, sample outputs
- Usage Pattern Template for standardized documentation
- 6 Pattern Categories: VGP, IGP, SUP, AIP, CFP, TRP
- Recording Workflow diagram
- Usage Pattern Log table for tracking
- Sample Patterns priority list
- Documentation Transfer Checklist

**Purpose:**
- บันทึกรูปแบบการใช้งานจริงระหว่างการทดสอบ
- นำข้อมูลไปเสริมใน pages/docs ในอนาคต
- เก็บ command examples ที่ใช้ได้จริง
- บันทึก error patterns และวิธีแก้ไข

**Files Modified:**
- `design/testing.design.md` - Updated to v1.1
- `changelog/testing.changelog.md` - Added v1.1 entry
- `changelog/changelog.md` - This file (v4.6)

---

### Version 4.5 (2026-02-08)

**Phase 7: Checkpoint Testing Design:**

**New Documents:**
- Created `design/testing.design.md` v1.0 - Comprehensive testing design document
- Created `changelog/testing.changelog.md` - Testing changelog

**Testing Framework:**
- 48 test cases across 7 categories (INS, VID, IMG, SKL, AGT, CFG, INT)
- Extensible design for adding new tests
- Installation tests for Claude Code deployment
- Prerequisites checklist
- Sign-off criteria (MVP and Full Release)
- Issue tracking template

**Test Categories:**
| Category | Tests | Description |
|----------|-------|-------------|
| INS | 10 | Installation & Deployment |
| VID | 15 | Video Generation |
| IMG | 7 | Image Generation |
| SKL | 11 | /generative Skill |
| AGT | 6 | Agent Auto-Detection |
| CFG | 4 | Configuration System |
| INT | 4 | Integration Tests |

**TODO Updates:**
- Updated TODO.md to v4.0
- Added Phase 7: Checkpoint Testing section
- Documented testing tasks and sign-off criteria

**Files Created/Modified:**
- `design/testing.design.md` - NEW
- `changelog/testing.changelog.md` - NEW
- `TODO.md` - Updated to v4.0
- `changelog/changelog.md` - This file (v4.5)

---

### Version 4.4 (2026-02-07)

**Documentation Structure Update:**

**Changes:**
- Updated Related Documents table in design.md:
  - Added `skill.design.md` - Smart Skill System
  - Added `pages.design.md` - GitHub Pages Design
  - Added `config.design.md` - Config System
  - Added `agent.design.md` - Agent Design
  - Fixed README.md path to `../README.md`
- Updated Quick Navigation:
  - Added Smart Skill System link
  - Added GitHub Pages link
  - Updated "8 Modes" → "9 Modes" (Remix Mode added)
- Clarified document locations:
  - `design/*.design.md` - All design documents
  - `changelog/*.changelog.md` - All changelogs
  - `pages/` - Git worktree for GitHub Pages (separate branch)

**Note:** `pages/` directory is a git worktree linked to `pages` branch. Design/changelog files for Pages belong in `design/pages.design.md` and `changelog/pages.changelog.md`, NOT inside `pages/`.

---

### Version 4.3 (2026-02-01)

**Phase 2.8.3: Smart Defaults Implementation - Complete:**

**Code Changes:**
- **video_gen.py:**
  - Added `load_source_metadata(video_path)` function
    - Extracts timestamp from video filename (video_YYYYMMDD_HHMMSS_*.mp4)
    - Finds matching metadata_YYYYMMDD_HHMMSS.json in same directory
    - Returns command_args section for inheritance
  - Updated `build_metadata()` to include new fields:
    - `project_id` - for project inheritance
    - `storage_uri` - for GCS bucket inheritance
    - `location` - for region inheritance
  - Updated `apply_config_defaults()` for Phase 2.8.3:
    - Loads source metadata when `--extend-video` is used
    - Applies full priority chain: CLI > Metadata > Config > Code Defaults
    - Shows inheritance messages for each value inherited from metadata
    - Inherits: model, storage_uri, project_id, resolution, aspect_ratio, location

**Inheritable Fields:**
| Field | Inheritance Behavior |
|-------|---------------------|
| model | Inherits if CLI at default |
| resolution | Inherits if CLI at default |
| aspect_ratio | Inherits if CLI at default |
| storage_uri | Inherits if not set via CLI |
| location | Inherits if CLI at default (and different from default) |
| project_id | Used in resolve_project() priority chain |

**User Experience:**
When extending a video:
```bash
# Original video was created with veo-3.1, 1080p, custom project
python video_gen.py "Continue the scene" --extend-video video_20260201_123456_0.mp4

# Output:
# ℹ Found source metadata for inheritance
# ℹ Inherited project from source metadata: my-project-1
# ℹ Inherited model from metadata: veo-3.1-generate-preview
# ℹ Inherited resolution from metadata: 1080p
```

**Files Modified:**
- `video_gen.py` - Added load_source_metadata(), updated build_metadata(), apply_config_defaults()
- `changelog/changelog.md` - This file (v4.3)

### Version 4.2 (2026-02-01)

**Phase 2.8.2: Config System Update - Implementation Complete:**

**Code Changes:**
- **config.py v1.1:**
  - Removed `active_project` auto-loading from `load()` method
  - Added `resolve_project(cli_project, metadata_project)` method
    - Returns `(project_id, source)` tuple
    - Sources: "cli", "metadata", "auto", "error", "none"
  - Added `is_multi_project()` helper method
  - Updated docstrings with Smart Defaults references

- **video_gen.py:**
  - Updated `apply_config_defaults()` for Phase 2.8.2
  - Now uses `config.resolve_project()` for multi-project handling
  - Error messages for: invalid project, multi-project without --project
  - Auto-select message when single project in config

- **image_gen.py:**
  - Updated `apply_config_defaults()` for Phase 2.8.2
  - Same multi-project logic as video_gen.py
  - Consistent error/info messages

**Behavior Changes:**
- Multi-project config without `--project` flag → Error with hint
- Single project config → Auto-select (no warning needed)
- Invalid `--project` value → Error with available projects list
- Prepared for Phase 2.8.3 metadata inheritance

**Files Modified:**
- `config.py` - v1.1 (removed active_project, added resolve_project)
- `video_gen.py` - Updated apply_config_defaults()
- `image_gen.py` - Updated apply_config_defaults()
- `design/config.design.md` - v1.4 (implementation status update)
- `changelog/changelog.md` - This file

### Version 4.1 (2026-01-31)

**Smart Defaults System v2.0 - Complete Design:**

**Design Updates:**
- Updated **Section 18: Smart Defaults System** to v2.0 in design.md
  - Extended Priority Chain: CLI > Metadata > config.json[--project] > Env > Code Defaults
  - Added `--project` integration with metadata inheritance
  - Defined inheritable fields with notes (model, storage_uri, project_id, resolution, aspect_ratio, location)
  - Added metadata schema enhancement (v2.25)
  - Added implementation functions with pseudocode
  - Added user experience examples
  - Added edge case handling
  - Added implementation phases with status tracking

**Config System Updates (v1.3):**
- Updated `design/config.design.md` to v1.3
  - Added Value Priority Chain with Metadata
  - Updated schema to v2.1 (removed active_project)
  - Added CLI --project flag behavior design
  - Added Smart Defaults integration section
  - Added implementation TODO list

**Metadata Schema Updates (v2.25):**
- Updated `design/video.design.md` Section 14.1
  - Added `project_id` field to `command_args`
  - Added `storage_uri` field to `command_args`
  - Added `location` field to `command_args`
  - Reference to Smart Defaults System

**TODO Updates:**
- Added Phase 2.8: Smart Defaults System with clear sub-phases
  - Phase 2.8.1: Metadata Schema Enhancement ✅ DESIGNED
  - Phase 2.8.2: Config System Update
  - Phase 2.8.3: Smart Defaults Implementation
  - Phase 2.8.4: User Experience

**Files Modified:**
- `design/design.md` - Section 18 v2.0
- `design/config.design.md` - v1.3
- `design/video.design.md` - Section 14.1 v2.25
- `TODO.md` - Phase 2.8 tasks
- `changelog/changelog.md` - This file

### Version 4.0 (2026-01-30)

**Smart Defaults System (Section 18) + Config System Updates:**

**New Features:**
- Added **Section 18: Smart Defaults System (Metadata-Driven)** to design.md
  - Concept: Extend video commands inherit settings from source video metadata
  - Priority Chain: CLI > Source Metadata > config.json > Code Defaults
  - Inheritable args: model, storage_uri, project, resolution, aspect_ratio
  - Metadata lookup via timestamp extraction from video filename
  - Edge case handling (metadata not found, external URLs, corrupted files)

**Config System Updates (v1.2):**
- Renamed `config_loader.py` → `config.py` (cleaner imports)
- Removed `active_project` from config schema (use `--project` flag instead)
- Created `design/config.design.md` v1.2
- Created `changelog/config.changelog.md`
- Updated `config.example.json`
- Updated imports in `video_gen.py` and `image_gen.py`

**Files Modified:**
- `design/design.md` - Added Section 18, Quick Navigation update
- `design/config.design.md` - New design document
- `changelog/config.changelog.md` - New changelog
- `config.py` - Renamed from config_loader.py
- `config.example.json` - Removed active_project
- `TODO.md` - Added Config v1.1/v1.2 tasks

### Version 3.9 (2026-01-27)

**GitHub Pages Modernization - Glassmorphism Design:**
- Complete redesign of GitHub Pages with modern 2026 design
- **Design Style:** Glassmorphism + Gradient + Bento Grid
- **Files Updated:**
  - `pages/docs/stylesheets/extra.css` - Complete rewrite with:
    - Glassmorphism cards (frosted glass, blur effects)
    - Gradient backgrounds (purple → cyan)
    - Neon accent colors (#a78bfa, #22d3ee, #f472b6)
    - Smooth hover animations with glow
    - Dark/Light mode optimized
    - CSS animations (fadeInUp, float, glow)
  - `pages/docs/index.md` - Complete rewrite with:
    - New hero section with gradient text
    - 3 CTA buttons (Get Started, GitHub, Documentation)
    - Bento grid for features and 8 video modes
    - Tabbed Quick Start section
    - GCS warning with danger admonition
    - Pricing and Project Status tables
  - `pages/mkdocs.yml` - Font changed to Plus Jakarta Sans
- **New Overrides Templates:**
  - `pages/overrides/main.html` - SEO meta, smooth scroll, fade animations
  - `pages/overrides/partials/footer.html` - Custom footer with Vertex AI branding
  - `pages/overrides/partials/header.html` - Gradient overlay for homepage
- **Typography:**
  - Plus Jakarta Sans (headings & body)
  - JetBrains Mono (code blocks)

### Version 3.8 (2026-01-25)

**GCS Bucket Naming Convention Standard:**
- Added **MANDATORY** GCS bucket naming pattern: `YOUR_PROJECT_ID-media-output`
- Documented root cause analysis for GCS permission issues
- Updated design.md Section: Bucket Naming Convention (Mandatory Standard)
- Updated TODO.md v1.8 with Phase 2.7 completion and GCS naming fix

**Root Cause Fix:**
| Issue | Previously Documented | Actual Root Cause |
|-------|----------------------|-------------------|
| gsutil permission denied | gsutil requires `storage.objects.list` | ❌ Wrong |
| | | GCS bucket name ไม่ตรง pattern `YOUR_PROJECT_ID-media-output` ✅ |

**Key Insight:**
- Vertex AI API Key automatically grants permissions ONLY to bucket named `YOUR_PROJECT_ID-media-output`
- Using different bucket names = permission denied (NOT a bug, designed security boundary)
- This explains why some GCS operations failed while others worked

### Version 3.7 (2026-01-23)

**MkDocs Material for GitHub Pages:**
- Created complete MkDocs Material documentation site
- **Configuration:**
  - Created `mkdocs.yml` with Material theme
  - Dark/Light mode toggle
  - Navigation tabs, sections, search
  - Code copy, syntax highlighting
  - Emoji support, admonitions
- **GitHub Actions:**
  - Created `.github/workflows/docs.yml`
  - Auto-deploy on push to main/master
  - Triggered by docs/, mkdocs.yml, or workflow changes
- **Pages Created (16 files):**
  - docs/index.md - Home page with card layout
  - docs/getting-started/installation.md
  - docs/getting-started/authentication.md - With tabbed content
  - docs/getting-started/quick-start.md
  - docs/video/overview.md - With admonitions
  - docs/video/modes.md - All 8 modes
  - docs/video/models.md - Model comparison
  - docs/video/reference-images.md
  - docs/video/extension.md
  - docs/image/index.md
  - docs/cli/video_gen.md
  - docs/cli/image_gen.md
  - docs/cli/check_api.md
  - docs/guides/gcs-storage.md
  - docs/guides/presets.md
  - docs/guides/troubleshooting.md
- **GitHub Pages URL:** https://darkwingtm.github.io/claude-code-media-generator/
- Updated TODO.md v1.7 with MkDocs completion

### Version 3.6 (2026-01-23)

**GitHub Wiki Documentation:**
- Created complete GitHub Wiki documentation (17 pages)
- **Pages Created:**
  - Home.md - Landing page with feature overview
  - _Sidebar.md / _Footer.md - Navigation components
  - Installation.md - Setup and dependencies
  - Authentication.md - API keys and OAuth2 guide
  - Quick-Start.md - First video/image tutorial
  - Video-Overview.md - Complete video guide
  - Generation-Modes.md - All 8 modes explained
  - Video-Models.md - Veo 2.0 vs 3.1 comparison
  - Reference-Images.md - Using reference images
  - Video-Extension.md - Extending videos (FPS requirements)
  - Image-Generation.md - Image generation guide
  - CLI-video_gen.md - video_gen.py reference
  - CLI-image_gen.md - image_gen.py reference
  - CLI-check_api.md - check_api.py reference
  - GCS-Storage.md - Google Cloud Storage setup
  - Presets.md - Quick/Quality/Extend/Budget
  - Troubleshooting.md - Common issues and solutions
  - Changelog.md - Version history
- Updated TODO.md v1.6 with wiki completion

### Version 3.5 (2026-01-19)

**Documentation Restructure:**
- Changelog separated to `changelog/changelog.master.md`
- Established changelog at end of document as standard

### Version 3.4 (2026-01-19)

**Google Cloud Storage Integration:**
- Added **Cloud Storage (GCS) Integration** section as central documentation
- Documented `storageUri` parameter for GCS output instead of base64
- Added gcloud CLI & gsutil installation instructions (Ubuntu, macOS, Windows)
- Added GCS bucket creation and permission requirements
- Added troubleshooting section for common GCS errors
- Updated Quick Navigation with Cloud Storage link
- Cross-referenced from video.design.md

### Version 3.3 (2026-01-19)

**Separation of Concerns:**
- Moved `check_api.py` documentation to separate file: **check.design.md**
- Added Quick Reference table showing API Key compatibility by tool
- Clarified that `AQ...` keys work for video/image gen but NOT List Models

### Version 3.2 (2026-01-19)

**API Key Verification & Model Listing:**
- Added **API Key Verification & Model Listing** section
- Documented **API Key Formats & Capabilities** table
- Added **Critical Discovery: Vertex AI API Keys Do NOT Work**
- Documented **List Models Endpoints** for Gemini API and Vertex AI
- Added **check_api.py Usage** documentation
- Added **Key Detection Logic** code example

### Version 3.0 (2026-01-18)

**Documentation Restructure:**
- Added **Documentation Structure** section with cross-references
- Added **Critical Discoveries** table (Image vs Video differences)
- Simplified Authentication to **3 methods** (removed Custom OAuth)
- Created separate detail documents:
  - `image.design.md` - Image generation details
  - `video.design.md` - Video generation details (8 modes)
- Merged and archived:
  - `DESIGN.md` → merged into design.md
  - `architecture.md` → merged into design.md
  - `AUTHENTICATION_GUIDE.md` → merged into design.md

**Key Insight Added:**
- cloudcode-pa.googleapis.com does NOT support `predictLongRunning`
- This is why Image (generateContent) works but Video fails via CLIProxyAPI

### Version 2.7 (2026-01-17)

**Major Addition: Complete Video Generation Modes (All Veo Features)**

Following official Google Vertex AI documentation review, this update adds comprehensive support for ALL Veo video generation features.

**8 Video Generation Modes (Complete Coverage):**
1. **Text-to-Video** - Generate video from text prompt only
2. **Image-to-Video** - Animate a single image with text guidance
3. **First & Last Frames** - Interpolate video between two keyframe images
4. **Video Extension** - Continue/extend an existing video
5. **Reference Asset** - Use up to 3 reference images for subject preservation
6. **Reference Style** - Apply style from 1 reference image
7. **Insert Objects** - Add objects to video using mask
8. **Remove Objects** - Remove objects from video using mask

**New CLI Parameters:**
- `--last-frame PATH` - Last frame image for interpolation mode
- `--reference-asset PATH [PATH ...]` - Asset reference images (up to 3)
- `--reference-style PATH` - Style reference image (1 only)
- `--video PATH` - Input video for editing operations
- `--mask PATH` - Mask image for insert/remove operations
- `--mask-mode MODE` - "insert" or "remove" (default: insert)
- `--resize-mode MODE` - "pad" or "crop" for image-to-video (Veo 3 only)

**New Documentation Sections:**
- Complete Video Generation Modes (All Veo Features) table
- Mode Detection Logic (Python function)
- API Request Format by Mode (8 JSON examples)
- Model Compatibility Matrix
- Input Constraints table
- Output Specifications table
- CLI Examples for All Modes

**Sources Verified:**
- https://docs.cloud.google.com/vertex-ai/generative-ai/docs/video/overview
- https://docs.cloud.google.com/vertex-ai/generative-ai/docs/video/generate-videos-from-first-and-last-frames
- https://docs.cloud.google.com/vertex-ai/generative-ai/docs/video/insert-objects-into-videos
- https://docs.cloud.google.com/vertex-ai/generative-ai/docs/video/remove-objects-from-videos
- https://docs.cloud.google.com/vertex-ai/generative-ai/docs/model-reference/veo-video-generation

### Version 2.6 (2026-01-17)

**Major Addition: Dual REST API Endpoint Support**

Following user inquiry about the two different REST endpoint formats, this update clarifies and documents support for both Google Gemini API and Vertex AI endpoints.

**Key Clarification:**
- **API Key**: Works ONLY with Gemini API (`generativelanguage.googleapis.com`)
- **OAuth Methods**: Work with BOTH endpoints

**New Sections Added:**
- Authentication Compatibility Matrix
- Endpoint Selection Logic (Python code)
- Endpoint Mapping by Auth Method table
- Polling Endpoint Differences (Gemini uses GET, Vertex AI uses POST)
- Authentication Methods Overview table
- Detailed Authentication Method Explanations (API Key, CLIProxyAPI, Service Account, ADC)
- Authentication Comparison Summary diagram
- Video Extension (Continue Video) Design section

### Version 2.5 (2026-01-17)

**Major Design Decision: CLIProxyAPI OAuth Token as Primary Authentication**

Following research into CLIProxyAPI's OAuth login mechanism, this update establishes CLIProxyAPI OAuth tokens as the PRIMARY source for Direct Mode video generation authentication.

**Key Finding:**
- CLIProxyAPI stores OAuth tokens at `~/.ccs/cliproxy/auth/gemini-{email}-{project}.json`
- Token includes `access_token`, `refresh_token`, `client_id`, `client_secret`
- Scopes: `cloud-platform`, `userinfo.email`, `userinfo.profile` (covers Vertex AI)
- Token is compatible with `google.oauth2.credentials.Credentials`

**Benefits:**
- **No Separate Login Required:** Reuse existing `cliproxy login gemini` credentials
- **Single Source of Truth:** One OAuth token for both Proxy and Direct modes
- **Auto-Refresh:** Tokens auto-refresh using embedded refresh_token
- **Seamless Integration:** video_gen.py automatically syncs from CLIProxyAPI

### Version 2.4 (2026-01-17)

**Major Addition: Direct API Connection Details (Verified)**

Following Zero Hallucination Policy, this update includes comprehensive verified technical details from official Google documentation and Stack Overflow.

**Key Design Decision: OAuth as Primary Authentication**
- OAuth (Service Account / ADC) is now the PRIMARY authentication method
- API Key is SECONDARY (fallback only for quick prototyping)
- Rationale: Better security, token auto-refresh, IAM permission control, audit logging

**Technical Content:**
- API Comparison Table: Side-by-side comparison of Gemini API vs Vertex AI
- Gemini API Video Generation with complete request examples
- Vertex AI Video Generation with GCS output support
- Video Extension Critical Info: `video.uri` is required (not `bytesBase64Encoded`)
- Parameters Reference: Complete table with all parameters
- Authentication Code Examples: Python code for API Key, Service Account, ADC
- Polling Pattern: Complete polling implementation with timeout handling
- Error Handling: HTTP error codes and resolutions table

### Version 2.3 (2026-01-17)

**New Documentation:**
- Direct Google API Integration References
- Vertex AI Authentication Methods
- Veo 3.1 Official Documentation links
- CLI Interface Design with `--auth` parameter
- Credential Discovery System priority order
- Implementation Strategy: Single script with dual-mode support

**Key Design Decision:**
- **Single Script Approach:** `video_gen.py` will support both CCS and Direct API modes
- **No Separate Scripts:** Rejected `video_gen_direct.py` approach
- **Auto-Detection:** Smart default mode that tries CCS first, then falls back to Direct API

### Version 2.2 (2026-01-16)

**New Features:**
- **Multiple Reference Images:** Support for multiple images via CLI
- **Smart MIME Type Handling:** Auto-detect URL extensions, GIF/WebP support
- **Robust Input Processing:** Improved error handling for download/encoding failures

### Version 2.1 (2026-01-14)

**Documentation Consolidation:**
- Consolidated GitHub issue documents into single file
- Verified README.md for Concept and Overview
- Verified architecture.md for structure documentation
- Organized documentation with clear separation of concerns

### Version 2.0 (2026-01-14)

**Added:**
- Video generation support design (Veo 3.1)
- MCP Tool integration plan
- Roadmap for future development
- Detailed Flow Diagrams

**Changed:**
- Restructured documentation with clear sections
- Updated architecture diagrams with accurate flow

**Fixed:**
- Corrected request flow (CLIProxyAPI is main handler, CCS is token provider only)

### Version 1.0 (2025-01-14)

**Initial:**
- Basic architecture documentation
- Image generation design
- OAuth flow documentation
