# Video Extension Workflow Design - Claude Code Media Generator

## 0) Document Control

> **Parent Scope:** Claude Code Media Generator Project
> **Current Version:** 1.0
> **Session:** (current session - 2026-02-15)

---

## 1) Overview

### Purpose

Video Extension Workflow module สำหรับ automated chain extension — ต่อวิดีโอหลาย segments ต่อเนื่องกันอัตโนมัติ เพื่อสร้างวิดีโอยาวกว่า 8 วินาที (ซึ่งเป็น limit ของ single generation)

### Current Status

| Component | File | Status |
|-----------|------|--------|
| Single Extension | `video_gen.py` (`--extend-video`) | ✅ Implemented |
| Chain Extension Module | `chain.py` (planned) | 📋 Planned |
| Design Document | `chain.design.md` | 📝 This document |

### Implementation Priority

**📋 Planned** — Design ไว้ก่อน ยังไม่ implement ในตอนนี้

---

## 2) Design Goals

| Goal | Description |
|------|-------------|
| **Automated Chaining** | ต่อวิดีโอหลาย segment โดยอัตโนมัติ (extend N ครั้ง) |
| **Smart Prompts** | AI ช่วย generate prompt ต่อเนื่องจาก prompt ก่อนหน้า |
| **Cost Awareness** | แสดง estimated cost ก่อนเริ่ม chain |
| **Resume Support** | หาก process ค้าง สามารถ resume ต่อจากจุดที่หยุด |
| **Quality Control** | ตรวจสอบแต่ละ segment ก่อนต่อ segment ถัดไป |
| **Reuse Config** | ใช้ config.py + video_gen.py เดิมเป็น backend |

---

## 3) Background: Current Extension System

### 3.1 Single Extension (Implemented)

```bash
# Extend existing video by 7 seconds
python video_gen.py "continue the scene" --extend-video original.mp4

# Extend with last frame reference
python video_gen.py "continue" --extend-video original.mp4 --last-frame closing.jpg

# Extend with character reference
python video_gen.py "continue" --extend-video original.mp4 --reference-asset avatar.jpg
```

**Limitations:**
- ต่อได้ครั้งละ 7 วินาทีเท่านั้น
- ต้องพิมพ์คำสั่งใหม่ทุกครั้ง
- ต้อง copy output filename มาใส่ `--extend-video` เอง
- ไม่มี automatic prompt continuation

### 3.2 Gap Analysis

| Feature | Current | Planned |
|---------|---------|---------|
| Extension per command | 1 segment (7s) | N segments (7s × N) |
| Prompt generation | Manual every time | Auto-generated or user-provided list |
| Output management | Manual file tracking | Automatic segment naming + final merge |
| Cost estimation | Per segment only | Total chain cost upfront |
| Resume capability | None | Checkpoint-based resume |
| Quality check | Manual inspection | Optional pause-and-review mode |

---

## 4) Architecture

### 4.1 Module Structure

```
~/.claude/skills/generative/
├── chain.py              # Chain Extension module (NEW)
├── video_gen.py          # Called by chain.py for each segment
├── video_utils.py        # Shared video utilities
└── config.py             # Shared config
```

### 4.2 Processing Flow

```
Parse chain request (prompt list or auto-generate)
  ↓
Calculate total cost estimate (segments × cost_per_segment)
  ↓
Show summary → Ask user confirmation
  ↓
Generate segment 1 (initial video)
  ↓
Save checkpoint
  ↓
Loop: Generate segment 2..N (extend previous segment)
  ↓
  ├─ Each segment: call video_gen.py --extend-video <prev_output>
  ├─ Save checkpoint after each segment
  └─ Optional: pause for review between segments
  ↓
Concatenate all segments → Final output
  ↓
Generate summary report
```

### 4.3 Class Design

```python
class ChainExtender:
    def __init__(
        self,
        initial_prompt: str,          # First segment prompt
        segments: int = 3,            # Total number of segments
        prompt_mode: str = "auto",    # "auto" | "manual" | "list"
        prompts: List[str] = None,    # For "list" mode
        review_mode: bool = False,    # Pause between segments
        resume_file: str = None,      # Resume from checkpoint
        dry_run: bool = False,        # Cost estimate only
        **video_args,                 # Pass-through to video_gen.py
    ):
        pass

    def generate_prompts(self) -> List[str]:
        """Generate prompts for all segments."""

    def estimate_cost(self) -> ChainCostEstimate:
        """Calculate total chain cost."""

    def execute(self) -> ChainResult:
        """Execute chain extension sequentially."""

    def resume(self) -> ChainResult:
        """Resume from last checkpoint."""

    def concatenate(self, segments: List[str]) -> str:
        """Concatenate all segments into final video."""
```

### 4.4 ChainSegment Data Structure

```python
@dataclass
class ChainSegment:
    index: int                          # Segment number (1-based)
    prompt: str                         # Generation prompt
    source_video: Optional[str]         # Previous segment output (None for first)
    status: str = "pending"             # pending | generating | completed | failed
    output_path: Optional[str] = None   # Generated video path
    duration: float = 0.0               # Actual duration in seconds
    error: Optional[str] = None
    cost: Optional[float] = None
```

---

## 5) Prompt Modes

### 5.1 Auto Mode (AI-Generated Prompts)

AI สร้าง continuation prompt โดยอัตโนมัติจาก initial prompt

```bash
python chain.py "A cat playing in a garden" --segments 4 --mode auto
```

**Auto Prompt Generation Strategy:**

```
Segment 1: "A cat playing in a garden" (user's original prompt)
  ↓
Segment 2: "Continue the scene, the cat chases a butterfly" (AI generates)
  ↓
Segment 3: "The cat catches the butterfly and plays with it" (AI generates)
  ↓
Segment 4: "The cat lies down peacefully in the garden" (AI generates)
```

**Implementation:**
- ใช้ Gemini API (text model) เพื่อ generate continuation prompts
- Input: original prompt + previous prompts + segment number
- Output: natural continuation prompt
- ต้อง maintain narrative coherence

### 5.2 Manual Mode (User Provides Each Prompt)

User พิมพ์ prompt เองสำหรับแต่ละ segment (interactive)

```bash
python chain.py "A cat playing in a garden" --segments 3 --mode manual
```

**Flow:**
```
Segment 1: "A cat playing in a garden"
  → Generated → saved
  → User sees result
Prompt for segment 2: [user types new prompt]
  → Generated → saved
Prompt for segment 3: [user types new prompt]
  → Generated → saved
```

### 5.3 List Mode (Pre-defined Prompts)

User ให้ prompt list ทั้งหมดล่วงหน้า

```bash
python chain.py --prompts-file chain_prompts.txt --mode list
```

**chain_prompts.txt:**
```
A cat playing in a garden
The cat chases a butterfly across the flowers
The cat catches the butterfly gently
The cat lies down peacefully as the sun sets
```

**JSON format (chain_prompts.json):**
```json
{
  "segments": [
    {"prompt": "A cat playing in a garden", "duration": 8},
    {"prompt": "The cat chases a butterfly", "duration": 8},
    {"prompt": "The cat catches the butterfly", "duration": 5},
    {"prompt": "The cat lies down peacefully", "duration": 8}
  ]
}
```

---

## 6) Cost Estimation

### 6.1 Cost Calculation

```
Total Cost = segments × cost_per_segment

Where:
  cost_per_segment = based on model + preset
  - veo-3.1 quality (8s) = ~$1.75
  - veo-3.1-fast quick (5s) = ~$0.75
```

### 6.2 Pre-execution Summary

```
═══════════════════════════════════════════
  CHAIN EXTENSION SUMMARY
═══════════════════════════════════════════

  Initial Prompt: "A cat playing in a garden"
  Mode: auto (AI-generated prompts)
  Total Segments: 4
  Duration Per Segment: ~7-8 seconds
  Total Duration: ~28-32 seconds

  Cost Breakdown:
  ├─ Segment 1: generate (initial)  = $1.75
  ├─ Segment 2: extend              = $1.75
  ├─ Segment 3: extend              = $1.75
  └─ Segment 4: extend              = $1.75
  ────────────────────────────
  Estimated Total: $7.00

  Continue? [y/N]
═══════════════════════════════════════════
```

### 6.3 Dry Run Mode

```bash
# Show cost estimate only, don't generate
python chain.py "A cat playing in a garden" --segments 4 --dry-run
```

---

## 7) Resume Support

### 7.1 Checkpoint File Format

```json
{
  "chain_id": "chain_20260215_143000",
  "initial_prompt": "A cat playing in a garden",
  "mode": "auto",
  "total_segments": 4,
  "started_at": "2026-02-15T14:30:00",
  "video_args": {
    "preset": "quality",
    "model": "veo-3.1-generate-preview",
    "aspect_ratio": "16:9"
  },
  "segments": [
    {
      "index": 1,
      "prompt": "A cat playing in a garden",
      "status": "completed",
      "output": "+generated_videos/chain_20260215_143000/segment_001.mp4",
      "duration": 8.0,
      "cost": 1.75
    },
    {
      "index": 2,
      "prompt": "The cat chases a butterfly",
      "status": "completed",
      "output": "+generated_videos/chain_20260215_143000/segment_002.mp4",
      "duration": 7.5,
      "cost": 1.75
    },
    {
      "index": 3,
      "prompt": "The cat catches the butterfly",
      "status": "failed",
      "error": "API quota exceeded"
    },
    {
      "index": 4,
      "prompt": "The cat lies down peacefully",
      "status": "pending"
    }
  ]
}
```

### 7.2 Resume Command

```bash
# Resume from last checkpoint
python chain.py --resume chain_20260215_143000.json
```

---

## 8) Output Structure

### 8.1 Directory Layout

```
{user's CWD}/
├── +generated_videos/
│   └── chain_20260215_143000/          # Chain-specific subfolder
│       ├── segment_001.mp4             # Individual segments
│       ├── segment_002.mp4
│       ├── segment_003.mp4
│       ├── segment_004.mp4
│       └── final_chain.mp4            # Concatenated output (if merge enabled)
└── chain_20260215_143000.json          # Checkpoint/results file
```

### 8.2 Video Concatenation

**Option A: FFmpeg Concatenation (Preferred)**

```python
def concatenate_segments(segments: List[str], output: str) -> str:
    """Concatenate video segments using FFmpeg."""
    # Create concat file
    with open("concat_list.txt", "w") as f:
        for seg in segments:
            f.write(f"file '{seg}'\n")

    # Run FFmpeg concat
    subprocess.run([
        "ffmpeg", "-f", "concat", "-safe", "0",
        "-i", "concat_list.txt",
        "-c", "copy", output
    ])
    return output
```

**Option B: Keep Segments Only (No Merge)**

```bash
# Skip concatenation — user merges manually
python chain.py "prompt" --segments 4 --no-merge
```

**FFmpeg Availability:**
- ถ้ามี ffmpeg → auto-merge
- ถ้าไม่มี ffmpeg → เก็บ segments แยก + แจ้ง user

---

## 9) CLI Interface

```bash
# Basic chain extension (auto mode, 3 segments)
python chain.py "A cat playing in a garden" --segments 3

# Chain with specific prompt mode
python chain.py "A cat playing in a garden" --segments 4 --mode auto
python chain.py "A cat playing in a garden" --segments 3 --mode manual

# Chain from prompt file
python chain.py --prompts-file chain_prompts.txt --mode list

# Chain with review mode (pause between segments)
python chain.py "A cat playing" --segments 4 --review

# Dry run (cost estimate only)
python chain.py "A cat playing" --segments 5 --dry-run

# Resume failed chain
python chain.py --resume chain_20260215_143000.json

# With video generation arguments (pass-through)
python chain.py "A cat playing" --segments 3 --preset quality --model veo-3.1-generate-preview

# Skip final merge
python chain.py "A cat playing" --segments 3 --no-merge

# Chain with reference assets
python chain.py "A cat playing" --segments 3 --reference-asset cat_ref.jpg
```

---

## 10) Smart Prompt Continuation (AI)

### 10.1 Prompt Generation Strategy

```python
CONTINUATION_SYSTEM_PROMPT = """
You are a video scene continuation expert.
Given the original scene description and previous segments,
generate a natural continuation prompt for the next video segment.

Rules:
- Maintain visual and narrative continuity
- Keep the same characters, setting, and mood
- Progress the scene naturally
- Keep prompts concise (1-2 sentences)
- Avoid abrupt scene changes
"""

def generate_continuation_prompt(
    original_prompt: str,
    previous_prompts: List[str],
    segment_number: int,
    total_segments: int,
) -> str:
    """Use Gemini to generate a continuation prompt."""
    pass
```

### 10.2 Prompt Generation Options

| Option | Description |
|--------|-------------|
| `--style narrative` | เล่าเรื่องต่อเนื่อง (default) |
| `--style loop` | สร้าง seamless loop (segment สุดท้ายกลับมาเชื่อมกับ segment แรก) |
| `--style escalate` | เพิ่มความเข้มข้นทีละ segment |
| `--style calm` | ค่อยๆ ลดความเข้มข้นลง (cinematic ending) |

### 10.3 Prompt Preview

```bash
# Preview AI-generated prompts without generating video
python chain.py "A cat playing in a garden" --segments 4 --preview-prompts
```

**Output:**
```
═══════════════════════════════════════════
  PROMPT PREVIEW (auto mode)
═══════════════════════════════════════════

  Segment 1: "A cat playing in a garden"
  Segment 2: "The cat notices a butterfly and begins to chase it"
  Segment 3: "The cat leaps through the flowers, butterfly just out of reach"
  Segment 4: "The cat rests under a tree, butterfly lands on its nose"

  Approve these prompts? [y/edit/N]
═══════════════════════════════════════════
```

---

## 11) Review Mode

### 11.1 Interactive Review

```bash
python chain.py "A cat playing" --segments 4 --review
```

**Flow:**
```
Generating segment 1/4...
  → Completed: segment_001.mp4 (8.0s)

  Review segment 1:
  [c]ontinue | [r]egenerate | [e]dit prompt | [s]top
  > c

Generating segment 2/4...
  → Completed: segment_002.mp4 (7.5s)

  Review segment 2:
  [c]ontinue | [r]egenerate | [e]dit prompt | [s]top
  > e

  New prompt for segment 3: [user types new prompt]

Generating segment 3/4...
```

### 11.2 Review Options

| Option | Action |
|--------|--------|
| `c` (continue) | ใช้ segment นี้ ต่อ segment ถัดไป |
| `r` (regenerate) | Generate segment นี้ใหม่ (same prompt) |
| `e` (edit prompt) | แก้ prompt สำหรับ segment ถัดไป |
| `s` (stop) | หยุด chain ณ จุดนี้ (save checkpoint) |

---

## 12) Error Handling

| Error Type | Behavior |
|-----------|----------|
| API quota exceeded | Pause + save checkpoint + retry after delay |
| Single segment fails | Retry 3 times → save checkpoint if still fails |
| Network timeout | Retry with exponential backoff (3 attempts) |
| Invalid prompt | Skip + use fallback prompt "continue the scene" |
| FFmpeg not found | Skip merge → keep individual segments |
| All segments fail | Stop + save checkpoint for resume |

---

## 13) Integration

### Related Modules

| Module | Relationship |
|--------|-------------|
| `video_gen.py` | Called for each segment (generate + extend) |
| `config.py` | Shared configuration (API keys, defaults) |
| `video_utils.py` | Video file utilities |
| `batch.py` | Can trigger chain jobs from batch file |
| `gcsdb_api.py` | Optional GCS upload for chain results |

### Skill Integration (Future)

```bash
# Via /generative skill
/generative chain "A cat playing" --segments 4
/generative chain "A cat playing" --segments 3 --mode auto --review
```

### Batch Integration

```json
{
  "prompts": [
    {
      "prompt": "A cat playing in a garden",
      "type": "chain",
      "segments": 4,
      "mode": "auto"
    }
  ]
}
```

---

## 14) Quality Metrics

| Metric | Target |
|--------|--------|
| Chain completion rate | > 95% (with resume) |
| Prompt coherence | Narrative continuity across segments |
| Cost estimation accuracy | ±5% of actual |
| Resume reliability | 100% (no duplicate generation) |
| Segment quality | Consistent with single generation |
| FFmpeg merge | Seamless concatenation |

---

> Full history: [changelog/chain.changelog.md](../changelog/chain.changelog.md)
