# Pages Changelog

> **Current Version:** 1.4.0
> **Last Updated:** 2026-02-05

---

## Version History

| Version | Date | Changes | Summary |
|---------|------|---------|---------|
| 1.4.0 | 2026-02-05 | **Sidebar Transparent** | ลบ sidebar background ทั้งหมด |
| 1.3.0 | 2026-02-05 | **Skill-First Implementation** | เพิ่ม skills/, advanced/ sections, ปรับ docs ทั้งหมด |
| 1.2.0 | 2026-02-05 | **Left Sidebar Fix + Skill-First Design** | ปิด navigation.tabs, เพิ่ม spacing, ออกแบบ Skill-First |
| 1.1.0 | 2026-02-05 | **Sidebar Enhancement Options (Reverted)** | Options A, B, C - Reverted เพราะเว็บพัง |
| 1.0.0 | 2026-02-05 | **Clean Slate Release** | ลบ Section Navigator ทั้งหมด, สร้าง documentation files |

---

## Version 1.4.0: Sidebar Transparent

**Date:** 2026-02-05

### Changes

**Removed - Sidebar Background:**
- ลบ `.md-sidebar` glass panel background (Dark mode)
- ลบ Light mode sidebar background (rgba และ solid colors)
- ทำให้ sidebar transparent ทั้ง dark และ light mode

**CSS Changes:**
- Line 235-239: Removed glass panel background
- Line 2198-2201: Light mode sidebar → transparent
- Line 2360-2363: Professional light mode → transparent
- Line 3087-3092: Light mode no glass → transparent

**Files Modified:**
- `docs/stylesheets/extra.css`: 4 sidebar background rules changed to transparent
- `design.md`: Updated to v1.4.0 with sidebar styling info
- `TODO.md`: Added v1.4.0 completed tasks

### Philosophy
- Cleaner, less cluttered look
- Sidebar blends with main content area
- Focus on content, not UI elements

---

## Version 1.3.0: Skill-First Implementation

**Date:** 2026-02-05

### Changes

**Added - New Sections:**
- `skills/` section with 4 new pages:
  - `overview.md` - Skill introduction and installation
  - `commands.md` - Complete command reference
  - `examples.md` - Real-world usage examples
  - `configuration.md` - Setup and configuration guide
- `advanced/` section with 5 pages:
  - `index.md` - Advanced usage overview
  - `automation.md` - Automation and CI/CD guide
  - `video_gen.md` - Moved from cli/
  - `image_gen.md` - Moved from cli/
  - `check_api.md` - Moved from cli/

**Modified - Skill-First Approach:**
- `index.md` - Quick Start now shows Skill tab first
- `installation.md` - Skill installation is now the recommended method
- `quick-start.md` - Dual tabs with Skill first, Script second
- `authentication.md` - Updated links to new skills section

**Removed:**
- `guides/skill-installation.md` - Content moved to skills/overview.md

**Updated - Navigation Structure:**
```
1. Home
2. Getting Started
3. Using Skills ⭐ (NEW)
4. Video Generation
5. Image Generation
6. Advanced Usage ⭐ (NEW - replaces CLI Reference)
7. Guides
```

### Philosophy
- **Skill = Primary** - `/generative` skill is the recommended way
- **Script = Advanced** - Direct scripts for automation and power users

### Files Changed
- 15 files changed, 2504 insertions(+), 447 deletions(-)

### Commits
- `9a03858` - feat(docs): Implement Skill-First documentation approach

---

## Version 1.2.0: Left Sidebar Fix + Skill-First Design

**Date:** 2026-02-05

### Changes

**Fixed:**
- ปิด `navigation.tabs` และ `navigation.tabs.sticky` ใน mkdocs.yml
- Left Sidebar แสดง 6 หัวข้อหลักทั้งหมดแล้ว (ไม่ถูก "lift" ไปที่ header tabs)
- เพิ่ม `padding-bottom: 16px` ระหว่างหัวข้อหลักใน Left Sidebar

**Added:**
- **Skill-First Design** ใน design.md
  - Section 2.2: Proposed Skill-First Navigation Structure
  - Section 2.3: Content Changes Required
  - Section 6.2: Proposed File Structure

**Design Philosophy:**
- Skill = วิธีใช้งานหลัก (ง่าย, Claude Code native)
- Script = วิธีใช้ทางเลือก (สำหรับ automation, advanced users)

**Proposed New Sections:**
- "Using Skills" - หัวข้อใหม่สำหรับ Skill documentation
- "Advanced Usage" - รวม CLI Reference และ automation

**Files Modified:**
- `mkdocs.yml`: Disabled navigation.tabs
- `extra.css`: Added main section spacing CSS
- `design.md`: Added Skill-First design proposal

### Commits
- Left Sidebar fix deployed
- Skill-First design documented

---

## Version 1.1.0: Sidebar Enhancement Options (Reverted)

**Date:** 2026-02-05

### Changes

**Added:**
- **Option A:** Main section styling - prominent headers with icons, colors, uppercase text
- **Option B:** Gradient separators between main sections
- **Option C:** Header Tabs - show 6 main sections in header

**Files Modified:**
- `extra.css`: Added 123 lines for sidebar enhancement (3269 → 3393 lines)
- `header.html`: Added `{% include "partials/tabs.html" %}` for tabs

**Purpose:**
- User สามารถดูของจริงทั้ง 3 ตัวเลือกและตัดสินใจได้

### Commits
- `88502d8` - feat(sidebar): Add Options A, B, C for Left Sidebar Enhancement

---

## Version 1.0.0: Clean Slate Release

**Date:** 2026-02-05

### Changes

**Removed:**
- `toc.html`: Section Navigator code (25 → 13 lines)
- `main.html`: JavaScript injection (140 → 25 lines)
- `extra.css`: Section Navigator CSS (1625 lines removed)

**Added:**
- `design.md`: Design documentation
- `TODO.md`: Task tracking
- `changelog.md`: This file

**Fixed:**
- ไม่มี "Sections" duplicate ใน left sidebar อีกต่อไป
- Left sidebar แสดง 6 หัวข้อหลักตาม Material theme default

**Known Issues:**
- Header tabs ไม่แสดง (header.html override ไม่มี tabs include)
- หัวข้อหลักใน left sidebar อาจต้องการ styling เพิ่มเติม

### Commits
- `fef1a9d` - fix: Remove ALL Section Navigator - Clean slate for redesign
- `055aa93` - fix(sidebar): v3.0 Clean Redesign - Section Navigator via JS injection
- `467acff` - fix(css): v1.9.19 - Match Section Navigator styling to left sidebar

---

## Previous Versions (Before Clean Slate)

### Section Navigator History (Removed)

| Version | Description |
|---------|-------------|
| v1.0 - v1.9 | Initial Section Navigator implementations |
| v2.0 | HTML structure matching TOC |
| v3.0 | JavaScript injection approach |
| Removed | All code deleted for clean slate |

---

> **Design:** [design.md](design.md)
