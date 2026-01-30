# 📜 Changelog - Video Generation Design Document

> **Parent Document:** [video.design.md](../design/video.design.md)
> **Current Version:** 2.25
> **Previous:** 2.24.1

---

## Version 2.25: External URLs & FPS Requirements Documentation

**Date:** 2026-01-23
**Session:** Testing Session
**Status:** 📋 Documented - Implementation Planned

### Overview

เพิ่ม Section 5.18 ใน video.design.md เพื่อบันทึกความรู้ใหม่จากการทดสอบ:

1. **External HTTP/HTTPS URLs ไม่รองรับ** - API error: "video is empty"
2. **Video Extension ต้องการ 24fps เท่านั้น** - Non-24fps videos → FPS mismatch error
3. **storage-uri requirement** - จำเป็นสำหรับ Video Extension, ไม่จำเป็นสำหรับ text_to_video

### Test Results (2026-01-23)

| Test | Input Type | Result | Error |
|------|------------|--------|-------|
| External HTTPS URL | `https://test-videos.co.uk/...` | ❌ FAIL | `"video is empty"` |
| External HTTP URL | `http://commondatastorage.googleapis.com/...` | ❌ FAIL | `"video is empty"` |
| GCS URI (23fps) | `gs://gtv-videos-bucket/...` | ❌ FAIL | `"fps mismatch: Expected 24 got 23"` |
| GCS URI (25fps) | `gs://cloud-samples-data/video/cat.mp4` | ❌ FAIL | `"fps mismatch: Expected 24 got 25"` |
| Local Veo video → Upload GCS | Veo-generated (24fps) | ✅ SUCCESS | - |
| Text-to-Video (no storage-uri) | prompt only | ✅ SUCCESS | - |

### Proposed Feature: `--extend-video-from-url`

Planned implementation to enable external video extension:

```
Download URL → FFmpeg Convert 24fps → Upload GCS → Use gcsUri
```

### Files Changed
- `design/video.design.md`: Added Section 5.18
- `TODO.md`: Added Phase 2.7, updated Known Issues

### Summary
Documented API limitations for external URLs and 24fps requirement. Proposed `--extend-video-from-url` feature for v2.25.

---

## Version 2.24.1: API Limitation Fix - No Mixed Types

**Date:** 2026-01-23
**Session:** 982d8499-61cd-443e-a6b6-a8d0e72f7be5
**Status:** ✅ Implemented

### Changes
- **Discovered API Limitation**: Google Veo API does not support mixing `asset` + `style` in same request
- **Added Validation**: `validate_reference_images()` now prevents mixed types before sending to API
- **Error Message**: Clear message explaining the limitation

### API Error
```
Reference to video does not support this mix of reference images.
```

### Summary
Added validation to prevent mixing asset and style reference images (API limitation).

### Test Results (2026-01-23)

| Test Case | Result | Output File |
|-----------|--------|-------------|
| Two Asset Images | ✅ SUCCESS | video_20260123_005611_0.mp4 |
| Mixed Asset+Style | ❌ FAILED | API Error: "does not support this mix" |
| Style Only | ✅ SUCCESS | video_20260123_005734_0.mp4 |
| First + Last Frames | ✅ SUCCESS | video_20260123_010422_0.mp4 |

---

## Version 2.24: Unified Reference Image System

**Date:** 2026-01-23
**Session:** 982d8499-61cd-443e-a6b6-a8d0e72f7be5
**Status:** ✅ Implemented

### Overview

Implemented unified `--reference-image PATH:TYPE` format to replace separate `--reference-asset` and `--reference-style` options.

### Key Features
- **Unified CLI**: Single `--reference-image` option with `path:type` format
- **Flexible Types**: Each image can be `asset` or `style`
- **Multiple Images**: Can be repeated for multiple reference images
- **Default Type**: `asset` when type not specified
- **Backward Compatible**: Legacy options still work (with deprecation warning)
- **⚠️ No Mixed Types**: API does not support mixing asset + style (see v2.24.1)

### CLI Syntax
```bash
# New format
--reference-image avatar.png:asset
--reference-image painting.png:style

# Can be repeated
--reference-image img1.png:asset --reference-image img2.png:asset --reference-image style.jpg:style

# Default type is asset
--reference-image avatar.png  # Same as avatar.png:asset
```

### API Constraints
| Type | Max Count | Supported Models |
|------|-----------|------------------|
| `asset` | 3 | veo-2.0-exp, veo-3.1-preview |
| `style` | 1 | veo-2.0-exp only |

### Implementation Details
- `parse_reference_image()`: Parses `PATH:TYPE` format
- `validate_reference_images()`: Validates counts and model compatibility
- Deprecation warnings for `--reference-asset` and `--reference-style`
- Mode detection updated to handle new format

### Design Reference
See Section 5.17 in [video.design.md](../design/video.design.md#517-unified-reference-image-system-planned-v224)

### Summary
Implemented unified reference image system for more flexible image type control.

---

## Version 2.23: CRITICAL FIX - referenceType Format

**Date:** 2026-01-22
**Session:** 982d8499-61cd-443e-a6b6-a8d0e72f7be5

### Changes
- **Critical Fix:** `referenceType` format was incorrect
  - ❌ Old: `"REFERENCE_TYPE_ASSET"`, `"REFERENCE_TYPE_STYLE"`
  - ✅ New: `"asset"`, `"style"` (per official docs)
- Removed unnecessary `referenceId` field (not in official docs)

### Bug Details

| Error | Cause | Fix |
|-------|-------|-----|
| `Invalid referenceType: REFERENCE_TYPE_ASSET` | Incorrect format | Changed to `"asset"` |
| `Invalid referenceType: REFERENCE_TYPE_STYLE` | Incorrect format | Changed to `"style"` |

### Files Changed
- `video_gen.py`: Lines 1928, 1942, 1955

### Verification
- ✅ Test 1: `--reference-asset` with veo-3.1-generate-preview - **PASSED**
- ✅ Test 2: `--extend-video` + `--reference-asset` (Phase 2.5) - **PASSED**

### Source
[Official Docs: Generate Veo videos from reference images](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/video/use-reference-images-to-guide-video-generation)

### Summary
Fixed critical referenceType format to match official API specification.

---

## Version 2.22: Preventive Smart Validation

**Date:** 2026-01-22
**Session:** 982d8499-61cd-443e-a6b6-a8d0e72f7be5

### Changes
- **Smart Validation Enhancements in `apply_smart_defaults()`:**
  - Added warning when video extension used without `--storage-uri`
  - Added note about video source requirement (should be from Veo generation)
  - Added duration validation for Veo 3.1 text-to-video (5s → 6s auto-fix)

### Preventive Measures Added

| Issue | Validation Added |
|-------|-----------------|
| `storage uri required` error | Warning if no `--storage-uri` for video extension |
| Source video requirement | Info note when using local file |
| Duration 5s not supported | Auto-fix 5s → 6s with warning for Veo 3.1 |

### Summary
Added smart validation to prevent common errors discovered during Phase 2.5 testing.

---

## Version 2.21: Phase 2.5 Tested + Video Extension Bug Fixes

**Date:** 2026-01-22
**Session:** 982d8499-61cd-443e-a6b6-a8d0e72f7be5

### Changes
- **Phase 2.5 Features Tested Successfully:**
  - ✅ `--extend-video` + `--last-frame` - API accepts combined payload
  - ✅ `--extend-video` + `--reference-asset` - API accepts combined payload
  - Status changed from 🔬 Experimental to ✅ Tested

- **Critical Bug Fixes in video_gen.py:**
  - Fixed: Video extension must use `gcsUri` (not `uri`) for GCS paths
  - Fixed: `mimeType: "video/mp4"` is required for video extension
  - Updated Section 5.16.1 and 5.16.2 API request examples with correct format

- **Added Section 5.16.4:** Critical API Format Notes with common errors table

### Technical Details
- Error `video is empty` was caused by using `uri` instead of `gcsUri`
- Error `video mime type is empty` was caused by missing `mimeType` field
- Both fixes applied to: direct GCS URI input and auto-uploaded videos

### Summary
Phase 2.5 experimental features now tested and working. Critical video extension API format bugs fixed.

---

## Version 2.20: Phase 2.5 Experimental Features

**Date:** 2026-01-22
**Session:** LEGACY-000

### Changes
- Added Section 5.16 documenting Phase 2.5 Experimental Video Extension Enhancements
- Implemented `--last-frame` with `--extend-video` (experimental) - uses `parameters.lastFrame` with `instances[].video`
- Implemented `--reference-asset` with `--extend-video` (experimental) - uses `parameters.referenceImages` with `instances[].video`
- Section 5.16.3: Changed from `create_last_frame.py` to reference `image_gen.py` planned feature

### Technical Details
- Phase 2.5 features combine different API fields (`instances[].video` vs `parameters.*`) which may work together
- Marked as experimental - API compatibility not officially documented
- Closing frame creation moved to image_gen.py as planned feature (not separate tool)

### Summary
Phase 2.5 experimental features for combining video extension with lastFrame and referenceImages parameters

---

## Version 2.19.1: Design Clarification: Auto-Upload Integration

**Date:** 2026-01-19
**Session:** LEGACY-001

### Changes
- Added Section 5.12 documenting planned Auto-Upload feature for Video Extension
- Clarified that local video files will be auto-uploaded to GCS before extension (requires `--storage-uri`)
- Documented problem statement, proposed solution, implementation design, workflow diagram, and CLI usage examples

### Summary
Target: v2.20

---

## Version 2.19: Model-Mode Compatibility Validation

**Date:** 2026-01-19
**Session:** LEGACY-002

### Changes
- Added `MODE_FALLBACK_MODELS` constant for auto-switching incompatible models
- New Smart Validation rule: if model doesn't support mode (e.g., veo-3.0 + video_extension), auto-switch to fallback model with warning
- Added Section 7.12 with fallback table and examples
- Covers: video_extension, first_last_frames, reference_asset/style, insert/remove_objects

### Summary
Smart model fallback for incompatible modes

---

## Version 2.18: Mode-Aware Smart Defaults & Presets

**Date:** 2026-01-19
**Session:** LEGACY-003

### Changes
- Changed DEFAULT_MODEL to `veo-3.1-generate-preview`
- Added `--preset` argument (quick/quality/extend/budget)
- Added MODE_DEFAULTS for auto-correction per mode
- Smart Validation: auto-fix duration for video_extension (7 sec), reference modes (8 sec), mask ops (veo-2.0)
- Added Section 7.10-7.11 with preset usage and smart validation rules

### Summary
Preset system for quick configuration

---

## Version 2.17: GCS Download/Upload Strategy

**Date:** 2026-01-19
**Session:** LEGACY-004

### Changes
- Added Section 5.13 with REST API first, gsutil fallback design
- Pre-check availability, 3 retry attempts per method, exponential backoff
- Pseudo-code and status messages documented

### Summary
Robust download/upload with fallback strategy

---

## Version 2.16: Unified Metadata Format

**Date:** 2026-01-19
**Session:** LEGACY-005

### Changes
- SUCCESS/ERROR use same `metadata_*.json` schema
- Added `status` field, `google_api_response` for debugging
- Added Format 3 response parsing (`videos[].bytesBase64Encoded`) for Veo 3.x + API Key
- Updated Section 8.3-8.4 Response Formats, Section 14.1-14.5 Metadata Schema

### Summary
Unified metadata schema for all response types

---

## Version 2.15: GCS Storage Output

**Date:** 2026-01-19
**Session:** LEGACY-006

### Changes
- Added `--storage-uri` parameter for GCS output instead of base64
- Added Section 5.10 with CLI usage and benefits
- Added link to design.md Cloud Storage central documentation

### Summary
Direct GCS output support

---

## Version 2.13: CRITICAL FIX: Vertex AI API Key Compatibility

**Date:** 2026-01-19
**Session:** LEGACY-007

### Changes
- Discovered that `AQ...` keys DO work for `predictLongRunning` (video/image generation)
- Previous v2.11 documentation was incorrect
- Fixed Sections 2.1, 2.2, 2.3, 3.1, 3.3
- `AQ...` keys ✅ work for video gen, ❌ don't work for List Models/generateContent
- Added link to check.design.md for check_api.py operations

### Summary
Corrected API Key compatibility documentation

---

## Version 2.11: CRITICAL: Vertex AI API Key Correction

**Date:** 2026-01-19
**Session:** LEGACY-008

### Changes
- Discovered that Vertex AI does NOT support API Keys (`AQ...`), returns 401 "UNAUTHENTICATED"
- Corrected all documentation
- Vertex AI requires OAuth2 token (`ya29...`) or Service Account
- Tested both Global and Regional endpoints
- Recommended using Gemini API Key (`AIzaSy...`) for simplicity

### Summary
Initial API Key testing (later corrected in v2.13)

---

## Version 2.10: Verified against Official Docs

**Date:** 2026-01-18
**Session:** LEGACY-009

### Changes
- Updated Model IDs table (Section 4.2) with complete list from official Vertex AI docs
- Added veo-3.0-generate-preview, veo-3.0-fast-generate-preview, veo-3.1-fast-generate-001 etc.

### Summary
Model list updated from official documentation

---

## Version 2.9: Unified API Key

**Date:** 2026-01-18
**Session:** LEGACY-010

### Changes
- Auto-detect Gemini/Vertex from key format (AIzaSy=Gemini, AQ=Vertex)
- Simplified to 2 auth methods: api-key (default) and service-account (optional)

### Summary
Auto-detection of API Key type

---

## Version 2.8: Vertex AI API Key Support

**Date:** 2026-01-18
**Session:** LEGACY-011

### Changes
- Added Vertex AI API Key (`AQ.Ab8R...`) as 3rd auth method
- SEPARATE quota from Gemini API, solves RPD 10/10 limit
- Updated Sections 2.1, 3.1-3.7

### Summary
Additional auth method for separate quota

---

## Version 2.7: Video Extension Input Standards

**Date:** 2026-01-18
**Session:** LEGACY-012

### Changes
- Added Section 5 Video Extension Input Standards
- Critical documentation for Gemini API vs Vertex AI
- Added Section 16.1 External Reference Links

### Summary
Video extension requirements documented

---

## Version 2.6: Cost Tracking & Display

**Date:** 2026-01-18
**Session:** LEGACY-013

### Changes
- Added Section 14 Cost Tracking & Display
- Comprehensive cost management features

### Summary
Cost estimation and display system

---

## Version 2.5: Unified Naming Convention

**Date:** 2026-01-18
**Session:** LEGACY-014

### Changes
- Added Section 13.4 Unified Naming Convention
- Aligned with image_gen.py format

### Summary
Consistent file naming across modules

---

## Version 2.4: Video Metadata Design

**Date:** 2026-01-18
**Session:** LEGACY-015

### Changes
- Added Section 13 Video Metadata Design with schema and implementation status

### Summary
Metadata schema specification

---

## Version 2.3: CLIProxyAPI Case Study

**Date:** 2026-01-18
**Session:** LEGACY-016

### Changes
- Added Section 3.8 Case Study: Why CLIProxyAPI Cannot Work with Veo

### Summary
Technical analysis of API limitations

---

## Version 2.2: CLI Quick Reference

**Date:** 2026-01-18
**Session:** LEGACY-017

### Changes
- Added Section 7.10 CLI Quick Reference with all parameters

### Summary
Quick parameter reference

---

## Version 2.1: Simplified Auth

**Date:** 2026-01-18
**Session:** LEGACY-018

### Changes
- Simplified auth to API Key + Service Account only
- ADC moved to Appendix A

### Summary
Authentication simplification

---

## Version 2.0: Major Refactor

**Date:** 2026-01-18
**Session:** LEGACY-019

### Changes
- Major refactor - consolidated all API details from design.md

### Summary
Complete restructure of documentation

---

## Version 1.0: Initial Version

**Date:** 2026-01-18
**Session:** LEGACY-020

### Changes
- Initial version - extracted from consolidated design with 8 modes

### Summary
Initial extraction of video generation design

---

## Version History (Unified)

| Version | Date | Changes | Session ID |
|---------|------|---------|------------|
| 2.25 | 2026-01-23 | **[External URLs & FPS Requirements](#version-225-external-urls--fps-requirements-documentation)** | Testing |
| | | Summary: Documented API limitations, proposed --extend-video-from-url feature | |
| 2.24.1 | 2026-01-23 | **[No Mixed Types Fix](#version-2241-api-limitation-fix---no-mixed-types)** | 982d8499... |
| | | Summary: Added validation to prevent mixing asset+style | |
| 2.24 | 2026-01-23 | **[Unified Reference Image System](#version-224-unified-reference-image-system)** | 982d8499... |
| | | Summary: --reference-image PATH:TYPE format | |
| 2.23 | 2026-01-22 | **[referenceType Format Fix](#version-223-critical-fix---referencetype-format)** | 982d8499... |
| | | Summary: Fixed to use "asset"/"style" | |
| 2.22 | 2026-01-22 | **[Preventive Smart Validation](#version-222-preventive-smart-validation)** | 982d8499... |
| | | Summary: Added warnings for common errors | |
| 2.21 | 2026-01-22 | **[Phase 2.5 Tested](#version-221-phase-25-tested--video-extension-bug-fixes)** | 982d8499... |
| | | Summary: Video extension bugs fixed | |
| 2.20 | 2026-01-22 | **[Phase 2.5 Experimental Features](#version-220-phase-25-experimental-features)** | LEGACY-000 |
| | | Summary: Video extension with lastFrame/referenceImages (experimental) | |
| 2.19.1 | 2026-01-19 | **[Auto-Upload Integration](#version-2191-design-clarification-auto-upload-integration)** | LEGACY-001 |
| | | Summary: Auto-upload local files for video extension | |
| 2.19 | 2026-01-19 | **[Model-Mode Compatibility](#version-219-model-mode-compatibility-validation)** | LEGACY-002 |
| | | Summary: Smart model fallback for incompatible modes | |
| 2.18 | 2026-01-19 | **[Smart Defaults & Presets](#version-218-mode-aware-smart-defaults--presets)** | LEGACY-003 |
| | | Summary: Preset system for quick configuration | |
| 2.17 | 2026-01-19 | **[GCS Download/Upload Strategy](#version-217-gcs-downloadupload-strategy)** | LEGACY-004 |
| | | Summary: Robust download/upload with fallback | |
| 2.16 | 2026-01-19 | **[Unified Metadata Format](#version-216-unified-metadata-format)** | LEGACY-005 |
| | | Summary: Unified metadata schema | |
| 2.15 | 2026-01-19 | **[GCS Storage Output](#version-215-gcs-storage-output)** | LEGACY-006 |
| | | Summary: Direct GCS output support | |
| 2.13 | 2026-01-19 | **[CRITICAL FIX: API Key Compatibility](#version-213-critical-fix-vertex-ai-api-key-compatibility)** | LEGACY-007 |
| | | Summary: Corrected API Key compatibility | |
| 2.11 | 2026-01-19 | **[CRITICAL: API Key Correction](#version-211-critical-vertex-ai-api-key-correction)** | LEGACY-008 |
| | | Summary: Initial API Key testing | |
| 2.10 | 2026-01-18 | **[Verified against Official Docs](#version-210-verified-against-official-docs)** | LEGACY-009 |
| | | Summary: Model list updated | |
| 2.9 | 2026-01-18 | **[Unified API Key](#version-29-unified-api-key)** | LEGACY-010 |
| | | Summary: Auto-detection of API Key type | |
| 2.8 | 2026-01-18 | **[Vertex AI API Key Support](#version-28-vertex-ai-api-key-support)** | LEGACY-011 |
| | | Summary: Additional auth method | |
| 2.7 | 2026-01-18 | **[Video Extension Input Standards](#version-27-video-extension-input-standards)** | LEGACY-012 |
| | | Summary: Video extension requirements | |
| 2.6 | 2026-01-18 | **[Cost Tracking & Display](#version-26-cost-tracking--display)** | LEGACY-013 |
| | | Summary: Cost estimation system | |
| 2.5 | 2026-01-18 | **[Unified Naming Convention](#version-25-unified-naming-convention)** | LEGACY-014 |
| | | Summary: Consistent file naming | |
| 2.4 | 2026-01-18 | **[Video Metadata Design](#version-24-video-metadata-design)** | LEGACY-015 |
| | | Summary: Metadata schema specification | |
| 2.3 | 2026-01-18 | **[CLIProxyAPI Case Study](#version-23-cliproxyapi-case-study)** | LEGACY-016 |
| | | Summary: Technical analysis | |
| 2.2 | 2026-01-18 | **[CLI Quick Reference](#version-22-cli-quick-reference)** | LEGACY-017 |
| | | Summary: Quick parameter reference | |
| 2.1 | 2026-01-18 | **[Simplified Auth](#version-21-simplified-auth)** | LEGACY-018 |
| | | Summary: Authentication simplification | |
| 2.0 | 2026-01-18 | **[Major Refactor](#version-20-major-refactor)** | LEGACY-019 |
| | | Summary: Complete restructure | |
| 1.0 | 2026-01-18 | **[Initial Version](#version-10-initial-version)** | LEGACY-020 |
| | | Summary: Initial extraction from design.md | |
