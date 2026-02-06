# Agent Design Changelog

> **Parent Document:** [agent.design.md](agent.design.md)
> **Current Version:** 0.3.0
> **Last Updated:** 2026-02-06

---

## Version History

| Version | Date | Changes | Summary |
|---------|------|---------|---------|
| 0.3.0 | 2026-02-06 | **Correct Claude Code Agent Structure** | แก้ไขโครงสร้างตาม Claude Code agents convention |
| 0.2.0 | 2026-02-06 | **Simplified to Skill Navigator** | ลบ Orchestrator concept, ใช้ auto-detect แทน |
| 0.1.0 | 2026-02-06 | **Initial Design Draft** | สร้าง design document (Orchestrator approach - rejected) |

---

## Version 0.3.0: Correct Claude Code Agent Structure

**Date:** 2026-02-06
**Status:** Ready to Implement

### Major Changes

**🔧 Renamed:**
- Agent name: `skill-navigator` → `generative-media-navigator`
- สอดคล้องกับ `/generative` skill ที่ทำงานร่วมกัน

**📁 Correct Location:**
- Wrong: `.claude/skills/generative/navigator.md`
- Correct: `~/.claude/agents/generative-media-navigator.md`

**📋 Added YAML Frontmatter:**
```yaml
---
name: generative-media-navigator
description: Auto-detect when user wants to create video or image...
tools: Bash, Read, Glob, AskUserQuestion
model: inherit
color: purple
---
```

**✅ Research Verified:**
- ศึกษา existing agents ใน `/home/node/.claude/agents/`
- ค้นหา Claude Code documentation
- ยืนยันโครงสร้างถูกต้องตาม convention

### Rationale

User feedback ระบุว่าโครงสร้างเดิมคลาดเคลื่อน:
- "ฉันคิดว่าโครงสร้างและการวางความเข้าใจมีความคลาดเคลื่อนแน่นอน"
- ต้อง research ให้ครบก่อนดำเนินการ

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
