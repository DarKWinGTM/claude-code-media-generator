# Config System Design - Claude Code Media Generator

## 0) Document Control

> **Parent Scope:** Claude Code Media Generator Project
> **Current Version:** 1.4
> **Session:** (current session - 2026-02-01)

---

## 1) Overview

### Purpose

จัดการ configuration สำหรับ Claude Code Media Generator tools ทั้งหมด รองรับ multi-project, environment variable fallback, และ **Smart Defaults** integration กับ metadata system

### Current Status

| Component | File | Lines | Status |
|-----------|------|-------|--------|
| Config Module | `config.py` | 329 | ✅ Implemented (renamed from config_loader.py) |
| Config Template | `config.example.json` | 46 | ✅ Implemented |
| Design Document | `config.design.md` | - | 📝 This document |
| Smart Defaults | `video_gen.py` | - | 🔲 TODO (Section 10) |

---

## 2) Architecture

### 2.1 File Structure

```
claude-code-media-generator/
├── config.py                 # Config module
├── config.example.json       # Template for users
├── config.json               # User's actual config (git ignored)
└── ~/.claude-media-gen/
    └── config.json           # Alternative user config location
```

### 2.2 Config Search Priority

```
1. Explicit path (--config flag)
   ↓
2. ./config.json (current directory)
   ↓
3. ~/.claude-media-gen/config.json (user home)
   ↓
4. Environment variables (fallback)
```

### 2.3 Value Priority Chain (Extended with Metadata)

```
CLI Arguments (highest)
   ↓
Source Metadata (for --extend-video only)
   ↓
config.json [--project]
   ↓
Environment Variables
   ↓
Code Defaults (lowest)
```

> **Reference:** See [design.md Section 18](./design.md#18-smart-defaults-system-metadata-driven) for full Smart Defaults design

---

## 3) Config Schema (v2.1)

### 3.1 Full Schema

```json
{
  "_comment": "Claude Code Media Generator - Configuration",
  "_version": "2.1",

  "main_projects": {
    "<project-id>": {
      "_comment": "Project description",
      "location": "us-central1",
      "api_key": "AIzaSy...",
      "gcs": {
        "bucket": "bucket-name",
        "output_path": "path/"
      }
    }
  },

  "defaults": {
    "video": {
      "model": "veo-3.1-generate-preview",
      "duration": 5,
      "aspect_ratio": "16:9",
      "resolution": "720p"
    },
    "image": {
      "model": "gemini-3-pro-image-preview",
      "aspect_ratio": "16:9",
      "resolution": "1K"
    }
  },

  "output": {
    "auto_save_metadata": true,
    "video_dir": "+generated_videos",
    "image_dir": "+generated_images"
  }
}
```

**Note:** `active_project` field ถูกลบออกใน v2.1 - ใช้ `--project` flag แทน

### 3.2 Schema Fields

| Section | Field | Type | Required | Description |
|---------|-------|------|----------|-------------|
| `main_projects` | - | object | Yes | Map of project configs |
| `main_projects.<id>` | location | string | No | GCP region (default: us-central1) |
| `main_projects.<id>` | api_key | string | No | API key for this project |
| `main_projects.<id>` | gcs.bucket | string | No | GCS bucket name |
| `main_projects.<id>` | gcs.output_path | string | No | Output path in bucket |
| `defaults.video` | model | string | No | Default video model |
| `defaults.video` | duration | int | No | Default duration (5 or 8) |
| `defaults.video` | aspect_ratio | string | No | Default aspect ratio |
| `defaults.video` | resolution | string | No | Default resolution |
| `defaults.image` | model | string | No | Default image model |
| `defaults.image` | aspect_ratio | string | No | Default aspect ratio |
| `defaults.image` | resolution | string | No | Default resolution |
| `output` | auto_save_metadata | bool | No | Save metadata JSON |
| `output` | video_dir | string | No | Video output directory |
| `output` | image_dir | string | No | Image output directory |

---

## 4) Environment Variable Mapping

### 4.1 Supported Variables

| Config Key | Environment Variables (priority order) |
|------------|---------------------------------------|
| api_key | GOOGLE_API_KEY, GEMINI_API_KEY, VERTEX_AI_API_KEY |
| project_id | GOOGLE_CLOUD_PROJECT, GCP_PROJECT |
| location | GOOGLE_CLOUD_LOCATION, GCP_LOCATION |
| gcs_bucket | GCS_BUCKET, GOOGLE_CLOUD_BUCKET |

### 4.2 Fallback Behavior

```python
def get_api_key():
    # 1. Try config.json
    if config.api_key:
        return config.api_key

    # 2. Try environment variables
    for env in ["GOOGLE_API_KEY", "GEMINI_API_KEY", "VERTEX_AI_API_KEY"]:
        if os.environ.get(env):
            return os.environ[env]

    # 3. Return None
    return None
```

---

## 5) API Reference

### 5.1 ConfigLoader Class

```python
class ConfigLoader:
    """Handles loading and accessing configuration."""

    # Properties
    is_loaded: bool           # True if config loaded
    config_path: Path         # Path to loaded config
    active_project_id: str    # Current active project (set via set_active_project)

    # Methods
    def load() -> bool
    def set_active_project(project_id: str) -> bool
    def get_project_config(project_id: str = None) -> dict
    def get_api_key(project_id: str = None) -> str
    def get_location(project_id: str = None) -> str
    def get_gcs_config(project_id: str = None) -> dict
    def get_storage_uri(project_id: str = None) -> str
    def get_defaults(media_type: str) -> dict
    def get_output_config() -> dict
    def list_projects() -> list
    def get_all() -> dict
```

### 5.2 Convenience Functions

```python
# Singleton access
get_config(config_path=None, reload=False) -> ConfigLoader
load_config(config_path=None) -> Tuple[bool, ConfigLoader]

# Direct access
get_api_key(project_id=None) -> str
get_storage_uri(project_id=None) -> str
get_location(project_id=None) -> str
get_video_defaults() -> dict
get_image_defaults() -> dict
```

---

## 6) CLI Integration

### 6.1 --project Flag Behavior

```python
# Add --project flag
parser.add_argument("--project", help="Config project to use")
```

**Decision Logic:**

| Scenario | Behavior |
|----------|----------|
| `--project` specified | Use that project's config |
| Single project in config | Auto-select (no warning) |
| Multi-project + no `--project` + no metadata | Error with hint |
| Multi-project + no `--project` + has metadata | Inherit from metadata |

### 6.2 Smart Defaults Integration

```python
def apply_smart_defaults(args):
    """Apply defaults with full priority chain."""
    config = get_config()

    # Step 1: Load source metadata (if extend mode)
    source_meta = None
    if args.extend_video:
        source_meta = load_source_metadata(Path(args.extend_video))

    # Step 2: Determine project
    if args.project:
        config.set_active_project(args.project)
    elif source_meta and 'project_id' in source_meta:
        config.set_active_project(source_meta['project_id'])
    elif len(config.list_projects()) == 1:
        config.set_active_project(config.list_projects()[0])

    # Step 3: Apply defaults with priority chain
    # CLI > Metadata > Config > Code Defaults
    ...

    return args
```

### 6.3 Import Pattern

```python
# In video_gen.py
try:
    from config import get_config, get_api_key
    CONFIG_AVAILABLE = True
except ImportError:
    CONFIG_AVAILABLE = False
```

---

## 7) File Naming (Completed)

| Aspect | Before | After |
|--------|--------|-------|
| File name | `config_loader.py` | `config.py` ✅ |
| Import | `from config_loader import ...` | `from config import ...` ✅ |
| Variable | `CONFIG_LOADER_AVAILABLE` | `CONFIG_AVAILABLE` ✅ |

---

## 8) Code Review: Current Implementation

### 8.1 Strengths

| Aspect | Status | Notes |
|--------|--------|-------|
| Type hints | ✅ Good | All methods have type hints |
| Docstrings | ✅ Good | Clear documentation |
| Error handling | ✅ Good | Try-catch on file load |
| Singleton pattern | ✅ Good | `_config_instance` |
| Env fallback | ✅ Good | Multiple env vars |
| Test block | ✅ Good | `if __name__ == "__main__"` |

### 8.2 Areas for Improvement

| Issue | Severity | Recommendation | Status |
|-------|----------|----------------|--------|
| Remove `active_project` from load() | 🟡 Medium | Use only `--project` flag | 🔲 TODO |
| Add Smart Defaults integration | 🟡 Medium | Integrate with metadata system | 🔲 TODO |
| No schema validation | 🟢 Low | Add optional JSON schema validation | Future |

### 8.3 Code Quality Score

| Metric | Score | Notes |
|--------|-------|-------|
| Readability | 9/10 | Clean, well-organized |
| Type Safety | 8/10 | Good type hints |
| Error Handling | 8/10 | Handles common errors |
| Documentation | 9/10 | Good docstrings |
| Test Coverage | 6/10 | Only manual test block |
| **Overall** | **8/10** | Production ready |

---

## 9) Implementation TODO

| Task | Priority | Status | Notes |
|------|----------|--------|-------|
| Remove `active_project` logic from config.py load() | High | ✅ Done | v1.1 - Phase 2.8.2 |
| Update video_gen.py for multi-project requirement | High | ✅ Done | Phase 2.8.2 |
| Update image_gen.py for multi-project requirement | Medium | ✅ Done | Phase 2.8.2 |
| Integrate with Smart Defaults system | High | ✅ Done | Phase 2.8.3 - metadata inheritance |

---

## 10) Smart Defaults Cross-Reference

> **Full Design:** [design.md Section 18](./design.md#18-smart-defaults-system-metadata-driven)

**Key Points:**

1. **Priority Chain:** CLI > Source Metadata > config.json > Code Defaults
2. **Inheritable Fields:** model, storage_uri, project_id, resolution, aspect_ratio, location
3. **--project Flag:** Works with both config.json and metadata inheritance
4. **Auto-select:** Single project in config = auto-select, no warning needed

**Implementation (Phase 2.8.3):**
- `load_source_metadata(video_path)` - Loads command_args from matching metadata file
- `apply_config_defaults()` - Now applies full priority chain with metadata inheritance
- `build_metadata()` - Saves project_id, storage_uri, location for future inheritance

---

## 11) Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.5 | 2026-02-01 | Phase 2.8.3 Smart Defaults implementation complete |
| 1.4 | 2026-02-01 | Phase 2.8.2 implementation complete |
| 1.3 | 2026-01-31 | Smart Defaults integration, --project behavior design |
| 1.2 | 2026-01-30 | Removed active_project from schema |
| 1.1 | 2026-01-30 | Renamed config_loader.py → config.py |
| 1.0 | 2026-01-30 | Initial design document |

---

> Full history: [changelog/config.changelog.md](../changelog/config.changelog.md)
