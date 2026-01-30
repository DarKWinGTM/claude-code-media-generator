# 🔍 API Checker Design Document

## Version: 1.0
## Date: 2026-01-19
## Tool: `check_api.py`
## Parent Document: [design.md](./design.md)

---

## 📋 Document Overview

เอกสารนี้อธิบายรายละเอียดของ `check_api.py` - เครื่องมือสำหรับตรวจสอบ API Key และ List Models

> **⚠️ IMPORTANT:** เอกสารนี้เกี่ยวกับ `check_api.py` เท่านั้น!
> - สำหรับ Video Generation ดูที่ [video.design.md](./video.design.md)
> - สำหรับ Image Generation ดูที่ [image.design.md](./image.design.md)

---

## 1. Purpose & Scope

### 1.1 What check_api.py Does

```
check_api.py ใช้สำหรับ:
  ├─ List Models (ดูรายการ models ที่ใช้ได้)
  ├─ Get Model Info (ดูข้อมูล model)
  ├─ Test API Connectivity (ทดสอบการเชื่อมต่อ)
  └─ Compare Endpoints (เปรียบเทียบ Global vs Regional)

check_api.py ไม่ได้ใช้สำหรับ:
  ├─ Video Generation (ใช้ video_gen.py)
  └─ Image Generation (ใช้ image_gen.py)
```

### 1.2 Scope Limitation

| Operation | Tool | Notes |
|-----------|------|-------|
| List Models | `check_api.py` | เอกสารนี้ |
| Get Model Info | `check_api.py` | เอกสารนี้ |
| **Video Generation** | `video_gen.py` | ดู video.design.md |
| **Image Generation** | `image_gen.py` | ดู image.design.md |

---

## 2. API Key Support for check_api.py Operations

### 2.1 API Key Compatibility (List Models / Get Model Info)

> **⚠️ NOTE:** ตารางนี้ใช้กับ operations ของ check_api.py เท่านั้น (List Models, Get Model Info)
> Video/Image Generation มี compatibility ต่างกัน - ดูที่ video.design.md

| Key Format | API Type | List Models | Get Model Info | Auth Method |
|------------|----------|:-----------:|:--------------:|-------------|
| `AIzaSy...` | Gemini API | ✅ Yes | ✅ Yes | API Key (header) |
| `AQ.Ab8R...` | Vertex AI | ❌ **No** | ❌ **No** | Not Supported |
| `ya29...` | Vertex AI | ✅ Yes | ✅ Yes | OAuth2 Bearer |

### 2.2 Vertex AI API Key Limitation (check_api.py only)

```text
Vertex AI API Key (AQ...) + List Models:

Error Response:
  401 UNAUTHENTICATED
  "API keys are not supported by this API. Expected OAuth2 access token..."

This limitation applies ONLY to:
  - List Models endpoint
  - Get Model Info endpoint

This does NOT apply to:
  - predictLongRunning (Video/Image Generation) → Works with AQ... keys!
```

---

## 3. Supported Endpoints

### 3.1 Gemini API (List Models)

```bash
# List all available Gemini models
curl -s "https://generativelanguage.googleapis.com/v1beta/models?key=AIzaSy..."

# Response includes:
# - gemini-2.5-flash, gemini-2.5-pro
# - gemini-2.0-flash, gemini-2.0-flash-001
# - veo-2.0-generate-001, imagen-3.0-generate-002
# - ... and more
```

### 3.2 Vertex AI (List Publisher Models)

```bash
# Requires OAuth2 token (not API Key!)
ACCESS_TOKEN=$(gcloud auth application-default print-access-token)

# List Google publisher models (v1beta1 only)
curl -s "https://aiplatform.googleapis.com/v1beta1/publishers/google/models" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}"
```

**Important Notes:**
- `publishers.models.list` is only available in **v1beta1**, not v1
- **API Keys (AQ...) will NOT work** for this endpoint
- Use OAuth2 token (`ya29...`) instead

---

## 4. check_api.py Usage

### 4.1 Basic Usage

```bash
# Test Gemini API Key (will list models)
python check_api.py -k "AIzaSy..." --list-models

# Filter by model type
python check_api.py -k "AIzaSy..." --filter veo
python check_api.py -k "AIzaSy..." --filter imagen

# Show model details
python check_api.py -k "AIzaSy..." --list-models --details
```

### 4.2 Vertex AI with OAuth2

```bash
# Get OAuth2 token first
ACCESS_TOKEN=$(gcloud auth application-default print-access-token)

# Test Vertex AI with OAuth2 token
python check_api.py -k "${ACCESS_TOKEN}" -p "PROJECT_ID" --list-models

# Compare endpoints
python check_api.py -k "${ACCESS_TOKEN}" -p "PROJECT_ID" --compare-endpoints
```

### 4.3 What Happens with AQ... Key

```bash
# This will FAIL for List Models
python check_api.py -k "AQ.Ab8R..." -p "PROJECT_ID" --list-models

# Error: 401 UNAUTHENTICATED
# "API keys are not supported by this API"

# Solution: Use OAuth2 token instead
ACCESS_TOKEN=$(gcloud auth application-default print-access-token)
python check_api.py -k "${ACCESS_TOKEN}" -p "PROJECT_ID" --list-models
```

---

## 5. Key Detection Logic

```python
def detect_api_type(api_key: str) -> tuple:
    """Detect API type from key/token format.

    For check_api.py operations (List Models, Get Model Info):
      - AIzaSy...  → Gemini API (works)
      - ya29...    → Vertex AI OAuth2 (works)
      - AQ.Ab8R... → Vertex AI API Key (NOT SUPPORTED for these operations!)

    NOTE: AQ... keys DO work for video_gen.py (predictLongRunning)
          but NOT for check_api.py (List Models)
    """
    if api_key.startswith("AIzaSy"):
        return "gemini", "api_key"
    elif api_key.startswith("ya29."):
        return "vertex", "oauth2"
    elif api_key.startswith("AQ."):
        return "vertex", "invalid_api_key"  # Not supported for List Models
    else:
        return "unknown", "unknown"
```

---

## 6. Summary

### For check_api.py (List Models):

| Key Type | Works? | Solution |
|----------|:------:|----------|
| `AIzaSy...` (Gemini) | ✅ | Use directly |
| `AQ...` (Vertex AI Key) | ❌ | Use OAuth2 token instead |
| `ya29...` (OAuth2) | ✅ | Use directly |

### For video_gen.py (Video Generation):

| Key Type | Works? |
|----------|:------:|
| `AIzaSy...` (Gemini) | ✅ |
| `AQ...` (Vertex AI Key) | ✅ **Yes!** |
| `ya29...` (OAuth2) | ✅ |

> **Key Difference:** `AQ...` keys work for `predictLongRunning` (video/image gen) but NOT for `List Models`!

---

---

> Full history: [check.changelog.md](../changelog/check.changelog.md)
