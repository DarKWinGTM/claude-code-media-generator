# GitHub Pages Design - Claude Code Media Generator

## 0) Document Control

> **Parent Scope:** Claude Code Media Generator Project
> **Current Version:** 2.1
> **Session:** (current session) (2026-02-05)

---

## 1) Overview

This document defines the UI/UX design specifications for the GitHub Pages documentation site at:
**https://darkwingtm.github.io/claude-code-media-generator/**

### Technology Stack

| Component | Technology |
|-----------|------------|
| **Static Site Generator** | MkDocs Material |
| **Hosting** | GitHub Pages |
| **Branch** | `pages` (git worktree) |
| **Theme** | Material for MkDocs |
| **CSS** | Custom `extra.css` |

### Design Style

| Aspect | Choice |
|--------|--------|
| **Primary Style** | Glassmorphism + Gradient |
| **Secondary Style** | Bento Grid, Modern Cards |
| **Color Scheme** | Indigo (#818cf8) + Cyan (#22d3ee) |
| **Typography** | Plus Jakarta Sans + JetBrains Mono |

---

## 2) Issue Tracking

### ✅ Resolved Issues (v1.1 - 2026-01-27)

All 25 issues from MCP analysis have been resolved in Phases 1-4.

| Phase | Issues | Status |
|-------|--------|--------|
| **Phase 1: Critical** | #1-4 | ✅ Complete |
| **Phase 2: High Priority** | #5-10 | ✅ Complete |
| **Phase 3: Medium Priority** | #11-18 | ✅ Complete |
| **Phase 4: Polish** | #19-25 | ✅ Complete |

### Additional MCP Re-analysis Fixes (v1.1)

| Issue | Solution Applied |
|-------|------------------|
| Hero text contrast (white on light purple) | Dark solid background (#0d0d14) + white text |
| Color too vibrant | Subdued palette: #818cf8 (was #a78bfa) |
| Cards lack definition | Visible borders rgba(129, 140, 248, 0.2) + stronger shadows |
| Inconsistent spacing | Section dividers, max-width 900px, better margins |
| Typography hierarchy weak | Larger sizes: h2=2rem, h3=1.5rem, h4=1.25rem, h5=1.125rem |
| Light mode issues | Purple-tinted borders, proper card backgrounds |

---

## 3) Design Specifications

### 3.1 Color Palette (Updated v1.1)

| Color | Hex | Usage |
|-------|-----|-------|
| **Primary** | #818cf8 (indigo-400) | Buttons, accents, links |
| **Secondary** | #22d3ee (cyan-400) | Highlights, gradients |
| **Accent** | #f472b6 (pink-400) | Special elements |
| **Background Dark** | #0a0a0f | Dark mode bg |
| **Background Light** | #ffffff | Light mode bg |
| **Text Dark** | #f1f5f9 (slate-100) | Dark mode text |
| **Text Light** | #0f172a (slate-900) | Light mode text |
| **Hero Background** | #0d0d14 | Hero section bg |
| **Card Border** | rgba(129, 140, 248, 0.2) | Card borders |

### 3.2 Typography (Updated v2.1 - Metro Cyberpunk)

**Dark Mode (Cyberpunk Theme)**:

| Element | Font | Size | Weight | Notes |
|---------|------|------|--------|-------|
| **H1** | Space Grotesk | 3rem | 800 | + Blinking `_` cursor |
| **H2** | Space Grotesk | 2rem | 700 | `## ` prefix, Neon Cyan |
| **H3** | Space Grotesk | 1.5rem | 600 | `### ` prefix |
| **H4-H6** | Space Grotesk | 1.25-1rem | 600 | Standard |
| **Body** | DM Sans | 1rem | 400 | Line-height 1.625 |
| **Code** | Fira Code | 0.875rem | 400 | `> TERMINAL` header |
| **Logo** | Share Tech Mono | 1rem | 400 | `// ` prefix, Neon Cyan |
| **Nav Tabs** | Fira Code | 0.8rem | 500 | Mono style |
| **Sidebar Headers** | Space Grotesk | 0.72rem | 700 | Uppercase |
| **Section Title** | Share Tech Mono | 0.65rem | 700 | `> ` prefix, HUD style |
| **TOC Title** | Share Tech Mono | 0.6rem | 400 | Uppercase, border |
| **Table Headers** | Share Tech Mono | 0.75rem | 400 | Uppercase, Neon Cyan |
| **Buttons** | Fira Code | 0.85rem | 500 | Uppercase |

**Light Mode** (Fallback - Uses Plus Jakarta Sans):

| Element | Font | Size | Weight | Line Height |
|---------|------|------|--------|-------------|
| **H1** | Plus Jakarta Sans | 3rem | 800 | 1.2 |
| **H2** | Plus Jakarta Sans | 2rem | 700 | 1.3 |
| **H3** | Plus Jakarta Sans | 1.5rem | 600 | 1.4 |
| **H4** | Plus Jakarta Sans | 1.25rem | 600 | 1.5 |
| **H5** | Plus Jakarta Sans | 1.125rem | 600 | 1.5 |
| **Body** | Plus Jakarta Sans | 1rem | 400 | 1.625 |
| **Code** | JetBrains Mono | 0.875rem | 400 | 1.7 |

### 3.3 Spacing Scale

| Name | Value | Usage |
|------|-------|-------|
| **xs** | 0.25rem (4px) | Tight spacing |
| **sm** | 0.5rem (8px) | Icon gaps |
| **md** | 1rem (16px) | Card padding |
| **lg** | 1.5rem (24px) | Section gaps |
| **xl** | 2rem (32px) | Major sections |
| **2xl** | 3rem (48px) | Section dividers |
| **3xl** | 4rem (64px) | Hero spacing |

### 3.4 Card Styles (Updated v1.1)

```css
/* Professional Card with Strong Definition */
.grid.cards > li {
  background: rgba(15, 15, 25, 0.6);
  backdrop-filter: blur(20px);
  border: 1px solid rgba(129, 140, 248, 0.2);
  border-radius: 16px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.4);
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

.grid.cards > li:hover {
  transform: translateY(-8px) scale(1.02);
  border-color: rgba(129, 140, 248, 0.4);
  box-shadow: 0 16px 48px rgba(0, 0, 0, 0.5), 0 0 30px rgba(99, 102, 241, 0.15);
}

/* Light mode */
[data-md-color-scheme="default"] .grid.cards > li {
  background: rgba(255, 255, 255, 0.9);
  border-color: rgba(99, 102, 241, 0.15);
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}
```

### 3.5 Hero Section (Updated v1.1)

```css
/* Professional Dark Hero */
.hero-section {
  background:
    radial-gradient(ellipse 80% 60% at 50% -10%, rgba(99, 102, 241, 0.25), transparent 60%),
    radial-gradient(ellipse 60% 50% at 90% 10%, rgba(6, 182, 212, 0.15), transparent 50%),
    linear-gradient(180deg, #0d0d14 0%, #0a0a0f 100%);
  border-bottom: 1px solid rgba(139, 92, 246, 0.3);
}

.md-typeset h1 {
  color: #ffffff;
  text-shadow: 0 2px 20px rgba(139, 92, 246, 0.4);
}

.hero-section p {
  color: #cbd5e1;
  font-size: 1.25rem;
}
```

---

## 4) Files Structure

```
pages/                              # pages branch
├── docs/                           # Content
│   ├── index.md                    # Homepage
│   ├── stylesheets/
│   │   └── extra.css               # Custom styles (~1200 lines)
│   ├── getting-started/
│   ├── video/
│   ├── image/
│   ├── cli/
│   └── guides/
├── overrides/                      # Theme overrides
│   ├── main.html
│   └── partials/
│       ├── header.html
│       └── footer.html
├── mkdocs.yml                      # Configuration
└── .github/
    └── workflows/
        └── docs.yml                # Auto-deploy
```

---

## 5) Implementation Status

### ✅ All Phases Complete (v1.1)

| Phase | Items | Status | Commit |
|-------|-------|--------|--------|
| **Phase 1** | Critical Fixes (4 items) | ✅ | `1d6ad58` |
| **Phase 2** | High Priority (6 items) | ✅ | `5465556` |
| **Phase 3** | Medium Priority (8 items) | ✅ | `00bc73a` |
| **Phase 4** | Polish (7 items) | ✅ | `00bc73a` |
| **MCP Re-analysis** | Professional Overhaul (6 items) | ✅ | `b4b6596` |

---

## 6) Quality Metrics

| Metric | Target | Current |
|--------|--------|---------|
| **Text Contrast** | ≥4.5:1 (WCAG AA) | ✅ Achieved |
| **Lighthouse Accessibility** | ≥90 | TBD |
| **Lighthouse Performance** | ≥90 | TBD |
| **Mobile Responsiveness** | 320px-1440px | ✅ Implemented |
| **Code Highlighting** | Full syntax colors | ✅ Monokai Pro |
| **Interactive Feedback** | All elements | ✅ Complete |
| **Card Definition** | Visible borders + shadows | ✅ Implemented |
| **Typography Hierarchy** | Clear H1-H5 distinction | ✅ Implemented |

---

## 7) Layout Architecture (v1.2 - CSS v1.8.1)

### 7.1 Sidebar + Footer Layout

**Design Principle:** Sidebar always above Footer, consistent in both Dark and Light modes.

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

### 7.2 Technical Implementation

| Component | Property | Value |
|-----------|----------|-------|
| .md-sidebar | position | fixed |
| .md-sidebar | z-index | 100 |
| .md-sidebar | top | var(--md-header-height, 3rem) |
| .md-sidebar | height | calc(100vh - header height) |
| .md-sidebar--primary | left | 0 |
| .md-sidebar--primary | width | 12.1rem |
| .md-sidebar--secondary | right | 0 |
| .md-sidebar--secondary | width | 12.1rem |
| .md-sidebar__inner | padding-top | 0.6rem |
| .md-footer | margin-left | 12.5rem |
| .md-footer | margin-right | 12.5rem |
| .md-footer | z-index | 50 |

### 7.3 JavaScript Dynamic Height

```javascript
function fixSidebarPosition() {
  const header = document.querySelector('.md-header');
  const headerHeight = header ? header.offsetHeight + 'px' : '48px';

  sidebars.forEach(sidebar => {
    sidebar.style.top = headerHeight;
    sidebar.style.height = `calc(100vh - ${headerHeight})`;
  });
}
```

---

## 8) Git Workflow & Branch Strategy (v1.4)

### 8.1 Branch Architecture

**⚠️ CRITICAL: Two separate branches with different purposes**

| Branch | Purpose | Location | Content |
|--------|---------|----------|---------|
| **main** | Source code, design docs, changelogs | Project root | Python scripts, design/, changelog/, README.md |
| **pages** | MkDocs documentation site | Git worktree at `./pages/` | docs/, mkdocs.yml, overrides/ |

### 8.2 Git Worktree Setup

```
claude-code-media-generator/     # main branch
├── design/                      # ✅ Track in main
├── changelog/                   # ✅ Track in main
├── src/                         # ✅ Track in main
├── README.md                    # ✅ Track in main
└── pages/                       # ⚠️ Git worktree (pages branch)
    ├── docs/                    # ✅ Track in pages ONLY
    ├── mkdocs.yml               # ✅ Track in pages ONLY
    └── overrides/               # ✅ Track in pages ONLY
```

### 8.3 ⛔ PROHIBITED Actions

| Action | Why Prohibited |
|--------|----------------|
| `git add pages/*` ใน main branch | pages/ เป็น worktree แยก ไม่ควร track ใน main |
| `git add -f pages/*` ใน main branch | แม้ใช้ `-f` ก็ห้าม เพราะจะทำให้ files ซ้ำซ้อน |
| Commit `pages/docs/stylesheets/extra.css` ใน main | CSS อยู่ใน pages branch เท่านั้น |

### 8.4 ✅ Correct Git Workflow

**เมื่อแก้ไข CSS หรือ MkDocs content:**

```bash
# Step 1: เข้าไปใน pages worktree
cd pages/

# Step 2: ตรวจสอบว่าอยู่ถูก branch
git branch
# Output: * pages

# Step 3: Add, commit, push ใน pages branch
git add docs/stylesheets/extra.css
git commit -m "CSS v1.8.x: Description"
git push origin pages
```

**เมื่อแก้ไข design docs หรือ changelog:**

```bash
# Step 1: อยู่ที่ project root (main branch)
cd /path/to/claude-code-media-generator/

# Step 2: ตรวจสอบว่าอยู่ถูก branch
git branch
# Output: * main

# Step 3: Add, commit, push ใน main branch
git add design/pages.design.md changelog/pages.changelog.md
git commit -m "docs: Update design and changelog"
git push origin main
```

### 8.5 Branch-File Mapping

| File/Folder | ✅ Correct Branch | ❌ Wrong Branch |
|-------------|-------------------|-----------------|
| `design/*.design.md` | main | pages |
| `changelog/*.changelog.md` | main | pages |
| `README.md` | main | pages |
| `pages/docs/*` | pages | main |
| `pages/mkdocs.yml` | pages | main |
| `pages/overrides/*` | pages | main |
| `pages/docs/stylesheets/extra.css` | pages | main |

### 8.6 Verification Commands

```bash
# ตรวจสอบว่า pages/ ถูก ignore ใน main
cat .gitignore | grep pages

# ตรวจสอบว่าไม่มี pages files ถูก track ใน main
git ls-files | grep "^pages/"
# Should return empty

# ตรวจสอบ worktree status
git worktree list
```

### 8.7 Recovery: ถ้า commit pages files ไป main ผิดพลาด

```bash
# Step 1: ลบ pages files ออกจาก git tracking (ไม่ลบ file จริง)
git rm --cached pages/docs/stylesheets/extra.css

# Step 2: Commit การลบ
git commit -m "chore: Remove pages/ files from main branch tracking"

# Step 3: Push
git push origin main
```

---

## 9) Content Updates (v1.5)

### 9.1 Required Updates - February 2026

Based on Skill v3.2.1 release, the following pages need content updates:

#### Priority 1: Critical Updates

| File | Update Required | Details |
|------|-----------------|---------|
| `index.md` | Version table | Update all component versions |
| `installation.md` | Skill installation | Add Claude Code Skill section |
| `authentication.md` | Config wizard | Add `/generative config` reference |

#### Priority 2: New Navigation

| Change | Location | Details |
|--------|----------|---------|
| Add skill guide | `mkdocs.yml` nav | `guides/skill-installation.md` |

### 9.2 Version Updates Required

| Component | Current (Pages) | Latest | Update To |
|-----------|-----------------|--------|-----------|
| video_gen.py | v2.26 | v2.27 | **v2.27** |
| image_gen.py | v1.3 | v1.4 | **v1.4** |
| Documentation | v3.8 | v3.10 | **v3.10** |
| Claude Code Skill | "Phase 3 Planned" | v3.2.1 | **v3.2.1 Available** |

### 9.3 New Pages Required

| New Page | Content |
|----------|---------|
| `guides/skill-installation.md` | Full Claude Code Skill installation guide (Windows/Linux) |

### 9.4 File Structure Updates

**Current (Outdated):**
```
├── config_loader.py    # OLD - no longer exists
```

**Updated:**
```
├── config.py           # Configuration module
├── video_utils.py      # Video utilities
```

### 9.5 Implementation Checklist

- [ ] Update `index.md` status table
- [ ] Update `installation.md` file structure
- [ ] Add Skill installation to `installation.md`
- [ ] Create `guides/skill-installation.md`
- [ ] Add navigation entry to `mkdocs.yml`
- [ ] Update `authentication.md` with config wizard reference

---

## 10) Section Navigator Feature (v1.6.3 - Implemented)

### 10.1 Overview

**Purpose:** เพิ่ม Section Navigator ใน Right Sidebar และ Table of Contents ใน Left Sidebar เพื่อให้ผู้ใช้สามารถ navigate ได้รวดเร็ว

**Problem Statement:**
- ปัจจุบัน ผู้ใช้ต้องไล่ดู Left Sidebar ทีละหัวข้อ
- ไม่มี quick access ไปยัง major sections
- Mobile experience ขาด section navigation

**Current Layout (v1.3):**
- **LEFT Sidebar**: Table of Contents (หน้าปัจจุบัน)
- **RIGHT Sidebar**: Section Navigator (major sections)
- **Theme Default**: Dark Mode เสมอ (ไม่ follow system preference)

### 10.2 Design Specification

#### Target Sections

| Section | Icon | Link |
|---------|------|------|
| Getting Started | 🚀 | `/getting-started/installation/` |
| Video Generation | 🎬 | `/video/overview/` |
| Image Generation | 🖼️ | `/image/` |
| CLI Reference | 💻 | `/cli/video_gen/` |
| Guides | 📖 | `/guides/skill-installation/` |

#### Sidebar Layout (v1.3)

| Sidebar | Content | CSS Class |
|---------|---------|-----------|
| **LEFT (Primary)** | Table of Contents | `.toc-for-left-sidebar` |
| **RIGHT (Secondary)** | Section Navigator | `.section-navigator-sidebar` |

#### Responsive Behavior

| Breakpoint | Screen Size | Navigator Location |
|------------|-------------|-------------------|
| Desktop | ≥1220px | Right Sidebar (above TOC) |
| Tablet | 960-1219px | Inline in content area |
| Mobile | <960px | Inline in content area (compact) |

### 10.3 Technical Implementation

#### 10.3.1 mkdocs.yml Changes (v1.3)

```yaml
theme:
  palette:
    # Dark mode (ALWAYS default - modern feel)
    - scheme: slate
      primary: deep purple
      accent: cyan
      toggle:
        icon: material/weather-sunny
        name: Switch to light mode
    # Light mode (optional toggle)
    - scheme: default
      primary: deep purple
      accent: cyan
      toggle:
        icon: material/weather-night
        name: Switch to dark mode
  features:
    # Note: toc.integrate is DISABLED to enable Right Sidebar
    # - toc.integrate
```

#### 10.3.2 Template Override: `overrides/partials/toc.html` (v1.3)

```html
<!--
  Section Navigator + Table of Contents Template
  Version: 1.3
  Fix v1.1: Removed redundant md-sidebar__scrollwrap wrapper
  Fix v1.2: CSS hides Section Navigator in primary sidebar
  Fix v1.3: TOC moved to left sidebar via CSS, Sections stays in right sidebar
-->

{% if page.toc %}
<!-- Section Navigator - Shows in RIGHT sidebar only -->
<nav class="section-navigator section-navigator-sidebar" aria-label="Section Navigation">
  <span class="section-navigator__title">Sections</span>
  <ul class="section-navigator__list">
    <li>
      <a href="{{ config.site_url }}getting-started/installation/">
        <span class="section-navigator__icon">🚀</span>
        <span>Getting Started</span>
      </a>
    </li>
    <!-- ... other sections ... -->
  </ul>
</nav>

<!-- Table of Contents - Will be moved to LEFT sidebar via CSS -->
<nav class="md-nav md-nav--secondary toc-for-left-sidebar" aria-label="Table of contents">
  <label class="md-nav__title" for="__toc">
    <span class="md-nav__icon md-icon"></span>
    Table of contents
  </label>
  <ul class="md-nav__list" data-md-component="toc" data-md-scrollfix>
    {% for toc_item in page.toc %}
      {% include "partials/toc-item.html" %}
    {% endfor %}
  </ul>
</nav>
{% endif %}
```

#### 10.3.3 CSS: `extra.css` Sidebar Layout (v1.3)

```css
/* ===== SIDEBAR LAYOUT FIX v1.3 ===== */
/* Goal: TOC in LEFT sidebar, Sections in RIGHT sidebar */

/* Hide Section Navigator in Primary (Left) Sidebar */
.md-sidebar--primary .section-navigator,
.md-sidebar--primary .section-navigator-sidebar {
  display: none !important;
}

/* Show Section Navigator in Secondary (Right) Sidebar */
.md-sidebar--secondary .section-navigator-sidebar {
  display: block;
}

/* Hide TOC in Secondary (Right) Sidebar - only show Sections there */
.md-sidebar--secondary .toc-for-left-sidebar {
  display: none !important;
}

/* Show TOC in Primary (Left) Sidebar */
.md-sidebar--primary .toc-for-left-sidebar {
  display: block !important;
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid rgba(129, 140, 248, 0.15);
}

/* Style TOC in left sidebar */
.md-sidebar--primary .toc-for-left-sidebar .md-nav__title {
  font-size: 0.7rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.1em;
  color: var(--text-muted, #94a3b8);
  padding: 0 0.6rem;
  margin-bottom: 0.5rem;
}
```

### 10.4 Implementation Checklist

- [x] Disable `toc.integrate` in mkdocs.yml
- [x] Create `overrides/partials/toc.html`
- [x] Add Section Navigator CSS to `extra.css`
- [x] Fix toc.html redundant wrapper bug (v1.1 - 2026-02-05)
- [x] Fix Section Navigator showing in both sidebars (v1.2 - 2026-02-05)
- [x] Move TOC to left sidebar, keep Sections in right (v1.3 - 2026-02-05)
- [x] Set Dark mode as default theme (v1.3 - 2026-02-05)
- [ ] Add inline navigator component (for mobile fallback)
- [ ] Test on Desktop (≥1220px)
- [ ] Test on Tablet (960-1219px)
- [ ] Test on Mobile (<960px)
- [ ] Verify all navigation links work

### 10.5 Quality Metrics

| Metric | Target | Current |
|--------|--------|---------|
| Desktop visibility | 100% (always visible in sidebar) | ✅ Achieved |
| TOC location | Left sidebar | ✅ Achieved |
| Sections location | Right sidebar only | ✅ Achieved |
| Dark theme default | Always dark first | ✅ Achieved |
| Link accuracy | 100% (all links work) | TBD |
| Responsive transitions | Smooth (no layout shifts) | TBD |

### 10.6 Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.3 | 2026-02-05 | TOC → left sidebar, Sections → right sidebar, Dark default |
| 1.2 | 2026-02-05 | CSS hides Section Navigator in primary sidebar |
| 1.1 | 2026-02-05 | Fixed redundant wrapper bug |
| 1.0 | 2026-02-04 | Initial design |

---

## 11) Metro Cyberpunk Theme (v1.8 - Planned)

### 11.1 Overview

**Purpose:** ปรับ theme ทั้งหน้าเว็บให้มีความ Metro Cyberpunk อย่างเต็มรูปแบบ

**Design Direction:**
- Option 2: Metro Cyberpunk (Balanced - ชัดเจนแต่อ่านง่าย)
- Terminal-style index จาก Option 3: `[1] [2] [3]` และ `> NAVIGATE_`
- Demo file: `demo-metro-cyberpunk-full.html`

### 11.2 Color Palette (Cyberpunk)

| Color | Hex | CSS Variable | Usage |
|-------|-----|--------------|-------|
| **Neon Cyan** | #00ffff | `--neon-cyan` | Primary accent, links |
| **Neon Magenta** | #ff00ff | `--neon-magenta` | Active states, highlights |
| **Neon Green** | #00ff00 | `--neon-green` | Success, terminal style |
| **Deep Black** | #0a0a0f | `--cyber-bg-deep` | Main background |
| **Midnight** | #0f0f1a | `--cyber-bg-mid` | Cards, sidebars |
| **Dark Surface** | #151520 | `--cyber-bg-surface` | Elevated surfaces |
| **Cyber Purple** | #8b5cf6 | `--cyber-accent` | Secondary accent |

### 11.3 Typography

| Element | Font | Usage |
|---------|------|-------|
| **HUD Display** | Share Tech Mono | Section titles, navigation |
| **Code** | Fira Code | Code blocks, monospace |
| **Body** | Space Grotesk | General text |

### 11.4 UI Components

#### 11.4.1 Navigation with Terminal Index

```html
<div class="nav-section-title">NAVIGATE_</div>
<ul class="nav-menu">
  <li><a href="#"><span class="nav-index">[1]</span> Getting Started</a></li>
  <li><a href="#"><span class="nav-index">[2]</span> Video Generation</a></li>
  ...
</ul>
```

#### 11.4.2 Neon Glow Effects

```css
.nav-item:hover {
  color: #00ffff;
  text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
  box-shadow: 0 0 15px rgba(0, 255, 255, 0.3);
}

.nav-item.active {
  color: #ff00ff;
  text-shadow: 0 0 15px rgba(255, 0, 255, 0.6);
}
```

#### 11.4.3 Scanline Effect (Optional)

```css
.cyber-panel::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: repeating-linear-gradient(
    0deg,
    rgba(0, 0, 0, 0.1) 0px,
    rgba(0, 0, 0, 0.1) 1px,
    transparent 1px,
    transparent 3px
  );
  pointer-events: none;
  opacity: 0.3;
}
```

### 11.5 Responsive Behavior

| Breakpoint | Screen Size | Behavior |
|------------|-------------|----------|
| Desktop | ≥1220px | Full effects, glow, scanlines |
| Tablet | 960-1219px | Reduced glow, no scanlines |
| Mobile | <960px | Minimal effects for performance |

### 11.6 Performance Considerations

| Effect | Performance Impact | Fallback |
|--------|-------------------|----------|
| Neon glow | Low | Solid color |
| Scanlines | Medium | Disable on mobile |
| Text shadow | Low | Keep always |
| Animation pulse | Low | Reduce frequency on mobile |

### 11.7 Implementation Checklist

- [x] Add CSS custom properties for Cyberpunk colors (v1.9.0)
- [x] Implement terminal-style navigation index (v1.9.0)
- [x] Add neon glow effects for interactive elements (v1.9.0)
- [x] Implement scanline overlay (optional) (v1.9.0)
- [x] Update typography to HUD fonts (v1.9.0)
- [ ] Test accessibility (contrast ratios)
- [ ] Test performance on mobile devices
- [ ] Create light mode variant (optional)

### 11.8 Quality Metrics

| Metric | Target |
|--------|--------|
| Cyberpunk feel | 4/5 stars |
| Readability | 4/5 stars |
| Performance | Lighthouse ≥85 |
| Accessibility | WCAG AA (contrast ≥4.5:1) |

---

> Full history: [pages.changelog.md](../changelog/pages.changelog.md)
