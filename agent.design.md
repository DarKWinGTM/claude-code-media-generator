# Generative Media Navigator Agent Design

> **Current Version:** 0.3.0
> **Last Updated:** 2026-02-06
> **Status:** Ready to Implement

---

## 0. Document Control

> **Parent Scope:** Claude Code Media Generator
> **Design Type:** Agent Architecture
> **Approach:** Simple Auto-detect

---

## 1. Overview

### 1.1 Concept

**Generative Media Navigator** - Agent ที่ทำหน้าที่เรียบง่าย:
- **ฟัง** conversation context
- **ตรวจจับ** เมื่อ user ต้องการสร้าง media
- **เรียก** /generative skill อัตโนมัติ

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
Generative Media Navigator (ฟังและตรวจจับ)
  ↓
เรียก /generative Skill อัตโนมัติ
  ↓
Result
```

**ข้อดี:** ง่าย, ตรงไปตรงมา, ไม่มี overhead

---

## 3. Agent Definition

### 3.1 Claude Code Agent Structure

**Location:** `~/.claude/agents/generative-media-navigator.md`

**Format:** YAML frontmatter + Markdown system prompt

```yaml
---
name: generative-media-navigator
description: Auto-detect when user wants to create video or image, then invoke /generative skill automatically. Use proactively when user mentions creating media content.
tools: Bash, Read, Glob, AskUserQuestion
model: inherit
color: purple
---
```

### 3.2 YAML Frontmatter Fields

| Field | Value | Description |
|-------|-------|-------------|
| `name` | `generative-media-navigator` | Unique identifier |
| `description` | Auto-detect... | When to invoke agent |
| `tools` | `Bash, Read, Glob, AskUserQuestion` | Available tools |
| `model` | `inherit` | Use parent model |
| `color` | `purple` | UI color |

### 3.3 Capabilities

| Capability | Description |
|------------|-------------|
| **Context Listening** | อ่าน conversation ที่ผ่านมา |
| **Intent Detection** | ตรวจจับว่า user ต้องการสร้าง video/image |
| **Skill Invocation** | เรียก /generative command |
| **Result Relay** | ส่ง result กลับ user |

### 3.4 Out of Scope

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
| "create video", "make image", "I want a video" | Media | Detect type from keywords |

### 4.2 Detection Flow

```
1. ฟัง: User พูดอะไร?
   ↓
2. ตรวจจับ: มี keyword/intent สร้าง media ไหม?
   ↓
3. ถ้าใช่ → สกัด prompt และ parameters
   ↓
4. เรียก /generative skill
```

### 4.3 Examples

**Example 1: Direct Request (Thai)**
```
User: "สร้างวิดีโอแมวเดินบนดวงจันทร์"
Agent: ตรวจจับ → video, prompt="แมวเดินบนดวงจันทร์"
Action: /generative video "cat walking on the moon"
```

**Example 2: Direct Request (English)**
```
User: "Create a video of sunset over the ocean"
Agent: Detect → video, prompt="sunset over the ocean"
Action: /generative video "sunset over the ocean"
```

**Example 3: Conversational**
```
User: "ผมอยากได้รูปสำหรับโปรโมทร้านกาแฟ สไตล์ minimal"
Agent: ตรวจจับ → image, prompt="coffee shop promotion, minimal style"
Action: /generative image "coffee shop promotion minimal style"
```

**Example 4: No Intent**
```
User: "วิดีโอที่ส่งไปเมื่อกี้เป็นยังไงบ้าง?"
Agent: ไม่ตรวจจับ intent สร้าง → ไม่ทำอะไร
Action: (none - ตอบคำถามปกติ)
```

---

## 5. Integration

### 5.1 File Location

**Correct Location (Claude Code Agents):**
```
~/.claude/agents/
├── generative-media-navigator.md    ← NEW: This agent
├── nodejs-expert.md
├── html-css-js-frontend-expert.md
└── ...
```

**Related Skill:**
```
.claude/skills/generative/
├── SKILL.md              # Existing skill definition
├── video_gen.py
├── image_gen.py
└── check_api.py
```

### 5.2 Agent ↔ Skill Relationship

```
generative-media-navigator (Agent)
  ↓ detects intent
  ↓ invokes
/generative (Skill)
  ↓ executes
video_gen.py / image_gen.py
```

---

## 6. Scope & Limitations

### 6.1 In Scope (v1)

- ✅ Auto-detect video/image generation intent
- ✅ Extract prompt from natural language
- ✅ Invoke /generative skill
- ✅ Support Thai and English keywords

### 6.2 Out of Scope (v1)

- ❌ Complex prompt optimization
- ❌ Multi-step generation workflow
- ❌ Error recovery/retry logic
- ❌ Config/setup assistance

### 6.3 Future (v2+)

- Context-aware prompt enhancement
- Learn from user preferences
- Suggest improvements
- Parameter recommendations

---

## 7. Implementation Plan

### 7.1 Simple 2-Phase Approach

| Phase | Task | Effort |
|-------|------|--------|
| **1** | สร้าง `generative-media-navigator.md` | Low |
| **2** | ทดสอบ auto-detection กับ real conversations | Low |

### 7.2 File to Create

1. `~/.claude/agents/generative-media-navigator.md` - Agent definition

### 7.3 Testing

| Test Case | Expected |
|-----------|----------|
| "สร้างวิดีโอแมว" | Detect → invoke `/generative video` |
| "ทำรูปร้านกาแฟ" | Detect → invoke `/generative image` |
| "Create a video of sunset" | Detect → invoke `/generative video` |
| "วิดีโอเมื่อกี้เป็นยังไง" | No detection |

---

## 8. Decision Summary

| Question | Decision | Rationale |
|----------|----------|-----------|
| Agent Name | `generative-media-navigator` | สอดคล้องกับ `/generative` skill |
| Location | `~/.claude/agents/` | ตาม Claude Code agent convention |
| Complexity | Simple (not orchestrator) | ลด overhead, ตรงประเด็น |
| Approach | Auto-detect | User ไม่ต้องจำ command |
| Scope | `/generative` only | Focus, prove concept |
| Tools | `Bash, Read, Glob, AskUserQuestion` | เพียงพอสำหรับ invoke skill |

---

## 9. Next Steps

1. **สร้าง agent file** - `~/.claude/agents/generative-media-navigator.md`
2. **ทดสอบ** - Try with real conversations
3. **ปรับปรุง** - Based on testing feedback

---

> **Full history:** [agent.changelog.md](agent.changelog.md)
