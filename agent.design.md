# Skill Orchestrator Agent Design

> **Current Version:** 0.1.0
> **Last Updated:** 2026-02-06
> **Status:** Draft - Pending Review

---

## 0. Document Control

> **Parent Scope:** Claude Code Media Generator
> **Design Type:** Architecture Proposal
> **Review Required:** Yes - before implementation

---

## 1. Overview

### 1.1 Problem Statement

ปัจจุบัน `/generative` skill ต้องการให้ user รู้ syntax และเรียกใช้เอง ทำให้:
- User ต้องเรียนรู้ command syntax
- ไม่มี context awareness จาก conversation
- Error handling เป็นภาระของ user
- ไม่มี intelligent parameter optimization

### 1.2 Proposed Solution

สร้าง **Skill Orchestrator Agent** ที่ทำหน้าที่:
- Parse natural language intent
- Extract และ validate parameters
- Invoke skill commands อัตโนมัติ
- Handle errors และ recovery

### 1.3 Goals

| Goal | Description |
|------|-------------|
| **UX Improvement** | User ใช้ภาษาธรรมชาติได้ |
| **Error Recovery** | จัดการ errors อัตโนมัติ |
| **Context Awareness** | รู้ context จาก conversation |
| **Skill Enhancement** | เสริมประสิทธิภาพ skill ที่มีอยู่ |

---

## 2. Current State Analysis

### 2.1 Current Flow (Without Agent)

```
User Prompt
  ↓
Claude Code (Main)
  ↓
Manual: /generative video "prompt"
  ↓
Skill Script (video_gen.py)
  ↓
Result
```

### 2.2 Pain Points

| Issue | Impact | Priority |
|-------|--------|----------|
| Manual Invocation | User ต้องรู้ syntax | High |
| Context Loss | Skill ไม่รู้ conversation context | Medium |
| No Intelligence | ไม่มี reasoning | Medium |
| Error Handling | User ต้อง debug เอง | High |

---

## 3. Proposed Architecture

### 3.1 Target Flow (With Orchestrator)

```
User Intent (Natural Language)
  ↓
Skill Orchestrator Agent
  ↓
Intent Analysis → Parameter Extraction → Validation
  ↓
Skill Invocation (Automated)
  ↓
Result Processing → Error Recovery → User Response
```

### 3.2 Architecture Options

#### Option A: Thin Orchestrator (Recommended for v1)

**Description:** Lightweight agent ที่ parse intent และ invoke skill

```
User: "สร้างวิดีโอแมวกำลังเล่นบนดาวอังคาร 16:9"
  ↓
Orchestrator (Thin)
  → Parse: video generation
  → Extract: prompt, aspect="16:9"
  → Invoke: /generative video "cat playing on Mars" --aspect 16:9
  ↓
Result
```

| Pros | Cons |
|------|------|
| Simple, low overhead | Limited error recovery |
| Fast execution | No complex reasoning |
| Easy to maintain | Basic intelligence |

#### Option B: Full Orchestrator (Future)

**Description:** Full intelligence agent พร้อม reasoning และ optimization

```
User: "อยากได้วิดีโอโปรโมทร้านกาแฟ สไตล์ cinematic"
  ↓
Orchestrator (Full)
  → Understand business context
  → Generate optimized prompt
  → Select best model
  → Execute with retry
  → Validate output
  ↓
Curated Result
```

| Pros | Cons |
|------|------|
| Best UX | Higher latency |
| Smart error recovery | More complex |
| Context aware | Higher token cost |

#### Option C: Hybrid (Adaptive)

```
Simple Request → Thin Orchestrator → Fast
Complex Request → Full Orchestrator → Quality
```

### 3.3 Recommendation

**Phase 1:** Option A (Thin Orchestrator)
- Quick to implement
- Immediate UX improvement
- Can evolve to Option B/C later

---

## 4. Detailed Design

### 4.1 Agent Definition

```yaml
name: "media-orchestrator"
type: "skill-orchestrator"
description: |
  Orchestrates media generation skills based on natural language.
  Automatically invokes /generative commands.

capabilities:
  - intent_parsing
  - parameter_extraction
  - skill_invocation
  - error_recovery

supported_skills:
  - /generative video
  - /generative image
  - /generative config
```

### 4.2 Intent Mapping

| User Intent Pattern | Action | Parameters |
|---------------------|--------|------------|
| "สร้างวิดีโอ...", "generate video..." | video | prompt, aspect, duration |
| "ทำรูป...", "create image..." | image | prompt, aspect, count |
| "ตั้งค่า API", "setup config" | config | - |
| "เช็ค status", "check API" | check | - |

### 4.3 Workflow Phases

**Phase 1: Intent Analysis**
- Parse natural language
- Classify: video | image | config | unknown
- Calculate confidence score

**Phase 2: Parameter Extraction**
- Extract explicit parameters
- Infer implicit parameters (defaults)
- Validate completeness

**Phase 3: Pre-flight Check**
- Verify API availability
- Check rate limits
- Validate parameters

**Phase 4: Execution**
- Invoke skill command
- Monitor progress
- Handle errors/retries

**Phase 5: Result Processing**
- Validate output
- Format response
- Provide recommendations

### 4.4 Error Recovery Strategy

| Error Type | Recovery Action |
|------------|-----------------|
| API Key Invalid | Prompt: `/generative config setup` |
| Rate Limited | Wait + retry with backoff |
| Invalid Parameters | Suggest corrections |
| Generation Failed | Retry with adjusted prompt |
| Network Error | Exponential backoff retry |

---

## 5. Multi-Perspective Analysis

### 5.1 Developer Perspective

| Aspect | Assessment |
|--------|------------|
| Complexity | Medium - agent definition + integration |
| Maintainability | Good - clear separation of concerns |
| Testability | Need unit tests for intent parsing |
| Integration | Use Task tool with subagent_type |

### 5.2 Security Perspective

| Aspect | Assessment |
|--------|------------|
| API Key Safety | Agent must not expose keys in logs |
| Prompt Injection | Sanitize user input before passing |
| Resource Limits | Rate limiting to prevent abuse |
| Validation | Validate parameters before execute |

### 5.3 Architect Perspective

| Aspect | Assessment |
|--------|------------|
| Scalability | Good - agent pattern scales well |
| Extensibility | Excellent - easy to add new skills |
| Separation | Orchestrator separate from skill logic |
| Future-proofing | Ready for MCP migration |

---

## 6. Integration Design

### 6.1 Current Skill Structure

```
.claude/skills/generate-video/
├── skill.md
├── video_gen.py
├── image_gen.py
└── check_api.py
```

### 6.2 Proposed Addition

```
.claude/skills/generate-video/
├── skill.md          # Updated with orchestrator info
├── orchestrator.md   # NEW: Agent definition
├── video_gen.py
├── image_gen.py
└── check_api.py
```

### 6.3 Skill.md Updates

```markdown
## Orchestrator Mode (Optional)

Natural language → Orchestrator → Skill invocation

Example:
  User: "วิดีโอแมวเดินบนดวงจันทร์ 4 วินาที"
  Orchestrator: /generative video "cat walking on moon" --duration 4
```

---

## 7. Implementation Plan

### 7.1 Phases

| Phase | Feature | Effort | Priority |
|-------|---------|--------|----------|
| 1 | Intent parsing (video/image/config) | Low | High |
| 2 | Parameter extraction | Low | High |
| 3 | Basic error handling | Medium | Medium |
| 4 | Context awareness | Medium | Low |
| 5 | Smart prompt optimization | High | Low |

### 7.2 Dependencies

- Claude Code Task tool with subagent_type support
- Existing `/generative` skill scripts
- API configuration (check_api.py)

### 7.3 Testing Strategy

| Test Type | Coverage |
|-----------|----------|
| Unit | Intent parsing accuracy |
| Integration | Skill invocation flow |
| E2E | Natural language → Result |
| Error | Recovery scenarios |

---

## 8. Open Questions

ต้องตัดสินใจก่อน implement:

### Q1: Scope
- Orchestrator สำหรับ `/generative` เท่านั้น?
- หรือ generic สำหรับทุก skill?

### Q2: Intelligence Level
- Thin (parse + invoke)?
- Full (reasoning + optimization)?

### Q3: Integration Point
- Standalone agent?
- Extend skill.md?

### Q4: Activation
- Auto-detect intent?
- Explicit command (e.g., `/orchestrate`)?

---

## 9. Decision Log

| Date | Decision | Rationale |
|------|----------|-----------|
| 2026-02-06 | Created design document | Initial analysis complete |
| - | Pending: Architecture choice | Awaiting review |
| - | Pending: Scope decision | Awaiting review |

---

## 10. Next Steps

1. **Review** - ตรวจสอบ design document นี้
2. **Decide** - ตอบ Open Questions (Section 8)
3. **Prototype** - สร้าง proof-of-concept
4. **Implement** - ตาม Implementation Plan (Section 7)

---

> **Full history:** [agent.changelog.md](agent.changelog.md)
