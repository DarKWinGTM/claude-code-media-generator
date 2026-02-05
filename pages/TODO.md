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
- ✅ Option A: Main section styling (font, color, icon, uppercase)
- ✅ Option B: Gradient separators between main sections
- ✅ Option C: Header Tabs (show 6 sections in header)
- ✅ Deploy all 3 options for user evaluation

---

## 📋 Tasks To Do

### User Decision Required
- [ ] ดู live site แล้วเลือกว่าจะเก็บตัวเลือกไหนบ้าง (A, B, C)
- [ ] อาจเลือกเก็บทั้งหมด หรือบางตัว หรือไม่เก็บเลย

### CSS Cleanup (After Decision)
- [ ] ลบ CSS ของ options ที่ไม่ต้องการ
- [ ] Review extra.css หา unused rules
- [ ] Organize CSS by section

---

## 📜 History

| Date | Changes |
|------|---------|
| 2026-02-05 | Created TODO.md, design.md, changelog.md |
| 2026-02-05 | Removed all Section Navigator code (1838 lines total) |
| 2026-02-05 | Identified header.html missing tabs issue |
