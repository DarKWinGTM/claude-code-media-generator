# Skill Navigator Agent Design

> **Current Version:** 0.2.0
> **Last Updated:** 2026-02-06
> **Status:** Draft - Simplified Design

---

## 0. Document Control

> **Parent Scope:** Claude Code Media Generator
> **Design Type:** Agent Architecture
> **Approach:** Simple Auto-detect

---

## 1. Overview

### 1.1 Concept

**Skill Navigator** - Agent ที่ทำหน้าที่เรียบง่าย:
- **ฟัง** conversation context
- **ตรวจจับ** เมื่อ user ต้องการสร้าง media
- **เรียก** skill อัตโนมัติ

### 1.2 Philosophy

```
Simple > Complex
Auto-detect > Explicit commands
Assistant > Controller
```

### 1.3 Goals

| Goal | Description |
|------|-------------|
| **ลด friction** | User ไม่ต้องจำ command syntax |
| **Auto-detect** | เข้าใจ intent จากการสนทนา |
| **Seamless** | เหมือนคุยกับ assistant ที่เข้าใจ |

---

## 2. Design Principle

### 2.1 ❌ ไม่ทำ (Rejected Approach)

```
User → Orchestrator (complex) → Skill
         ↓
    Parse → Validate → Plan → Execute → Handle
```

**ปัญหา:** สร้าง layer ซับซ้อนที่ไม่จำเป็น

### 2.2 ✅ ทำ (Simplified Approach)

```
User (สนทนาปกติ)
  ↓
Skill Navigator (ฟังและตรวจจับ)
  ↓
เรียก Skill อัตโนมัติ
  ↓
Result
```

**ข้อดี:** ง่าย, ตรงไปตรงมา, ไม่มี overhead

---

## 3. Agent Definition

### 3.1 Core Identity

```yaml
name: "skill-navigator"
type: "auto-detect-assistant"
purpose: |
  ฟัง conversation และเรียก /generative skill
  อัตโนมัติเมื่อตรวจจับว่า user ต้องการสร้าง media

behavior:
  - listen: true        # ฟัง context
  - auto_detect: true   # ตรวจจับ intent
  - invoke_skill: true  # เรียก skill เอง
```

### 3.2 Capabilities

| Capability | Description |
|------------|-------------|
| **Context Listening** | อ่าน conversation ที่ผ่านมา |
| **Intent Detection** | ตรวจจับว่า user ต้องการสร้าง video/image |
| **Skill Invocation** | เรียก /generative command |
| **Result Relay** | ส่ง result กลับ user |

### 3.3 ไม่ทำ (Out of Scope)

- ❌ Complex orchestration
- ❌ Multi-step planning
- ❌ Parameter optimization
- ❌ Error recovery strategies

---

## 4. Intent Detection

### 4.1 Trigger Patterns

| Pattern | Intent | Action |
|---------|--------|--------|
| "สร้างวิดีโอ...", "ทำวิดีโอ...", "generate video..." | Video | `/generative video` |
| "สร้างรูป...", "ทำรูป...", "generate image..." | Image | `/generative image` |
| "อยากได้วิดีโอ...", "ต้องการรูป..." | Media | Detect type from context |

### 4.2 Detection Flow

```
1. ฟัง: User พูดอะไร?
   ↓
2. ตรวจจับ: มี keyword/intent สร้าง media ไหม?
   ↓
3. ถ้าใช่ → สกัด prompt และ parameters
   ↓
4. เรียก skill
```

### 4.3 Examples

**Example 1: Direct Request**
```
User: "สร้างวิดีโอแมวเดินบนดวงจันทร์"
Agent: ตรวจจับ → video, prompt="แมวเดินบนดวงจันทร์"
Action: /generative video "cat walking on the moon"
```

**Example 2: Conversational**
```
User: "ผมอยากได้รูปสำหรับโปรโมทร้านกาแฟ สไตล์ minimal"
Agent: ตรวจจับ → image, prompt="coffee shop promotion, minimal style"
Action: /generative image "coffee shop promotion minimal style"
```

**Example 3: No Intent**
```
User: "วิดีโอที่ส่งไปเมื่อกี้เป็นยังไงบ้าง?"
Agent: ไม่ตรวจจับ intent สร้าง → ไม่ทำอะไร
Action: (none - ตอบคำถามปกติ)
```

---

## 5. Integration

### 5.1 File Structure

```
.claude/skills/generate-video/
├── skill.md              # Existing skill definition
├── navigator.md          # NEW: Navigator agent definition
├── video_gen.py
├── image_gen.py
└── check_api.py
```

### 5.2 navigator.md Content

```markdown
# Skill Navigator

## Trigger
เมื่อ user แสดง intent ต้องการสร้าง video หรือ image

## Behavior
1. ตรวจจับ intent จาก conversation
2. สกัด prompt และ parameters
3. เรียก /generative command
4. ส่ง result กลับ user

## Keywords
- สร้างวิดีโอ, ทำวิดีโอ, generate video
- สร้างรูป, ทำรูป, generate image
- อยากได้, ต้องการ + video/image context
```

---

## 6. Scope & Limitations

### 6.1 In Scope (v1)

- ✅ Auto-detect video/image generation intent
- ✅ Extract prompt from natural language
- ✅ Invoke /generative skill
- ✅ Basic parameter extraction (aspect, duration)

### 6.2 Out of Scope (v1)

- ❌ Complex prompt optimization
- ❌ Multi-step generation workflow
- ❌ Error recovery/retry logic
- ❌ Config/setup assistance

### 6.3 Future (v2+)

- Context-aware prompt enhancement
- Learn from user preferences
- Suggest improvements

---

## 7. Implementation Plan

### 7.1 Simple 2-Phase Approach

| Phase | Task | Effort |
|-------|------|--------|
| **1** | สร้าง navigator.md พร้อม intent patterns | Low |
| **2** | ทดสอบ auto-detection กับ real conversations | Low |

### 7.2 Files to Create

1. `navigator.md` - Agent definition
2. Update `skill.md` - Reference to navigator

### 7.3 Testing

| Test Case | Expected |
|-----------|----------|
| "สร้างวิดีโอแมว" | Detect → invoke video |
| "ทำรูปร้านกาแฟ" | Detect → invoke image |
| "วิดีโอเมื่อกี้เป็นยังไง" | No detection |

---

## 8. Decision Summary

| Question | Decision | Rationale |
|----------|----------|-----------|
| Complexity | Simple (not orchestrator) | ลด overhead, ตรงประเด็น |
| Approach | Auto-detect | User ไม่ต้องจำ command |
| Scope | `/generative` only | Focus, prove concept |
| Integration | navigator.md แยกไฟล์ | Clear separation |

---

## 9. Next Steps

1. **สร้าง navigator.md** - Agent definition file
2. **อัปเดต skill.md** - Reference navigator
3. **ทดสอบ** - Try with real conversations

---

> **Full history:** [agent.changelog.md](agent.changelog.md)
