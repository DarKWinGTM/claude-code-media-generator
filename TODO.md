# Pages - TODO

> **Last Updated:** 2026-02-05

---

## ✅ Completed

### Section Navigator Cleanup (2026-02-05)
- ✅ ลบ Section Navigator จาก toc.html
- ✅ ลบ JavaScript injection จาก main.html
- ✅ ลบ Section Navigator CSS 1625 lines จาก extra.css
- ✅ Deploy clean version to GitHub Pages

### Root Cause Analysis (2026-02-05)
- ✅ พบว่า Material theme integrate toc.html เข้า left sidebar
- ✅ พบว่า header.html override ไม่มี tabs include
- ✅ ยืนยันว่า 6 หัวข้อหลักแสดงใน left sidebar อยู่แล้ว

### Sidebar Enhancement v1.1.0 (2026-02-05)
- ✅ Option A: Main section styling (reverted)
- ✅ Option B: Gradient separators (reverted)
- ✅ Option C: Header Tabs (reverted)
- ✅ **Reverted** - ทำให้เว็บพัง กลับไปใช้ clean version

### Left Sidebar Fix v1.2.0 (2026-02-05)
- ✅ ปิด navigation.tabs ใน mkdocs.yml
- ✅ Left Sidebar แสดง 6 หัวข้อหลักทั้งหมดแล้ว
- ✅ เพิ่ม padding-bottom: 16px ระหว่างหัวข้อหลัก
- ✅ Deploy สำเร็จ

### Skill-First Design (2026-02-05)
- ✅ วิเคราะห์และออกแบบ Skill-First Approach
- ✅ บันทึกลง design.md Section 2.2, 2.3, 6.2

---

## 📋 Tasks To Do

### Skill-First Content Creation ⭐ (Completed)
- [x] สร้าง skills/overview.md - Skill overview
- [x] สร้าง skills/commands.md - All /generative commands
- [x] สร้าง skills/examples.md - Usage examples
- [x] สร้าง skills/configuration.md - Configuration options
- [x] สร้าง advanced/index.md - Advanced usage overview
- [x] สร้าง advanced/automation.md - Automation guide

### Content Updates (Skill-First) - Completed
- [x] ปรับ index.md - เพิ่ม Skill tab เป็นตัวเลือกแรกใน Quick Start
- [x] ปรับ installation.md - เน้น Skill installation ก่อน
- [x] ปรับ quick-start.md - แยก 2 tabs: Skill (หลัก) vs Script (ทางเลือก)
- [x] ย้าย cli/*.md ไป advanced/
- [x] ลบ guides/skill-installation.md (ย้ายเนื้อหาไป skills/)

### mkdocs.yml Updates - Completed
- [x] เพิ่ม "Using Skills" section ใน nav
- [x] เปลี่ยน "CLI Reference" เป็น "Advanced Usage"
- [x] ปรับ Guides section

---

## 📜 History

| Date | Changes |
|------|---------|
| 2026-02-05 | Created TODO.md, design.md, changelog.md |
| 2026-02-05 | Removed all Section Navigator code (1838 lines total) |
| 2026-02-05 | Identified header.html missing tabs issue |
| 2026-02-05 | Reverted Options A, B, C (website broke) |
| 2026-02-05 | Fixed Left Sidebar - disabled navigation.tabs |
| 2026-02-05 | Added spacing between main sections (16px) |
| 2026-02-05 | Designed Skill-First Approach - saved to design.md |
| 2026-02-05 | **Implemented Skill-First Approach** - All content changes complete |
