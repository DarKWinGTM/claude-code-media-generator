# 📋 TODO - Claude Code Media Generator

> **Last Updated:** 2026-01-27
> **Version:** 2.0

---

## ✅ Completed Features

### Core Functionality
- [x] **video_gen.py v2.24.1** - Video generation with Veo models
  - [x] 8 generation modes (text_to_video, image_to_video, video_extension, etc.)
  - [x] Mode-Aware Smart Defaults
  - [x] Model-Mode Compatibility Validation
  - [x] Preset system (quick/quality/extend/budget)
  - [x] GCS storage output support (`--storage-uri`)
  - [x] Cost estimation and tracking
  - [x] Metadata auto-save
  - [x] **Phase 2.5 Features** (2026-01-22) ✅ TESTED
    - [x] `--last-frame` with `--extend-video`
    - [x] `--reference-asset` with `--extend-video`
  - [x] **v2.21**: Fixed video extension API format (gcsUri + mimeType)
  - [x] **v2.22**: Added preventive smart validation
  - [x] **v2.23**: Fixed referenceType format (asset/style)
  - [x] **v2.24**: Unified Reference Image System (`--reference-image PATH:TYPE`)
  - [x] **v2.24.1**: Fixed mixed type validation (API limitation discovered)

- [x] **image_gen.py** - Image generation with Imagen models
  - [x] Text-to-image generation
  - [x] Multiple aspect ratios and resolutions
  - [x] Cost estimation
  - [ ] **Planned:** `--template closing-frame` for video closing frames (moved from create_last_frame.py)

- [x] **check_api.py** - API verification tool
  - [x] Gemini API / Vertex AI API testing
  - [x] Model listing

### Documentation
- [x] **design/design.md** v3.5 - Master design document
- [x] **design/video.design.md** v2.24.1 - Video generation design ✅ Updated with API discoveries
- [x] **design/image.design.md** v1.2 - Image generation design
- [x] **design/gcsdb.design.md** v1.2 - GCS module design
- [x] **design/check.design.md** v1.0 - API checker design

### Project Structure
- [x] Reorganized folder structure
  - [x] `./design/` - All design documents
  - [x] `./docs/` - Archive and issues
  - [x] `./mcp/` - MCP server (placeholder)
  - [x] `./skill/` - Claude Code skill (placeholder)
  - [x] `./design/changelog/` - Separated changelog files
- [x] Created `.gitignore` for sensitive files
- [x] Removed generated files with sensitive data
- [x] **Changelog Standardization** ✅ (2026-01-19)
  - [x] Separated changelogs to `design/changelog/` folder
  - [x] changelog.master.md (from design.md)
  - [x] video.changelog.md (from video.design.md)
  - [x] image.changelog.md (from image.design.md)
  - [x] gcsdb.changelog.md (from gcsdb.design.md)
  - [x] check.changelog.md (from check.design.md)
  - [x] mcp.changelog.md (from mcp.design.md)

---

## 🚧 In Progress

### ✅ Completed: GitHub Wiki Documentation (2026-01-23)
- [x] **GitHub Wiki** - Complete documentation site
  - [x] Home.md - Landing page with feature overview
  - [x] _Sidebar.md / _Footer.md - Navigation
  - [x] Getting Started (Installation, Authentication, Quick-Start)
  - [x] Video Generation (Overview, 8 Modes, Models, Reference Images, Video Extension)
  - [x] Image Generation guide
  - [x] CLI Reference (video_gen, image_gen, check_api)
  - [x] Guides (GCS Storage, Presets, Troubleshooting)
  - [x] Changelog page

### ✅ Completed: MkDocs Material for GitHub Pages (2026-01-23)
- [x] **MkDocs Material** - Professional documentation site
  - [x] Created `docs/` directory structure
  - [x] Converted wiki files to MkDocs format (with admonitions, tabs, icons)
  - [x] Created `mkdocs.yml` configuration
    - [x] Material theme with dark/light mode
    - [x] Navigation tabs, sections, search
    - [x] Code copy, syntax highlighting
    - [x] Emoji support, admonitions
  - [x] Created GitHub Actions workflow (`.github/workflows/docs.yml`)
    - [x] Auto-deploy on push to main/master
    - [x] Triggered by docs/, mkdocs.yml, or workflow changes
  - [x] **Pages Created (16 files):**
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
  - [x] **GitHub Pages URL:** https://darkwingtm.github.io/claude-code-media-generator/

### ✅ Completed: GitHub Pages Modernization (2026-01-27)
- [x] **Glassmorphism Design Overhaul**
  - [x] Complete redesign with modern 2026 design
  - [x] Design style: Glassmorphism + Gradient + Bento Grid
  - [x] `extra.css` - Frosted glass cards, gradient backgrounds, animations
  - [x] `index.md` - New hero section, bento grid, tabbed quick start
  - [x] `mkdocs.yml` - Plus Jakarta Sans font
  - [x] Created overrides templates (main.html, footer.html, header.html)

### ✅ Completed: GitHub Pages UI/UX Improvements (2026-01-27)

> **Design Document:** [design/pages.design.md](design/pages.design.md) v1.1
> **Changelog:** [changelog/pages.changelog.md](changelog/pages.changelog.md)
> **25 Issues Identified via MCP + UI/UX Pro Max Analysis** - ✅ ALL RESOLVED

#### ✅ Phase 1: Critical Fixes (Must Fix) - Complete
- [x] Fix code syntax highlighting (mkdocs.yml - Monokai Pro theme)
- [x] Fix text contrast ratio (extra.css - WCAG AA ≥4.5:1)
- [x] Fix table row padding (extra.css - 1rem 1.25rem)
- [x] Fix table header contrast (extra.css - bg-primary + white text)

#### ✅ Phase 2: High Priority - Complete
- [x] Add card hover effects (hover:shadow-lg, cursor-pointer)
- [x] Fix line height (leading-relaxed 1.625)
- [x] Standardize section spacing (space-y-8)
- [x] Reduce cookie banner prominence
- [x] Improve heading hierarchy (H2/H3 size/weight difference)
- [x] Add code line numbers (linenums: true)

#### ✅ Phase 3: Medium Priority - Complete
- [x] Button hover states (hover:scale-105 transition)
- [x] Unified shadow system for cards
- [x] Consistent icon sizes (w-6 h-6)
- [x] Table zebra striping (odd:bg-gray-50)
- [x] Focus states for accessibility (focus:ring-2)
- [x] Output label styling (font-semibold)
- [x] Reduce content density (more whitespace)
- [x] Warning box styling (stronger border + bg)

#### ✅ Phase 4: Polish - Complete
- [x] Breadcrumb navigation
- [x] Footer improvements (larger links, more spacing)
- [x] Smooth scroll (scroll-behavior: smooth)
- [x] Code copy button size increase
- [x] Responsive tables (horizontal scroll on mobile)
- [x] Hero spacing improvement (more padding-y)
- [x] Sidebar active state (bg-primary/10 border-left)

#### ✅ MCP Re-analysis Fixes - Complete
- [x] Hero text contrast: Dark bg (#0d0d14) + white text
- [x] Color scheme: Subdued palette #818cf8 (was #a78bfa)
- [x] Cards: Visible borders + stronger shadows
- [x] Layout spacing: Section dividers, max-width 900px
- [x] Typography: h2=2rem, h3=1.5rem, h4=1.25rem, h5=1.125rem
- [x] Light mode: Purple-tinted borders, proper backgrounds

### High Priority
- [ ] **README.md** - Redesign for GitHub
  - [ ] Clear installation instructions
  - [ ] Usage examples
  - [ ] API key setup guide
  - [ ] Screenshots/demos

### ✅ Completed: Config Integration (Phase 1) - 2026-01-20
- [x] **config.json Schema** ✅ (v2.0 Multi-Project)
  - [x] Design schema with `main_projects` structure
  - [x] Create `config.example.json` template
  - [x] Implement config loader (`config_loader.py`)
  - [x] Integrate with video_gen.py (v2.10)
  - [x] Integrate with image_gen.py (v1.2)
  - [x] Add `--project` flag for runtime project switching

### ✅ Completed: Auto-Upload Integration (Phase 2) - 2026-01-20
- [x] **Auto-Upload for Video Extension** ✅
  - [x] Added Section 5.15 in video.design.md
  - [x] Documented problem statement and solution
  - [x] Implementation pseudocode ready
  - [x] Implement in video_gen.py (v2.10)
  - [x] Auto-detect local file vs URI
  - [x] Auto-upload to GCS before extend when `--storage-uri` provided
  - [x] Fallback to base64 if no storage-uri

---

## 📅 Planned Features

### ~~Phase 1: Config Integration~~ ✅ COMPLETED (2026-01-20)
### ~~Phase 2: Auto-Upload for Video Extension~~ ✅ COMPLETED (2026-01-20)
### ~~Phase 2.5: Video Extension Enhancements~~ ✅ COMPLETED & TESTED (2026-01-22)

> **Note:** Phase 2.5 features now tested and confirmed working with API

- [x] **`--last-frame` for Video Extension Mode** ✅
  - [x] Added `--last-frame` parameter support for `--extend-video`
  - [x] Implemented in video_gen.py v2.20 (lines 1882-1895)
  - [x] Documented in video.design.md Section 5.16.1
  - [x] **API testing confirmed** ✅ (2026-01-22)

- [x] **`--reference-asset` for Video Extension Mode** ✅
  - [x] Added `--reference-asset` parameter support for `--extend-video`
  - [x] Implemented in video_gen.py v2.20 (lines 1897-1912)
  - [x] Documented in video.design.md Section 5.16.2
  - [x] **API testing confirmed** ✅ (2026-01-22)
  - [x] **Bug fix v2.23**: referenceType format corrected

### ✅ Completed: Phase 2.6 - Unified Reference Image System (v2.24.1) - 2026-01-23

> **Status:** ✅ Implemented & API Tested
> **Design:** [video.design.md Section 5.17](./design/video.design.md#517-unified-reference-image-system-planned-v224)

**Goal:** Replace separate `--reference-asset` and `--reference-style` with unified `--reference-image PATH:TYPE` format.

**Key Features:**
- [x] **`--reference-image PATH:TYPE`** - Unified format
  - [x] Format: `--reference-image avatar.png:asset`
  - [x] Default type: `asset` when not specified
  - [x] Can be repeated for multiple images
  - [x] ~~Mixed types allowed~~ ❌ API ไม่รองรับ mixed types (v2.24.1 fix)

- [x] **Validation:**
  - [x] Max 3 `asset` type images
  - [x] Max 1 `style` type image
  - [x] Model compatibility check (`style` only on veo-2.0-exp)
  - [x] **NEW v2.24.1:** Prevent mixed asset+style (API limitation)

- [x] **Backward Compatibility:**
  - [x] Keep `--reference-asset` working (deprecated warning)
  - [x] Keep `--reference-style` working (deprecated warning)

**🧪 API Test Results (2026-01-23):**
| Test | Result | File |
|------|--------|------|
| Two Asset Images | ✅ | video_20260123_005611_0.mp4 |
| Style Only | ✅ | video_20260123_005734_0.mp4 |
| Mixed Asset+Style | ❌ | API Error: "does not support this mix" |
| First + Last Frames | ✅ | video_20260123_010422_0.mp4 |

**CLI Examples:**
```bash
# Single image (default = asset)
--reference-image avatar.png

# With explicit type
--reference-image avatar.png:asset
--reference-image painting.png:style

# ⚠️ Cannot mix asset + style in same request (API limitation)
# Use ONLY asset OR ONLY style
```

- [ ] **Closing Frame Template** (→ image_gen.py)
  - [ ] Planned as `--template closing-frame` feature in image_gen.py
  - [ ] Moved from separate create_last_frame.py (deleted)

### Phase 3: Claude Code Integration
- [ ] **Skill Development** (`./skill/`)
  - [ ] `/generate-video` skill
  - [ ] `/generate-image` skill
  - [ ] Skill manifest (skill.json)

- [ ] **MCP Server Development** (`./mcp/`)
  - [ ] Update mcp.design.md for current architecture
  - [ ] Implement MCP protocol
  - [ ] Tool definitions (generate_video, generate_image, etc.)
  - [ ] Dual transport (stdio + SSE)

### ✅ Completed: Phase 2.7 - External Video Extension (v2.26) - 2026-01-24

> **Status:** ✅ PRODUCTION READY
> **Design:** [video.design.md Section 5.18](./design/video.design.md#518-video-extension-external-urls--fps-requirements-v225)
> **Updated:** 2026-01-25

**Key Features:**
- [x] `--extend-video-from-url URL` parameter
- [x] Auto download from HTTP/HTTPS URLs
- [x] Auto FPS detection using ffprobe
- [x] Auto convert to 24fps using ffmpeg
- [x] GCS upload via REST API (not gsutil - less permissions required)
- [x] Progress indicators for all steps

**Bug Fixes (2026-01-24):**
- [x] FPS tolerance 0.1 → 0.01 (Veo requires exactly 24.0fps)
- [x] Output path `-o` now always saves to `generated_videos/`
- [x] **GCS Bucket Naming Convention** - MANDATORY pattern: `YOUR_PROJECT_ID-media-output`

**Root Cause Analysis:**
| Issue | Previously Documented | Actual Root Cause |
|-------|----------------------|-------------------|
| gsutil permission denied | gsutil requires `storage.objects.list` | ❌ **Wrong** |
| | | GCS bucket name ไม่ตรง pattern `YOUR_PROJECT_ID-media-output` ✅ |

**GCS Naming Standard (MANDATORY):**
```
Pattern: gs://YOUR_PROJECT_ID-media-output/

Example:
  Project ID:  gen-lang-client-0344941103
  GCS Bucket:  gen-lang-client-0344941103-media-output
  Storage URI: gs://gen-lang-client-0344941103-media-output/path/
```

**Test Results:**
| Step | Status | Details |
|------|--------|---------|
| Download from URL | ✅ | 16.63 MB |
| Detect FPS | ✅ | 23.976fps detected |
| Convert to 24fps | ✅ | Using ffmpeg |
| GCS Upload (REST API) | ✅ | 15.70 MB |
| Veo Extension | ✅ | 55 seconds |
| Download Result | ✅ | 30 MB output |

---

### ~~Phase 2.7: External Video Extension Support (v2.25)~~ ✅ COMPLETED (2026-01-24)

> **See:** [Phase 2.7 Completed section above](#-completed-phase-27---external-video-extension-v226---2026-01-24)

---

### Phase 4: Advanced Features
- [ ] **gcsdb_api.py** - GCS database module
  - [ ] Upload/download operations
  - [ ] Object management
  - [ ] Tracking & cleanup

- [ ] **Batch Processing**
  - [ ] Multiple prompts from file
  - [ ] Parallel generation
  - [ ] Batch cost estimation

- [ ] **Video Extension Workflow**
  - [ ] Automated chain extension
  - [ ] Smart prompt continuation

---

## 🐛 Known Issues

| Issue | Status | Priority |
|-------|--------|----------|
| veo-3.1-generate-001 video_extension = False | ⚠️ Pending API update | Low |
| GCS download sometimes fails | 🔄 Retry logic added | Medium |
| **External URLs not supported for extend** | 📋 Documented | Medium |
| **Non-24fps videos fail extend** | 📋 Documented | Medium |

---

## 📊 Skill vs MCP Analysis

### Comparison

| Aspect | Claude Code Skill | MCP Server |
|--------|------------------|------------|
| **Complexity** | 🟢 Low | 🔴 High |
| **Deployment** | Copy to ~/.claude/skills | Install + config |
| **Use Case** | Quick personal use | Multi-client integration |
| **Development Time** | ~1-2 days | ~1-2 weeks |

### Recommendation

**Phase 1: Start with Skill** (simpler, faster)
```
./skill/
├── generate-video.md    # Skill instructions
├── generate-image.md
└── skill.json          # Manifest
```

**Phase 2: Add MCP Server** (for advanced users)
```
./mcp/
├── server.py           # Main MCP server
├── tools/
│   ├── video.py
│   └── image.py
└── config.py
```

---

## 📝 Notes

### Design Decisions Made
1. **veo-3.1-generate-preview as default** - Audio support, latest features
2. **Smart Validation** - Auto-correct common mistakes with warnings
3. **GCS recommended for Vertex AI** - Better for video extension workflow
4. **Mode-Aware Defaults** - Each mode has optimal settings

### Breaking Changes Log
- v2.20: Added Phase 2.5 Experimental Features (--last-frame + --extend-video, --reference-asset + --extend-video)
- v2.19: Added Model-Mode Compatibility Validation
- v2.18: Changed DEFAULT_MODEL from veo-2.0 to veo-3.1-generate-preview
- v2.17: Added --preset argument

---

## 🔗 References

- [Design Documents](./design/)
- [GitHub Wiki](./wiki/) - Full documentation
- [Google Veo API Docs](https://cloud.google.com/vertex-ai/docs/generative-ai/video/overview)
- [Google Imagen API Docs](https://cloud.google.com/vertex-ai/docs/generative-ai/image/overview)
