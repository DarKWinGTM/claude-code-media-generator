# GitHub Pages Design - Claude Code Media Generator

## 0) Document Control

> **Parent Scope:** Claude Code Media Generator Project
> **Current Version:** 1.4
> **Session:** 5584c223-ebff-4c03-b92c-697360841c5e (2026-01-30)

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

### 3.2 Typography (Updated v1.1)

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

> Full history: [pages.changelog.md](../changelog/pages.changelog.md)
