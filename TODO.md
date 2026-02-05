# Pages - TODO

> **Last Updated:** 2026-02-06

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

### Skill-First Implementation v1.3.0 (2026-02-05)
- ✅ สร้าง skills/ folder (4 ไฟล์ใหม่)
- ✅ สร้าง advanced/ folder (2 ใหม่ + 3 ย้ายจาก cli/)
- ✅ ปรับ index.md, installation.md, quick-start.md เป็น Skill-First
- ✅ อัปเดต mkdocs.yml navigation
- ✅ ลบ guides/skill-installation.md

### Sidebar Transparent v1.4.0 (2026-02-05)
- ✅ ลบ Sidebar background ออกทั้งหมด (Dark และ Light mode)
- ✅ อัปเดต extra.css - 4 จุดที่มี .md-sidebar background

---

## 📋 Tasks To Do

### Skill Orchestrator Agent ⭐ (Pending Design Review)

**Design:** [agent.design.md](agent.design.md)
**Status:** ⏸️ Pending Review - ต้องตัดสินใจ Open Questions ก่อน

**Open Questions ต้องตอบ:**
- [ ] Q1: Scope - `/generative` only หรือ all skills?
- [ ] Q2: Intelligence - Thin หรือ Full orchestrator?
- [ ] Q3: Integration - Standalone agent หรือ extend skill.md?
- [ ] Q4: Activation - Auto-detect หรือ explicit command?

**Implementation Tasks (หลัง Review):**
- [ ] Phase 1: Intent parsing (video/image/config)
- [ ] Phase 2: Parameter extraction
- [ ] Phase 3: Basic error handling
- [ ] Phase 4: Context awareness
- [ ] Phase 5: Smart prompt optimization

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
| 2026-02-05 | **v1.4.0 - Removed Sidebar background** - Transparent sidebars |
| 2026-02-06 | **Created agent.design.md** - Skill Orchestrator Agent design (Pending Review) |
