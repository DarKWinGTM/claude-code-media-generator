# 🧪 Checkpoint Testing Design Document

## 0) Document Control

> **Parent Scope:** claude-code-media-generator
> **Current Version:** 1.2
> **Session:** S1591 (2026-02-09)

---

## 1) Testing Goals

| Goal | Description |
|------|-------------|
| **Validation** | ยืนยันว่า core features ทำงานได้จริงใน production environment |
| **Installation** | ยืนยันว่า installation ไปยัง Claude Code ถูกต้อง |
| **Regression** | ป้องกัน features เดิมพังจาก changes ใหม่ |
| **Documentation** | บันทึกพฤติกรรมจริงของ system |
| **Issue Discovery** | ค้นหา bugs/issues ก่อน production use |

---

## 2) Testing Principles

### 2.1 Extensibility First

Testing design ต้องรองรับการเพิ่ม test cases ใหม่ได้ง่าย:

```
Test ID Format: [CATEGORY]-[NUMBER]

Categories:
  INS-XXX  = Installation Tests
  VID-XXX  = Video Generation Tests
  IMG-XXX  = Image Generation Tests
  SKL-XXX  = Skill Tests
  AGT-XXX  = Agent Tests
  CFG-XXX  = Configuration Tests
  INT-XXX  = Integration Tests (cross-component)
  REG-XXX  = Regression Tests (for bug fixes)
  NEW-XXX  = New Feature Tests (temporary, re-categorize later)
```

### 2.2 Adding New Tests

เมื่อต้องการเพิ่ม test ใหม่:

1. **เลือก Category** - ใช้ existing หรือสร้างใหม่ (ต้อง update Section 4)
2. **เพิ่ม Test Case** - ใน Section ที่เหมาะสม
3. **ใช้ Number ต่อจากเดิม** - เช่น VID-010, VID-011
4. **Update Changelog** - บันทึกว่าเพิ่ม test อะไร

### 2.3 Test Status Legend

| Symbol | Status | Description |
|--------|--------|-------------|
| ⏳ | Pending | ยังไม่ได้ทดสอบ |
| 🔄 | In Progress | กำลังทดสอบ |
| ✅ | Pass | ผ่านการทดสอบ |
| ❌ | Fail | ไม่ผ่าน - มี issue |
| ⚠️ | Partial | ผ่านบางส่วน |
| ⏭️ | Skipped | ข้ามไป (มีเหตุผล) |
| 🔁 | Re-test | ต้องทดสอบใหม่ |

---

## 3) Prerequisites

### 3.1 Environment Requirements

| Requirement | Check Command | Status |
|-------------|---------------|--------|
| Python 3.9+ | `python --version` | ⏳ |
| ffmpeg | `ffmpeg -version` | ⏳ |
| ffprobe | `ffprobe -version` | ⏳ |
| gcloud CLI | `gcloud --version` | ⏳ |

### 3.2 API Configuration

| Requirement | Location | Status |
|-------------|----------|--------|
| Google Cloud Project ID | Environment or config.json | ⏳ |
| Vertex AI API enabled | Google Cloud Console | ⏳ |
| API Key (Gemini or Vertex) | `GOOGLE_API_KEY` or config.json | ⏳ |
| GCS Bucket | Pattern: `PROJECT_ID-media-output` | ⏳ |

### 3.3 Test Assets

| Asset | Path | Description | Status |
|-------|------|-------------|--------|
| Sample Image | `test/assets/test_image.png` | 512x512 PNG | ⏳ |
| Sample Video | `test/assets/test_video.mp4` | 24fps, <8s | ⏳ |
| Reference Style | `test/assets/style_ref.png` | For style transfer | ⏳ |

### 3.4 Claude Code Environment

| Requirement | Check | Status |
|-------------|-------|--------|
| Claude Code CLI installed | `claude --version` | ⏳ |
| Skills directory exists | `~/.claude/skills/` | ⏳ |
| Agents directory exists | `~/.claude/agents/` | ⏳ |

---

## 4) Testing Phases

> **สำคัญ:** การทดสอบแบ่งเป็น Phases ที่ต้องทำตามลำดับ - บาง Phase ต้องผ่าน Phase ก่อนหน้าถึงจะทดสอบได้

### 4.1 Phase Overview

```
Phase 1: Environment & Direct Script Testing
    ↓ (ไม่ต้อง install อะไร - ทำได้เลย)
    ↓
Phase 2: Installation to Claude Code
    ↓ (ต้อง install skill/agent ก่อน)
    ↓
Phase 3: Skill & Agent Testing
    ↓ (ต้องผ่าน Phase 2 ก่อน)
    ↓
Phase 4: Integration Testing
    ↓ (ต้องผ่าน Phase 3 ก่อน)
    ↓
✅ All Tests Complete
```

### 4.2 Phase Summary

| Phase | Name | Prerequisites | Tests | Categories |
|-------|------|---------------|-------|------------|
| **1** | Environment & Scripts | Python, ffmpeg, API | 29 | ENV, VID, IMG, CFG |
| **2** | Installation | Phase 1 ✅ | 6 | INS |
| **3** | Skill & Agent | Phase 2 ✅ | 17 | SKL, AGT |
| **4** | Integration | Phase 3 ✅ | 4 | INT |

**Total Test Cases:** 56

### 4.3 Phase Dependencies Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│ PHASE 1: Environment & Direct Script Testing                   │
│ ─────────────────────────────────────────────                   │
│ ✓ ทำได้เลยโดยไม่ต้อง install อะไรเพิ่ม                            │
│ ✓ ทดสอบ video_gen.py, image_gen.py, config.py โดยตรง             │
│                                                                 │
│ Tests: ENV-001~004, VID-001~015, IMG-001~007, CFG-001~004       │
└────────────────────────────┬────────────────────────────────────┘
                             │ Pass ✅
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│ PHASE 2: Installation to Claude Code                           │
│ ─────────────────────────────────────                           │
│ ✓ Copy skill/agent ไปยัง ~/.claude/                              │
│ ✓ Verify installation สำเร็จ                                     │
│                                                                 │
│ Tests: INS-001~006                                              │
└────────────────────────────┬────────────────────────────────────┘
                             │ Pass ✅
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│ PHASE 3: Skill & Agent Testing                                  │
│ ───────────────────────────────                                 │
│ ⚠️ ต้องผ่าน Phase 2 ก่อน (ต้อง install แล้ว)                       │
│ ✓ ทดสอบ /generative skill commands                              │
│ ✓ ทดสอบ Agent auto-detection                                    │
│                                                                 │
│ Tests: SKL-001~011, AGT-001~006                                 │
└────────────────────────────┬────────────────────────────────────┘
                             │ Pass ✅
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│ PHASE 4: Integration Testing                                    │
│ ────────────────────────────                                    │
│ ⚠️ ต้องผ่าน Phase 3 ก่อน                                          │
│ ✓ ทดสอบ end-to-end workflows                                    │
│ ✓ ทดสอบ cross-component interactions                            │
│                                                                 │
│ Tests: INT-001~004                                              │
└─────────────────────────────────────────────────────────────────┘
```

### 4.4 Category Overview (by Phase)

#### Phase 1 Categories (No Installation Required)

| Category | Code | Component | Priority | Tests |
|----------|------|-----------|----------|-------|
| Environment | ENV | Python, ffmpeg, API | 🔴 Critical | 4 |
| Video Generation | VID | video_gen.py | 🔴 Critical | 15 |
| Image Generation | IMG | image_gen.py | 🔴 Critical | 7 |
| Configuration | CFG | config.py | 🟢 Medium | 4 |

**Phase 1 Total:** 30 tests

#### Phase 2 Categories (Installation)

| Category | Code | Component | Priority | Tests |
|----------|------|-----------|----------|-------|
| Installation | INS | Claude Code Setup | 🔴 Critical | 6 |

**Phase 2 Total:** 6 tests

#### Phase 3 Categories (Requires Installation)

| Category | Code | Component | Priority | Tests |
|----------|------|-----------|----------|-------|
| Skill | SKL | /generative | 🟡 High | 11 |
| Agent | AGT | Auto-detection | 🟡 High | 6 |

**Phase 3 Total:** 17 tests

#### Phase 4 Categories (Requires Phase 3)

| Category | Code | Component | Priority | Tests |
|----------|------|-----------|----------|-------|
| Integration | INT | Cross-component | 🟢 Medium | 4 |

**Phase 4 Total:** 4 tests

### 4.5 Adding New Category

เมื่อต้องการเพิ่ม category ใหม่:

```markdown
| [Name] | [CODE] | [Component] | [Priority] | [Count] | ⏳ |
```

แล้วสร้าง Section ใหม่ใน Section 5 ตาม pattern:

```markdown
### 5.X TC-[CODE]: [Category Name]

| ID | Test Case | Command/Action | Expected | Status |
|----|-----------|----------------|----------|--------|
| [CODE]-001 | [Description] | [Command] | [Expected Result] | ⏳ |
```

---

## 5) Test Cases

> **หมายเหตุ:** Test Cases แบ่งตาม Phase - ต้องทำตามลำดับ Phase

---

### 📦 PHASE 1: Environment & Direct Script Testing

> **Prerequisites:** Python 3.9+, ffmpeg, API Key configured
> **ไม่ต้อง install skill/agent** - ทดสอบได้เลย

---

#### 5.1 TC-ENV: Environment Check

> **Objective:** ยืนยันว่า environment พร้อมสำหรับการทดสอบ

| ID | Test Case | Command | Expected | Status |
|----|-----------|---------|----------|--------|
| ENV-001 | Python Version | `python --version` | Python 3.9+ | ⏳ |
| ENV-002 | ffmpeg Available | `ffmpeg -version` | Version displayed | ⏳ |
| ENV-003 | ffprobe Available | `ffprobe -version` | Version displayed | ⏳ |
| ENV-004 | gcloud CLI | `gcloud --version` | Version displayed | ⏳ |

---

#### 5.2 TC-VID: Video Generation (Direct Script)

> **Objective:** ทดสอบ video_gen.py ทุก mode และ options (รัน script โดยตรง)

##### 5.2.1 Basic Generation

| ID | Test Case | Command | Expected | Status |
|----|-----------|---------|----------|--------|
| VID-001 | Text-to-Video Basic | `python video_gen.py "A cat walking"` | Video generated | ⏳ |
| VID-002 | With Preset Quick | `python video_gen.py "prompt" --preset quick` | 5s 720p video | ⏳ |
| VID-003 | With Preset Quality | `python video_gen.py "prompt" --preset quality` | 8s 1080p video | ⏳ |
| VID-004 | Dry Run | `python video_gen.py "prompt" --dry-run` | No API call, params shown | ⏳ |
| VID-005 | Show Defaults | `python video_gen.py --show-defaults` | Defaults displayed | ⏳ |

##### 5.2.2 Image-Based Modes

| ID | Test Case | Command | Expected | Status |
|----|-----------|---------|----------|--------|
| VID-006 | Image-to-Video | `python video_gen.py "prompt" --image photo.png` | Video from image | ⏳ |
| VID-007 | First+Last Frames | `python video_gen.py "prompt" --image first.png --last-frame last.png` | Interpolation | ⏳ |
| VID-008 | Reference Asset | `python video_gen.py "prompt" --reference-image ref.png:asset` | Subject preserved | ⏳ |
| VID-009 | Reference Style | `python video_gen.py "prompt" --reference-image style.png:style` | Style applied | ⏳ |

##### 5.2.3 Video Extension & Remix

| ID | Test Case | Command | Expected | Status |
|----|-----------|---------|----------|--------|
| VID-010 | Video Extension | `python video_gen.py "continue" --extend-video source.mp4` | Extended video | ⏳ |
| VID-011 | Remix Mode | `python video_gen.py "new style" --remix source.mp4` | Remixed video | ⏳ |
| VID-012 | Remix + Last Frame | `python video_gen.py "style" --remix src.mp4 --remix-last-frame` | First+Last mode | ⏳ |
| VID-013 | Remix with Time Range | `python video_gen.py "style" --remix src.mp4 --remix-start 0:05 --remix-end 0:10` | Section remixed | ⏳ |

##### 5.2.4 Output Options

| ID | Test Case | Command | Expected | Status |
|----|-----------|---------|----------|--------|
| VID-014 | GCS Output | `python video_gen.py "prompt" --storage-uri gs://bucket/` | Video in GCS | ⏳ |
| VID-015 | Custom Output Path | `python video_gen.py "prompt" -o custom_name.mp4` | Custom filename | ⏳ |

---

#### 5.3 TC-IMG: Image Generation (Direct Script)

> **Objective:** ทดสอบ image_gen.py ทุก options (รัน script โดยตรง)

| ID | Test Case | Command | Expected | Status |
|----|-----------|---------|----------|--------|
| IMG-001 | Basic Generation | `python image_gen.py "A red apple"` | Image generated | ⏳ |
| IMG-002 | Aspect Ratio 16:9 | `python image_gen.py "prompt" --aspect-ratio 16:9` | Landscape image | ⏳ |
| IMG-003 | Aspect Ratio 9:16 | `python image_gen.py "prompt" --aspect-ratio 9:16` | Portrait image | ⏳ |
| IMG-004 | Aspect Ratio 4:3 | `python image_gen.py "prompt" --aspect-ratio 4:3` | 4:3 image | ⏳ |
| IMG-005 | Reference Image | `python image_gen.py "prompt" --image ref.png` | Style applied | ⏳ |
| IMG-006 | Show Defaults | `python image_gen.py --show-defaults` | Defaults shown | ⏳ |
| IMG-007 | Custom Output | `python image_gen.py "prompt" -o custom.png` | Custom filename | ⏳ |

---

#### 5.4 TC-CFG: Configuration System (Direct Script)

> **Objective:** ทดสอบ config.py และ config.json (รัน script โดยตรง)

| ID | Test Case | Action | Expected | Status |
|----|-----------|--------|----------|--------|
| CFG-001 | Load Config | `python -c "from config import Config; c=Config(); print(c.load())"` | Config loaded | ⏳ |
| CFG-002 | Multi-Project | Test with 2+ projects in config.json | Project selection works | ⏳ |
| CFG-003 | Project Flag | `python video_gen.py "prompt" --project my-project` | Correct project used | ⏳ |
| CFG-004 | Environment Fallback | Unset config, use `GOOGLE_API_KEY` | Env var used | ⏳ |

---

### 🔧 PHASE 1 COMPLETE CHECKLIST

| Category | Tests | Pass | Fail | Status |
|----------|-------|------|------|--------|
| ENV | 4 | - | - | ⏳ |
| VID | 15 | - | - | ⏳ |
| IMG | 7 | - | - | ⏳ |
| CFG | 4 | - | - | ⏳ |
| **Total** | **30** | - | - | ⏳ |

**Phase 1 Sign-off:** ⏳ Pending

---

### 📦 PHASE 2: Installation to Claude Code

> **Prerequisites:** Phase 1 ✅ Complete
> **Objective:** Install skill/agent ไปยัง Claude Code

---

#### 5.5 TC-INS: Installation & Deployment

> **Objective:** ยืนยันว่า installation ไปยัง Claude Code ถูกต้อง

##### 5.5.1 Core Files Installation

| ID | Test Case | Action | Expected | Status |
|----|-----------|--------|----------|--------|
| INS-001 | Copy Skill to Claude | `cp -r .claude/skills/generative ~/.claude/skills/` | Skill copied | ⏳ |
| INS-002 | Copy Agent to Claude | `cp -r .claude/agents/* ~/.claude/agents/` | Agent copied | ⏳ |
| INS-003 | Verify Skill exists | `ls ~/.claude/skills/generative/SKILL.md` | File exists | ⏳ |
| INS-004 | Verify Agent exists | `ls ~/.claude/agents/generative-media-navigator.md` | File exists | ⏳ |

##### 5.5.2 Dependencies Check

| ID | Test Case | Action | Expected | Status |
|----|-----------|--------|----------|--------|
| INS-005 | Python imports | `python -c "import video_gen"` | No import errors | ⏳ |
| INS-006 | Config import | `python -c "import config"` | No import errors | ⏳ |

---

### 🔧 PHASE 2 COMPLETE CHECKLIST

| Category | Tests | Pass | Fail | Status |
|----------|-------|------|------|--------|
| INS | 6 | - | - | ⏳ |
| **Total** | **6** | - | - | ⏳ |

**Phase 2 Sign-off:** ⏳ Pending

---

### 📦 PHASE 3: Skill & Agent Testing

> **Prerequisites:** Phase 2 ✅ Complete (skill/agent installed)
> ⚠️ **ต้อง install skill/agent ก่อนถึงจะทดสอบได้**

---

#### 5.6 TC-SKL: /generative Skill

> **Objective:** ทดสอบ Claude Code Skill commands

##### 5.6.1 Mode Detection

| ID | Test Case | Trigger | Expected | Status |
|----|-----------|---------|----------|--------|
| SKL-001 | Video Mode | `/generative video "prompt"` | Video workflow starts | ⏳ |
| SKL-002 | Image Mode | `/generative image "prompt"` | Image workflow starts | ⏳ |
| SKL-003 | Info Mode | `/generative info` | Info displayed | ⏳ |
| SKL-004 | Config Mode | `/generative config` | Config menu shown | ⏳ |

##### 5.6.2 Help System

| ID | Test Case | Trigger | Expected | Status |
|----|-----------|---------|----------|--------|
| SKL-005 | Help Main | `/generative help` | Help overview | ⏳ |
| SKL-006 | Help Image | `/generative help image` | Image help | ⏳ |
| SKL-007 | Help Video | `/generative help video` | Video help | ⏳ |
| SKL-008 | Help Examples | `/generative help examples` | Examples shown | ⏳ |
| SKL-009 | Help Modes | `/generative help modes` | 9 modes listed | ⏳ |

##### 5.6.3 Config Sub-commands

| ID | Test Case | Trigger | Expected | Status |
|----|-----------|---------|----------|--------|
| SKL-010 | Config Show | `/generative config show` | Current config | ⏳ |
| SKL-011 | Config Setup | `/generative config setup` | Setup wizard | ⏳ |

---

#### 5.7 TC-AGT: Agent Auto-Detection

> **Objective:** ทดสอบ Agent trigger keywords

##### 5.7.1 Thai Triggers

| ID | Test Case | User Input | Expected | Status |
|----|-----------|------------|----------|--------|
| AGT-001 | Thai Video | "สร้างวิดีโอแมวเดิน" | /generative skill invoked | ⏳ |
| AGT-002 | Thai Image | "ทำรูปแอปเปิ้ลสีแดง" | /generative skill invoked | ⏳ |
| AGT-003 | Thai Generate | "generate วิดีโอพระอาทิตย์ตก" | /generative skill invoked | ⏳ |

##### 5.7.2 English Triggers

| ID | Test Case | User Input | Expected | Status |
|----|-----------|------------|----------|--------|
| AGT-004 | English Video | "create a video of sunset" | /generative skill invoked | ⏳ |
| AGT-005 | English Image | "generate an image of cat" | /generative skill invoked | ⏳ |
| AGT-006 | English Make | "make a video showing rain" | /generative skill invoked | ⏳ |

---

### 🔧 PHASE 3 COMPLETE CHECKLIST

| Category | Tests | Pass | Fail | Status |
|----------|-------|------|------|--------|
| SKL | 11 | - | - | ⏳ |
| AGT | 6 | - | - | ⏳ |
| **Total** | **17** | - | - | ⏳ |

**Phase 3 Sign-off:** ⏳ Pending

---

### 📦 PHASE 4: Integration Testing

> **Prerequisites:** Phase 3 ✅ Complete
> ⚠️ **ต้องผ่าน Phase 3 ก่อนถึงจะทดสอบได้**

---

#### 5.8 TC-INT: Integration Tests

> **Objective:** ทดสอบ cross-component workflows

| ID | Test Case | Workflow | Expected | Status |
|----|-----------|----------|----------|--------|
| INT-001 | Skill → video_gen | Use skill to generate video | End-to-end works | ⏳ |
| INT-002 | Skill → image_gen | Use skill to generate image | End-to-end works | ⏳ |
| INT-003 | Agent → Skill → CLI | Natural language → skill → CLI | Full pipeline | ⏳ |
| INT-004 | Image → Video | Generate image, use as video input | Multi-tool workflow | ⏳ |

---

### 🔧 PHASE 4 COMPLETE CHECKLIST

| Category | Tests | Pass | Fail | Status |
|----------|-------|------|------|--------|
| INT | 4 | - | - | ⏳ |
| **Total** | **4** | - | - | ⏳ |

**Phase 4 Sign-off:** ⏳ Pending

---

### 🎯 ALL PHASES SUMMARY

| Phase | Name | Tests | Status | Prerequisites |
|-------|------|-------|--------|---------------|
| 1 | Environment & Scripts | 30 | ⏳ | None - ทำได้เลย |
| 2 | Installation | 6 | ⏳ | Phase 1 ✅ |
| 3 | Skill & Agent | 17 | ⏳ | Phase 2 ✅ |
| 4 | Integration | 4 | ⏳ | Phase 3 ✅ |
| **Total** | | **57** | ⏳ | |

---

## 6) Test Execution Log

### 6.1 Test Run Template

```markdown
## Test Run: [DATE]

**Tester:** [Name]
**Environment:** [Description]
**Session:** [Session ID]

### Summary
| Category | Total | Pass | Fail | Skip |
|----------|-------|------|------|------|
| INS | X | X | X | X |
| VID | X | X | X | X |
| ... | ... | ... | ... | ... |

### Results
[Detailed results per test case]

### Issues Found
[List of issues discovered]
```

### 6.2 Test Run History

| Run # | Date | Tester | Total | Pass | Fail | Notes |
|-------|------|--------|-------|------|------|-------|
| - | - | - | - | - | - | No runs yet |

---

## 7) Issue Tracking

### 7.1 Issue Template

```markdown
### Issue #[NUMBER]: [Title]

**Test ID:** [Test Case ID]
**Severity:** 🔴 Critical / 🟠 High / 🟡 Medium / 🟢 Low
**Status:** Open / In Progress / Fixed / Closed

**Description:**
[Detailed description]

**Steps to Reproduce:**
1. [Step 1]
2. [Step 2]

**Expected:** [What should happen]
**Actual:** [What actually happened]

**Error Message:**
```
[Error if any]
```

**Fix:** [How it was fixed, if fixed]
**Related Commit:** [Commit hash, if fixed]
```

### 7.2 Issue Log

| Issue # | Test ID | Severity | Title | Status |
|---------|---------|----------|-------|--------|
| - | - | - | No issues yet | - |

---

## 8) Sign-Off Criteria

### 8.1 MVP (Minimum Viable Product)

การ sign-off MVP ต้องผ่านเงื่อนไขเหล่านี้:

| Criteria | Target | Current | Status |
|----------|--------|---------|--------|
| INS Tests Pass | 100% | - | ⏳ |
| VID Basic Tests Pass (VID-001 to VID-005) | 100% | - | ⏳ |
| IMG Basic Tests Pass (IMG-001 to IMG-003) | 100% | - | ⏳ |
| No Critical Issues | 0 | - | ⏳ |

### 8.2 Full Release

| Criteria | Target | Current | Status |
|----------|--------|---------|--------|
| All Test Categories | 100% pass | - | ⏳ |
| Critical Issues | 0 open | - | ⏳ |
| High Issues | 0 open | - | ⏳ |
| Documentation Updated | Yes | - | ⏳ |

---

## 9) Future Test Extensions

### 9.1 Planned Test Categories

| Category | Code | Description | Priority | ETA |
|----------|------|-------------|----------|-----|
| Performance | PRF | Response time, memory usage | 🟢 Low | TBD |
| Load | LOD | Multiple concurrent requests | 🟢 Low | TBD |
| Security | SEC | API key handling, data privacy | 🟡 Medium | TBD |
| Accessibility | A11Y | Skill/Agent usability | 🟢 Low | TBD |

### 9.2 Test Expansion Guidelines

เมื่อเพิ่ม features ใหม่:

1. **สร้าง Test Cases** ก่อน implement (TDD approach)
2. **ใช้ NEW-XXX** สำหรับ temporary tests
3. **Re-categorize** เมื่อ feature stable
4. **Update Category Count** ใน Section 4.1

---

## 10) Appendix

### 10.1 Quick Commands Reference

```bash
# Installation Check
ls ~/.claude/skills/generative/SKILL.md
ls ~/.claude/agents/generative-media-navigator.md

# Python Scripts Help
python video_gen.py --help
python image_gen.py --help

# Dry Run Tests (No API calls)
python video_gen.py "test prompt" --dry-run
python video_gen.py --show-defaults
python image_gen.py --show-defaults

# Skill Tests (in Claude Code)
/generative info
/generative help
/generative config show
```

### 10.2 Test Assets Preparation

```bash
# Create test assets directory
mkdir -p test/assets

# Sample test image (512x512)
# - Use any PNG image for testing

# Sample test video (24fps, <8s)
# - Must be exactly 24fps for video extension
# - Use ffmpeg to check: ffprobe -v error -select_streams v:0 -show_entries stream=r_frame_rate -of csv=p=0 video.mp4
```

---

## 11) Usage Pattern Recording

> **Purpose:** บันทึกรูปแบบการใช้งานจริงเพื่อนำไปเสริมเป็น pages/docs ในอนาคต

### 11.1 Recording Goals

| Goal | Description |
|------|-------------|
| **Command Examples** | บันทึก command ที่ใช้จริงและ output ที่ได้ |
| **Error Patterns** | บันทึก error messages และวิธีแก้ไข |
| **Best Practices** | บันทึก workflow ที่ดีที่สุดจากการทดสอบ |
| **Edge Cases** | บันทึก cases พิเศษที่ users อาจเจอ |
| **Sample Outputs** | เก็บ sample outputs สำหรับ documentation |

### 11.2 Usage Pattern Template

```markdown
## Pattern: [Pattern Name]

**Category:** [VIDEO/IMAGE/SKILL/AGENT]
**Difficulty:** ⭐/⭐⭐/⭐⭐⭐ (Basic/Intermediate/Advanced)
**Test ID:** [Related test ID, e.g., VID-006]

### Use Case
[อธิบาย scenario ที่ใช้ pattern นี้]

### Command
\`\`\`bash
[exact command used]
\`\`\`

### Output Sample
\`\`\`
[sample output - truncated if needed]
\`\`\`

### Tips
- [Tip 1]
- [Tip 2]

### Common Errors
| Error | Cause | Solution |
|-------|-------|----------|
| [error msg] | [why] | [how to fix] |
```

### 11.3 Usage Pattern Categories

| Category | Code | Target Docs Page |
|----------|------|------------------|
| Video Generation Patterns | VGP | pages/docs/video-generation.md |
| Image Generation Patterns | IGP | pages/docs/image-generation.md |
| Skill Usage Patterns | SUP | pages/docs/skill-usage.md |
| Agent Interaction Patterns | AIP | pages/docs/agent-interaction.md |
| Configuration Patterns | CFP | pages/docs/configuration.md |
| Troubleshooting Patterns | TRP | pages/docs/troubleshooting.md |

### 11.4 Recording Workflow

```
Test Execution
  ↓
Discover Interesting Pattern?
  ↓ YES
Record Using Template (11.2)
  ↓
Categorize (11.3)
  ↓
Add to Usage Pattern Log (11.5)
  ↓
Later: Transfer to pages/docs
```

### 11.5 Usage Pattern Log

> **Note:** บันทึก patterns ที่พบระหว่างการทดสอบ

| # | Pattern Name | Category | Test ID | Difficulty | Status |
|---|--------------|----------|---------|------------|--------|
| - | (No patterns recorded yet) | - | - | - | - |

### 11.6 Sample Patterns to Record

**Priority patterns ที่ควรบันทึก:**

| Priority | Pattern | Why Important |
|----------|---------|---------------|
| 🔴 High | Text-to-Video basic workflow | Most common use case |
| 🔴 High | Image-to-Video workflow | Second most common |
| 🟠 Medium | Video extension workflow | Need clear steps |
| 🟠 Medium | Skill mode detection | Users need examples |
| 🟡 Low | Multi-project config | Advanced use case |
| 🟡 Low | GCS output configuration | Optional feature |

### 11.7 Documentation Transfer Checklist

เมื่อพร้อมที่จะ transfer patterns ไป pages/docs:

- [ ] Pattern ทดสอบแล้ว (✅ Pass)
- [ ] Command ถูกต้อง 100%
- [ ] Output sample เป็นปัจจุบัน
- [ ] Tips validated จากการใช้งานจริง
- [ ] Error solutions ใช้ได้จริง

---

> Full history: [changelog/testing.changelog.md](../changelog/testing.changelog.md)
