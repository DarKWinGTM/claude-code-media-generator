# Agent Design Changelog

> **Parent Document:** [agent.design.md](agent.design.md)
> **Current Version:** 0.2.0
> **Last Updated:** 2026-02-06

---

## Version History

| Version | Date | Changes | Summary |
|---------|------|---------|---------|
| 0.2.0 | 2026-02-06 | **Simplified to Skill Navigator** | ลบ Orchestrator concept, ใช้ auto-detect แทน |
| 0.1.0 | 2026-02-06 | **Initial Design Draft** | สร้าง design document (Orchestrator approach - rejected) |

---

## Version 0.2.0: Simplified to Skill Navigator

**Date:** 2026-02-06
**Status:** Draft - Simplified Design

### Major Changes

**❌ Removed (Orchestrator Concept):**
- Complex orchestration layer
- Multi-step workflow (Parse → Validate → Plan → Execute → Handle)
- 4 Open Questions about complexity levels
- Heavy architecture proposal

**✅ Added (Skill Navigator Concept):**
- Simple 3-step flow: ฟัง → ตรวจจับ → เรียก skill
- Auto-detect approach
- navigator.md file proposal
- Simple 2-phase implementation plan

### New Design Philosophy

```
Simple > Complex
Auto-detect > Explicit commands
Assistant > Controller
```

### Simplified Architecture

```
User (สนทนาปกติ)
  ↓
Skill Navigator (ฟังและตรวจจับ)
  ↓
เรียก Skill อัตโนมัติ
  ↓
Result
```

### Decision Summary

| Question | Decision |
|----------|----------|
| Complexity | Simple (not orchestrator) |
| Approach | Auto-detect |
| Scope | `/generative` only |
| Integration | navigator.md แยกไฟล์ |

### Rationale

User feedback ระบุว่า Orchestrator approach ซับซ้อนเกินไป:
- ไม่ต้องการ complex layer
- ต้องการแค่ agent ที่ฟังและเรียก skill อัตโนมัติ
- Focus on "assistance" not "orchestration"

---

## Version 0.1.0: Initial Design Draft (Rejected)

**Date:** 2026-02-06
**Status:** ❌ Rejected - Too Complex

### Content (Historical)

- Proposed "Skill Orchestrator Agent"
- 3 architecture options: Thin/Full/Hybrid
- 4 Open Questions requiring decisions
- Complex 5-phase implementation plan

### Why Rejected

- Over-engineered for the use case
- User wanted simple auto-detect, not orchestration
- Unnecessary complexity and overhead

---

> **Design:** [agent.design.md](agent.design.md)
