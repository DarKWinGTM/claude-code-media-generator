# Changelog - GitHub Pages Design

> **Parent Document:** [pages.design.md](../design/pages.design.md)
> **Current Version:** 2.0
> **Session:** (current session)

---

## Version History

### Version 2.0 (2026-02-05)

**Design v1.8: Metro Cyberpunk Theme + Section Navigator Fix**

**Session:** (current session)

#### Changes

- **Section 11: Metro Cyberpunk Theme** - New full-page Cyberpunk theme design
  - Option 2: Metro Cyberpunk selected (Balanced - ชัดเจนแต่อ่านง่าย)
  - Terminal-style index from Option 3: `[1] [2] [3]` และ `> NAVIGATE_`
  - Color palette: Neon Cyan (#00ffff), Neon Magenta (#ff00ff), Neon Green (#00ff00)
  - Typography: Share Tech Mono, Fira Code, Space Grotesk
  - Neon glow effects, optional scanlines
  - Responsive behavior for desktop/tablet/mobile
  - Performance considerations and accessibility targets

- **Section 10 Updated** - Marked as Implemented (was Planned)

- **toc.html Bug Fix (v1.1)** - Fixed redundant wrapper
  - Removed duplicate `md-sidebar__scrollwrap` wrapper
  - MkDocs Material already provides this wrapper
  - This fix resolves the sidebar showing same content on left and right issue

- **Demo Files Created**:
  - `demo-cyberpunk-styles.html` - 3 options comparison demo
  - `demo-metro-cyberpunk-full.html` - Full page Metro Cyberpunk demo

#### Files Modified

| File | Changes |
|------|---------|
| `design/pages.design.md` | v1.8: Added Section 11, updated Section 10 |
| `pages/overrides/partials/toc.html` | v1.1: Removed redundant wrapper |
| `pages/demo-cyberpunk-styles.html` | NEW: 3 options demo |
| `pages/demo-metro-cyberpunk-full.html` | NEW: Full theme demo |
| `TODO.md` | v3.3: Added Metro Cyberpunk tasks |
| `changelog/pages.changelog.md` | This entry |

#### Summary

Designed Metro Cyberpunk theme for entire site and fixed sidebar bug from Section Navigator implementation.

---

### Version 1.9 (2026-02-04)

**Design v1.6: Section Navigator Feature**

**Session:** (current session)

#### Changes

- **Added Section 10: Section Navigator Feature** - New responsive navigation component
  - 10.1 Overview: Problem statement and purpose
  - 10.2 Design Specification: Target sections, responsive behavior
  - 10.3 Technical Implementation: mkdocs.yml, toc.html, CSS
  - 10.4 Implementation Checklist
  - 10.5 Quality Metrics
- **Responsive Design Strategy:**
  - Desktop (≥1220px): Right Sidebar above TOC
  - Tablet (960-1219px): Inline in content area
  - Mobile (<960px): Inline compact mode
- **Updated TODO.md**: Added Section Navigator implementation tasks

#### Files Modified

| File | Changes |
|------|---------|
| `design/pages.design.md` | Added Section 10, updated version to 1.6 |
| `TODO.md` | Added Section Navigator tasks |
| `changelog/pages.changelog.md` | This entry |

#### Summary

Designed Section Navigator feature with responsive layout for all screen sizes.

---

### Version 1.8 (2026-02-04)

**Content Updates v1.5: Claude Code Skill Documentation**

**Session:** (current session)

#### Changes

- **Updated Component Versions**: Updated all outdated versions in `index.md`
  - video_gen.py: v2.26 → v2.27
  - image_gen.py: v1.3 → v1.4
  - Documentation: v3.8 → v3.10
  - Claude Code Skill: "Phase 3 Planned" → v3.2.1 Available
- **Created `guides/skill-installation.md`**: Full Claude Code Skill installation guide
  - Linux/macOS installation with symlink option
  - Windows PowerShell installation
  - Windows CMD installation
  - Skill commands reference
  - Config sub-commands documentation
  - Troubleshooting section
- **Updated `installation.md`**: Fixed file structure (config.py, video_utils.py)
- **Updated `authentication.md`**: Added `/generative config setup` wizard tip
- **Updated `mkdocs.yml`**: Added Skill Installation to Guides navigation
- **Updated Design**: Added Section 9 (Content Updates v1.5) to pages.design.md

#### Files Modified

| File | Changes |
|------|---------|
| `pages/docs/index.md` | Version table updated |
| `pages/docs/getting-started/installation.md` | File structure + skill link |
| `pages/docs/getting-started/authentication.md` | Config wizard tip + skill link |
| `pages/docs/guides/skill-installation.md` | NEW - Full skill guide |
| `pages/mkdocs.yml` | Added navigation entry |
| `design/pages.design.md` | Section 9 added |

#### Summary

Added comprehensive Claude Code Skill documentation with installation guides for Windows/Linux.

---

### Version 1.7 (2026-01-30)

**Design v1.4: Added Git Workflow & Branch Strategy Documentation**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

#### Changes

- **Section 8: Git Workflow & Branch Strategy**: เพิ่ม documentation ครบถ้วนเกี่ยวกับ git workflow
  - 8.1 Branch Architecture: อธิบาย main vs pages branch
  - 8.2 Git Worktree Setup: แสดงโครงสร้าง directory
  - 8.3 ⛔ PROHIBITED Actions: สิ่งที่ห้ามทำ
  - 8.4 ✅ Correct Git Workflow: วิธีการที่ถูกต้อง
  - 8.5 Branch-File Mapping: ตาราง file → branch
  - 8.6 Verification Commands: คำสั่งตรวจสอบ
  - 8.7 Recovery: วิธีแก้ไขถ้าทำผิด

#### Rationale

ป้องกันปัญหาที่เคยเกิดขึ้น:
- `pages/docs/stylesheets/extra.css` ถูก commit ไป main branch ผิดพลาด
- ใช้ `git add -f` โดยไม่ตรวจสอบ branch
- ไม่เข้าใจว่า `pages/` เป็น git worktree แยกต่างหาก

#### Commits

| Commit | Description |
|--------|-------------|
| TBD | docs: Add Git Workflow & Branch Strategy section |

---

### Version 1.6 (2026-01-30)

**CSS v1.8.3: Explicit Button ::after Reset**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

#### Changes

- **Explicit Button ::after Reset**: เพิ่ม rule เฉพาะเพื่อปิด underline effect บน `.md-button`
  - `.md-button::after, .md-button::before { display: none !important; content: none !important; }`
  - ป้องกัน inheritance จาก `.md-typeset a::after`
- **Position Relative**: เพิ่ม `position: relative` ใน `.md-button` base styles

#### Technical Implementation

| Selector | Property | Value |
|----------|----------|-------|
| `.md-button::after, .md-button::before` | display | none !important |
| `.md-button::after, .md-button::before` | content | none !important |
| `.md-button::after, .md-button::before` | width | 0 !important |
| `.md-button::after, .md-button::before` | height | 0 !important |
| `.md-button` | position | relative |

#### Commits

| Commit | Description |
|--------|-------------|
| TBD | CSS v1.8.3 Explicit Button ::after Reset |

---

### Version 1.5 (2026-01-29)

**CSS v1.8.2: Prominent Sidebar Headers + Fix Button Underline**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

#### Changes

- **Prominent Section Headers**: Sidebar หัวข้อหลัก (Video Generation, Getting Started) โดดเด่นขึ้น
  - Uppercase text-transform
  - Bold font-weight (700)
  - Accent border-left (3px)
  - Letter-spacing for readability
- **Fix Button Underline Animation**: ลบ underline animation จาก `.md-button`
  - เปลี่ยน selector เป็น `.md-typeset a:not(.md-button):not(.md-nav__link)...`
- **Light Mode Support**: เพิ่ม styles สำหรับ Light Mode ครบถ้วน

#### Technical Implementation

| Component | Property | Dark Mode | Light Mode |
|-----------|----------|-----------|------------|
| Section header | font-weight | 700 | 700 |
| Section header | text-transform | uppercase | uppercase |
| Section header | border-left | 3px solid cyan | 3px solid indigo |
| Section header | color | #818cf8 | #4F46E5 |
| Sub-item | font-weight | 400 | 400 |
| Sub-item | padding-left | 1.5rem | 1.5rem |

#### Commits

| Commit | Description |
|--------|-------------|
| TBD | CSS v1.8.2 Prominent Sidebar Headers + Fix Button Underline |

---

### Version 1.4 (2026-01-28)

**CSS v1.8.1: Dynamic Header Height + Reduced Sidebar Padding**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

#### Changes

- **Dynamic Header Height**: Changed from hardcoded `3.5rem` to `var(--md-header-height, 3rem)`
- **JavaScript Header Detection**: Reads actual `header.offsetHeight` for precise positioning
- **Reduced Sidebar Padding**: `.md-sidebar__inner` padding reduced from 1.2rem to 0.6rem
- **Fixed Header-Sidebar Gap**: Eliminated gap between header and sidebar

#### Problem Solved

Before v1.8.1:
- Hardcoded `top: 3.5rem` (56px) didn't match actual header height
- Visible gap between header and sidebar

After v1.8.1:
- CSS uses `var(--md-header-height, 3rem)` for flexibility
- JavaScript reads actual header height dynamically
- No gap between header and sidebar

#### Technical Implementation

| Component | Property | Value |
|-----------|----------|-------|
| .md-sidebar | top | var(--md-header-height, 3rem) |
| .md-sidebar | height | calc(100vh - var(--md-header-height, 3rem)) |
| .md-sidebar__inner | padding-top | 0.6rem |
| JavaScript | headerHeight | header.offsetHeight + 'px' |

#### Commits

| Commit | Description |
|--------|-------------|
| `1048fb6` | CSS v1.8.1 Dynamic Header Height + Reduced Padding |

---

### Version 1.3 (2026-01-28)

**CSS v1.8.0: Sidebar + Footer Layout Consistency**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

#### Changes

- **Sidebar z-index: 100**: Sidebar always appears above Footer
- **Footer Centered**: Footer has margin-left/right to avoid sidebar overlap
- **Footer z-index: 50**: Footer appears below Sidebar
- **Consistent Layout**: Same behavior for BOTH Dark and Light Mode
- **Fixed Position**: Sidebar stays fixed to viewport with full height

#### Layout Design

```
Header (md-header)
  ↓
Sidebar (Left)     Main Content     Sidebar (Right)
z-index: 100       scrollable       z-index: 100
position: fixed                     position: fixed
  ↓                    ↓                ↓
  │              Footer (Centered)      │
  │              z-index: 50            │
  │              margin-left: 12.5rem   │
  │              margin-right: 12.5rem  │
  └──────────────────────────────────────┘
  Sidebar extends to bottom of viewport
```

#### Technical Implementation

| Component | CSS Property | Value |
|-----------|--------------|-------|
| Sidebar | position | fixed |
| Sidebar | z-index | 100 |
| Sidebar | height | calc(100vh - header height) |
| Footer | margin-left/right | 12.5rem |
| Footer | z-index | 50 |

#### Commits

| Commit | Description |
|--------|-------------|
| `edbb1d3` | CSS v1.8.0 Sidebar + Footer Layout Consistency |

---

### Version 1.2 (2026-01-27)

**CSS v1.6 Professional Design System - Complete Light Mode Fixes**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

#### Changes

- Complete Light Mode redesign with WCAG AA compliance
- Header: Dark text (#0f172a) on light background with blur effect
- Navigation tabs: Semi-transparent white background with subtle borders
- Sidebar: Professional styling with proper text colors (#334155, #1E293B)
- Grid cards: Indigo icons (#6366f1) with proper hover states
- Tables: Fixed-width standardization (100% width, table-layout: fixed)
- Search input: Visible borders and proper text contrast
- Source/GitHub: Dark text colors for visibility
- Buttons: Consistent styling and alignment
- Code blocks, admonitions, footer: All properly styled for Light Mode

#### Technical Implementation

| Component | Light Mode Value |
|-----------|------------------|
| Header background | rgba(248, 250, 252, 0.95) |
| Header text | #0f172a |
| Tab background | rgba(255, 255, 255, 0.8) |
| Sidebar background | rgba(248, 250, 252, 0.95) |
| Primary color | #6366F1 (Indigo) |
| Text primary | #1E293B |
| Text secondary | #334155 |
| Border color | #E2E8F0 |

#### Commits

| Commit | Description |
|--------|-------------|
| `7c8865f` | CSS v1.6 Professional Design System - Complete Light Mode fixes |

---

### Version 1.1 (2026-01-27)

**Professional UI Overhaul - MCP Re-analysis Fixes**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

#### Changes

- Implemented all 25 issues from Phase 1-4
- Fixed MCP re-analysis issues (hero contrast, color vibrancy, card definition)
- Updated color palette: #a78bfa → #818cf8 (more subdued)
- Hero section: Dark solid background (#0d0d14) + white text
- Cards: Visible borders rgba(129, 140, 248, 0.2) + stronger shadows
- Typography: h2=2rem, h3=1.5rem, h4=1.25rem, h5=1.125rem
- Layout: Section dividers, max-width 900px, better margins
- Light mode: Purple-tinted borders, proper card backgrounds

#### Commits

| Commit | Description |
|--------|-------------|
| `1d6ad58` | Phase 1: Critical Fixes - Syntax highlighting, text contrast, tables |
| `5465556` | Phase 2: High Priority - Card hover, heading hierarchy, spacing |
| `00bc73a` | Phase 3 & 4: Medium Priority + Polish - All remaining 15 items |
| `b4b6596` | Professional UI Overhaul - MCP re-analysis fixes |

#### MCP Re-analysis Issues Resolved

| Issue | Solution |
|-------|----------|
| Hero text contrast | Dark bg (#0d0d14) + white text + text-shadow |
| Color too vibrant | Subdued palette #818cf8 (was #a78bfa) |
| Cards lack definition | Visible borders + box-shadow |
| Inconsistent spacing | Section dividers, better margins |
| Typography weak | Larger sizes, clearer H1-H5 hierarchy |
| Light mode issues | Purple-tinted borders, proper backgrounds |

---

### Version 1.0 (2026-01-27)

**Initial Release - Design Document & Issue Analysis**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

#### Changes

- Created `pages.design.md` with full UI/UX specifications
- Documented 25 design issues from MCP + UI/UX Pro Max analysis
- Defined color palette, typography, and spacing scales
- Established implementation plan (4 phases)
- Set quality metrics and targets

#### Analysis Performed

| Tool | Pages Analyzed |
|------|----------------|
| **MCP analyze_image** | Homepage, Video Overview, Quick Start |
| **UI/UX Pro Max** | product, style, ux, typography domains |

#### Issues Identified

| Priority | Count |
|----------|-------|
| **Critical (Must Fix)** | 4 |
| **High Priority** | 6 |
| **Medium Priority** | 8 |
| **Nice to Have** | 7 |
| **Total** | 25 |

#### Design Decisions

1. **Glassmorphism + Gradient** as primary style (matches Modern SaaS)
2. **Plus Jakarta Sans** for typography (professional, modern)
3. **Purple + Cyan** color scheme (existing brand colors)
4. **WCAG AA compliance** as accessibility target

---

## Version History (Unified)

| Version | Date | Changes | Session ID |
|---------|------|---------|------------|
| 1.8 | 2026-02-04 | **[Content Updates v1.5: Skill Documentation](#version-18-2026-02-04)** | (current) |
| | | - Updated component versions (video_gen, image_gen, skill) | |
| | | - Created guides/skill-installation.md | |
| | | - Updated installation.md, authentication.md, mkdocs.yml | |
| | | Summary: Claude Code Skill documentation for Windows/Linux | |
| 1.7 | 2026-01-30 | **[Design v1.4: Git Workflow Documentation](#version-17-2026-01-30)** | 5584c223... |
| | | - Added Section 8: Git Workflow & Branch Strategy | |
| | | - Branch architecture, worktree setup | |
| | | - Prohibited actions and correct workflow | |
| | | Summary: Complete git workflow documentation | |
| 1.6 | 2026-01-30 | **[CSS v1.8.3 Button ::after Reset](#version-16-2026-01-30)** | 5584c223... |
| | | - Explicit Button ::after Reset | |
| | | Summary: Button underline animation removed | |
| 1.5 | 2026-01-29 | **[CSS v1.8.2 Prominent Sidebar Headers](#version-15-2026-01-29)** | 5584c223... |
| | | - Prominent section headers (uppercase, bold, accent border) | |
| | | - Fix button underline animation | |
| | | - Light Mode support | |
| | | Summary: Sidebar UX improvement + button animation fix | |
| 1.4 | 2026-01-28 | **[CSS v1.8.1 Dynamic Header Height](#version-14-2026-01-28)** | 5584c223... |
| | | - Dynamic header height with CSS variable | |
| | | - JavaScript reads actual header.offsetHeight | |
| | | - Reduced sidebar padding (0.6rem) | |
| | | Summary: Fixed header-sidebar gap with dynamic height | |
| 1.3 | 2026-01-28 | **[CSS v1.8.0 Sidebar + Footer Layout](#version-13-2026-01-28)** | 5584c223... |
| | | - Sidebar z-index: 100 (above footer) | |
| | | - Footer centered with margins | |
| | | - Consistent Dark/Light Mode layout | |
| | | Summary: Sidebar + Footer layout consistency | |
| 1.2 | 2026-01-27 | **[CSS v1.6 Professional Design System](#version-12-2026-01-27)** | 5584c223... |
| | | - Complete Light Mode redesign with WCAG AA compliance | |
| | | - Header, tabs, sidebar, grid cards, tables all fixed | |
| | | - Professional color system (#6366F1 primary) | |
| | | Summary: Complete Light Mode fixes with professional design system | |
| 1.1 | 2026-01-27 | **[Professional UI Overhaul](#version-11-2026-01-27)** | 5584c223... |
| | | - Implemented all 25 issues (Phase 1-4) | |
| | | - Fixed MCP re-analysis issues | |
| | | - Updated color palette, hero, cards, typography | |
| | | Summary: Complete professional overhaul with MCP fixes | |
| 1.0 | 2026-01-27 | **[Initial Release](#version-10-2026-01-27)** | 5584c223... |
| | | - Created design document | |
| | | - Documented 25 issues | |
| | | - Established implementation plan | |
| | | Summary: Initial pages design specs with full issue analysis | |

---

## Previous Updates (from changelog.md v3.9)

### Glassmorphism Modernization (2026-01-27)

**Commits pushed to `pages` branch:**

| Commit | Description |
|--------|-------------|
| `50735c0` | Initial Glassmorphism design + overrides |
| `2b3da48` | Fixed button syntax + workflow paths |
| `3a803d9` | Fixed footer markdown rendering |
| `d325dc6` | Changed :framed_picture: to :frame_with_picture: |
| `c6258ba` | Changed to :material-image: (final fix) |

**Files Modified:**
- `pages/docs/stylesheets/extra.css` - Complete rewrite
- `pages/docs/index.md` - New hero section, bento grid
- `pages/mkdocs.yml` - Font changed to Plus Jakarta Sans
- `pages/overrides/main.html` - SEO meta, animations
- `pages/overrides/partials/footer.html` - Custom footer
- `pages/overrides/partials/header.html` - Gradient overlay

---

> Parent Document: [pages.design.md](../design/pages.design.md)
