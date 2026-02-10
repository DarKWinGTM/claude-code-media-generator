# 🎯 Smart Skill Design Document

## Version: 3.1
## Date: 2026-02-04
## Parent Document: [design.md](./design.md)
## Status: ✅ Implemented v3.0 (Unified Generative Skill)

---

## 📋 Document Overview

เอกสารนี้ออกแบบ **Smart Skill System** ที่เป็นตัวกลางระหว่าง:

```
Vibe Code User <---> SKILL <---> *.py (video_gen.py / image_gen.py)
```

### Problem Statement (ปัญหาที่ต้องแก้)

**Current SKILL Issues:**

| ปัญหา | ผลกระทบ |
|-------|---------|
| ไม่มี Prompt Assistance | User ต้องคิด prompt เอง ไม่มีคำแนะนำ |
| ไม่มี Requirement Discovery | ไม่สำรวจความต้องการก่อน gen |
| Args ไม่ครบถ้วน | พลาด options ที่สำคัญ |
| ไม่มี Standard Workflow | ไม่มีลำดับขั้นตอนที่ชัดเจน |
| ไม่อ้างอิง Design Docs | ขาดข้อมูลอ้างอิงที่ถูกต้อง |

### Vision: Smart Skill

```
User บอกความต้องการแบบ vague
    ↓
SKILL สำรวจ + ถามคำถาม
    ↓
SKILL ช่วยออกแบบ Prompt
    ↓
User ยืนยัน/ปรับแก้
    ↓
SKILL ดำเนินการ Generate
    ↓
SKILL แสดงผลลัพธ์
```

---

## 1. Smart Skill Architecture

### 1.1 Skill Layers

```
┌─────────────────────────────────────────────────┐
│  Layer 1: User Interface                        │
│  - Slash command parsing                        │
│  - Natural language understanding               │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  Layer 2: Requirement Discovery                 │
│  - AskUserQuestion for clarification            │
│  - Context analysis (files, project)            │
│  - Intent detection                             │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  Layer 3: Prompt Engineering                    │
│  - Prompt suggestions                           │
│  - Best practices application                   │
│  - Negative prompt recommendations              │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  Layer 4: Pre-flight Validation                 │
│  - API key check                                │
│  - Config verification                          │
│  - Resource availability                        │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  Layer 5: Execution                             │
│  - CLI command construction                     │
│  - python *.py execution                        │
│  - Result handling                              │
└─────────────────────────────────────────────────┘
```

### 1.2 Standard Workflow (6 Phases)

```
Phase 0: Trigger Detection
    ↓ User invokes /generate-video or /generate-image
Phase 1: Requirement Discovery
    ↓ Survey user needs via AskUserQuestion
Phase 2: Prompt Engineering
    ↓ Build and refine prompt with user
Phase 3: Pre-flight Checks
    ↓ Verify API, config, resources
Phase 4: Preview & Confirm
    ↓ Show final command, cost estimate
Phase 5: Execute & Report
    ↓ Run generation, show results
```

---

## 2. Requirement Discovery System

### 2.1 Discovery Questions (Video)

**Question 1: Purpose/Use Case**
```
Question: "วิดีโอนี้จะใช้ทำอะไร?"
Options:
  1. "Social Media (TikTok, Reels)" → 9:16, short, dynamic
  2. "YouTube / Presentation" → 16:9, longer, professional
  3. "Product Demo" → Focus on clarity, good lighting
  4. "Creative / Art" → Experimental, unique styles
```

**Question 2: Content Type**
```
Question: "เนื้อหาหลักในวิดีโอเป็นแบบไหน?"
Options:
  1. "คน/ตัวละคร" → person-generation: allow
  2. "สินค้า/วัตถุ" → Focus on object, lighting
  3. "ธรรมชาติ/วิว" → Cinematic, wide shots
  4. "Abstract/Motion Graphics" → Geometric, patterns
```

**Question 3: Style/Mood**
```
Question: "อารมณ์/สไตล์ที่ต้องการ?"
Options:
  1. "Cinematic / ภาพยนตร์" → Film grain, dramatic lighting
  2. "Minimalist / เรียบง่าย" → Clean, simple
  3. "Vibrant / สดใส" → Bright colors, energetic
  4. "Dark / Moody" → Low key, atmospheric
```

**Question 4: Generation Mode**
```
Question: "ต้องการสร้างวิดีโอแบบไหน?"
Options:
  1. "Text-to-Video (สร้างจาก prompt)" → Standard generation
  2. "Image-to-Video (ทำรูปเคลื่อนไหว)" → Trigger Image Attachment flow
  3. "Video Extension (ต่อวิดีโอ)" → Trigger Video Source flow
  4. "Reference Image (ใช้รูปอ้างอิง)" → Trigger Reference Attachment flow
```

**Question 5: Quality Settings** (NEW v2.1)
```
Question: "ต้องการคุณภาพระดับไหน?"
Options:
  1. "Quick Draft (720p, 5 sec)" → --resolution 720p --duration 5
     Cost: ~$0.75-1.75
  2. "Standard (720p, 8 sec)" → --resolution 720p --duration 8
     Cost: ~$1.20-2.80
  3. "High Quality (1080p, 8 sec)" → --resolution 1080p --duration 8
     Cost: ~$2.80+
  4. "Custom" → Ask for specific duration and resolution
```

**Question 5.1: Custom Duration** (Conditional - if Custom selected)
```
Question: "ความยาววิดีโอที่ต้องการ (วินาที)?"
Options:
  1. "5 วินาที" → --duration 5
  2. "6 วินาที" → --duration 6
  3. "7 วินาที" → --duration 7
  4. "8 วินาที" → --duration 8
```

**Question 5.2: Custom Resolution** (Conditional - if Custom selected)
```
Question: "ความละเอียดที่ต้องการ?"
Options:
  1. "720p (Standard)" → --resolution 720p
  2. "1080p (Full HD)" → --resolution 1080p
```

### 2.1.1 Image Attachment Flow (NEW v2.1)

**Triggered when:** Question 4 = "Image-to-Video" or "Reference Image"

```
Flow: Image Attachment

Step 1: Search for images in project
  → Use Glob: ls *.{jpg,png,jpeg,webp} +generated_images/*.png
  → List found images

Step 2: AskUserQuestion
  Question: "เลือกรูปภาพที่ต้องการใช้"
  Options:
    1. [Image from search results - if found]
    2. "ระบุ path เอง" → Ask for file path
    3. "ใช้ URL" → Ask for image URL
    4. "ยังไม่มีภาพ - สร้างใหม่" → Switch to Text-to-Video

Step 3: Verify image exists
  → ls -la "$IMAGE_PATH"
  → If not found: Ask again or suggest alternatives

Step 4: Add to command
  → For Image-to-Video: --image "$IMAGE_PATH"
  → For Reference: --reference-image "$IMAGE_PATH:asset"
```

**Image Search Pattern:**
```bash
# Search common image locations
ls -la *.{jpg,png,jpeg,webp} 2>/dev/null
ls -la +generated_images/*.{jpg,png} 2>/dev/null
ls -la images/*.{jpg,png,jpeg,webp} 2>/dev/null
```

### 2.1.2 Video Source Flow (NEW v2.1)

**Triggered when:** Question 4 = "Video Extension"

```
Flow: Video Source Selection

Step 1: Search for videos in project
  → Use Glob: ls +generated_videos/*.mp4
  → List found videos with timestamps

Step 2: AskUserQuestion
  Question: "เลือกวิดีโอที่ต้องการต่อ"
  Options:
    1. [Videos from search results - sorted by date]
    2. "ระบุ path เอง" → Ask for file path
    3. "ใช้ URL" → Ask for video URL (--extend-video-from-url)
    4. "ยกเลิก" → Back to mode selection

Step 3: Verify video exists
  → ls -la "$VIDEO_PATH"
  → Check for matching metadata file

Step 4: Add to command
  → --extend-video "$VIDEO_PATH"
  → Or --extend-video-from-url "$URL"
```

### 2.2 Discovery Questions (Image)

**Question 1: Purpose**
```
Question: "รูปภาพนี้จะใช้ทำอะไร?"
Options:
  1. "Social Media Post" → 1:1 or 4:5
  2. "YouTube Thumbnail" → 16:9
  3. "Story / Reels Cover" → 9:16
  4. "Website Banner" → 16:9 or custom
```

**Question 2: Style**
```
Question: "สไตล์ภาพที่ต้องการ?"
Options:
  1. "Realistic / สมจริง" → Photo-realistic prompts
  2. "Illustration / วาด" → Art style, cartoon
  3. "3D Render" → CGI, Blender-like
  4. "Abstract / Pattern" → Geometric, textures
```

**Question 3: Content Type** (NEW v2.1)
```
Question: "เนื้อหาหลักในภาพคืออะไร?"
Options:
  1. "คน/ตัวละคร" → Portrait focus, natural lighting
  2. "สินค้า/วัตถุ" → Product photography, studio lighting
  3. "ธรรมชาติ/วิว/สถานที่" → Landscape, wide shots
  4. "Diagram/Illustration" → Clean lines, suggest --doc
```

**Question 4: Aspect Ratio** (NEW v2.1)
```
Question: "สัดส่วนภาพที่ต้องการ?"
Options:
  1. "1:1 (Square)" → Instagram, Profile pics
     → --aspect-ratio 1:1
  2. "16:9 (Wide)" → YouTube, Banners
     → --aspect-ratio 16:9
  3. "9:16 (Tall)" → Stories, TikTok
     → --aspect-ratio 9:16
  4. "4:3 / 3:4 (Standard)" → Photos, Portraits
     → --aspect-ratio 4:3 or 3:4
```

**Question 5: Quality/Size** (NEW v2.1)
```
Question: "ความละเอียดที่ต้องการ?"
Options:
  1. "1K (Standard)" → --image-size 1K
     Best for: Web, Social media
  2. "2K (High Quality)" → --image-size 2K
     Best for: Print, Large displays
  3. "4K (Maximum)" → --image-size 4K
     Best for: Professional, Large prints
```

### 2.2.1 Reference Image Flow (NEW v2.1)

**Triggered when:** User mentions reference image or context

```
Flow: Reference Image Attachment

Step 1: AskUserQuestion
  Question: "ต้องการใช้รูปอ้างอิงไหม?"
  Options:
    1. "ใช่ - ระบุ path" → Ask for file path
    2. "ใช่ - ค้นหาในโปรเจค" → Search and list images
    3. "ไม่ใช้รูปอ้างอิง" → Continue without reference

Step 2: If using reference
  → Verify image exists
  → Add --image "$REF_PATH" to command
```

### 2.2.2 Document Context Flow (NEW v2.1)

**Triggered when:** Content Type = "Diagram/Illustration"

```
Flow: Document Context

Step 1: Search for documents
  → ls *.md README.md docs/*.md

Step 2: AskUserQuestion
  Question: "ต้องการใช้เอกสารเป็น context ไหม?"
  Options:
    1. "README.md" → --doc README.md
    2. "ระบุไฟล์อื่น" → Ask for file path
    3. "ไม่ใช้ document context" → Continue without --doc
```

---

## 3. Prompt Engineering System

### 3.1 Prompt Builder Template

**Video Prompt Structure:**
```
[Subject] + [Action] + [Setting] + [Style] + [Camera] + [Lighting]
```

**Example Transformation:**
```
User Input: "แมวเล่นลูกบอล"

Smart Prompt:
"A fluffy orange cat playfully batting a red ball across a sunlit
hardwood floor, natural afternoon lighting streaming through windows,
shallow depth of field, cinematic framing, smooth camera tracking"
```

### 3.2 Prompt Enhancement Rules

| Element | Enhancement Strategy |
|---------|---------------------|
| **Subject** | Add details: color, size, texture, material |
| **Action** | Add motion quality: smooth, dynamic, gentle |
| **Setting** | Add environment: time of day, weather, location details |
| **Style** | Add reference: cinematic, documentary, artistic |
| **Camera** | Add movement: tracking, pan, static, dolly |
| **Lighting** | Add quality: soft, dramatic, natural, studio |
| **Audio** | Add sounds: dialogue, effects, ambient (Veo 3.x only) |

### 3.3 Audio Prompting (Veo 3.x Only)

> **Added:** v2.3 (2026-02-03)

**Audio is a model-level feature:**
- **Veo 3.1/3.0**: Audio always generated (cannot disable)
- **Veo 2.0**: Silent only (no audio capability)

**Audio Control Through Prompts:**

| Audio Type | Technique | Example |
|------------|-----------|---------|
| **Dialogue** | Use quotes | `A man says "Welcome to the future"` |
| **Sound Effects** | Describe explicitly | `tires screeching, engine roaring` |
| **Ambient Noise** | Describe soundscape | `birds chirping, wind rustling` |
| **Music/Mood** | Describe music | `dramatic orchestral music swells` |
| **Silence** | Request quiet | `in complete silence, no sound` |

**Audio-Enhanced Prompt Example:**
```
Basic: "A car driving on a road"

With Audio: "A sports car accelerating on an empty highway,
engine roaring loudly, tires screeching as it takes a turn,
wind rushing past the windows"
```

**Tips:**
- Be specific about sound sources
- Include ambient sounds for realism
- Use descriptive words: loud, soft, echoing, muffled
- For dialogue, use quotation marks

### 3.4 Negative Prompt Suggestions

**Auto-suggest based on content:**

| Content Type | Suggested Negative Prompts |
|--------------|---------------------------|
| **People** | "distorted face, extra limbs, bad anatomy" |
| **Product** | "blurry, low quality, watermark" |
| **Nature** | "artifacts, glitches, unrealistic colors" |
| **Text/Logo** | "misspelled, distorted text, unclear" |

### 3.4 Prompt Preview & Refinement

```
SKILL: "Based on your requirements, here's a suggested prompt:"

┌─────────────────────────────────────────────────────────┐
│ Prompt Preview                                          │
├─────────────────────────────────────────────────────────┤
│ "A professional product shot of a sleek smartphone      │
│ rotating slowly on a white pedestal, soft studio        │
│ lighting with subtle reflections, 4K quality,           │
│ commercial advertisement style"                         │
├─────────────────────────────────────────────────────────┤
│ Settings:                                               │
│   Model: veo-3.1-generate-preview                       │
│   Duration: 5 seconds                                   │
│   Aspect Ratio: 16:9                                    │
│   Estimated Cost: $1.75                                 │
└─────────────────────────────────────────────────────────┘

Use `AskUserQuestion`:
  1. "ใช้ prompt นี้เลย" → Proceed
  2. "ปรับแก้ prompt" → User edits
  3. "เริ่มใหม่" → Back to discovery
```

---

## 4. Complete CLI Arguments Reference

### 4.1 video_gen.py Arguments (v2.27)

> **Reference:** [video.design.md](./video.design.md) v2.27.1

#### Core Arguments

| Argument | Type | Default | Description |
|----------|------|---------|-------------|
| `prompt` | string | (required) | Text description of video |

#### Output Options

| Argument | Type | Default | Description |
|----------|------|---------|-------------|
| `-o, --output` | string | auto | Output video file path |
| `--output-json` | string | - | Save metadata to JSON |
| `--no-metadata` | flag | false | Skip metadata file creation |

#### Generation Parameters

| Argument | Type | Default | Description |
|----------|------|---------|-------------|
| `--preset` | choice | - | quick, quality, extend, budget |
| `--model` | string | veo-3.1-generate-preview | Model to use |
| `--duration` | int | 5 | Video length (5-8 seconds) |
| `--aspect-ratio` | string | 16:9 | 16:9, 9:16, or 1:1 |
| `--resolution` | string | 720p | 720p or 1080p |
| `--negative-prompt` | string | - | What to avoid |
| `--person-generation` | choice | - | allow or block |
| `--storage-uri` | string | - | GCS bucket for output |
| `--upload-gcs` | string | - | Upload local file to GCS first |

#### Input Options (Mode Selection)

| Argument | Type | Mode Triggered | Description |
|----------|------|----------------|-------------|
| `--image` | string | image_to_video | First frame image |
| `--last-frame` | string | first_last_frames | Last frame image |
| `--extend-video` | string | video_extension | Video to extend |
| `--extend-video-from-url` | string | video_extension | URL to download + extend |
| `--reference-asset` | string[] | reference_asset | Up to 3 reference images |
| `--reference-style` | string | reference_style | Style reference image |
| `--reference-image` | string | reference_* | Unified format PATH:TYPE |
| `--video` | string | insert/remove | Input video for editing |
| `--mask` | string | insert/remove | Mask image for editing |
| `--mask-mode` | choice | - | insert or remove |
| `--resize-mode` | choice | - | pad or crop |

#### Authentication Options

| Argument | Type | Default | Description |
|----------|------|---------|-------------|
| `--auth` | choice | api-key | api-key or service-account |
| `--api-key` | string | env | Google API key |
| `--credentials` | string | - | Service account JSON |
| `--project-id` | string | - | GCP project ID |
| `--project` | string | - | Config project name |
| `--location` | string | us-central1 | GCP region |
| `--endpoint` | choice | gemini | gemini or vertex |

#### Debug Options

| Argument | Type | Description |
|----------|------|-------------|
| `-v, --verbose` | flag | Verbose output |
| `--dry-run` | flag | Test without generating |
| `--estimate-cost` | flag | Show cost only |
| `--show-defaults` | flag | Preview settings |

### 4.2 image_gen.py Arguments (v1.4)

> **Reference:** [image.design.md](./image.design.md) v1.6

#### Core Arguments

| Argument | Type | Default | Description |
|----------|------|---------|-------------|
| `prompt` | string | (optional) | Text description of image |

#### Input Options

| Argument | Type | Description |
|----------|------|-------------|
| `-i, --image` | string[] | Reference image(s) |
| `-d, --doc` | string[] | Document(s) for context |

#### Generation Parameters

| Argument | Type | Default | Description |
|----------|------|---------|-------------|
| `-m, --model` | string | gemini-3-pro-image-preview | Model to use |
| `-ar, --aspect-ratio` | choice | 1:1 | 1:1, 3:4, 4:3, 9:16, 16:9 |
| `-s, --image-size` | choice | 1K | 1K, 2K, 4K |

#### Project & Debug

| Argument | Type | Description |
|----------|------|-------------|
| `--project` | string | Config project name |
| `--estimate-cost` | flag | Show cost only |
| `--show-defaults` | flag | Preview settings |

---

## 5. 8 Video Generation Modes

> **Reference:** [video.design.md Section 4](./video.design.md)

### 5.1 Mode Detection Matrix

| Mode | Required Args | Optional Args |
|------|--------------|---------------|
| **text_to_video** | prompt only | --duration, --aspect-ratio |
| **image_to_video** | prompt + --image | --duration |
| **first_last_frames** | prompt + --image + --last-frame | --duration |
| **video_extension** | prompt + --extend-video | --storage-uri |
| **reference_asset** | prompt + --reference-asset (1-3) | --duration |
| **reference_style** | prompt + --reference-style | --model veo-2.0-exp |
| **insert_objects** | prompt + --video + --mask --mask-mode insert | - |
| **remove_objects** | prompt + --video + --mask --mask-mode remove | - |

### 5.2 Mode-Specific Prompts

**Discovery Question for Mode:**
```
Question: "คุณต้องการสร้างวิดีโอแบบไหน?"
Options:
  1. "Text-to-Video (สร้างใหม่จาก prompt)"
  2. "Image-to-Video (ทำให้รูปเคลื่อนไหว)"
  3. "Video Extension (ต่อวิดีโอให้ยาวขึ้น)"
  4. "With Reference (ใช้รูปอ้างอิงสไตล์/ตัวละคร)"
```

**Follow-up based on selection:**

- **Text-to-Video** → Go to Prompt Engineering
- **Image-to-Video** → Ask for image file, then Prompt Engineering
- **Video Extension** → Ask for source video, check GCS, then prompt for continuation
- **Reference** → Ask for reference images and type (asset/style)

---

## 6. Standard Workflow Implementation

### 6.1 Phase 0: Trigger Detection

```yaml
# Skill YAML Header
triggers:
  - "/generate-video"
  - "/gen-video"
  - "/video"
  - "สร้างวิดีโอ"
  - "generate video"
```

### 6.2 Phase 1: Requirement Discovery

```markdown
**Step 1.1:** Check if user provided details
  - If detailed prompt given → Skip to Phase 2
  - If vague/no prompt → Start discovery questions

**Step 1.2:** Discovery Questions (via AskUserQuestion)
  - Purpose/Use Case
  - Content Type
  - Style/Mood
  - Duration/Quality preference

**Step 1.3:** Collect context
  - Read relevant project files if mentioned
  - Check for existing videos in directory
  - Look for reference images
```

### 6.3 Phase 2: Prompt Engineering

```markdown
**Step 2.1:** Build initial prompt from discovery
  - Apply [Subject] + [Action] + [Setting] + [Style] template
  - Add quality enhancers

**Step 2.2:** Present prompt for review
  - Show constructed prompt
  - Show suggested settings
  - Show estimated cost

**Step 2.3:** Refinement loop
  - User can: Accept / Modify / Start over
  - If modify → Apply changes, show again
```

### 6.4 Phase 3: Pre-flight Checks

```markdown
**Step 3.1:** API Authentication
  - Check GOOGLE_API_KEY
  - If missing → AskUserQuestion for key

**Step 3.2:** Config Check
  - Check config.json exists
  - Validate project settings

**Step 3.3:** Resource Check
  - For extend mode: Check source video exists
  - For image mode: Check reference image exists
  - For GCS: Validate storage-uri pattern
```

### 6.5 Phase 4: Preview & Confirm

```markdown
**Step 4.1:** Show final command
```bash
python ~/.claude/skills/generative/video_gen.py "Your refined prompt here" \
  --preset quality \
  --aspect-ratio 16:9 \
  --duration 8
```

**Step 4.2:** Show cost estimate
  - Call --estimate-cost first
  - Display: Model, Duration, Estimated Cost

**Step 4.3:** Confirm execution
```
Question: "พร้อมสร้างวิดีโอหรือยัง?"
Options:
  1. "สร้างเลย" → Execute
  2. "แก้ไข prompt" → Back to Phase 2
  3. "ยกเลิก" → Exit
```
```

### 6.6 Phase 5: Execute & Report

```markdown
**Step 5.1:** Execute command
  - Run python ~/.claude/skills/generative/video_gen.py with constructed args
  - Show progress if verbose

**Step 5.2:** Handle result
  - SUCCESS: Show output file path, thumbnail if available
  - ERROR: Show error message, suggest fixes

**Step 5.3:** Post-generation
  - Offer to generate more
  - Offer to extend the generated video
  - Save successful prompt for future reference
```

---

## 7. Skill File Structure

### 7.1 Recommended Structure

```
.claude/skills/
├── generate-video/
│   ├── SKILL.md              # Main skill definition
│   ├── prompts/
│   │   ├── discovery.md      # Discovery questions
│   │   ├── engineering.md    # Prompt templates
│   │   └── examples.md       # Example prompts
│   └── reference/
│       ├── args.md           # CLI arguments reference
│       └── modes.md          # Mode descriptions
│
└── generate-image/
    ├── SKILL.md              # Main skill definition
    ├── prompts/
    │   ├── discovery.md      # Discovery questions
    │   └── styles.md         # Style templates
    └── reference/
        └── args.md           # CLI arguments reference
```

### 7.2 Updated SKILL.md Structure

```yaml
---
name: generate-video
description: Smart video generation with prompt assistance
argument-hint: [prompt] or describe what you want
disable-model-invocation: true
allowed-tools:
  - Bash(python ~/.claude/skills/generative/video_gen.py *)
  - Bash(echo *)
  - Bash(export *)
  - Bash(ls *)
  - Read
  - Write
  - Glob
  - AskUserQuestion
---

# Generate Video with Smart Assistance

## References

- **Design:** [video.design.md](design/video.design.md) v2.27.1
- **Arguments:** [CLI Reference](#complete-arguments)
- **Modes:** [8 Generation Modes](#modes)

## Standard Workflow

[Phase 0-5 as described above]

## Complete Arguments

[Full argument tables from Section 4]

## Modes

[Mode descriptions from Section 5]
```

---

## 8. Success Metrics

### 8.1 User Experience Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Prompt Quality | 80%+ satisfaction | User accepts suggested prompt |
| Discovery Efficiency | ≤4 questions | Questions before prompt ready |
| Time to Generate | <2 min | From invoke to execution |
| First-time Success | 90%+ | Generation succeeds on first try |

### 8.2 Technical Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| Argument Coverage | 100% | All args documented in SKILL |
| Mode Coverage | 8/8 | All modes accessible via SKILL |
| Error Recovery | 100% | All errors have suggested fixes |
| Design Reference | Always | Links to design docs in SKILL |

---

## 9. Implementation Phases

### Phase 1: Foundation ✅ COMPLETED
- [x] Basic SKILL.md for video and image
- [x] Pre-flight checks (API key, config)
- [x] AskUserQuestion integration
- [x] Complete argument reference

### Phase 2: Discovery System ✅ COMPLETED
- [x] Implement discovery questions (4 for video, 3 for image)
- [x] Add mode detection logic (8 video modes)
- [x] Create question flow based on mode

### Phase 3: Prompt Engineering ✅ COMPLETED
- [x] Build prompt templates
- [x] Add enhancement suggestions
- [x] Implement preview & refinement loop

### Phase 4: Polish ✅ COMPLETED
- [x] Add example prompts library
- [x] Implement error recovery suggestions
- [x] Add post-generation options

---

## 10. Open Questions

1. **Skill File Size Limit?** - How large can SKILL.md be before it affects performance?

2. **Multi-file Skills?** - Can we reference external .md files from SKILL.md?

3. **Context Persistence?** - How to remember user preferences across sessions?

4. **Image Preview?** - Can skill show generated image/video thumbnail in response?

---

## 11. Gap Analysis & Future Features

> **Added:** v2.2 (2026-02-03)
> **Purpose:** Document missing features and plan future enhancements

### 11.1 Current Feature Coverage

#### ✅ Implemented Features (40+)

**Foundation Layer:**
- YAML Skill Configuration (name, description, allowed-tools)
- Design Document References (video.design.md, image.design.md)
- 6-Phase Standard Workflow

**Requirement Discovery:**
- Q1: Purpose/Use Case (Video + Image)
- Q2: Content Type (Video + Image)
- Q3: Style/Mood (Video + Image)
- Q4: Generation Mode (Video) / Aspect Ratio (Image)
- Q5: Quality Settings (Video + Image)
- Q5.1: Custom Duration (Video - Conditional)
- Q5.2: Custom Resolution (Video - Conditional)

**Attachment Flows:**
- Image Attachment Flow (for Image-to-Video)
- Video Source Flow (for Video Extension)
- Reference Image Flow (for Image)
- Document Context Flow (for Image)

**Prompt Engineering:**
- Prompt Structure Templates
- Enhancement Rules by Element
- Example Transformations
- Negative Prompt Suggestions by Content Type
- Preview & Refinement Loop

**Pre-flight Checks:**
- API Key Verification
- Config.json Check
- Mode-Specific Resource Checks

**Execute & Report:**
- Command Construction
- Cost Estimation
- Success/Error Handling
- Post-generation Options

**Reference Tables:**
- Complete CLI Arguments (video_gen.py, image_gen.py)
- 8 Video Generation Modes
- Presets Quick Reference
- Cost Reference Tables

### 11.2 Gap Analysis by Video Mode

| Mode | Discovery | Pre-flight | Execute | Status | Gap |
|------|:---------:|:----------:|:-------:|--------|-----|
| text_to_video | ✅ | ✅ | ✅ | Complete | - |
| image_to_video | ✅ | ✅ | ✅ | Complete | - |
| first_last_frames | ❌ | ⚠️ | ✅ | Partial | Missing discovery for --last-frame |
| video_extension | ✅ | ✅ | ✅ | Complete | - |
| reference_asset | ⚠️ | ✅ | ✅ | Partial | Mixed with style in Q4 |
| reference_style | ⚠️ | ✅ | ✅ | Partial | Mixed with asset in Q4 |
| insert_objects | ❌ | ❌ | ✅ | Incomplete | No discovery, no mask check |
| remove_objects | ❌ | ❌ | ✅ | Incomplete | No discovery, no mask check |

**Legend:**
- ✅ = Complete
- ⚠️ = Partial (needs improvement)
- ❌ = Missing

### 11.3 Missing Features Analysis

#### Priority 1: Critical (Should Implement)

| Feature | Description | Impact | Status |
|---------|-------------|--------|--------|
| **Audio Support** | Audio prompting techniques for Veo 3.x | High | ✅ **Implemented v2.3** |
| **First + Last Frame Flow** | Mode exists but no discovery questions for selecting last frame | Medium - Mode underutilized | ⏳ Pending |
| **Reference Type Clarification** | Asset vs Style not clearly separated in Q4 | Medium - User confusion | ⏳ Pending |

#### Priority 2: Important (Should Consider)

| Feature | Description | Impact | CLI Support |
|---------|-------------|--------|-------------|
| **Error Recovery Table** | Common errors and suggested fixes not documented | Medium - Better UX | N/A |
| **Model Selection Question** | Let user choose model (veo-3.1 vs veo-2.0) | Low-Medium - Power users | `--model` |
| **Seed/Reproducibility** | Allow setting seed for reproducible results | Low - Niche use | `--seed` (if available) |

#### Priority 3: Nice to Have (Future Enhancement)

| Feature | Description | Impact | CLI Support |
|---------|-------------|--------|-------------|
| **Prompt Templates System** | Pre-built templates for common use cases | Medium - Faster workflow | N/A |
| **Batch Generation** | Generate multiple variations at once | Low - Power feature | `--number-of-videos` |
| **Insert/Remove Objects Flow** | Discovery and mask file handling for edit modes | Low - Advanced use | `--video`, `--mask`, `--mask-mode` |
| **User Preference Memory** | Remember user's preferred settings across sessions | Medium - Better UX | N/A |
| **Prompt History** | Save and reuse successful prompts | Medium - Efficiency | N/A |

### 11.4 Feature Decision Matrix

> **Purpose:** ช่วยตัดสินใจว่าจะ implement หรือไม่

| Feature | Effort | Value | Frequency | Decision |
|---------|:------:|:-----:|:---------:|:--------:|
| Audio Support | Low | High | High | ✅ **Done v2.3** |
| First + Last Frame Flow | Medium | Medium | Low | ⏳ Consider |
| Reference Type Separation | Low | Medium | Medium | ⏳ Consider |
| Error Recovery Table | Low | High | High | ⏳ Consider |
| Model Selection | Low | Low | Medium | ⏳ Consider |
| Seed Support | Low | Low | Low | 🔘 Optional |
| Prompt Templates | High | High | Medium | 🔘 Optional |
| Batch Generation | Medium | Medium | Low | 🔘 Optional |
| Insert/Remove Flow | High | Low | Very Low | ❌ Skip |
| Preference Memory | High | Medium | Medium | ❌ Skip (Claude limitation) |

**Decision Legend:**
- ⏳ Consider = Worth reviewing with user
- 🔘 Optional = Implement if time permits
- ❌ Skip = Not worth the effort now

### 11.5 Recommended Next Steps

1. **Audio Support** - Check if Veo 3.1 has audio parameter, add Q6 if supported
2. **Reference Type Separation** - Split Q4 option 4 into "Reference Asset" and "Reference Style"
3. **Error Recovery Table** - Add common errors section to SKILL.md
4. **First + Last Frame** - Add conditional question when user selects Image-to-Video

### 11.6 Implementation Notes

**For Audio Support:**
```
Question 6: Audio (Conditional - if model supports audio)
Question: "Do you want audio in the video?"
Options:
  1. "Yes - Generate with audio" → --enable-audio (or relevant flag)
  2. "No - Silent video" → default
```

**For Reference Type Separation:**
```
# Current Q4 Option 4:
"Reference Image (ใช้รูปอ้างอิง)" → unclear type

# Should become:
Option 4a: "Reference Asset (preserve subject/character)" → --reference-asset
Option 4b: "Reference Style (transfer visual style)" → --reference-style
```

---

## 12. Multi-Tool Workflow Orchestration

> **Added:** v2.4 (2026-02-04)
> **Status:** ✅ Implemented

### 12.1 Design Decision

**Instead of adding CLI flags for cross-tool workflows, Skills orchestrate multi-tool sequences.**

**Rationale:**
- Keep each tool (image_gen.py, video_gen.py) simple and single-purpose
- Skill layer manages sequencing and user confirmation
- No code changes needed - leverage existing capabilities
- More flexible than hardcoded integrations

### 12.2 Supported Workflows

| Workflow | Tools | Trigger |
|----------|-------|---------|
| **Closing Credits** | image_gen → video_gen | "Create closing credits for video" |
| **Video + Thumbnail** | video_gen → image_gen | "Create video with matching thumbnail" |
| **Style Transfer** | (reference) → video_gen | "Create video in this art style" |

### 12.3 Workflow Execution Pattern

```
User Request
  ↓
Skill Discovery: Identify multi-tool workflow
  ↓
Phase 1: First tool execution
  → User confirms output
  ↓
Phase 2: Second tool execution (using Phase 1 output)
  → User confirms final output
```

### 12.4 Example: Closing Credits Workflow

```
User: "สร้าง closing credits สำหรับวิดีโอ main.mp4"
  ↓
Skill: Detected multi-tool workflow (Closing Credits)
  ↓
Phase 1: Generate closing frame
  → python ~/.claude/skills/generative/image_gen.py "Video credits with host avatars" \
      --image avatar1.jpg avatar2.jpg \
      -o closing.png
  ↓
User: Confirms closing.png looks good
  ↓
Phase 2: Extend video with closing frame
  → python ~/.claude/skills/generative/video_gen.py "Fade transition to credits" \
      --extend-video main.mp4 \
      --last-frame closing.png
```

### 12.5 Benefits vs CLI Integration

| Approach | Pros | Cons |
|----------|------|------|
| **Skill Orchestration** (chosen) | No code changes, flexible, user confirmation at each step | Requires Skill understanding |
| **CLI Integration** (rejected) | Single command | Code coupling, less flexible, harder to maintain |

### 12.6 Related Changes

- **image.design.md v1.9**: Removed Template System (Section 11.1)
- **generate-video/SKILL.md**: Added Multi-Tool Workflows section
- **`--template closing-frame`**: NOT implemented - use Skill workflow instead

---

## 13. Unified Generative Skill (Planned v3.0)

> **Added:** v2.5 (2026-02-04)
> **Implemented:** v3.0 (2026-02-04)
> **Status:** ✅ Implemented

### 13.1 Design Decision

**Consolidate separate skills into single unified `/generative` skill.**

**Previous Structure (v2.4):**
```
.claude/skills/
├── generate-video/SKILL.md    ← Deleted
└── generate-image/SKILL.md    ← Deleted
```

**Current Structure (v3.0):**
```
.claude/skills/
└── generative/SKILL.md        ← Unified (798 lines)
```

### 13.2 Rationale

| Problem (Current) | Solution (Unified) |
|-------------------|-------------------|
| Cross-tool workflows need path references | Single skill manages both tools |
| Separate commands to remember | One command with sub-modes |
| Workflow logic duplicated | Centralized workflow management |
| `/generate-video`, `/generate-image` | `/generative video`, `/generative image` |

### 13.3 Command Structure

```bash
/generative image [prompt]    # Generate image
/generative video [prompt]    # Generate video (+ auto image workflow)
/generative info              # Show capabilities and help
/generative config            # Show/manage configuration
```

**Argument Access:**
- `$ARGUMENTS[0]` → Mode: `image`, `video`, `info`, `config`
- `$ARGUMENTS[1..]` → Prompt or options

### 13.4 Mode Specifications

#### 13.4.1 `/generative image`

**Purpose:** Generate images using Imagen API

**Workflow:**
```
/generative image "product photo"
  ↓
Discovery: Ask aspect ratio, style, quality
  ↓
Prompt Engineering: Enhance prompt
  ↓
Execute: python ~/.claude/skills/generative/image_gen.py "enhanced prompt" [options]
  ↓
Report: Show output path and cost
```

#### 13.4.2 `/generative video`

**Purpose:** Generate videos using Veo API (with integrated image workflows)

**Standard Workflow:**
```
/generative video "cat playing"
  ↓
Discovery: Ask purpose, content type, style, mode, quality
  ↓
Prompt Engineering: Enhance prompt (+ audio cues for Veo 3.x)
  ↓
Execute: python ~/.claude/skills/generative/video_gen.py "enhanced prompt" [options]
  ↓
Report: Show output path and cost
```

**Integrated Image Workflow (Auto-detected):**
```
/generative video "create closing credits with avatars"
  ↓
Skill detects: Needs image first (closing frame)
  ↓
Phase 1: Generate image
  → python ~/.claude/skills/generative/image_gen.py "credits frame" --image avatars.jpg -o closing.png
  → User confirms
  ↓
Phase 2: Generate video with image
  → python ~/.claude/skills/generative/video_gen.py "fade to credits" --last-frame closing.png
  ↓
Report: Show final video
```

**Trigger Keywords for Image Integration:**
- "closing credits", "end card", "thumbnail"
- "with avatar", "with logo", "with image"
- "create frame first", "generate image then video"

#### 13.4.3 `/generative info`

**Purpose:** Display skill capabilities and quick reference

**Output:**
```
📹 Generative AI Media Skill

Commands:
  /generative image [prompt]  - Generate images (Imagen 3)
  /generative video [prompt]  - Generate videos (Veo 3.1/2.0)
  /generative info            - This help message
  /generative config          - Show configuration

Video Modes: text_to_video, image_to_video, video_extension, ...
Image Sizes: 1K, 2K, 4K with various aspect ratios

Docs: design/skill.design.md
```

#### 13.4.4 `/generative config`

**Purpose:** Show current configuration and defaults

**Output:**
```
⚙️ Current Configuration

API: Gemini (GOOGLE_API_KEY: [SET])
Project: default (from config.json)

Video Defaults:
  Model: veo-3.1-generate-preview
  Duration: 5 sec
  Resolution: 720p

Image Defaults:
  Model: imagen-3.0-generate-002
  Size: 1024x1024

Storage: gs://bucket-name/ (if configured)
```

### 13.5 SKILL.md Structure

```yaml
---
name: generative
description: AI media generation - images and videos with Imagen and Veo APIs
argument-hint: <image|video|info|config> [prompt or options]
disable-model-invocation: true
allowed-tools: Bash(python *_gen.py *), Bash(echo *), Read, Write, Glob, AskUserQuestion
---

# Unified Generative Skill

## Mode Detection

Check $ARGUMENTS[0]:
- `image` → Image Generation Mode
- `video` → Video Generation Mode
- `info` → Show Help
- `config` → Show Configuration

## Image Mode
[Image generation workflow...]

## Video Mode
[Video generation workflow with integrated image support...]

## Info Mode
[Display capabilities...]

## Config Mode
[Display configuration...]
```

### 13.6 Migration Plan

| Step | Action | Status |
|------|--------|--------|
| 1 | Design unified skill structure (this section) | ✅ Done |
| 2 | Create `.claude/skills/generative/SKILL.md` | ✅ Done (798 lines) |
| 3 | Test all 4 modes | ⏭️ Skipped (user request) |
| 4 | Delete `generate-video/` and `generate-image/` | ✅ Done |
| 5 | Update documentation | ✅ Done |

### 13.7 Benefits

| Aspect | Before (v2.4) | After (v3.0) |
|--------|---------------|--------------|
| **Commands** | 2 separate (`/generate-video`, `/generate-image`) | 1 unified (`/generative`) |
| **Workflow** | Manual cross-tool coordination | Auto-integrated in video mode |
| **Memory** | Learn 2 command names | Learn 1 command + 4 modes |
| **Maintenance** | 2 SKILL.md files | 1 SKILL.md file |
| **Path Issues** | Cross-folder references | Single folder |

---

## 14. Enhanced Help System (v3.1)

> **Added:** v3.1 (2026-02-04)
> **Status:** ✅ Implemented

### 14.1 Design Decision

**Expand `/generative info` + Add `/generative help [topic]` for detailed documentation.**

**Rationale:**
- Quick overview via `info` for experienced users
- Detailed help via `help <topic>` for learning
- Topic-based organization for easy navigation
- No external documentation needed

### 14.2 Command Structure

```bash
/generative info              # Quick overview (existing, unchanged)
/generative help              # Help overview + available topics
/generative help image        # Detailed image generation guide
/generative help video        # Detailed video generation guide
/generative help examples     # 10+ real usage examples
/generative help modes        # 8 video generation modes explained
```

### 14.3 Mode Detection Update

```
Mode = $ARGUMENTS[0]
  → "image" or "i"      → Image Generation Mode
  → "video" or "v"      → Video Generation Mode
  → "info"              → Quick Overview (existing)
  → "help"              → Detailed Help System (NEW v3.1)
  → "config"            → Configuration Mode
  → [other]             → Assume prompt, ask which mode
```

**Help Sub-topic Detection:**
```
$ARGUMENTS[0] = "help"
  → $ARGUMENTS[1] = empty     → Show help overview
  → $ARGUMENTS[1] = "image"   → Show image generation guide
  → $ARGUMENTS[1] = "video"   → Show video generation guide
  → $ARGUMENTS[1] = "examples"→ Show usage examples
  → $ARGUMENTS[1] = "modes"   → Show 8 video modes
  → $ARGUMENTS[1] = [other]   → Show "topic not found" + available topics
```

### 14.4 Help Content Specifications

| Topic | Content | Est. Lines |
|-------|---------|------------|
| **help** (main) | Overview, available topics, shortcuts | ~30 |
| **help image** | CLI args, aspect ratios, sizes, models, examples | ~50 |
| **help video** | CLI args, presets, models, audio tips, examples | ~60 |
| **help examples** | 10+ real usage examples (image + video + workflow) | ~80 |
| **help modes** | 8 video modes with triggers and examples | ~70 |

### 14.5 Content Details

#### 14.5.1 `/generative help` (Main)

```
🎯 GENERATIVE SKILL - HELP SYSTEM

Available Topics:
  /generative help image     - Image generation guide
  /generative help video     - Video generation guide
  /generative help examples  - Usage examples
  /generative help modes     - 8 video generation modes

Quick Commands:
  /generative image [prompt]  - Generate image (alias: i)
  /generative video [prompt]  - Generate video (alias: v)
  /generative info            - Quick overview
  /generative config          - Show configuration

Shortcuts:
  /generative i "cat"         = /generative image "cat"
  /generative v "sunset"      = /generative video "sunset"
```

#### 14.5.2 `/generative help image`

- CLI Arguments reference (--model, --aspect-ratio, --image-size, etc.)
- Aspect Ratios guide (1:1, 16:9, 9:16, 4:3, 3:4) with use cases
- Image Sizes & Cost (1K, 2K, 4K)
- Models & Pricing table
- Quick examples

#### 14.5.3 `/generative help video`

- CLI Arguments reference (--preset, --model, --duration, etc.)
- Input Options (--image, --last-frame, --extend-video, --reference-image)
- Presets guide (quick, quality, extend, budget)
- Models & Audio support table
- Audio prompting tips (Veo 3.x)
- Quick examples

#### 14.5.4 `/generative help examples`

- 5+ Image examples (simple, social media, thumbnail, reference, diagram)
- 5+ Video examples (simple, presets, image-to-video, extend, audio)
- 3+ Workflow examples (closing credits, thumbnail, style transfer)

#### 14.5.5 `/generative help modes`

- All 8 video modes with:
  - Description
  - Usage syntax
  - Required arguments
  - Cost estimate
  - Example command

### 14.6 Implementation Notes

**SKILL.md Changes:**
1. Update Mode Detection section (add "help" mode)
2. Add `# MODE: HELP` section with all sub-topics
3. Estimated additional lines: ~290 lines
4. Final SKILL.md size: ~1,100 lines

**Quality Metrics:**
| Metric | Target |
|--------|--------|
| Topic Coverage | 100% (all 5 topics implemented) |
| Accuracy | 100% (matches actual CLI) |
| Examples | 10+ working examples |
| Discoverability | Help suggests available topics |

---

## 15. Enhanced Config Mode (v3.2)

> **Added:** v3.2 (2026-02-04)
> **Status:** ✅ Implemented

### 15.1 Design Decision

**Expand `/generative config` to full Setup Wizard with dual API support.**

**Rationale:**
- First-time users need guided setup
- Two API types (Gemini API + Vertex AI) require clear guidance
- gcloud CLI optional but recommended for Vertex AI
- Auto-detect missing config on first use

### 15.2 Command Structure

```bash
/generative config           # Show current config + options menu
/generative config setup     # Start guided setup wizard
/generative config show      # Show all current settings
/generative config gemini    # Setup Gemini API Key only
/generative config vertex    # Setup Vertex AI only
/generative config gcloud    # Setup gcloud CLI
/generative config reset     # Reset config to defaults
```

### 15.3 API Types

| API Type | Environment Variable | Console URL | Purpose |
|----------|---------------------|-------------|---------|
| **Gemini API** | `GOOGLE_API_KEY` | https://console.cloud.google.com/apis/credentials | Gemini models |
| **Vertex AI** | `gcloud auth` or `GOOGLE_APPLICATION_CREDENTIALS` | https://console.cloud.google.com/vertex-ai/studio/settings/api-keys | Veo, Imagen (production) |

**Default Project:** `gen-lang-client-0344941103`

### 15.4 Config File Structure

**Location:** `./config.json` (project root)

```json
{
  "project_id": "gen-lang-client-0344941103",
  "api_keys": {
    "gemini_api_key": "[SET/NOT SET]",
    "vertex_ai": "[gcloud/service-account/NOT SET]"
  },
  "gcloud_installed": true,
  "defaults": {
    "image_model": "imagen-3.0-generate-002",
    "video_model": "veo-2.0-generate-001",
    "output_dir": "./output"
  },
  "setup_completed": true,
  "setup_date": "2026-02-04"
}
```

### 15.5 Setup Wizard Flow

```
Step 0: Welcome / Auto-detect
  ↓ (if no config → auto-start wizard)

Step 1: Project ID
  → "Enter your Google Cloud Project ID:"
  → Default: gen-lang-client-0344941103
  → [Skip]

Step 2: Gemini API Key
  → "Get your Gemini API Key from:"
  → URL: https://console.cloud.google.com/apis/credentials?project=...
  → "Paste your API key:" ___________
  → [Skip]

Step 3: Vertex AI Setup (Optional)
  → "Do you want to use Vertex AI? (for Veo/Imagen)"
  → Options: [Yes - Setup gcloud] [Yes - Use existing] [Skip]

Step 4: gcloud Installation (if needed)
  → "Install gcloud CLI from:"
  → URL: https://cloud.google.com/sdk/docs/install
  → Commands: gcloud auth login, gcloud config set project
  → [Skip]

Step 5: Verify & Save
  → Test API connectivity
  → Save config.json
  → "Setup complete! ✅"
```

### 15.6 Auto-detect (Welcome Start)

When user runs `/generative image` or `/generative video`:

```
IF config.json NOT EXISTS OR api_keys NOT SET:
  → Show: "🔧 First time setup detected!"
  → Ask: "Would you like to run the setup wizard?"
  → Options: [Yes - Start Setup] [No - Skip for now]
```

### 15.7 Config Show Output

```
⚙️ GENERATIVE SKILL - CONFIGURATION

Project: gen-lang-client-0344941103

API Keys:
  Gemini API:  ✅ Set (GOOGLE_API_KEY)
  Vertex AI:   ✅ gcloud authenticated

Tools:
  gcloud CLI:  ✅ Installed (v456.0.0)

Defaults:
  Image Model: imagen-3.0-generate-002
  Video Model: veo-2.0-generate-001
  Output Dir:  ./output

Config File: ./config.json (last updated: 2026-02-04)

Options:
  1. Run setup wizard     → /generative config setup
  2. Setup Gemini API     → /generative config gemini
  3. Setup Vertex AI      → /generative config vertex
  4. Install gcloud       → /generative config gcloud
  5. Reset config         → /generative config reset
```

### 15.8 Step-by-Step Guides

#### 15.8.1 Gemini API Key Setup

```
📋 GEMINI API KEY SETUP

Step 1: Open Google Cloud Console
  → https://console.cloud.google.com/apis/credentials?project=gen-lang-client-0344941103

Step 2: Click "Create Credentials" → "API Key"

Step 3: Copy the API key

Step 4: Paste your API key below:
  [Input field]

Options: [Submit] [Skip] [Cancel]
```

#### 15.8.2 Vertex AI Setup

```
📋 VERTEX AI SETUP

Option A: Use gcloud CLI (Recommended)
  1. Install gcloud: https://cloud.google.com/sdk/docs/install
  2. Run: gcloud auth login
  3. Run: gcloud config set project gen-lang-client-0344941103

Option B: Service Account Key
  1. Open: https://console.cloud.google.com/vertex-ai/studio/settings/api-keys?project=gen-lang-client-0344941103
  2. Create service account key (JSON)
  3. Set: export GOOGLE_APPLICATION_CREDENTIALS="/path/to/key.json"

Options: [I've completed gcloud setup] [Use service account] [Skip]
```

#### 15.8.3 gcloud Installation

```
📋 GCLOUD CLI INSTALLATION

Step 1: Download gcloud CLI
  → https://cloud.google.com/sdk/docs/install

Step 2: Install and initialize
  $ ./google-cloud-sdk/install.sh
  $ gcloud init

Step 3: Authenticate
  $ gcloud auth login

Step 4: Set project
  $ gcloud config set project gen-lang-client-0344941103

Step 5: Verify
  $ gcloud auth list

Options: [I've installed gcloud] [Skip] [Need help]
```

### 15.9 Implementation Notes

**SKILL.md Changes:**
1. Update Mode Detection section (add config sub-commands)
2. Rewrite `# MODE: CONFIG` section completely
3. Add Welcome Start check in Image/Video modes
4. Estimated additional lines: ~200 lines
5. Final SKILL.md size: ~1,300 lines

**Quality Metrics:**
| Metric | Target |
|--------|--------|
| Setup Completion | Guided to success |
| Skip Support | 100% (all steps skippable) |
| URL Accuracy | 100% (real console URLs) |
| Auto-detect | Works on first use |

### 15.10 gsutil Documentation (v3.2.1)

> **Added:** v3.2.1 (2026-02-04)

**Clarification:**
- `gcloud` = REQUIRED for Vertex AI authentication
- `gsutil` = OPTIONAL for manual GCS management
- `--storage-uri` = Works via REST API directly (no gsutil needed)

**Added to SKILL.md:**

1. **Config Show Output** - Added gsutil status check
```bash
which gsutil >/dev/null 2>&1 && echo "  gsutil: ✅ Installed"
```

2. **/generative config gcloud** - Added gsutil explanation
```
The gcloud SDK includes two main tools:
  - gcloud  → Authentication (REQUIRED for Vertex AI)
  - gsutil  → GCS CLI (OPTIONAL - for manual management)

Note: gsutil is NOT required for video generation.
      --storage-uri works via REST API directly.
```

3. **/generative help video** - Added GCS OUTPUT section
```
GCS OUTPUT (--storage-uri):
  Upload generated video directly to Google Cloud Storage.
  Works via REST API - no gsutil installation required.

  Usage:
    /generative v "prompt" --storage-uri gs://my-bucket/videos/
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 3.2.1 | 2026-02-04 | Added Section 15.10: gsutil Documentation |
| 3.2 | 2026-02-04 | Added Section 15: Enhanced Config Mode |
| 3.1 | 2026-02-04 | Added Section 14: Enhanced Help System |
| 2.5 | 2026-02-04 | Added Section 13: Unified Generative Skill (Planned v3.0) |
| 2.4 | 2026-02-04 | Added Section 12: Multi-Tool Workflow Orchestration |
| 2.3 | 2026-02-03 | Added Section 3.3: Audio Prompting (Veo 3.x) |
| 2.2 | 2026-02-03 | Added Section 11: Gap Analysis & Future Features |
| 2.1 | 2026-02-03 | Added Quality Settings, Image Attachment, Video Source flows |
| 2.0 | 2026-02-03 | Full implementation - all phases completed |
| 1.0 | 2026-02-03 | Initial design - Draft |

---

> **Full history:** [changelog/skill.changelog.md](../changelog/skill.changelog.md)
