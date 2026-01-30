# Changelog - GitHub Pages Design

> **Parent Document:** [pages.design.md](../design/pages.design.md)
> **Current Version:** 1.6
> **Session:** 5584c223-ebff-4c03-b92c-697360841c5e

---

## Version History

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
