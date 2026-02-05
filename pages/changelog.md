# Pages Changelog

> **Current Version:** 1.1.0
> **Last Updated:** 2026-02-05

---

## Version History

| Version | Date | Changes | Summary |
|---------|------|---------|---------|
| 1.1.0 | 2026-02-05 | **Sidebar Enhancement Options A, B, C** | เพิ่ม styling, separators, header tabs |
| 1.0.0 | 2026-02-05 | **Clean Slate Release** | ลบ Section Navigator ทั้งหมด, สร้าง documentation files |

---

## Version 1.1.0: Sidebar Enhancement Options

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
