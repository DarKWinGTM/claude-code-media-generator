# Changelog - Config System

> **Parent Document:** [config.design.md](../design/config.design.md)
> **Current Version:** 1.3
> **Session:** (current session - 2026-01-31)

---

## Version 1.3 (2026-01-31)

**Smart Defaults Integration:**

**Session:** (current session - 2026-01-31)

### Changes

- **Design Integration**: Updated config.design.md to integrate with Smart Defaults System
- **Value Priority Chain**: Extended to include Source Metadata (for --extend-video)
- **Schema Version**: Updated to v2.1 (active_project removed)
- **CLI --project Behavior**: Added decision logic for multi-project + metadata scenarios
- **Smart Defaults Section**: Added Section 10 with cross-reference to design.md Section 18
- **Implementation TODO**: Added Section 9 with clear tasks

### Cross-References

- Smart Defaults System: [design.md Section 18](../design/design.md#18-smart-defaults-system-metadata-driven)
- Metadata Schema: [video.design.md Section 14.1](../design/video.design.md#141-unified-metadata-schema-v225)

---

## Version 1.2 (2026-01-30)

**Removed: active_project field from schema**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

### Changes

- **Schema Change**: Removed `active_project` field from config schema
- **Files Updated**:
  - `design/config.design.md` (schema section)
  - `config.example.json` (removed line 27)

### Rationale

- `active_project` ไม่จำเป็น - ใช้ `--project` flag แทน
- ลดความซับซ้อนของ config
- หลีกเลี่ยง state ที่ต้อง sync

### TODO (Implementation)

- [ ] Update `config.py` to remove `active_project` logic
- [ ] Update `video_gen.py` to require `--project` when multi-project
- [ ] Update `image_gen.py` to require `--project` when multi-project

---

## Version 1.1 (2026-01-30)

**Renamed: config_loader.py → config.py**

**Session:** 5584c223-ebff-4c03-b92c-697360841c5e

### Changes

- **File Rename**: `config_loader.py` → `config.py`
- **Variable Rename**: `CONFIG_LOADER_AVAILABLE` → `CONFIG_AVAILABLE`
- **Import Update**: `from config_loader import` → `from config import`
- **Files Updated**:
  - `config.py` (renamed)
  - `video_gen.py` (line 89-94)
  - `image_gen.py` (line 30-35)
- **Design Document**: Created `design/config.design.md`

### Rationale

- Shorter, cleaner import: `from config import get_config`
- Standard Python naming convention
- "loader" is implementation detail

---

## Version 1.0 (2026-01-20)

**Initial Implementation: config_loader.py**

**Session:** (legacy)

### Changes

- Implemented `ConfigLoader` class (329 lines)
- Multi-project support with `main_projects` schema
- Environment variable fallback
- Created `config.example.json` template
- Integrated with `video_gen.py` v2.10
- Integrated with `image_gen.py` v1.2
- Added `--project` flag for runtime project switching

### Features

- Config file search paths: `./config.json`, `~/.claude-media-gen/config.json`
- Singleton pattern for global access
- Convenience functions: `get_api_key()`, `get_storage_uri()`, etc.
- Video/Image defaults support

---

## Version History (Unified)

| Version | Date | Changes | Session ID |
|---------|------|---------|------------|
| 1.2 | 2026-01-30 | **Removed active_project from schema** | 5584c223... |
| | | - Schema simplified | |
| | | - Use --project flag instead | |
| | | Summary: Cleaner config structure | |
| 1.1 | 2026-01-30 | **Renamed config_loader.py → config.py** | 5584c223... |
| | | - File and variable renames | |
| | | - Updated imports in video_gen.py, image_gen.py | |
| | | - Created design/config.design.md | |
| | | Summary: Cleaner naming convention | |
| 1.0 | 2026-01-20 | **Initial Implementation** | (legacy) |
| | | - ConfigLoader class (329 lines) | |
| | | - Multi-project support | |
| | | - Environment variable fallback | |
| | | Summary: Phase 1 Config Integration complete | |

---

> Parent Document: [config.design.md](../design/config.design.md)
