# 🗄️ GCS Database Module Design Document

## Version: 1.2
## Date: 2026-01-19
## Status: Planning / Active Development
## Module: gcsdb_api.py

---

## 📋 Document Overview

เอกสารนี้อธิบายการออกแบบ `gcsdb_api.py` - Central GCS Management Module สำหรับจัดการ Google Cloud Storage operations ทั้งหมดในโปรเจกต์ claude-code-media-generator

**Core Philosophy:**
- **Single Source of Truth**: ทุก GCS operation ต้องผ่าน gcsdb_api.py
- **Dual Auth Support**: รองรับทั้ง API Key และ Access Token (gcloud/gsutil)
- **Unified Interface**: video_gen.py, image_gen.py และ scripts อื่นๆ ใช้ API เดียวกัน
- **Token Provider**: เป็นแหล่งกลางสำหรับขอ access token ให้ video/image gen

---

> 📜 **Changelog:** See [changelog/gcsdb.changelog.md](./changelog/gcsdb.changelog.md)

---

## 🎯 Design Goals

| Goal | Description |
|------|-------------|
| **Centralized** | ทุก GCS operation อยู่ใน module เดียว |
| **Reusable** | video_gen.py, image_gen.py ใช้ร่วมกันได้ |
| **Dual Auth** | รองรับ API Key + Access Token |
| **Self-Contained** | ไม่ต้องติดตั้ง google-cloud-storage library (ใช้ REST API) |
| **Trackable** | ติดตาม objects ที่สร้างโดย tool นี้ |

---

## 📁 File Structure

```
claude-code-media-generator/
├── gcsdb_api.py          # Central GCS Module (NEW)
├── video_gen.py          # Uses: from gcsdb_api import GCSClient
├── image_gen.py          # Uses: from gcsdb_api import GCSClient
├── gcsdb.design.md       # This document
└── .gcs_tracking.json    # Local tracking file (auto-generated)
```

---

## 🔧 Module Architecture

### Class Structure

```python
# gcsdb_api.py

class GCSClient:
    """Central GCS Management Client.

    Usage:
        client = GCSClient(api_key="AQ...", project_id="my-project")
        # or
        client = GCSClient(access_token="ya29...", project_id="my-project")
        # or (auto-detect from gcloud)
        client = GCSClient.from_gcloud()
    """

    def __init__(
        self,
        api_key: Optional[str] = None,
        access_token: Optional[str] = None,
        project_id: Optional[str] = None,
        default_bucket: Optional[str] = None,
        tracking_file: Optional[str] = ".gcs_tracking.json"
    ):
        """Initialize GCS client with API Key or Access Token."""
```

---

## 📊 API Reference

### 1. Environment & Setup

```python
# ═══════════════════════════════════════════════════════════════
# ENVIRONMENT CHECKS
# ═══════════════════════════════════════════════════════════════

@staticmethod
def check_gsutil_installed() -> bool:
    """Check if gsutil is installed and accessible."""

@staticmethod
def check_gcloud_installed() -> bool:
    """Check if gcloud CLI is installed."""

@staticmethod
def get_gcloud_access_token() -> Optional[str]:
    """Get access token from gcloud auth print-access-token."""

@staticmethod
def get_gcloud_project() -> Optional[str]:
    """Get default project from gcloud config."""

@classmethod
def from_gcloud(cls) -> "GCSClient":
    """Create GCSClient using gcloud credentials automatically."""

def validate_credentials(self) -> Tuple[bool, str]:
    """Validate current credentials by making a test API call."""
```

### 2. Upload Operations

```python
# ═══════════════════════════════════════════════════════════════
# UPLOAD OPERATIONS
# ═══════════════════════════════════════════════════════════════

def upload(
    self,
    local_path: Union[str, Path],
    gcs_uri: str,
    content_type: Optional[str] = None,
    metadata: Optional[Dict[str, str]] = None,
    track: bool = True
) -> Optional[str]:
    """Upload local file to GCS.

    Args:
        local_path: Local file path
        gcs_uri: Destination (gs://bucket/path/ or gs://bucket/path/file.mp4)
        content_type: MIME type (auto-detect if None)
        metadata: Custom metadata to attach
        track: Add to tracking file

    Returns:
        Final GCS URI if success, None if failed
    """

def upload_bytes(
    self,
    data: bytes,
    gcs_uri: str,
    content_type: str = "application/octet-stream",
    track: bool = True
) -> Optional[str]:
    """Upload bytes directly to GCS."""

def upload_base64(
    self,
    base64_data: str,
    gcs_uri: str,
    content_type: str = "video/mp4",
    track: bool = True
) -> Optional[str]:
    """Upload base64-encoded data to GCS."""
```

### 3. Download Operations

```python
# ═══════════════════════════════════════════════════════════════
# DOWNLOAD OPERATIONS
# ═══════════════════════════════════════════════════════════════

def download(
    self,
    gcs_uri: str,
    local_path: Union[str, Path],
    overwrite: bool = True
) -> bool:
    """Download GCS object to local file.

    Args:
        gcs_uri: Source GCS URI (gs://bucket/path/file.mp4)
        local_path: Destination local path
        overwrite: Overwrite if exists

    Returns:
        True if success
    """

def download_bytes(self, gcs_uri: str) -> Optional[bytes]:
    """Download GCS object as bytes."""

def download_base64(self, gcs_uri: str) -> Optional[str]:
    """Download GCS object as base64 string."""

def get_download_url(
    self,
    gcs_uri: str,
    expiration_minutes: int = 60
) -> Optional[str]:
    """Get signed download URL (requires service account)."""
```

### 4. Object Management

```python
# ═══════════════════════════════════════════════════════════════
# OBJECT MANAGEMENT
# ═══════════════════════════════════════════════════════════════

def exists(self, gcs_uri: str) -> bool:
    """Check if GCS object exists."""

def get_metadata(self, gcs_uri: str) -> Optional[Dict[str, Any]]:
    """Get object metadata (size, content-type, created, etc.)."""

def list_objects(
    self,
    gcs_prefix: str,
    max_results: int = 1000,
    delimiter: Optional[str] = None
) -> List[Dict[str, Any]]:
    """List objects with prefix.

    Args:
        gcs_prefix: Prefix (gs://bucket/path/)
        max_results: Maximum objects to return
        delimiter: Use "/" to list "directories"

    Returns:
        List of object info dicts
    """

def delete(self, gcs_uri: str, untrack: bool = True) -> bool:
    """Delete single object from GCS."""

def delete_prefix(
    self,
    gcs_prefix: str,
    confirm: bool = True
) -> Tuple[int, int]:
    """Delete all objects with prefix.

    Returns:
        (deleted_count, failed_count)
    """

def copy(self, source_uri: str, dest_uri: str) -> bool:
    """Copy object within GCS."""

def move(self, source_uri: str, dest_uri: str) -> bool:
    """Move object within GCS (copy + delete)."""
```

### 5. Bucket Management

```python
# ═══════════════════════════════════════════════════════════════
# BUCKET MANAGEMENT
# ═══════════════════════════════════════════════════════════════

def list_buckets(self) -> List[Dict[str, Any]]:
    """List all buckets in project."""

def bucket_exists(self, bucket_name: str) -> bool:
    """Check if bucket exists."""

def create_bucket(
    self,
    bucket_name: str,
    location: str = "us-central1",
    storage_class: str = "STANDARD"
) -> bool:
    """Create new bucket."""

def get_bucket_info(self, bucket_name: str) -> Optional[Dict[str, Any]]:
    """Get bucket metadata and settings."""
```

### 6. Tracking & Cleanup

```python
# ═══════════════════════════════════════════════════════════════
# TRACKING & CLEANUP (Objects created by this tool)
# ═══════════════════════════════════════════════════════════════

def track_object(self, gcs_uri: str, metadata: Optional[Dict] = None):
    """Add object to local tracking file."""

def untrack_object(self, gcs_uri: str):
    """Remove object from tracking file."""

def list_tracked_objects(
    self,
    bucket_filter: Optional[str] = None,
    created_after: Optional[datetime] = None,
    created_before: Optional[datetime] = None
) -> List[Dict[str, Any]]:
    """List all objects tracked by this tool."""

def cleanup_tracked(
    self,
    bucket_filter: Optional[str] = None,
    older_than_days: Optional[int] = None,
    confirm: bool = True
) -> Tuple[int, int]:
    """Delete all tracked objects matching criteria.

    Returns:
        (deleted_count, failed_count)
    """

def cleanup_all(self, confirm: bool = True) -> Tuple[int, int]:
    """Delete ALL objects tracked by this tool."""

def sync_tracking(self) -> Tuple[int, int]:
    """Sync tracking file with actual GCS state.

    Returns:
        (found_count, missing_count)
    """
```

---

## 🔐 Authentication Methods

### Method 1: API Key (Vertex AI)

```python
# Requires: Storage Object User role on service account
client = GCSClient(
    api_key="AQ...",  # Vertex AI API Key
    project_id="YOUR_PROJECT_ID"
)
```

### Method 2: Access Token (gcloud/gsutil)

```python
# Get token from gcloud auth print-access-token
client = GCSClient(
    access_token="ya29...",
    project_id="my-project"
)

# Or auto-detect
client = GCSClient.from_gcloud()
```

### Authentication Priority

```
1. Explicit api_key parameter
   ↓ (if not provided)
2. Explicit access_token parameter
   ↓ (if not provided)
3. gcloud auth print-access-token (auto)
   ↓ (if failed)
4. Error: No credentials available
```

---

## 🔄 Download/Upload Strategy

### Design Principle: REST API First, gsutil Fallback

การ download/upload จาก GCS ใช้กลยุทธ์ **REST API ก่อน** แล้ว fallback ไป gsutil:

```
┌─────────────────────────────────────────────────────────┐
│  Pre-Check: ตรวจสอบ availability ของทั้งสองวิธี          │
│  ├─ REST API: ทดสอบ API Key validity                    │
│  └─ gsutil: shutil.which("gsutil")                      │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  Step 1: REST API + API Key (Primary)                   │
│  ├─ Attempt 1: REST API download/upload                 │
│  ├─ Attempt 2: Retry with exponential backoff           │
│  └─ Attempt 3: Final retry                              │
└─────────────────────────────────────────────────────────┘
                          ↓ (if failed)
┌─────────────────────────────────────────────────────────┐
│  Step 2: gsutil/gcloud (Fallback)                       │
│  ├─ Attempt 1: gsutil cp                                │
│  ├─ Attempt 2: Retry                                    │
│  └─ Attempt 3: Final retry                              │
└─────────────────────────────────────────────────────────┘
                          ↓ (if failed)
┌─────────────────────────────────────────────────────────┐
│  Error: Both methods failed                             │
│  └─ Return error with details                           │
└─────────────────────────────────────────────────────────┘
```

### Pre-Check Methods

```python
def check_rest_api_available(self) -> Tuple[bool, str]:
    """Test REST API availability with API Key.

    Returns:
        (success, message)
    """
    # Try to access bucket metadata as test

def check_gsutil_available(self) -> Tuple[bool, str]:
    """Test gsutil availability.

    Returns:
        (success, message)
    """
    # Use shutil.which("gsutil") for detection
```

### Strategy Configuration

```python
class GCSClient:
    def __init__(
        self,
        ...
        strategy: str = "rest_first",  # "rest_first" | "gsutil_first" | "rest_only" | "gsutil_only"
        max_retries: int = 3,
        retry_delay: int = 5
    ):
```

### Strategy Options

| Strategy | Primary | Fallback | Use Case |
|----------|---------|----------|----------|
| `rest_first` | REST API | gsutil | **Default** - ไม่ต้อง install gcloud |
| `gsutil_first` | gsutil | REST API | Large files, resumable |
| `rest_only` | REST API | None | Lightweight, no gcloud |
| `gsutil_only` | gsutil | None | Enterprise, service account |

### Retry Configuration

```python
RETRY_CONFIG = {
    "max_retries": 3,
    "retry_delay": 5,  # seconds
    "backoff_multiplier": 2,  # exponential backoff
    "max_delay": 30  # seconds
}
```

---

## 📝 Tracking File Format

`.gcs_tracking.json`:

```json
{
  "version": "1.0",
  "tool": "claude-code-media-generator",
  "created_at": "2026-01-19T10:00:00Z",
  "updated_at": "2026-01-19T12:30:00Z",
  "objects": [
    {
      "uri": "gs://my-bucket/videos/video_20260119_082718_0.mp4",
      "bucket": "my-bucket",
      "object_path": "videos/video_20260119_082718_0.mp4",
      "size_bytes": 1112540,
      "content_type": "video/mp4",
      "created_at": "2026-01-19T08:27:18Z",
      "source": "video_gen.py",
      "metadata": {
        "prompt": "A red apple rotating",
        "model": "veo-3.1-generate-preview"
      }
    }
  ],
  "statistics": {
    "total_objects": 15,
    "total_size_bytes": 156789012,
    "by_bucket": {
      "my-bucket": 10,
      "other-bucket": 5
    }
  }
}
```

---

## 🔄 REST API Endpoints

### Base URLs

```python
STORAGE_API_BASE = "https://storage.googleapis.com/storage/v1"
UPLOAD_API_BASE = "https://storage.googleapis.com/upload/storage/v1"
```

### Key Endpoints

| Operation | Method | Endpoint |
|-----------|--------|----------|
| Upload | POST | `/upload/storage/v1/b/{bucket}/o?uploadType=media&name={object}` |
| Download | GET | `/storage/v1/b/{bucket}/o/{object}?alt=media` |
| Get Metadata | GET | `/storage/v1/b/{bucket}/o/{object}` |
| Delete | DELETE | `/storage/v1/b/{bucket}/o/{object}` |
| List Objects | GET | `/storage/v1/b/{bucket}/o?prefix={prefix}` |
| List Buckets | GET | `/storage/v1/b?project={project}` |
| Create Bucket | POST | `/storage/v1/b?project={project}` |

### Authentication Headers

```python
# API Key
headers = {}  # API key in URL: ?key=API_KEY

# Access Token
headers = {"Authorization": f"Bearer {access_token}"}
```

---

## 📊 Integration with video_gen.py / image_gen.py

### Before (Current Implementation)

```python
# video_gen.py - inline GCS functions
def download_video(url, output_path, headers, ...):
    # 150+ lines of GCS download logic

def upload_to_gcs(local_path, gcs_uri, headers, ...):
    # 140+ lines of GCS upload logic
```

### After (Using gcsdb_api.py)

```python
# video_gen.py - clean, simple calls
from gcsdb_api import GCSClient

# Initialize once
gcs = GCSClient(api_key=args.api_key, project_id=project_id)

# Download
if gcs.download(video_uri, output_path):
    print_success("Video downloaded")

# Upload
gcs_uri = gcs.upload(output_path, args.upload_gcs)
if gcs_uri:
    print_success(f"Uploaded: {gcs_uri}")

# Cleanup old videos
gcs.cleanup_tracked(older_than_days=30)
```

---

## 🚀 CLI Standalone Usage (Bonus)

```bash
# gcsdb_api.py สามารถ run standalone ได้

# Check environment
python gcsdb_api.py --check

# List tracked objects
python gcsdb_api.py --list

# Upload file
python gcsdb_api.py --upload video.mp4 gs://bucket/videos/

# Download file
python gcsdb_api.py --download gs://bucket/videos/video.mp4 ./output/

# Cleanup all tracked objects
python gcsdb_api.py --cleanup-all

# Cleanup objects older than 7 days
python gcsdb_api.py --cleanup --older-than 7d
```

---

## 📋 Implementation Checklist

### Phase 1: Core Module
- [ ] Create gcsdb_api.py skeleton
- [ ] Implement GCSClient class
- [ ] Implement authentication (API Key + Access Token)
- [ ] Implement upload (REST API)
- [ ] Implement download (REST API)
- [ ] Add gsutil fallback for large files

### Phase 2: Object Management
- [ ] Implement exists()
- [ ] Implement list_objects()
- [ ] Implement delete()
- [ ] Implement copy() / move()
- [ ] Implement get_metadata()

### Phase 3: Tracking System
- [ ] Create tracking file format
- [ ] Implement track_object() / untrack_object()
- [ ] Implement list_tracked_objects()
- [ ] Implement cleanup_tracked()
- [ ] Implement sync_tracking()

### Phase 4: Integration
- [ ] Update video_gen.py to use gcsdb_api
- [ ] Update image_gen.py to use gcsdb_api
- [ ] Add CLI standalone mode
- [ ] Add unit tests

### Phase 5: Advanced Features
- [ ] Bucket management
- [ ] Signed URLs (service account only)
- [ ] Resumable uploads for large files
- [ ] Progress callbacks

---

## 🔧 Patch/Integration Plan

### Overview

เมื่อ gcsdb_api.py พร้อมใช้งาน จะต้อง patch video_gen.py และ image_gen.py ให้มาเรียกใช้ GCSClient แทน inline functions

### Current State Analysis

#### video_gen.py (ต้อง Patch)

| Function | Lines | Description | Action |
|----------|-------|-------------|--------|
| `download_video()` | 1172-1341 | Download from GCS URI (gsutil + REST API) | **Replace** → `gcs.download()` |
| `upload_to_gcs()` | 1344-1488 | Upload to GCS (gsutil + REST API) | **Replace** → `gcs.upload()` |

**Total lines to remove**: ~315 lines

#### image_gen.py (ไม่มี GCS functions)

| GCS Operations | Status |
|----------------|--------|
| Download | ❌ None (uses base64 only) |
| Upload | ❌ None |

**Action**: เพิ่ม GCS support ใหม่ (optional feature)

---

### Phase 4 Detailed: video_gen.py Integration

#### Step 1: Add Import

```python
# video_gen.py - Top of file
from gcsdb_api import GCSClient
```

#### Step 2: Initialize GCSClient in generate_video()

```python
# Before (current) - Inside generate_video()
headers = {...}  # Build headers manually

# After (new) - Add GCSClient initialization
gcs = GCSClient(
    api_key=args.api_key if args.api_key else None,
    access_token=headers.get("Authorization", "").replace("Bearer ", "") if "Authorization" in headers else None,
    project_id=project_id
)
```

#### Step 3: Replace download_video() calls

```python
# Before (current) - Line ~1790+
if download_video(video_uri, output_file, headers):
    print_success("Video downloaded")

# After (new)
if gcs.download(video_uri, output_file):
    print_success("Video downloaded")
```

#### Step 4: Replace upload_to_gcs() calls

```python
# Before (current) - Line ~1830+
uploaded_uri = upload_to_gcs(output_file, args.upload_gcs, headers)
if uploaded_uri:
    print_success(f"Uploaded: {uploaded_uri}")

# After (new)
uploaded_uri = gcs.upload(output_file, args.upload_gcs, track=True)
if uploaded_uri:
    print_success(f"Uploaded: {uploaded_uri}")
```

#### Step 5: Remove Inline Functions

```python
# DELETE these functions from video_gen.py:
# - def download_video() (lines 1172-1341) - ~170 lines
# - def upload_to_gcs() (lines 1344-1488) - ~145 lines
# Total: ~315 lines removed
```

---

### Phase 4 Detailed: image_gen.py Integration (Optional)

#### Current State: Base64 Only

image_gen.py ใช้ base64 response โดยตรง ไม่มี GCS operations

#### Optional Enhancement: Add GCS Upload

```python
# image_gen.py - Add optional GCS upload support

from gcsdb_api import GCSClient

# In generate_image() function - after saving image
if args.upload_gcs:
    gcs = GCSClient.from_gcloud()  # or with API key
    gcs.upload(saved_image_path, args.upload_gcs, track=True)
```

**New CLI parameter needed:**
```python
parser.add_argument("--upload-gcs", type=str,
    help="Upload generated image to GCS (e.g., gs://bucket/images/)")
```

---

### Migration Checklist

#### Pre-Migration
- [ ] gcsdb_api.py implemented and tested
- [ ] All GCSClient methods working (upload, download, exists, etc.)
- [ ] Unit tests passing

#### video_gen.py Patch
- [ ] Add `from gcsdb_api import GCSClient`
- [ ] Initialize GCSClient in generate_video()
- [ ] Replace download_video() call → gcs.download()
- [ ] Replace upload_to_gcs() call → gcs.upload()
- [ ] Delete download_video() function
- [ ] Delete upload_to_gcs() function
- [ ] Test: `--storage-uri` download working
- [ ] Test: `--upload-gcs` upload working
- [ ] Test: Both gsutil and REST API fallback

#### image_gen.py Enhancement (Optional)
- [ ] Add `from gcsdb_api import GCSClient`
- [ ] Add `--upload-gcs` CLI parameter
- [ ] Add GCS upload after image save
- [ ] Test: Image upload to GCS

#### Post-Migration
- [ ] Update video.design.md with new integration
- [ ] Update design.md cross-references
- [ ] Remove deprecated code comments
- [ ] Update version numbers

---

### Code Diff Preview

#### video_gen.py Before/After

```diff
# Imports
+ from gcsdb_api import GCSClient

# Inside generate_video() - Initialization
+ gcs = GCSClient(
+     api_key=args.api_key,
+     project_id=project_id
+ )

# Download section
- if download_video(video_uri, output_file, headers):
+ if gcs.download(video_uri, output_file):
      print_success("Video downloaded")

# Upload section
- uploaded_uri = upload_to_gcs(output_file, args.upload_gcs, headers)
+ uploaded_uri = gcs.upload(output_file, args.upload_gcs, track=True)

# Functions to DELETE
- def download_video(url: str, output_path: Path, headers: Dict[str, str],
-                    max_retries: int = 3, retry_delay: int = 5) -> bool:
-     """Download video from URL..."""
-     # ~170 lines
-
- def upload_to_gcs(local_path: Path, gcs_uri: str, headers: Dict[str, str],
-                   max_retries: int = 3, retry_delay: int = 5) -> Optional[str]:
-     """Upload local file to GCS..."""
-     # ~145 lines
```

---

### Benefits After Migration

| Metric | Before | After |
|--------|--------|-------|
| **GCS code in video_gen.py** | ~315 lines | ~10 lines |
| **Code reusability** | None | 100% (shared module) |
| **Tracking support** | None | Built-in |
| **Cleanup capability** | Manual | Automated |
| **Token management** | Per-script | Centralized |
| **Maintenance** | Multiple places | Single module |

---

## Cross-Reference

| Document | Description |
|----------|-------------|
| [design.md](./design.md) | Main design document, GCS integration section |
| [video.design.md](./video.design.md) | Video generation, --storage-uri, --upload-gcs |
| [video_gen.py](./video_gen.py) | Current GCS implementation (to be refactored) |

---

> Full history: [gcsdb.changelog.md](../changelog/gcsdb.changelog.md)
