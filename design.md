# Pages Design - Claude Code Media Generator Documentation

> **Current Version:** 1.1
> **Last Updated:** 2026-02-05

---

## 1. Overview

MkDocs Material documentation site สำหรับ Claude Code Media Generator project

**Site URL:** https://darkwingtm.github.io/claude-code-media-generator/

---

## 2. Navigation Structure

### 2.1 Current Structure (6 หัวข้อหลัก)

| # | Section | URL Path | Sub-pages |
|---|---------|----------|-----------|
| 1 | Home | `/` | - |
| 2 | Getting Started | `/getting-started/` | Installation, Authentication, Quick Start |
| 3 | Video Generation | `/video/` | Overview, Modes, Models, Reference Images, Extension |
| 4 | Image Generation | `/image/` | Overview |
| 5 | CLI Reference | `/cli/` | video_gen.py, image_gen.py, check_api.py |
| 6 | Guides | `/guides/` | Skill Installation, GCS Storage, Presets, Troubleshooting |

### 2.2 Proposed Structure: Skill-First Approach ⭐

**หลักการ:**
- Skill = วิธีใช้งานหลัก (ง่าย, Claude Code native)
- Script = วิธีใช้ทางเลือก (สำหรับ automation, advanced users)

| # | Section | Sub-pages | Notes |
|---|---------|-----------|-------|
| 1 | Home | - | ปรับให้เน้น Skill |
| 2 | Getting Started | Installation, Authentication, Quick Start | Installation เน้น Skill |
| 3 | **Using Skills** ⭐ | Overview, Commands, Examples, Configuration | หัวข้อใหม่! |
| 4 | Video Generation | Overview, Modes, Models, Reference Images, Extension | คงเดิม |
| 5 | Image Generation | Overview | คงเดิม |
| 6 | **Advanced Usage** ⭐ | CLI Reference, Direct Scripts, Automation | รวม CLI มาที่นี่ |
| 7 | Guides | GCS Storage, Presets, Troubleshooting | ย้าย Skill Installation ออก |

### 2.3 Content Changes Required

| หน้า | เปลี่ยนแปลง |
|------|------------|
| **index.md** | เพิ่ม Skill tab เป็นตัวเลือกแรกใน Quick Start |
| **installation.md** | เน้น Skill installation ก่อน, Script เป็นตัวเลือก |
| **quick-start.md** | แยก 2 tabs: Skill (หลัก) vs Script (ทางเลือก) |
| **ใหม่: skills/overview.md** | สร้างใหม่ - Skill overview |
| **ใหม่: skills/commands.md** | สร้างใหม่ - All /generative commands |
| **ใหม่: skills/examples.md** | สร้างใหม่ - Usage examples |
| **ใหม่: advanced/index.md** | สร้างใหม่ - Advanced usage overview |
| **ย้าย: cli/*.md** | ย้ายไป advanced/ |

### 2.4 Current Sidebar Behavior

**Left Sidebar (Primary):**
- แสดงหัวข้อหลักทั้งหมด (ปิด navigation.tabs แล้ว)
- Expandable/collapsible sections
- Active section expanded อัตโนมัติ
- เพิ่ม padding-bottom: 16px ระหว่างหัวข้อหลัก

**Right Sidebar (Secondary):**
- Table of Contents ของหน้าปัจจุบัน
- Auto-generated จาก headings

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

## 5. Design Decisions

| Decision | Choice | Reason |
|----------|--------|--------|
| Header Tabs | ไม่ใช้ | User preference - ต้องการหัวข้อใน Left Sidebar |
| navigation.tabs | ปิด | ให้ Left Sidebar แสดงหัวข้อหลักทั้งหมด |
| Section Spacing | 16px | เพิ่มระยะห่างระหว่างหัวข้อหลัก |
| **Skill-First** | ใช้ | Skill เป็นวิธีหลัก, Script เป็นทางเลือก |

---

## 6. File Structure

### 6.1 Current Structure

```
pages/
├── docs/
│   ├── index.md
│   ├── getting-started/
│   ├── video/
│   ├── image/
│   ├── cli/              # จะย้ายไป advanced/
│   └── guides/
├── overrides/
├── docs/stylesheets/
│   └── extra.css
├── mkdocs.yml
├── design.md
├── TODO.md
└── changelog.md
```

### 6.2 Proposed Structure (Skill-First)

```
pages/
├── docs/
│   ├── index.md          # ปรับให้เน้น Skill
│   ├── getting-started/
│   │   ├── installation.md   # เน้น Skill installation
│   │   ├── authentication.md
│   │   └── quick-start.md    # Skill-first tabs
│   ├── skills/           # ใหม่! ⭐
│   │   ├── overview.md
│   │   ├── commands.md
│   │   ├── examples.md
│   │   └── configuration.md
│   ├── video/
│   ├── image/
│   ├── advanced/         # ใหม่! ⭐
│   │   ├── index.md
│   │   ├── video_gen.md  # ย้ายจาก cli/
│   │   ├── image_gen.md  # ย้ายจาก cli/
│   │   ├── check_api.md  # ย้ายจาก cli/
│   │   └── automation.md
│   └── guides/
│       ├── gcs-storage.md
│       ├── presets.md
│       └── troubleshooting.md
```

---

> **Full history:** [changelog.md](changelog.md)
