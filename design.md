# Pages Design - Claude Code Media Generator Documentation

> **Current Version:** 1.4.0
> **Last Updated:** 2026-02-05

---

## 1. Overview

MkDocs Material documentation site สำหรับ Claude Code Media Generator project

**Site URL:** https://darkwingtm.github.io/claude-code-media-generator/

---

## 2. Navigation Structure

### 2.1 Current Structure (v1.3.0 - Skill-First Implemented) ✅

| # | Section | URL Path | Sub-pages |
|---|---------|----------|-----------|
| 1 | Home | `/` | - |
| 2 | Getting Started | `/getting-started/` | Installation, Authentication, Quick Start |
| 3 | **Using Skills** ⭐ | `/skills/` | Overview, Commands, Examples, Configuration |
| 4 | Video Generation | `/video/` | Overview, Modes, Models, Reference Images, Extension |
| 5 | Image Generation | `/image/` | Overview |
| 6 | **Advanced Usage** ⭐ | `/advanced/` | Overview, video_gen.py, image_gen.py, check_api.py, Automation |
| 7 | Guides | `/guides/` | GCS Storage, Presets, Troubleshooting |

### 2.2 Skill-First Philosophy

**หลักการ:**
- Skill = วิธีใช้งานหลัก (ง่าย, Claude Code native)
- Script = วิธีใช้ทางเลือก (สำหรับ automation, advanced users)

### 2.3 Sidebar Behavior

**Left Sidebar (Primary):**
- แสดงหัวข้อหลักทั้งหมด (ปิด navigation.tabs แล้ว)
- Expandable/collapsible sections
- Active section expanded อัตโนมัติ
- เพิ่ม padding-bottom: 16px ระหว่างหัวข้อหลัก
- **Background: Transparent** (v1.4.0) ✅

**Right Sidebar (Secondary):**
- Table of Contents ของหน้าปัจจุบัน
- Auto-generated จาก headings
- **Background: Transparent** (v1.4.0) ✅

---

## 3. Theme Configuration

### 3.1 MkDocs Material Features

```yaml
features:
  # - navigation.tabs         # Disabled: ต้องการหัวข้อใน Left Sidebar
  # - navigation.tabs.sticky  # Disabled: No tabs
  - navigation.sections       # Section headers
  - navigation.expand         # Expand sections
  - navigation.path           # Breadcrumb
  - navigation.top            # Back to top button
  - navigation.footer         # Footer navigation
  - navigation.instant        # Instant loading
```

### 3.2 Color Scheme

- **Primary:** Deep Purple
- **Accent:** Cyan
- **Dark Mode:** Slate (default)
- **Light Mode:** Default

---

## 4. Template Overrides

### 4.1 Current Overrides

| File | Purpose | Status |
|------|---------|--------|
| `overrides/main.html` | Base template | Minimal (SEO meta only) |
| `overrides/partials/header.html` | Custom header | Clean (no tabs) |
| `overrides/partials/footer.html` | Custom footer | Active |
| `overrides/partials/toc.html` | TOC template | Clean (Material default) |

---

## 5. CSS Styling

### 5.1 Sidebar Styling (v1.4.0)

| Component | Style | Notes |
|-----------|-------|-------|
| `.md-sidebar` | `background: transparent` | ลบ glass panel ออก |
| Light Mode Sidebar | `background: transparent` | ทั้ง dark และ light mode |
| Sidebar Scrollwrap | `background: transparent` | Inner scroll area |

---

## 6. Design Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Header Tabs | ไม่ใช้ | User preference - ต้องการหัวข้อใน Left Sidebar |
| navigation.tabs | ปิด | ให้ Left Sidebar แสดงหัวข้อหลักทั้งหมด |
| Section Spacing | 16px | เพิ่มระยะห่างระหว่างหัวข้อหลัก |
| **Skill-First** | ใช้ | Skill เป็นวิธีหลัก, Script เป็นทางเลือก |
| **Sidebar Background** | Transparent | Cleaner look (v1.4.0) |

---

## 7. File Structure

```
pages/
├── docs/
│   ├── index.md
│   ├── getting-started/
│   │   ├── installation.md   # Skill-first tabs
│   │   ├── authentication.md
│   │   └── quick-start.md    # Skill-first tabs
│   ├── skills/               # ⭐ NEW (v1.3.0)
│   │   ├── overview.md
│   │   ├── commands.md
│   │   ├── examples.md
│   │   └── configuration.md
│   ├── video/
│   ├── image/
│   ├── advanced/             # ⭐ NEW (v1.3.0)
│   │   ├── index.md
│   │   ├── video_gen.md
│   │   ├── image_gen.md
│   │   ├── check_api.md
│   │   └── automation.md
│   └── guides/
│       ├── gcs-storage.md
│       ├── presets.md
│       └── troubleshooting.md
├── overrides/
├── docs/stylesheets/
│   └── extra.css
├── mkdocs.yml
├── design.md
├── TODO.md
└── changelog.md
```

---

> **Full history:** [changelog.md](changelog.md)
