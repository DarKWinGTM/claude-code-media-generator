# Batch Processing Design - Claude Code Media Generator

## 0) Document Control

> **Parent Scope:** Claude Code Media Generator Project
> **Current Version:** 1.0
> **Session:** (current session - 2026-02-15)

---

## 1) Overview

### Purpose

Batch Processing module สำหรับ generate หลาย images/videos จาก prompt list ในครั้งเดียว ลดการพิมพ์คำสั่งซ้ำ ๆ และรองรับ parallel generation เพื่อประหยัดเวลา

### Current Status

| Component | File | Status |
|-----------|------|--------|
| Batch Module | `batch.py` (planned) | 📋 Planned |
| Design Document | `batch.design.md` | 📝 This document |

### Implementation Priority

**📋 Planned** — Design ไว้ก่อน ยังไม่ implement ในตอนนี้

---

## 2) Design Goals

| Goal | Description |
|------|-------------|
| **Simple Input** | อ่าน prompts จาก text file หรือ JSON file |
| **Cost Awareness** | คำนวณค่าใช้จ่ายรวมก่อนเริ่ม → ยืนยันจาก user |
| **Resume Support** | หาก process ค้าง สามารถ resume ได้จากจุดที่หยุด |
| **Parallel** | รองรับ parallel generation (configurable concurrency) |
| **Reuse Config** | ใช้ config.py เดิม + shared arguments |

---

## 3) Input Formats

### 3.1 Simple Text File (`.txt`)

```
# prompts.txt
# Lines starting with # are comments
# Each line = one prompt

A cat playing in a garden
A sunset over the ocean
A futuristic city at night
```

**Usage:**
```bash
python batch.py --file prompts.txt --type video --preset quality
```

### 3.2 JSON File (`.json`)

```json
{
  "defaults": {
    "type": "video",
    "preset": "quality",
    "model": "veo-3.1-generate-preview",
    "aspect_ratio": "16:9"
  },
  "prompts": [
    {
      "prompt": "A cat playing in a garden",
      "duration": 8
    },
    {
      "prompt": "A sunset over the ocean",
      "preset": "quick",
      "duration": 5
    },
    {
      "prompt": "A futuristic city at night",
      "aspect_ratio": "9:16"
    }
  ]
}
```

**Usage:**
```bash
python batch.py --file prompts.json
```

### 3.3 CSV File (`.csv`)

```csv
prompt,type,preset,duration,aspect_ratio
"A cat playing in a garden",video,quality,8,16:9
"A sunset over the ocean",video,quick,5,16:9
"A futuristic city at night",image,,,"1:1"
```

---

## 4) Architecture

### 4.1 Module Structure

```
~/.claude/skills/generative/
├── batch.py              # Batch Processing module (NEW)
├── video_gen.py          # Called by batch.py
├── image_gen.py          # Called by batch.py
└── config.py             # Shared config
```

### 4.2 Processing Flow

```
Read input file (txt/json/csv)
  ↓
Parse prompts + per-prompt overrides
  ↓
Apply defaults (from file + config.py)
  ↓
Calculate total cost estimate
  ↓
Show summary → Ask user confirmation
  ↓
Execute batch (sequential or parallel)
  ↓
Save progress to resume file
  ↓
Generate summary report
```

### 4.3 Class Design

```python
class BatchProcessor:
    def __init__(
        self,
        input_file: str,
        media_type: str = "video",      # "video" | "image" | "auto"
        concurrency: int = 1,           # parallel jobs
        resume_file: str = None,        # resume from previous run
        dry_run: bool = False,          # cost estimate only
    ):
        pass

    def parse_input(self) -> List[BatchJob]:
        """Parse input file into list of jobs."""

    def estimate_cost(self) -> BatchCostEstimate:
        """Calculate total cost estimate."""

    def execute(self) -> BatchResult:
        """Execute all jobs (sequential or parallel)."""

    def resume(self) -> BatchResult:
        """Resume from last checkpoint."""
```

### 4.4 BatchJob Data Structure

```python
@dataclass
class BatchJob:
    id: int                          # Job index (1-based)
    prompt: str                      # Generation prompt
    media_type: str                  # "video" | "image"
    arguments: Dict[str, Any]        # CLI arguments
    status: str = "pending"          # pending | running | completed | failed
    output_path: Optional[str] = None
    error: Optional[str] = None
    cost: Optional[float] = None
```

---

## 5) Cost Estimation

### 5.1 Pre-execution Summary

```
═══════════════════════════════════════════
  BATCH GENERATION SUMMARY
═══════════════════════════════════════════

  Input File: prompts.json
  Total Jobs: 10
  Type: 8 videos, 2 images

  Cost Breakdown:
  ├─ 5x veo-3.1 quality (8s)    = $8.75
  ├─ 3x veo-3.1-fast quick (5s) = $2.25
  └─ 2x imagen-3 image          = $0.08
  ────────────────────────────
  Estimated Total: $11.08

  Continue? [y/N]
═══════════════════════════════════════════
```

### 5.2 Dry Run Mode

```bash
# Show cost estimate only, don't generate
python batch.py --file prompts.json --dry-run
```

---

## 6) Resume Support

### 6.1 Resume File Format

```json
{
  "batch_id": "batch_20260215_143000",
  "input_file": "prompts.json",
  "started_at": "2026-02-15T14:30:00",
  "jobs": [
    {"id": 1, "status": "completed", "output": "+generated_videos/video_xxx.mp4"},
    {"id": 2, "status": "completed", "output": "+generated_videos/video_yyy.mp4"},
    {"id": 3, "status": "failed", "error": "API quota exceeded"},
    {"id": 4, "status": "pending"},
    {"id": 5, "status": "pending"}
  ]
}
```

### 6.2 Resume Command

```bash
# Resume from last checkpoint
python batch.py --resume batch_20260215_143000.json
```

---

## 7) Output Structure

```
{user's CWD}/
├── +generated_videos/
│   ├── batch_20260215_143000/        # Batch-specific subfolder
│   │   ├── 001_cat_playing.mp4
│   │   ├── 002_sunset_ocean.mp4
│   │   └── 003_futuristic_city.mp4
│   └── ...
├── +generated_images/
│   └── batch_20260215_143000/
│       └── 001_landscape.png
└── batch_20260215_143000.json        # Resume/results file
```

---

## 8) CLI Interface

```bash
# Basic batch from text file
python batch.py --file prompts.txt --type video

# Batch from JSON with per-prompt settings
python batch.py --file prompts.json

# Parallel generation (3 concurrent)
python batch.py --file prompts.json --parallel 3

# Dry run (cost estimate only)
python batch.py --file prompts.json --dry-run

# Resume failed batch
python batch.py --resume batch_20260215_143000.json

# With shared arguments
python batch.py --file prompts.txt --type video --preset quality --model veo-3.1-generate-preview
```

---

## 9) Error Handling

| Error Type | Behavior |
|-----------|----------|
| API quota exceeded | Pause + retry after delay |
| Single job fails | Log error, continue next job |
| Network timeout | Retry with exponential backoff (3 attempts) |
| Invalid prompt | Skip + log warning |
| All jobs fail | Stop + save resume file |

---

## 10) Integration

### Related Modules

| Module | Relationship |
|--------|-------------|
| `video_gen.py` | Called for video generation jobs |
| `image_gen.py` | Called for image generation jobs |
| `config.py` | Shared configuration (API keys, defaults) |
| `gcsdb_api.py` | Optional GCS upload for batch results |

### Skill Integration (Future)

```bash
# Via /generative skill
/generative batch prompts.json
/generative batch prompts.txt --type video --preset quality
```

---

## 11) Quality Metrics

| Metric | Target |
|--------|--------|
| Input format support | txt, json, csv |
| Cost estimation accuracy | ±5% of actual |
| Resume reliability | 100% (no duplicate generation) |
| Error isolation | Single job failure doesn't stop batch |

---

> Full history: [changelog/batch.changelog.md](../changelog/batch.changelog.md)
