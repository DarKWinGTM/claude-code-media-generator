# 🎬 Video Generation Design Document

## Version: 2.28
## Date: 2026-02-06
## Parent Document: [design.md](./design.md)

---

## 📋 Document Overview

เอกสารนี้อธิบายรายละเอียดการ Generate Video ผ่าน Google AI APIs (Veo) รวมถึง:
- 8 Video Generation Modes
- Connection Methods ที่รองรับ
- Endpoints และ API Methods
- สิ่งที่ทำได้/ไม่ได้
- ข้อจำกัดและ Pricing
- API Request/Response Formats
- Authentication Details

> **Note:** สำหรับ Overview ทั้งระบบ ดูที่ [design.md](./design.md)
> **Note:** สำหรับ Image Generation ดูที่ [image.design.md](./image.design.md)
> **Note:** สำหรับ Cloud Storage (GCS) ดูที่ [design.md - Cloud Storage](./design.md#️-google-cloud-storage-gcs-integration)

---

## 1. API Method

### 1.1 Core Method: `predictLongRunning`

```
Video Generation ใช้ API Method: predictLongRunning

Request Type:  Asynchronous (ต้อง polling รอผล)
Response Time: ~30-120 seconds
Payload Format: { instances: [...] }
```

### 1.2 Why Asynchronous?

- Video processing ใช้เวลานาน (~30-120 วินาที)
- ไม่สามารถรอ response ได้ทันที
- ต้องใช้ Long-Running Operation pattern:
  1. Submit request → ได้ Operation ID
  2. Poll operation → รอจนเสร็จ
  3. Get result → Download video

### 1.3 ⚠️ Critical Limitation

```
cloudcode-pa.googleapis.com (CLIProxyAPI Endpoint)
├─ generateContent:      ✅ Supported (Image works)
└─ predictLongRunning:   ❌ 404 NOT FOUND (Video fails!)

Video Generation CANNOT use CLIProxyAPI!
Must use API Key or Service Account.
```

---

## 2. Endpoint Support Matrix

### 2.1 Which Endpoints Support Video Generation?

| Endpoint | predictLongRunning | Video Models | Auth Methods |
|----------|-------------------|--------------|--------------|
| `cloudcode-pa.googleapis.com` | ❌ **404 NOT FOUND** | ❌ No | Built-in OAuth |
| `generativelanguage.googleapis.com` | ✅ Supported | ✅ Yes | API Key (`AIzaSy...`) |
| `aiplatform.googleapis.com` (Global) | ✅ Supported | ✅ Yes | **OAuth2** (`ya29...`) / Service Account |
| `{region}-aiplatform.googleapis.com` | ✅ Supported | ✅ Yes | **OAuth2** (`ya29...`) / Service Account |

**Key Finding:** Video generation (`predictLongRunning`) does NOT work on CLIProxyAPI endpoint.

**🆕 Critical Update (v2.13):**
- ✅ **Vertex AI API Keys (`AQ...`) WORK for `predictLongRunning`!** (video/image generation)
- ⚠️ But `AQ...` keys do **NOT** work for List Models, generateContent, etc. (returns 401)
- Both **Global** (`aiplatform.googleapis.com`) and **Regional** (`{region}-aiplatform.googleapis.com`) endpoints work
- For full Vertex AI access (all operations), use **OAuth2 token** (`ya29...`) or **Service Account**
- For simple development, use **Gemini API** with `AIzaSy...` key (works with all operations)

### 2.2 Endpoint Details

```
ENDPOINT CAPABILITIES FOR VIDEO

1. cloudcode-pa.googleapis.com (CLIProxyAPI/Built-in OAuth)
   ├─ predictLongRunning: ❌ 404 NOT FOUND
   ├─ Reason: Designed for CODE ASSIST, not media generation
   └─ Use Case: ไม่สามารถใช้กับ Video ได้

2. generativelanguage.googleapis.com (Gemini API)
   ├─ predictLongRunning: ✅ Supported
   ├─ Auth: API Key (AIzaSy...)
   ├─ Billing: ✅ Required (Veo has NO free tier)
   └─ Use Case: Simple integration (Recommended for development)

3. aiplatform.googleapis.com (Vertex AI - Global)
   ├─ predictLongRunning: ✅ Supported
   ├─ Auth: API Key (AQ...) ✅ for predictLongRunning ONLY!
   │        OAuth2 (ya29...) ✅ for ALL operations
   │        Service Account ✅ for ALL operations
   ├─ Billing: ✅ Required
   └─ Use Case: Enterprise, Production

4. {region}-aiplatform.googleapis.com (Vertex AI - Regional)
   ├─ predictLongRunning: ✅ Supported
   ├─ Auth: API Key (AQ...) ✅ for predictLongRunning ONLY!
   │        OAuth2 (ya29...) ✅ for ALL operations
   │        Service Account ✅ for ALL operations
   ├─ Regions: us-central1, us-east4, europe-west4, etc.
   ├─ Benefit: Lower latency for specific regions
   └─ Use Case: Production with region-specific requirements
```

### 2.3 API Comparison: Gemini API vs Vertex AI

| Feature | Gemini API | Vertex AI |
|---------|------------|-----------|
| **Base URL** | `https://generativelanguage.googleapis.com/v1beta` | `https://aiplatform.googleapis.com/v1` (Global) |
| **Regional URL** | - | `https://{LOCATION}-aiplatform.googleapis.com/v1` |
| **Authentication** | API Key (`AIzaSy...`) ✅ | OAuth2 (`ya29...`) / Service Account / API Key (limited) ⚠️ |
| **API Key Support** | ✅ Yes (all operations) | ⚠️ **`AQ...` works for predictLongRunning ONLY** |
| **Video Generation** | ✅ Supported | ✅ Supported |
| **Video Extension** | ✅ Supported | ✅ Supported |
| **Native Audio (Veo 3.1)** | ✅ Supported | ✅ Supported |
| **Best For** | Prototyping, Simple use cases | Production, Enterprise |
| **GCP Project Required** | ❌ Not required | ✅ Required |
| **Billing** | Pay-as-you-go (API Key) | GCP Billing Account |

---

## 3. Authentication Methods

### 3.1 Methods Comparison for Video (v2.13 - Corrected)

| Method | Key Format | Endpoint | Video Gen? | Other Ops? | Billing Required |
|--------|------------|----------|:----------:|:----------:|:----------------:|
| **Built-in OAuth** | - | cloudcode-pa | ❌ NO (404) | - | - |
| **Gemini API Key** | `AIzaSy...` | generativelanguage | ✅ Yes | ✅ Yes | ✅ Yes |
| **Vertex AI API Key** | `AQ.Ab8R...` | aiplatform | ✅ **Yes** | ❌ No | ✅ Yes |
| **OAuth2 Token** | `ya29...` | aiplatform | ✅ Yes | ✅ Yes | ✅ Yes |
| **Service Account** | JSON file | aiplatform | ✅ Yes | ✅ Yes | ✅ Yes |

**⚠️ Critical Update (v2.13):**
- **Vertex AI API Keys (`AQ...`) WORK for `predictLongRunning`!** (video/image generation)
- But they do **NOT** work for: List Models, Get Model Info, generateContent (returns 401)
- For full Vertex AI access (all operations), use **OAuth2 token** or **Service Account**

### 3.2 ✅ RECOMMENDED: Gemini API Key (Simplest)

> **Recommended for:** Development, Prototyping, Simple use cases
>
> **Key Benefits:**
> - **Simple Setup** - Just set `GOOGLE_API_KEY`
> - **Works Immediately** - No OAuth or Service Account needed
> - **All Veo Models** - Supports Veo 2.0, 3.0, 3.1

```
Gemini API Key Authentication

export GOOGLE_API_KEY="AIzaSy..."

python video_gen.py "A cat walking"
    │
    ▼
generativelanguage.googleapis.com/v1beta/models/veo-3.1-generate-preview:predictLongRunning
    │
    ▼
✅ VIDEO GENERATED
```

**Example curl:**
```bash
API_KEY="AIzaSy..."

curl -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/veo-3.1-generate-preview:predictLongRunning?key=${API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "instances": [{"prompt": "A cat walking in a garden"}],
    "parameters": {"aspectRatio": "16:9", "durationSeconds": 8}
  }'
```

> **Note:** Audio is automatic for Veo 3.x models - no `generateAudio` parameter needed.

### 3.3 ⚠️ Vertex AI Authentication Options

> **For Vertex AI, you have 3 options:**
> 1. **API Key (`AQ...`)** - Works for `predictLongRunning` (video/image gen) ONLY
> 2. **OAuth2 Token (`ya29...`)** - Works for ALL operations
> 3. **Service Account** - Works for ALL operations (Production recommended)

```
Vertex AI OAuth2 Authentication

# Step 1: Install gcloud CLI
# https://cloud.google.com/sdk/docs/install

# Step 2: Login and get token
gcloud auth login
gcloud auth application-default print-access-token
# Output: ya29.c.c0ASRK0G...

# Step 3: Use the token
export ACCESS_TOKEN="ya29.c.c0ASRK0G..."
export PROJECT_ID="your-project-id"

curl -X POST \
  "https://aiplatform.googleapis.com/v1/projects/${PROJECT_ID}/locations/us-central1/publishers/google/models/veo-3.1-generate-preview:predictLongRunning" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -H "Content-Type: application/json" \
  -d '{
    "instances": [{"prompt": "A cat walking in a garden"}],
    "parameters": {"aspectRatio": "16:9", "durationSeconds": 8}
  }'
```

**✅ Option 1: Vertex AI API Key (for Video Generation):**
```bash
# ✅ WORKS - Vertex AI API Key with predictLongRunning!
API_KEY="AQ.Ab8RN6Kn..."  # This key format WORKS for video gen!
PROJECT_ID="your-project-id"

curl -X POST \
  "https://us-central1-aiplatform.googleapis.com/v1/projects/${PROJECT_ID}/locations/us-central1/publishers/google/models/veo-3.1-generate-preview:predictLongRunning?key=${API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "instances": [{"prompt": "A cat walking in a garden"}],
    "parameters": {"aspectRatio": "16:9", "durationSeconds": 8}
  }'

# ⚠️ NOTE: This same key will NOT work for List Models, generateContent, etc.
# For those operations, use OAuth2 token or Gemini API Key instead.
```

**❌ This does NOT work (Vertex AI API Key with List Models):**
```bash
# ❌ WRONG - API Keys don't work for List Models!
API_KEY="AQ.Ab8RN6Kn..."

curl -X GET \
  "https://aiplatform.googleapis.com/v1beta1/publishers/google/models?key=${API_KEY}"

# Error: 401 UNAUTHENTICATED
# "API keys are not supported by this API. Expected OAuth2 access token"
```

> **💡 Key Insight:** `AQ...` keys work for `predictLongRunning` (video/image gen) but NOT for List Models.
> For List Models, use OAuth2 token or check with Gemini API Key instead.
> See [check.design.md](./check.design.md) for details on List Models operations.

### 3.4 ⚠️ Why CLIProxyAPI Doesn't Work for Video

```
Video Generation via CLIProxyAPI (FAILS)

video_gen.py request
    │
    ▼
CLIProxyAPI (localhost:8317)
    │ Uses built-in OAuth with cloud-platform scope
    ▼
cloudcode-pa.googleapis.com/v1internal:predictLongRunning
    │
    ▼
❌ 404 NOT FOUND

Failure reasons:
1. Method = predictLongRunning (NOT supported by cloudcode endpoint)
2. cloudcode-pa is designed for CODE ASSIST, not media generation
3. Even if method worked, Veo has NO free tier
```

### 3.3 Recommended: API Key (Simplest)

```
Video Generation via API Key (Recommended)

video_gen.py --auth api-key --api-key YOUR_KEY
    │
    ▼
x-goog-api-key header
    ▼
generativelanguage.googleapis.com/v1beta/models/{model}:predictLongRunning
    │
    ▼
Long-running operation (poll for result)
    ▼
✅ VIDEO GENERATED
```

### 3.4 Authentication Compatibility Matrix (⚠️ Updated v2.13)

> **⚠️ IMPORTANT:** Vertex AI API Keys (`AQ...`) have **LIMITED support**!
> - ✅ Works with `predictLongRunning` (Video/Image Generation)
> - ❌ Does NOT work with List Models, generateContent, etc.

| Auth Method | Gemini API | Vertex AI (Video Gen) | Vertex AI (Other) | Status | Notes |
|-------------|:----------:|:---------------------:|:-----------------:|:------:|-------|
| **Gemini API Key** (`AIzaSy...`) | ✅ | ❌ | ❌ | ✅ Active | Gemini API only |
| **Vertex AI Key** (`AQ.Ab8R...`) | ❌ | ✅ **Yes** | ❌ No | ⚠️ Limited | `predictLongRunning` only! |
| **OAuth2 Token** (`ya29...`) | ✅ | ✅ | ✅ | ✅ Active | `gcloud auth print-access-token` |
| **Service Account** (OAuth) | ✅ | ✅ | ✅ | ✅ Active | Production recommended |
| **CLIProxyAPI Token** | - | ❌ | - | ❌ N/A | 404 on predictLongRunning |

### 3.5 Supported Authentication Methods (v2.13)

**Current Implementation:** 3 working methods for Video Generation

| # | Method | CLI Flag | Key Format | Endpoint | Video Gen | List Models |
|---|--------|----------|------------|----------|:---------:|:-----------:|
| 1 | **Gemini API Key** | `--auth api-key` | `AIzaSy...` | Gemini API | ✅ | ✅ |
| 2 | **Vertex AI Key** | `--auth api-key` | `AQ...` | Vertex AI | ✅ | ❌ |
| 3 | **OAuth2 Token** | `--auth oauth2` | `ya29...` | Vertex AI | ✅ | ✅ |
| 4 | **Service Account** | `--auth service-account` | JSON file | Vertex AI | ✅ | ✅ |

> **⚠️ v2.13 UPDATE:**
> - **Vertex AI API Keys (`AQ...`) WORK for video generation!** (predictLongRunning)
> - But they do NOT work for: List Models, Get Model Info, generateContent
> - For full Vertex AI access, use **OAuth2 token** (`ya29...`) or **Service Account**
> - For simple development, use **Gemini API** with `AIzaSy...` key

### 3.6 Endpoint Selection Logic (v2.13)

> **Note:** สำหรับ `check_api.py` (List Models) ดูที่ [check.design.md](./check.design.md)
> **⚠️ สำคัญ:** `AQ...` keys ทำงานกับ video generation (ใน video_gen.py) แต่ไม่ทำงานกับ List Models (ใน check_api.py)

```python
def detect_api_key_type(api_key: str) -> tuple:
    """Detect API key type from format.

    Key Format Detection (v2.13):
      - AIzaSy...  → Gemini API (works with all operations)
      - ya29...    → Vertex AI (OAuth2 token - works with all operations)
      - AQ.Ab8R... → Vertex AI (API Key - works with predictLongRunning ONLY!)

    Returns: (api_type, auth_method)
    """
    if api_key.startswith("AIzaSy"):
        return "gemini", "api_key"      # ✅ Works (all operations)
    elif api_key.startswith("ya29."):
        return "vertex", "oauth2"        # ✅ Works (all operations)
    elif api_key.startswith("AQ."):
        return "vertex", "api_key"       # ⚠️ Works (predictLongRunning ONLY!)
    else:
        return "gemini", "api_key"  # Default fallback


def get_authentication(args):
    """
    Authentication logic (v2.13).

    Working methods for Video Generation:
    - GOOGLE_API_KEY=AIzaSy...  → Gemini API ✅ (all operations)
    - GOOGLE_API_KEY=AQ.Ab8R... → Vertex AI ✅ (predictLongRunning only!)
    - OAuth2 token (ya29...)    → Vertex AI ✅ (all operations)
    - Service Account JSON      → Vertex AI ✅ (all operations)

    NOTE: AQ... keys do NOT work with List Models, generateContent, etc.
    """
    if auth_method == "api-key":
        api_key = get_api_key()
        api_type, auth_type = detect_api_key_type(api_key)

        if api_type == "gemini":
            headers["x-goog-api-key"] = api_key
            return headers, "gemini", None
        else:
            # Vertex AI API Key - use URL param for predictLongRunning
            # Store key for URL building (not sent as header)
            headers["_vertex_api_key"] = api_key
            return headers, "vertex", project_id

    elif auth_method == "service-account":
        # OAuth with service account JSON
        return headers, endpoint, project_id
```

### 3.7 Endpoint Mapping Summary (v2.13)

| CLI Usage | Key Format | Detected As | Endpoint Used | Video Gen | List Models |
|-----------|------------|-------------|---------------|:---------:|:-----------:|
| `--auth api-key` | `AIzaSy...` | Gemini | **Gemini API** | ✅ | ✅ |
| `--auth api-key` | `AQ...` | Vertex | **Vertex AI** | ✅ | ❌ |
| `--auth oauth2` | `ya29...` | Vertex | **Vertex AI** | ✅ | ✅ |
| `--auth service-account` | JSON file | Vertex | **Vertex AI** | ✅ | ✅ |

> **💡 Recommendation (v2.13):**
> - **Quick Start:** ใช้ Gemini API Key (`AIzaSy...`) - ง่ายที่สุด, รองรับทุก operation!
> - **Vertex AI Video Gen:** ใช้ Vertex AI Key (`AQ...`) - ใช้ได้กับ video/image generation!
> - **Full Vertex AI Access:** ใช้ OAuth2 token (`ya29...`) หรือ Service Account

### 3.8 🔍 Case Study: Why CLIProxyAPI Cannot Work with Veo

> **Key Learning:** การทำความเข้าใจปัญหานี้ช่วยให้เห็นภาพรวมของ OAuth Scopes, Endpoints, และ API Methods อย่างชัดเจน

#### ปัญหา 2 ชั้นที่ซ้อนกัน

```
ปัญหาที่ 1: OAuth Scope ไม่ครอบคลุม
    │
    CLIProxyAPI Built-in OAuth Client
        │
        └── ขอแค่ scope: cloud-platform
                │
                ├── ใช้กับ cloudcode-pa.googleapis.com ได้ ✅
                │
                └── ใช้กับ generativelanguage.googleapis.com ไม่ได้ ❌
                        │
                        └── ต้องการ scope: generative-language.*


ปัญหาที่ 2: Endpoint ไม่รองรับ API Method
    │
    พยายามใช้ cloudcode-pa.googleapis.com แทน
        │
        ├── generateContent (Image) → ✅ Works
        │
        └── predictLongRunning (Video) → ❌ 404 NOT FOUND
                │
                └── Endpoint นี้ออกแบบมาสำหรับ Code Assist
                    ไม่ใช่ Media Generation
```

#### Scope Requirements by Endpoint

| Endpoint | Required Scope | CLIProxyAPI มี? | ผลลัพธ์ |
|----------|----------------|:---------------:|---------|
| `cloudcode-pa.googleapis.com` | `cloud-platform` | ✅ มี | Image ✅ / Video ❌ (404) |
| `generativelanguage.googleapis.com` | `generative-language.*` | ❌ ไม่มี | ❌ Scope Insufficient |
| `aiplatform.googleapis.com` | `cloud-platform` | ✅ มี | ต้องใช้ Service Account |

#### CLIProxyAPI Built-in OAuth Scopes

```
CLIProxyAPI ขอ Scopes เหล่านี้เท่านั้น:

✅ มี:
├── cloud-platform (ครอบคลุม GCP services)
├── userinfo.email
└── userinfo.profile

❌ ไม่มี:
├── generative-language.peruserquota
├── generative-language.tuning
└── generative-language.retriever
```

#### ความแตกต่าง: Image vs Video

| Factor | 🖼️ Image Generation | 🎬 Video Generation |
|--------|---------------------|---------------------|
| **API Method** | `generateContent` | `predictLongRunning` |
| **cloudcode-pa รองรับ?** | ✅ Yes | ❌ No (404) |
| **ต้องการ Scope พิเศษ?** | ❌ No | ✅ Yes (สำหรับ Gemini API) |
| **CLIProxyAPI Token ใช้ได้?** | ✅ Yes | ❌ No |
| **Free Tier?** | ✅ Yes (Gemini 2.0 Flash) | ❌ No |

#### สิ่งที่ลองทำและผลลัพธ์

| ลำดับ | วิธีที่ลอง | Endpoint | ผลลัพธ์ | สาเหตุ |
|:-----:|-----------|----------|---------|--------|
| 1 | ใช้ CLIProxyAPI Token ตรงๆ | `generativelanguage` | ❌ 403 | Scope ไม่พอ |
| 2 | ส่งผ่าน CLIProxyAPI | `cloudcode-pa` | ❌ 404 | Method ไม่รองรับ |
| 3 | **API Key** | `generativelanguage` | ✅ Works | ไม่ต้องการ OAuth |
| 4 | **Service Account** | `aiplatform` | ✅ Works | มี Scope ครบ |

#### ทำไมต้องใช้ API Key หรือ Service Account?

```
API Key:
    ├── ไม่ใช้ OAuth → ไม่มีปัญหา Scope
    ├── ใช้ Header: x-goog-api-key
    └── ส่งตรงไป generativelanguage.googleapis.com ได้

Service Account:
    ├── สร้าง JWT Token เอง → กำหนด Scope ได้เอง
    ├── ใช้ scope: cloud-platform (ครอบคลุมหมด)
    └── ส่งไป aiplatform.googleapis.com ได้
```

#### สรุป: ทางเลือกที่ใช้งานได้

```
ต้องการใช้ Veo Video Generation:
    │
    ├── ❌ CLIProxyAPI OAuth Token
    │       ├── Scope ไม่ครอบคลุม generative-language
    │       └── cloudcode-pa ไม่รองรับ predictLongRunning
    │
    ├── ✅ API Key (Recommended for Development)
    │       ├── ง่ายที่สุด
    │       ├── ไม่ต้อง OAuth
    │       └── ใช้กับ generativelanguage.googleapis.com
    │
    └── ✅ Service Account (Recommended for Production)
            ├── ปลอดภัยกว่า
            ├── กำหนด Scope ได้เอง
            └── ใช้กับ aiplatform.googleapis.com
```

---

## 4. Video Models & Pricing

### 4.1 Available Veo Models

| Model | Free Tier | Paid Price | Features |
|-------|-----------|------------|----------|
| **Veo 2.0** | ❌ No | $0.35/second | Standard |
| **Veo 3.0** | ❌ No | $0.15-0.40/second | Improved |
| **Veo 3.0 Fast** | ❌ No | $0.15/second | Faster |
| **Veo 3.1** | ❌ No | $0.15-0.60/second | Native Audio |
| **Veo 3.1 Fast** | ❌ No | $0.15/second | Fastest + Audio |

### 4.2 Model IDs (Updated 2026-01-18 from Official Docs)

| Model ID | Version | Status | Features |
|----------|---------|--------|----------|
| **Veo 2.0** ||||
| `veo-2.0-generate-001` | 2.0 | ✅ Stable | Text/Image-to-video, 5-8s |
| `veo-2.0-generate-exp` | 2.0 | 🧪 Experimental | enhancePrompt, referenceImages |
| `veo-2.0-generate-preview` | 2.0 | 🔬 Preview | mask (inpainting/outpainting) |
| **Veo 3.0** ||||
| `veo-3.0-generate-001` | 3.0 | ✅ Stable | Production-ready, native audio |
| `veo-3.0-fast-generate-001` | 3.0 | ✅ Stable | Faster generation |
| `veo-3.0-generate-preview` | 3.0 | 🔬 Preview | Preview features |
| `veo-3.0-fast-generate-preview` | 3.0 | 🔬 Preview | Faster preview |
| **Veo 3.1** ||||
| `veo-3.1-generate-001` | 3.1 | ✅ Stable | Native audio, 720p/1080p/4k |
| `veo-3.1-fast-generate-001` | 3.1 | ✅ Stable | Fastest + audio |
| `veo-3.1-generate-preview` | 3.1 | 🔬 Preview | lastFrame, referenceImages, **mask (Add/Remove Object)** |
| `veo-3.1-fast-generate-preview` | 3.1 | 🔬 Preview | Fastest preview, **mask (Add/Remove Object)** |

> **Status Legend:**
> - ✅ Stable = Production-ready
> - 🔬 Preview = New features, may change
> - 🧪 Experimental = Testing features

### 4.3 ⚠️ Critical: NO Free Tier

```
ALL Veo models require GCP Billing enabled.
There is NO free tier for video generation via API.

Free alternatives:
- gemini.google.com (Web UI) - 3-5 videos/day with Pro subscription
- aistudio.google.com (Web UI) - Limited quota
```

---

## 5. Video Extension: Input Standards (⚠️ Critical)

> **Key Learning:** จากการทดลองและศึกษา documentation พบว่า Video Extension มีข้อกำหนดเฉพาะที่แตกต่างกันตาม endpoint ที่ใช้

### 5.1 Video Extension Input Matrix

| Endpoint | Input Type | รองรับ? | ตัวอย่าง |
|----------|------------|:-------:|----------|
| **Gemini API** | Veo-generated URI | ✅ | `"uri": "https://generativelanguage.googleapis.com/v1beta/files/xxx:download"` |
| **Gemini API** | Local file (base64) | ❌ | `"bytesBase64Encoded": "..."` → **Error!** |
| **Gemini API** | GCS URI | ❌ | `"gcsUri": "gs://..."` → **Not supported** |
| **Vertex AI** | GCS URI | ✅ | `"gcsUri": "gs://bucket/video.mp4"` |
| **Vertex AI** | Local file | ❌ | ต้อง upload ไป GCS ก่อน |

### 5.2 ⚠️ Critical: Gemini API Requires Veo URI

```
Gemini API Video Extension:

✅ รับได้:
   "video": {
     "uri": "https://generativelanguage.googleapis.com/v1beta/files/xxxxx:download?alt=media"
   }
   ↑ URI นี้ได้จาก Veo generation ก่อนหน้า (อายุ 2 วัน)

❌ ไม่รับ:
   "video": { "bytesBase64Encoded": "..." }     ← Error: not supported
   "video": { "gcsUri": "gs://..." }            ← Error: not supported
   "video": { "uri": "file:///local/path" }    ← Error: invalid URI
```

**Error ที่พบเมื่อใช้ local file:**
```
API error (400): bytesBase64Encoded isn't supported by this model.
Please remove it or refer to the Gemini API documentation for supported usage.
```

### 5.3 Workflow: Correct Video Extension (Gemini API)

```
Step 1: Generate Initial Video
    │
    POST /models/veo-3.1-generate-preview:predictLongRunning
    { "prompt": "..." }
    │
    ▼
Step 2: Poll Operation จน done=true
    │
    GET /models/veo-3.1-generate-preview/operations/{id}
    │
    ▼
Step 3: Extract video URI จาก response
    │
    response.generateVideoResponse.generatedSamples[0].video.uri
    = "https://generativelanguage.googleapis.com/v1beta/files/xxx:download"
    │
    ▼
Step 4: บันทึก URI ไว้ใน metadata (สำคัญ!)
    │
    metadata.api_response.video_uri = URI
    metadata.api_response.video_uri_expires = timestamp + 2 days
    │
    ▼
Step 5: Extend ภายใน 2 วัน
    │
    POST /models/veo-3.1-generate-preview:predictLongRunning
    {
      "prompt": "continue the scene...",
      "video": { "uri": "https://...files/xxx:download" }
    }
    │
    ▼
✅ Extended Video (original + 7 seconds)
```

### 5.4 CLI Usage: Video Extension

```bash
# ✅ Correct: ใช้ Veo URI จาก previous generation
python video_gen.py "Continue with dramatic reveal" \
  --extend-video "https://generativelanguage.googleapis.com/v1beta/files/xxx:download"

# ❌ Error: ใช้ local file
python video_gen.py "Continue scene" \
  --extend-video video.mp4
# → Error: bytesBase64Encoded isn't supported by this model
```

### 5.5 Video Extension via Vertex AI (Alternative)

สำหรับ video file ใดๆ (ไม่ใช่แค่ Veo-generated) ต้องใช้ Vertex AI + GCS:

```bash
# Step 1: Upload video to GCS
gsutil cp video.mp4 gs://your-bucket/videos/

# Step 2: Extend via Vertex AI
python video_gen.py "Continue the scene" \
  --auth service-account \
  --credentials /path/to/sa.json \
  --project-id YOUR_PROJECT \
  --extend-video "gs://your-bucket/videos/video.mp4"
```

### 5.6 Comparison: Gemini API vs Vertex AI for Extension

| Aspect | Gemini API | Vertex AI |
|--------|:----------:|:---------:|
| **ใช้ Veo URI** | ✅ | ✅ |
| **ใช้ GCS URI** | ❌ | ✅ |
| **ใช้ Local File** | ❌ | ❌ (ต้อง upload GCS) |
| **ใช้ Video อะไรก็ได้** | ❌ (Veo เท่านั้น) | ✅ (ผ่าน GCS) |
| **URI อายุ** | 2 วัน | ไม่จำกัด (GCS) |
| **Setup ที่ต้องการ** | API Key | Service Account + GCS |

### 5.7 Metadata: Save Video URI for Future Extension

```json
{
  "api_response": {
    "operation_name": "models/veo-3.1-generate-preview/operations/xxx",
    "video_uri": "https://generativelanguage.googleapis.com/v1beta/files/xxx:download?alt=media",
    "video_uri_expires": "2026-01-20T13:52:46Z",
    "processing_time_seconds": 131.03
  }
}
```

> **Implementation Note:** `video_gen.py` ควรบันทึก `video_uri` และ `video_uri_expires` ไว้ใน metadata ทุกครั้ง เพื่อให้ user สามารถ extend video ได้ภายใน 2 วัน

### 5.8 Key Takeaways

1. **Gemini API**: ต้องใช้ Veo URI จาก previous generation เท่านั้น
2. **Local file (base64)**: ไม่รองรับสำหรับ Video Extension ใน Gemini API
3. **GCS URI**: รองรับเฉพาะใน Vertex AI
4. **Video URI มีอายุ 2 วัน**: ต้อง extend ภายในเวลานั้น
5. **บันทึก URI ไว้ใน metadata**: สำคัญสำหรับการ extend ในอนาคต

### 5.9 API Feature Limitations (2026-01)

> **⚠️ Current Status:** Video Extension อาจยังไม่เปิดให้ใช้งานสำหรับทุก API key/project

**Error ที่อาจพบ:**
```
API error (400): Your use case is currently not supported.
Please refer to Gemini API documentation for current model offering.
```

**สาเหตุที่เป็นไปได้:**
- Video Extension ยังอยู่ใน preview/limited access
- API key ไม่มี permission สำหรับ feature นี้
- Model version ไม่รองรับ Video Extension

**วิธีแก้ไข:**
1. ตรวจสอบ [Gemini API release notes](https://ai.google.dev/gemini-api/docs/release-notes)
2. ลองใช้ Vertex AI endpoint แทน (รองรับ GCS URI)
3. ติดต่อ Google Cloud support หากต้องการ access

### 5.10 GCS Storage Output: `--storage-uri` Parameter

> **New Feature (v2.15):** สามารถกำหนดให้ API บันทึก video ไปยัง GCS โดยตรง แทนที่จะ return เป็น base64

#### Response Format Control

| Parameter | Response Format | Video Extension Compatible |
|-----------|-----------------|:--------------------------:|
| ไม่ระบุ `--storage-uri` | `bytesBase64Encoded` | ❌ ใช้ extend ไม่ได้ |
| ระบุ `--storage-uri gs://...` | `gcsUri` | ✅ ใช้ extend ได้ |

#### CLI Usage

```bash
# Generate video with GCS output
python video_gen.py "Your prompt" \
  --storage-uri "gs://YOUR_BUCKET/videos/" \
  --model veo-3.1-generate-preview

# Response contains gcsUri instead of base64:
# {
#   "videos": [{
#     "gcsUri": "gs://YOUR_BUCKET/videos/generated_video.mp4"
#   }]
# }

# Use gcsUri for video extension
python video_gen.py "Continue the scene" \
  --extend-video "gs://YOUR_BUCKET/videos/generated_video.mp4"
```

#### Prerequisites

1. **GCS Bucket** ต้องสร้างก่อน (ดู [design.md - Cloud Storage](./design.md#️-google-cloud-storage-gcs-integration))
2. **API Key/Service Account** ต้องมี write permission ไปยัง bucket
3. **Location** ควรเป็น `us-central1` (ตรงกับ Vertex AI location)

#### Benefits

| Benefit | Description |
|---------|-------------|
| **Video Extension** | gcsUri สามารถใช้เป็น input สำหรับ `--extend-video` |
| **Persistent Storage** | Video เก็บถาวรใน GCS ไม่หายเมื่อ session จบ |
| **Faster Response** | ไม่ต้อง transfer base64 data ขนาดใหญ่ |
| **Shareable** | สามารถ share GCS link ได้ |

### 5.11 Response Format Recommendation

> **Design Decision:** Base64 เป็น default response เพราะใช้งานง่าย ไม่ต้องตั้งค่า GCS แต่แนะนำ GCS สำหรับ Video Extension

#### Comparison: Base64 vs GCS

| Factor | Base64 (Default) | GCS (`--storage-uri`) |
|--------|:----------------:|:---------------------:|
| **Setup Required** | ❌ ไม่ต้อง | ⚠️ ต้องสร้าง bucket |
| **ใช้งานทันที** | ✅ ได้เลย | ⚠️ ต้อง setup ก่อน |
| **Video Extension** | ❌ ไม่รองรับ | ✅ **รองรับ** |
| **File Size Transfer** | ⚠️ ใหญ่ (base64 ~33% larger) | ✅ เล็กกว่า |
| **Persistent Storage** | ❌ ต้อง save เอง | ✅ เก็บใน cloud |
| **Dependencies** | ❌ ไม่มี | ⚠️ gcloud/gsutil |

#### When to Use Each

**ใช้ Base64 (Default) เมื่อ:**
- ต้องการใช้งานทันทีไม่ต้อง setup
- ไม่ต้องการ extend video
- One-time generation
- ไม่ต้องการ dependencies เพิ่ม

**ใช้ GCS (`--storage-uri`) เมื่อ:**
- ต้องการ **extend video** ในอนาคต
- ต้องการ persistent cloud storage
- ต้องการ share video link
- Batch generation หลายไฟล์

### 5.11.1 Response Format Strategy (v2.18 Recommendation)

> **💡 Recommended Strategy:** ใช้ GCS response (`--storage-uri`) สำหรับ Vertex AI API Key เพื่อรองรับ Video Extension

#### Strategy by API Type

| API Type | Default Response | Recommended | Reason |
|----------|-----------------|-------------|--------|
| **Gemini API** (`AIzaSy...`) | base64 | base64 + Veo URI | Veo URI ใช้ extend ได้ 2 วัน |
| **Vertex AI** (`AQ...`) | base64 | **GCS (`--storage-uri`)** | GCS URI ไม่หมดอายุ, extend ได้เลย |

#### Vertex AI API Key: Recommended Workflow

```
┌─────────────────────────────────────────────────────────────┐
│ Step 1: First Video Generation                              │
│ python video_gen.py "prompt"                                │
│   --api-key AQ.xxx...                                       │
│   --storage-uri gs://bucket/videos/                         │
│   --project-id PROJECT_ID                                   │
│                                                             │
│ Result: gs://bucket/videos/sample_0.mp4                     │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ Step 2: Extend Video (anytime - no expiry)                  │
│ python video_gen.py "continue the scene"                    │
│   --extend-video gs://bucket/videos/sample_0.mp4            │
│   --api-key AQ.xxx...                                       │
│   --storage-uri gs://bucket/videos/                         │
│   --project-id PROJECT_ID                                   │
│                                                             │
│ Result: gs://bucket/videos/sample_0.mp4 (+7 seconds)        │
└─────────────────────────────────────────────────────────────┘
```

#### Why GCS is Better for Vertex AI

| Aspect | Base64 Response | GCS Response |
|--------|-----------------|--------------|
| **Video Extension** | ❌ ต้อง upload ไป GCS ก่อน | ✅ ใช้ได้เลย |
| **URI Expiry** | N/A | ✅ ไม่หมดอายุ |
| **Storage** | ❌ Local only | ✅ Cloud persistent |
| **Share Video** | ❌ ต้อง upload | ✅ GCS URL พร้อมใช้ |
| **Setup Required** | ✅ ไม่ต้อง | ⚠️ ต้องสร้าง GCS bucket |

#### Quick Setup for GCS (One-time)

```bash
# 1. Create bucket (one-time)
gsutil mb -l us-central1 gs://YOUR_BUCKET_NAME/

# 2. Grant permission to Vertex AI service account
gsutil iam ch serviceAccount:vertex-express@PROJECT_ID.iam.gserviceaccount.com:objectUser \
  gs://YOUR_BUCKET_NAME

# 3. Generate with GCS output
python video_gen.py "Your prompt" \
  --api-key AQ.xxx... \
  --storage-uri gs://YOUR_BUCKET_NAME/videos/ \
  --project-id PROJECT_ID
```

#### Quick Start Examples

```bash
# Base64 (Default) - ใช้งานทันที ไม่ต้อง setup
python video_gen.py "Your prompt" --model veo-3.1-generate-preview

# GCS - สำหรับ Video Extension
python video_gen.py "Your prompt" \
  --storage-uri "gs://YOUR_BUCKET/videos/" \
  --model veo-3.1-generate-preview

# Extend video (ต้องใช้ GCS response)
python video_gen.py "Continue the scene" \
  --extend-video "gs://YOUR_BUCKET/videos/previous_video.mp4"
```

#### GCS Download Methods

| Method | Requires | Best For |
|--------|----------|----------|
| `gsutil cp` | gcloud CLI | CLI users |
| REST API + OAuth | gcloud CLI (for token) | Python integration |
| REST API + API Key | API Key with GCS permission | Serverless/Lambda |

**REST API Download:**
```bash
# With OAuth token (recommended)
curl -H "Authorization: Bearer $(gcloud auth print-access-token)" \
  "https://storage.googleapis.com/storage/v1/b/BUCKET/o/OBJECT?alt=media" \
  -o video.mp4

# With API Key (requires Storage Object User permission)
curl "https://storage.googleapis.com/storage/v1/b/BUCKET/o/OBJECT?alt=media&key=API_KEY" \
  -o video.mp4
```

### 5.12 API Key + GCS Permission Setup

> **ทำไมต้องตั้งค่า?** Vertex AI API Key (`AQ...`) สร้าง video ได้ แต่ไม่สามารถ download จาก GCS โดยตรง ต้องให้ permission เพิ่ม

#### Step 1: ค้นหา Service Account ที่ใช้กับ API Key

```bash
# ดู service account สำหรับ Vertex AI Express
gcloud iam service-accounts list --project=YOUR_PROJECT_ID \
  --filter="email:vertex-express@" --format="value(email)"
```

ปกติจะเป็น: `vertex-express@PROJECT_ID.iam.gserviceaccount.com`

#### Step 2: Grant Storage Object User Permission

**Option A: ผ่าน Google Cloud Console (Recommended)**

1. เปิด [Service Accounts Console](https://console.cloud.google.com/iam-admin/serviceaccounts)
2. เลือก Project ของคุณ
3. ค้นหา `vertex-express@...` service account
4. คลิก service account → Tab **PERMISSIONS**
5. คลิก **GRANT ACCESS**
6. เพิ่ม roles:
   - `Storage Object User` (สำหรับ download จาก GCS)
   - หรือ `Storage Object Viewer` (read-only)

**Direct Link:**
```
https://console.cloud.google.com/iam-admin/serviceaccounts/details/SERVICE_ACCOUNT_ID;edit=true/permissions?project=PROJECT_ID
```

**Option B: ผ่าน gcloud CLI**

```bash
# Grant bucket-level permission
gsutil iam ch serviceAccount:vertex-express@PROJECT_ID.iam.gserviceaccount.com:objectUser \
  gs://YOUR_BUCKET_NAME

# หรือ project-level (กว้างกว่า)
gcloud projects add-iam-policy-binding PROJECT_ID \
  --member="serviceAccount:vertex-express@PROJECT_ID.iam.gserviceaccount.com" \
  --role="roles/storage.objectUser"
```

#### Step 3: ทดสอบ

```bash
# Test API Key download หลังจากให้ permission
API_KEY="YOUR_VERTEX_AI_API_KEY"
BUCKET="your-bucket-name"
OBJECT="path%2Fto%2Fvideo.mp4"  # URL-encoded

curl -s -o /dev/null -w "%{http_code}" \
  "https://storage.googleapis.com/storage/v1/b/${BUCKET}/o/${OBJECT}?alt=media&key=${API_KEY}"
# 200 = Success, 403 = Permission denied
```

#### Permission Summary

| Download Method | Requirements |
|-----------------|--------------|
| `gsutil cp` | gcloud CLI installed + authenticated |
| OAuth token | gcloud CLI (for token) |
| API Key | **`Storage Object User` role** on service account |

> **Full Documentation:** ดู [design.md - Cloud Storage](./design.md#️-google-cloud-storage-gcs-integration) สำหรับการติดตั้ง gcloud, สร้าง bucket, และ permission settings

### 5.13 Download/Upload Strategy (v2.16+)

> **Design Principle:** REST API First, gsutil Fallback
> video_gen.py ใช้กลยุทธ์ลำดับความสำคัญในการ download/upload จาก GCS

#### Strategy Workflow

```
Pre-Check: ตรวจสอบ availability ของทั้งสองวิธี
├─ REST API: ทดสอบ API Key validity
└─ gsutil: shutil.which("gsutil")
        ↓
Step 1: REST API + API Key (Primary)
├─ Attempt 1: REST API download/upload
├─ Attempt 2: Retry with exponential backoff
└─ Attempt 3: Final retry
        ↓ (if failed)
Step 2: gsutil/gcloud (Fallback)
├─ Attempt 1: gsutil cp
├─ Attempt 2: Retry
└─ Attempt 3: Final retry
```

#### Implementation Details

| Phase | Action | Implementation |
|-------|--------|----------------|
| **Pre-Check** | ตรวจสอบ REST API | API Key validity check via test request |
| **Pre-Check** | ตรวจสอบ gsutil | `shutil.which("gsutil") is not None` |
| **Download** | REST API | `requests.get()` with `x-goog-api-key` header |
| **Download** | gsutil | `subprocess.run(["gsutil", "cp", uri, dest])` |
| **Upload** | REST API | `requests.post()` with multipart upload |
| **Upload** | gsutil | `subprocess.run(["gsutil", "cp", file, uri])` |

#### Error Handling & Retry Logic

```python
# Pseudo-code for download strategy
def download_from_gcs(uri, dest, headers):
    api_key = headers.get("x-goog-api-key")
    gsutil_available = shutil.which("gsutil") is not None

    # Phase 1: REST API (3 attempts)
    if api_key:
        for attempt in range(3):
            try:
                response = requests.get(rest_url, headers={"x-goog-api-key": api_key})
                if response.status_code == 200:
                    save_file(dest, response.content)
                    return True
            except Exception:
                time.sleep(2 ** attempt)  # Exponential backoff

    # Phase 2: gsutil fallback (3 attempts)
    if gsutil_available:
        for attempt in range(3):
            result = subprocess.run(["gsutil", "cp", uri, dest])
            if result.returncode == 0:
                return True
            time.sleep(2 ** attempt)

    return False
```

#### Status Messages

| Scenario | Log Message |
|----------|-------------|
| REST API success | `✓ Downloaded via REST API` |
| REST API failed, trying gsutil | `REST API failed, falling back to gsutil...` |
| gsutil success | `✓ Downloaded via gsutil` |
| Both failed | `✗ Download failed - use video_uri to retry manually` |
| gsutil not available | `gsutil not found, using REST API only` |

---

### 5.14 🔬 Experimental: Parameter Combinations with Video Extension

> **Status:** 🔬 Experimental - Needs API Testing
> **Added:** 2026-01-21
> **Use Case:** Closing credits with specific visual elements (logos, avatars)

### 5.14.1 Hypothesis: Parameters That Might Work with Video Extension

จาก API structure พบว่า Video Extension ใช้ `instances[0].video` ในขณะที่ features อื่นๆ ใช้ `parameters`:

```json
{
  "instances": [{
    "prompt": "Continue with closing credits...",
    "video": {"uri": "gs://bucket/source.mp4"}  // Video Extension
  }],
  "parameters": {
    "lastFrame": {...},           // Might work?
    "referenceImages": [...]      // Might work?
  }
}
```

### 5.14.2 Experimental Combinations to Test

| Combination | API Structure | Hypothesis | Status |
|-------------|---------------|------------|:------:|
| **Extension + lastFrame** | `video` + `parameters.lastFrame` | อาจทำงานได้ (คนละ field) | 🔬 Untested |
| **Extension + referenceImages** | `video` + `parameters.referenceImages` | อาจทำงานได้ (คนละ field) | 🔬 Untested |
| **Extension + Both** | `video` + `lastFrame` + `referenceImages` | ไม่แน่ใจ | 🔬 Untested |

### 5.14.3 Proposed Test: Extension + lastFrame

**Use Case:** Extend video แล้วจบที่ specific closing frame

```bash
# Proposed CLI (if implemented)
python video_gen.py "Smooth transition to closing credits" \
  --extend-video gs://bucket/original.mp4 \
  --last-frame closing_credits.png \
  --storage-uri gs://bucket/output/
```

**Expected API Payload:**
```json
{
  "instances": [{
    "prompt": "Smooth transition to closing credits",
    "video": {"uri": "gs://bucket/original.mp4"}
  }],
  "parameters": {
    "lastFrame": {"inlineData": {"mimeType": "image/png", "data": "BASE64..."}}
  }
}
```

### 5.14.4 Proposed Test: Extension + referenceImages

**Use Case:** Extend video โดยใช้ reference images เป็น visual guide

```bash
# Proposed CLI (if implemented)
python video_gen.py "Continue with orange starburst logo and network icon" \
  --extend-video gs://bucket/original.mp4 \
  --reference-asset avatar1.png avatar2.png \
  --storage-uri gs://bucket/output/
```

**Expected API Payload:**
```json
{
  "instances": [{
    "prompt": "Continue with orange starburst logo and network icon",
    "video": {"uri": "gs://bucket/original.mp4"}
  }],
  "parameters": {
    "referenceImages": [
      {"image": {"inlineData": {...}}, "referenceType": "asset"},
      {"image": {"inlineData": {...}}, "referenceType": "asset"}
    ]
  }
}
```

### 5.14.5 Implementation Plan

**Phase 1: Test API Directly**
1. ใช้ curl หรือ Python test script ส่ง payload โดยตรง
2. ทดสอบกับ Vertex AI endpoint (GCS URI)
3. บันทึกผลลัพธ์ (success/error)

**Phase 2: If Works - Implement in video_gen.py**
1. Update mode detection logic
2. Allow `--last-frame` และ `--reference-asset` กับ `--extend-video`
3. Build combined payload
4. Update documentation

**Phase 3: If Fails - Document Limitation**
1. Record error message
2. Add to "Known Limitations" section
3. Provide alternative workflow (FFmpeg concatenation)

### 5.14.6 Alternative Workflow (If API Doesn't Support)

ถ้า API ไม่รองรับ combination เหล่านี้ สามารถใช้ workflow นี้แทน:

```bash
# Step 1: Create closing segment separately (8 seconds)
python video_gen.py "Closing credits with logos" \
  --reference-asset avatar1.png avatar2.png \
  --duration 8 \
  --storage-uri gs://bucket/segments/

# Step 2: Concatenate with FFmpeg
ffmpeg -i original.mp4 -i closing_segment.mp4 \
  -filter_complex "[0:v][1:v]concat=n=2:v=1:a=0[outv]" \
  -map "[outv]" final_video.mp4
```

### 5.14.7 Test Results Log

| Date | Test | Result | Notes |
|------|------|:------:|-------|
| - | Extension + lastFrame | 🔬 Pending | - |
| - | Extension + referenceImages | 🔬 Pending | - |

> **Update this table** after running tests

---

### 5.15 Auto-Upload Implementation Details (Reference)

> **Status:** ✅ Implemented (v2.19)

#### Problem Statement

ปัจจุบัน Video Extension จาก local file ต้องทำ 2 ขั้นตอน:

```bash
# Current Workflow (2 steps)
Step 1: Upload local video to GCS
  python video_gen.py --upload-gcs video_A.mp4 gs://bucket/video_A.mp4

Step 2: Extend using GCS URI
  python video_gen.py "Continue scene" --extend-video gs://bucket/video_A.mp4
```

**Pain Points:**
- ต้องรันคำสั่งแยก 2 ครั้ง
- User ต้องจัดการ GCS URI เอง
- ไม่สะดวกสำหรับ workflow ที่ต้องการ extend หลายครั้ง

#### Proposed Solution: Auto-Upload Before Extend

```bash
# New Workflow (1 step) - Planned
python video_gen.py "Continue scene" \
  --extend-video video_A.mp4 \
  --storage-uri gs://bucket/videos/

# Auto-workflow:
# 1. Detect: video_A.mp4 is local file (not URI)
# 2. Auto-Upload: Upload video_A.mp4 → gs://bucket/videos/video_A.mp4
# 3. Use GCS URI: Pass GCS URI to Video Extension API
# 4. Generate: Create extended video
```

#### Implementation Design

```python
# Pseudocode for Auto-Upload Integration

elif mode == GenerationMode.VIDEO_EXTENSION:
    source = args.extend_video

    # Case 1: Already a URI (http, https, gs) - use directly
    if source.startswith(('http://', 'https://', 'gs://')):
        instance["video"] = {"uri": source}

    # Case 2: Local file - Auto-Upload first
    else:
        # Require --storage-uri for upload destination
        if not args.storage_uri:
            print_error("Local video extension requires --storage-uri")
            print_info("Example: --storage-uri gs://your-bucket/videos/")
            sys.exit(1)

        # Upload to GCS
        print_info(f"📤 Auto-uploading {source} to GCS...")
        gcs_uri = upload_to_gcs(Path(source), args.storage_uri, headers)

        if gcs_uri:
            print_success(f"✅ Uploaded: {gcs_uri}")
            instance["video"] = {"uri": gcs_uri}
        else:
            print_error("Failed to upload video to GCS")
            sys.exit(1)
```

#### Workflow Diagram

```
User Command:
python video_gen.py "Continue" --extend-video local.mp4 --storage-uri gs://bucket/

Internal Process:
  ┌─────────────────────────────────────────────────────────────┐
  │ Step 1: Detect Input Type                                   │
  │   local.mp4 → Local file (not URI)                          │
  ├─────────────────────────────────────────────────────────────┤
  │ Step 2: Auto-Upload to GCS                                  │
  │   📤 Uploading local.mp4 → gs://bucket/local.mp4            │
  ├─────────────────────────────────────────────────────────────┤
  │ Step 3: Build API Request                                   │
  │   instance["video"] = {"uri": "gs://bucket/local.mp4"}      │
  ├─────────────────────────────────────────────────────────────┤
  │ Step 4: Call Video Extension API                            │
  │   → Generate extended video                                 │
  └─────────────────────────────────────────────────────────────┘
```

#### CLI Usage (Planned)

```bash
# Basic: Extend local video (auto-upload to GCS)
python video_gen.py "Continue the scene" \
  --extend-video video_A.mp4 \
  --storage-uri gs://bucket/videos/

# With preset: Quality extension
python video_gen.py "The camera pans right" \
  --extend-video video_A.mp4 \
  --storage-uri gs://bucket/videos/ \
  --preset extend

# Chained extension: Multiple extends
python video_gen.py "Scene continues" \
  --extend-video gs://bucket/videos/video_A_extended.mp4
  # Already on GCS, no upload needed
```

#### Benefits

| Benefit | Description |
|---------|-------------|
| **Single Command** | ไม่ต้องแยก upload และ extend เป็น 2 คำสั่ง |
| **Automatic** | ตรวจจับ local file และ upload อัตโนมัติ |
| **Backward Compatible** | URI ยังใช้ได้เหมือนเดิม |
| **Chain-Friendly** | Output GCS URI พร้อมใช้สำหรับ extend ต่อ |

#### Prerequisites

1. **--storage-uri Required**: ต้องระบุ GCS bucket destination
2. **GCS Write Permission**: API key/credentials ต้องมี write access
3. **gcloud/gsutil**: ใช้สำหรับ upload (fallback to REST API)

---

### 5.16 Phase 2.5: Experimental Video Extension Enhancements (v2.20)

> **Status:** ✅ Implemented in `video_gen.py` v2.20
> **Added:** 2026-01-22

#### Overview

Phase 2.5 introduces experimental features that combine Video Extension mode with additional parameters. These features explore whether `parameters.*` fields can work together with `instances[0].video`.

#### 5.16.1 `--last-frame` with `--extend-video`

**Purpose:** Add a target closing frame to guide the video extension.

**Use Case:** Create videos that transition to a specific end state (e.g., credits, logo reveal).

**CLI Usage:**
```bash
# Extend video and guide it toward a specific closing frame
python video_gen.py "Transition to credits" \
  --extend-video source.mp4 \
  --last-frame closing_credits.jpg

# With GCS storage
python video_gen.py "Fade to sunset" \
  --extend-video gs://bucket/video.mp4 \
  --last-frame sunset.jpg \
  --storage-uri gs://bucket/output/
```

**API Request Structure:**
```json
{
  "instances": [{
    "prompt": "Transition to credits",
    "video": { "gcsUri": "gs://bucket/source.mp4", "mimeType": "video/mp4" }
  }],
  "parameters": {
    "durationSeconds": 7,
    "lastFrame": {
      "image": { "bytesBase64Encoded": "<base64>" },
      "mimeType": "image/jpeg"
    }
  }
}
```

**Status:** ✅ Tested (2026-01-22) - API accepts combined video + lastFrame payload

#### 5.16.2 `--reference-asset` with `--extend-video`

**Purpose:** Use reference images for subject consistency during video extension.

**Use Case:** Ensure characters or objects remain consistent when extending video (e.g., adding closing credits with team avatars).

**CLI Usage:**
```bash
# Extend video with character reference
python video_gen.py "Character waves goodbye" \
  --extend-video scene.mp4 \
  --reference-asset character.jpg

# Multiple references (up to 3)
python video_gen.py "Team appears for credits" \
  --extend-video intro.mp4 \
  --reference-asset avatar1.jpg avatar2.jpg avatar3.jpg
```

**API Request Structure:**
```json
{
  "instances": [{
    "prompt": "Character waves goodbye",
    "video": { "gcsUri": "gs://bucket/scene.mp4", "mimeType": "video/mp4" }
  }],
  "parameters": {
    "durationSeconds": 7,
    "referenceImages": [
      {
        "referenceType": "REFERENCE_TYPE_ASSET",
        "referenceId": 1,
        "image": { "bytesBase64Encoded": "<base64>", "mimeType": "image/jpeg" }
      }
    ]
  }
}
```

**Status:** ✅ Tested (2026-01-22) - API accepts combined video + referenceImages payload

#### 5.16.3 Closing Frame Images (→ image_gen.py)

> **Note:** Closing frame image creation is planned as a feature of `image_gen.py`, not a separate tool.
> See [image.design.md](./image.design.md) for planned `--template closing-frame` feature.

**Workflow Integration:**
```bash
# Step 1: Create closing frame using image_gen.py (planned feature)
python image_gen.py "Video credits with circular avatars" --template closing-frame -o closing.png

# Step 2: Use as last-frame in video extension
python video_gen.py "Transition to credits" \
  --extend-video main_content.mp4 \
  --last-frame closing.png \
  --storage-uri gs://bucket/output/
```

#### 5.16.4 Critical API Format Notes (v2.21)

> **Discovered:** 2026-01-22 during Phase 2.5 testing

**GCS URI Format:**
- ✅ CORRECT: `"video": { "gcsUri": "gs://...", "mimeType": "video/mp4" }`
- ❌ WRONG: `"video": { "uri": "gs://..." }` → Error: "video mime type is empty"

**Key Requirements:**
1. Use `gcsUri` (not `uri`) for GCS paths
2. Always include `mimeType: "video/mp4"` for video extension
3. `uri` is only for http/https URLs

**Common Errors and Solutions:**

| Error Message | Cause | Solution |
|---------------|-------|----------|
| `video is empty` | Using `uri` instead of `gcsUri` | Use `gcsUri` for `gs://` paths |
| `video mime type is empty` | Missing `mimeType` field | Add `"mimeType": "video/mp4"` |
| `Unsupported output video duration 5 seconds` | Invalid duration for text-to-video | Use 4, 6, or 8 seconds |
| `BucketNotFoundException` | Wrong GCS bucket path | Verify bucket exists and path is correct |

---

### 5.17 Unified Reference Image System (v2.24.1) ✅ TESTED

> **Status:** ✅ Implemented & Tested (2026-01-23)
> **Version:** v2.24.1

#### 5.17.1 Overview

**Problem Statement:**
- ปัจจุบันมี `--reference-asset` และ `--reference-style` แยกกัน
- ไม่ยืดหยุ่นในการกำหนด type ต่อภาพ

**🚨 CRITICAL API DISCOVERY (v2.24.1):**
> **API ไม่รองรับ mixed asset + style ใน request เดียวกัน!**
> - Error: `Reference to video does not support this mix of reference images.`
> - ต้องเลือกใช้ asset เท่านั้น หรือ style เท่านั้น ไม่สามารถผสมได้

**Solution: Option B1 - Unified `--reference-image` with Path:Type Format**

#### 5.17.2 CLI Syntax

```bash
# Basic format
--reference-image PATH:TYPE

# Where:
#   PATH = file path or GCS URI
#   TYPE = "asset" or "style" (default: "asset")
```

**Usage Examples:**

```bash
# Single image (default type = asset)
--reference-image avatar.png

# Single image with explicit type
--reference-image avatar.png:asset
--reference-image painting.png:style

# Multiple images (can be repeated)
--reference-image avatar1.png:asset \
--reference-image avatar2.png:asset \
--reference-image painting.png:style

# Mixed types in one command
python video_gen.py "Character in artistic style" \
  --reference-image character.png:asset \
  --reference-image artStyle.jpg:style \
  --model veo-2.0-generate-exp
```

#### 5.17.3 API Constraints & Validation

| Type | Max Count | Supported Models | Validation |
|------|-----------|------------------|------------|
| `asset` | 3 | veo-2.0-exp, veo-3.1-preview, veo-3.1-fast-preview | Error if > 3 |
| `style` | 1 | veo-2.0-exp **only** | Error if > 1, Error if wrong model |
| **🚨 Mixed** | **N/A** | **❌ NOT SUPPORTED** | **Error if both asset + style** |

**Smart Validation Rules:**

```python
# Pseudo-code
def validate_reference_images(images, model):
    asset_count = sum(1 for img in images if img.type == "asset")
    style_count = sum(1 for img in images if img.type == "style")

    if asset_count > 3:
        raise Error("Maximum 3 asset images allowed")

    if style_count > 1:
        raise Error("Maximum 1 style image allowed")

    if style_count > 0 and model != "veo-2.0-generate-exp":
        raise Error(f"Style reference only supported by veo-2.0-generate-exp, not {model}")

    # 🚨 NEW v2.24.1: API does not support mixing!
    if asset_count > 0 and style_count > 0:
        raise Error("Cannot mix asset and style in same request. API limitation.")
```

#### 5.17.4 API Payload Generation

> **⚠️ Note:** เนื่องจาก API ไม่รองรับ mixed types ตัวอย่างนี้แสดงเฉพาะ asset-only

**Input:**
```bash
--reference-image avatar1.png:asset \
--reference-image avatar2.png:asset
```

**Generated Payload:**
```json
{
  "instances": [{
    "prompt": "...",
    "referenceImages": [
      {
        "referenceType": "asset",
        "image": {
          "bytesBase64Encoded": "<base64 of avatar1.png>",
          "mimeType": "image/png"
        }
      },
      {
        "referenceType": "asset",
        "image": {
          "bytesBase64Encoded": "<base64 of avatar2.png>",
          "mimeType": "image/png"
        }
      }
    ]
  }],
  "parameters": {
    "durationSeconds": 8
  }
}
```

#### 5.17.5 Test Results (2026-01-23)

| Test Case | Input | Result | Notes |
|-----------|-------|--------|-------|
| **Two Asset Images** | 2x avatar PNG | ✅ SUCCESS | video_20260123_005611_0.mp4 |
| **Mixed Asset+Style** | 1 asset + 1 style | ❌ FAILED | API Error: "does not support this mix" |
| **Style Only** | 1 style image | ✅ SUCCESS | video_20260123_005734_0.mp4 |

#### 5.17.6 Backward Compatibility

**Legacy options ยังใช้งานได้:**

| Legacy Option | Equivalent New Option |
|---------------|----------------------|
| `--reference-asset img1.png img2.png` | `--reference-image img1.png:asset --reference-image img2.png:asset` |
| `--reference-style style.png` | `--reference-image style.png:style` |

**Implementation:**
- Keep legacy options working (deprecated warning)
- Internal conversion to unified format
- Gradual migration path

#### 5.17.7 Argument Parser Design

```python
# argparse configuration
parser.add_argument(
    "--reference-image",
    type=str,
    action="append",
    metavar="PATH[:TYPE]",
    help="Reference image with optional type (asset|style). Can be repeated. Default type: asset"
)

# Parsing function
def parse_reference_image(value):
    """Parse 'path:type' or 'path' (default type=asset)"""
    if ":" in value and not value.startswith("gs://"):
        # Handle local path:type
        path, ref_type = value.rsplit(":", 1)
        if ref_type not in ("asset", "style"):
            # It's not a type, treat whole as path
            return {"path": value, "type": "asset"}
        return {"path": path, "type": ref_type}
    elif value.startswith("gs://") and ":asset" in value or ":style" in value:
        # Handle gs://path:type
        if value.endswith(":asset"):
            return {"path": value[:-6], "type": "asset"}
        elif value.endswith(":style"):
            return {"path": value[:-6], "type": "style"}
    return {"path": value, "type": "asset"}
```

#### 5.17.8 Edge Cases

| Input | Parsed Path | Parsed Type |
|-------|-------------|-------------|
| `avatar.png` | `avatar.png` | `asset` (default) |
| `avatar.png:asset` | `avatar.png` | `asset` |
| `painting.jpg:style` | `painting.jpg` | `style` |
| `gs://bucket/img.png` | `gs://bucket/img.png` | `asset` (default) |
| `gs://bucket/img.png:asset` | `gs://bucket/img.png` | `asset` |
| `C:\path\to\img.png` | `C:\path\to\img.png` | `asset` (Windows path) |
| `C:\path\to\img.png:asset` | `C:\path\to\img.png` | `asset` |

#### 5.17.9 Implementation Checklist

- [x] Add `--reference-image` argument to argparse ✅
- [x] Implement `parse_reference_image()` function ✅
- [x] Add validation for max counts and model compatibility ✅
- [x] Add validation to prevent mixed asset+style (v2.24.1) ✅
- [x] Generate combined `referenceImages` array in payload ✅
- [x] Add deprecation warning for legacy options ✅
- [x] Update help text and examples ✅
- [ ] Add unit tests for parsing edge cases
- [x] Update documentation ✅
- [x] **API TESTED** (2026-01-23) ✅

---

### 5.18 Video Extension: External URLs & FPS Requirements (v2.25) 🆕

> **Date Added:** 2026-01-23
> **Session:** From testing session
> **Status:** 📋 Documented - Implementation Planned

#### 5.18.1 Problem Statement

จากการทดสอบจริงพบว่า:

1. **External HTTP/HTTPS URLs ไม่รองรับ** - API ไม่สามารถ fetch video จาก internet URLs
2. **Video ต้องมี 24fps เท่านั้น** - Video ที่ไม่ใช่ 24fps จะ error

#### 5.18.2 Test Results (2026-01-23)

| Test | Input | Result | Error |
|------|-------|--------|-------|
| External HTTPS URL | `https://test-videos.co.uk/...` | ❌ FAIL | `"video is empty"` |
| External HTTP URL | `http://commondatastorage.googleapis.com/...` | ❌ FAIL | `"video is empty"` |
| GCS URI (23fps) | `gs://gtv-videos-bucket/...` | ❌ FAIL | `"fps mismatch: Expected 24 got 23"` |
| GCS URI (25fps) | `gs://cloud-samples-data/video/cat.mp4` | ❌ FAIL | `"fps mismatch: Expected 24 got 25"` |
| Local Veo video → Upload GCS | Veo-generated (24fps) | ✅ SUCCESS | - |

#### 5.18.3 Key Discoveries

**1. External URLs Not Supported:**
```
External HTTP/HTTPS URLs → ❌ "video is empty"

สาเหตุ: Veo API ไม่สามารถ fetch videos จาก external internet URLs
ทั้ง "uri" และ "gcsUri" fields ไม่รองรับ external URLs
```

**2. FPS Requirement (24fps ONLY):**
```
Veo API สำหรับ Video Extension ต้องการ:
- Frame rate: 24fps (exactly)
- Videos ที่ไม่ใช่ 24fps จะ error: "Video fps mismatch. Expected: 24 got: XX"

ทำไม Veo-generated videos ใช้งานได้?
→ Veo สร้าง videos ที่ 24fps โดยอัตโนมัติ
```

**3. storage-uri Requirement:**
```
| Mode | --storage-uri Required? | Reason |
|------|-------------------------|--------|
| Text-to-Video | ❌ ไม่จำเป็น | ใช้ base64 response ได้ |
| Image-to-Video | ❌ ไม่จำเป็น | ใช้ base64 response ได้ |
| Video Extension | ✅ จำเป็น | Output ใหญ่เกินไป |
```

#### 5.18.4 Proposed Solution: Auto-Convert FPS Workflow

สำหรับ External Videos ที่ต้องการ extend:

```
Download URL → FFmpeg Convert 24fps → Upload GCS → Use gcsUri

Step 1: Download video จาก URL
        curl -L -o /tmp/source.mp4 "https://example.com/video.mp4"

Step 2: ตรวจสอบ FPS ปัจจุบัน
        ffprobe -v quiet -show_entries stream=r_frame_rate -of csv=p=0 /tmp/source.mp4
        # Output: 25/1 (25fps)

Step 3: แปลงเป็น 24fps
        ffmpeg -i /tmp/source.mp4 -r 24 -c:v libx264 -crf 23 /tmp/source_24fps.mp4

Step 4: Upload to GCS
        gsutil cp /tmp/source_24fps.mp4 gs://bucket-name/

Step 5: ใช้งาน gcsUri
        --extend-video gs://bucket-name/source_24fps.mp4
```

#### 5.18.5 Implementation (v2.25) - Local FFmpeg Approach

**Decision:** Use Local FFmpeg (ฟรี, unlimited)
- ✅ No cloud service costs (vs Cloudinary $99/mo after 25 credits)
- ✅ Unlimited conversions
- ✅ Full control and privacy
- ✅ FFmpeg already installed on system (v4.2.7)

**Feature: `--extend-video-from-url URL` with Auto-Convert**

```bash
# Proposed CLI:
python video_gen.py "Continue the scene" \
    --extend-video-from-url "https://example.com/video.mp4" \
    --storage-uri gs://bucket-name/ \
    --api-key "AQ..." --project-id "PROJECT_ID"

# Internal workflow:
# 1. Download URL to temp file
# 2. Check FPS with ffprobe
# 3. If not 24fps: convert with ffmpeg
# 4. Upload to GCS (--storage-uri)
# 5. Use gcsUri for extension
```

**Dependencies Required:**
- ✅ `ffmpeg` - Already installed (v4.2.7)
- ✅ `ffprobe` - Already installed
- ✅ GCS upload - Already implemented (Phase 2)

**New Functions (video_utils.py):**

```python
def check_video_fps(video_path: str) -> float:
    """Check video FPS using ffprobe"""

def convert_to_24fps(input_path: str, output_path: str) -> str:
    """Convert video to 24fps using ffmpeg"""

def download_video_url(url: str, output_dir: str) -> str:
    """Download video from external URL"""

def prepare_video_for_extension(
    video_input: str,
    storage_uri: Optional[str]
) -> str:
    """Complete workflow: download → check → convert → upload"""
```

**Implementation Checklist:**
- [ ] Create `video_utils.py` with helper functions
- [ ] Implement `check_video_fps()` using ffprobe
- [ ] Implement `convert_to_24fps()` using ffmpeg
- [ ] Implement `download_video_url()` for HTTP/HTTPS
- [ ] Implement `prepare_video_for_extension()` workflow
- [ ] Add `--extend-video-from-url` CLI parameter
- [ ] Integrate with video_gen.py main workflow
- [ ] Add progress indicators (download, convert, upload)
- [ ] Error handling for missing ffmpeg/ffprobe
- [ ] Test with various FPS videos (30fps, 60fps, etc.)
- [ ] Update TODO.md and changelog

#### 5.18.6 Summary Matrix: Video Extension Input Types

| Input Type | Supported? | Notes |
|------------|:----------:|-------|
| Veo URI (Gemini API) | ✅ | From previous generation, 2-day expiry |
| GCS URI (24fps) | ✅ | Veo-generated or converted videos |
| GCS URI (non-24fps) | ❌ | FPS mismatch error |
| External HTTPS URL | ❌ | "video is empty" error |
| External HTTP URL | ❌ | "video is empty" error |
| Local file (base64) | ❌ | Not supported by Gemini API |
| Local file → Auto-upload GCS | ✅ | With --storage-uri |
| URL → Download → Convert → Upload | ✅ | Proposed v2.25 feature |

---

## 6. 8 Video Generation Modes

### 6.1 Complete Mode List

| # | Mode | Input | Description |
|---|------|-------|-------------|
| 1 | **Text-to-Video** | Text prompt | Generate from text only |
| 2 | **Image-to-Video** | Image + Text | Animate single image |
| 3 | **First & Last Frames** | 2 Images + Text | Interpolate between keyframes |
| 4 | **Video Extension** | Video + Text | Continue existing video |
| 5 | **Reference Asset** | 1-3 Images + Text | Subject preservation |
| 6 | **Reference Style** | 1 Image + Text | Style transfer |
| 7 | **Insert Objects** | Video + Mask + Text | Add objects |
| 8 | **Remove Objects** | Video + Mask + Text | Remove objects |

### 6.2 CLI Parameters by Mode

| Parameter | Mode | Description |
|-----------|------|-------------|
| `--image PATH` | Image-to-Video, First Frame | Input image |
| `--last-frame PATH` | First & Last Frames | Last frame image |
| `--reference-asset PATH [...]` | Reference Asset | Up to 3 reference images |
| `--reference-style PATH` | Reference Style | Style reference image |
| `--video PATH` | Video Extension, Insert/Remove | Input video |
| `--mask PATH` | Insert/Remove Objects | Mask image |
| `--mask-mode MODE` | Insert/Remove | "insert" or "remove" |
| `--resize-mode MODE` | Image-to-Video | "pad" or "crop" (Veo 3+) |

### 6.3 Model Compatibility Matrix

| Mode | Veo 2 | Veo 3 | Veo 3.1 |
|------|-------|-------|---------|
| Text-to-Video | ✅ | ✅ | ✅ |
| Image-to-Video | ✅ | ✅ | ✅ |
| First & Last Frames | ❌ | ✅ | ✅ |
| Video Extension | ✅ | ✅ | ✅ |
| Reference Asset | ❌ | ✅ | ✅ |
| Reference Style | ❌ | ✅ | ✅ |
| Insert Objects | ❌ | ❌ | ✅ |
| Remove Objects | ❌ | ❌ | ✅ |
| **Native Audio** | ❌ | ❌ | ✅ |

### 6.4 Mode Detection Logic

```python
def detect_generation_mode(args) -> str:
    """Detect video generation mode from CLI arguments."""
    if args.mask and args.video:
        return "video_editing"  # Insert or Remove objects
    elif args.extend_video:
        return "video_extension"
    elif args.reference_asset:
        return "reference_asset"
    elif args.reference_style:
        return "reference_style"
    elif args.image and args.last_frame:
        return "first_last_frames"
    elif args.image:
        return "image_to_video"
    else:
        return "text_to_video"
```

---

## 7. What Works / Doesn't Work

### 7.1 ✅ What Works

| Feature | Via API Key | Via Service Account |
|---------|-------------|---------------------|
| Text-to-Video | ✅ | ✅ |
| Image-to-Video | ✅ | ✅ |
| All 8 Modes | ✅ | ✅ |
| Native Audio (Veo 3.1) | ✅ | ✅ |

### 7.2 ❌ What Doesn't Work

| Limitation | Reason |
|------------|--------|
| **CLIProxyAPI (Built-in OAuth)** | `predictLongRunning` returns 404 |
| **Free tier** | Veo has NO free tier - billing required |
| **Real-time streaming** | Must use async polling |
| **Videos > 8 seconds (Veo 2)** | Model limitation |

---

## 8. CLI Usage Examples

### 7.1 Text-to-Video (Simplest)

```bash
# Using API Key
python video_gen.py "A sunset over mountains" \
  --auth api-key \
  --api-key YOUR_KEY
```

### 7.2 Image-to-Video

```bash
python video_gen.py "Make this image come alive with gentle wind" \
  --auth api-key \
  --api-key YOUR_KEY \
  --image input.jpg
```

### 7.3 First & Last Frames

```bash
python video_gen.py "Smooth transition between scenes" \
  --auth api-key \
  --api-key YOUR_KEY \
  --image first.jpg \
  --last-frame last.jpg
```

> **✅ API TESTED** (2026-01-23): video_20260123_010422_0.mp4 generated successfully
>
> **📋 Constraints:**
> - Only 1 first frame (`--image`) allowed
> - Only 1 last frame (`--last-frame`) allowed
> - Cannot use multiple images for either

### 7.4 Video Extension

```bash
python video_gen.py "Continue with dramatic reveal" \
  --auth api-key \
  --api-key YOUR_KEY \
  --video original.mp4
```

### 7.5 Reference Asset (Subject Preservation)

```bash
python video_gen.py "The character runs through a forest" \
  --auth api-key \
  --api-key YOUR_KEY \
  --reference-asset character.jpg background.jpg
```

### 7.6 Reference Style (Style Transfer)

```bash
python video_gen.py "A cityscape at sunset" \
  --auth api-key \
  --api-key YOUR_KEY \
  --reference-style impressionist_painting.jpg
```

### 7.7 Insert Objects

```bash
python video_gen.py "A flying bird appears in the sky" \
  --auth api-key \
  --api-key YOUR_KEY \
  --video outdoor.mp4 \
  --mask sky_area.png \
  --mask-mode insert
```

### 7.8 Remove Objects

```bash
python video_gen.py "Clean background without the car" \
  --auth api-key \
  --api-key YOUR_KEY \
  --video street.mp4 \
  --mask car_area.png \
  --mask-mode remove
```

### 7.9 Service Account (Enterprise)

```bash
python video_gen.py "Professional product showcase" \
  --auth service-account \
  --credentials /path/to/sa.json \
  --project-id YOUR_PROJECT
```

### 7.10 CLI Quick Reference (v2.17+)

> **⚠️ v2.17 Changes:** Default model changed to `veo-3.1-generate-preview` (audio support). Added `--preset` for quick configuration. Mode-Aware Smart Defaults auto-correct parameters.

**Preset Quick Start:**
```bash
# Quick preset - fast generation for testing
python video_gen.py "A cat walking" --preset quick

# Quality preset - high quality for production
python video_gen.py "A cat walking" --preset quality

# Extend preset - optimized for video extension
python video_gen.py "Continue the scene" --extend-video video.mp4 --preset extend

# Budget preset - cost-effective ($0.15/sec)
python video_gen.py "A cat walking" --preset budget
```

**Available Presets:**

| Preset | Model | Duration | Resolution | Cost/sec | Use Case |
|--------|-------|----------|------------|----------|----------|
| `quick` | veo-3.1-fast | 5 | 720p | $0.15 | Drafts, testing |
| `quality` | veo-3.1 | 8 | 1080p | $0.35 | Production |
| `extend` | veo-3.1 | 7 | 720p | $0.35 | Video extension |
| `budget` | veo-3.0-fast | 5 | 720p | $0.15 | Cost-effective |

**All Available Parameters:**

| Category | Parameter | Values | Default | Description |
|----------|-----------|--------|---------|-------------|
| **Auth** | `--auth` | `api-key`, `service-account` | `api-key` | Authentication method |
| | `--api-key` | string | - | Google API Key |
| | `--credentials` | path | - | Service Account JSON file |
| | `--project-id` | string | - | GCP Project ID |
| | `--location` | string | `us-central1` | GCP location |
| | `--endpoint` | `gemini`, `vertex` | `gemini` | API endpoint |
| **Generation** | `--preset` | `quick`, `quality`, `extend`, `budget` | - | **NEW** Quick preset |
| | `--model` | veo-2.0-*, veo-3.0-*, veo-3.1-* | `veo-3.1-generate-preview` | Veo model |
| | `--duration` | 4, 5, 6, 7, 8 | 5 | Video duration (seconds) |
| | `--aspect-ratio` | `16:9`, `9:16`, `1:1` | `16:9` | Aspect ratio |
| | `--resolution` | `720p`, `1080p` | `720p` | Video resolution |
| | `--negative-prompt` | string | - | What to avoid |
| | `--person-generation` | `allow_adult`, `dont_allow` | - | Person generation |
| **Input** | `--image` | path | - | First frame image |
| | `--last-frame` | path | - | Last frame image |
| | `--extend-video` | path | - | Video to extend |
| | `--reference-asset` | path [...] | - | Up to 3 reference images |
| | `--reference-style` | path | - | Style reference image |
| | `--video` | path | - | Input video (for editing) |
| | `--mask` | path | - | Mask image |
| | `--mask-mode` | `insert`, `remove` | `insert` | Mask operation |
| | `--resize-mode` | `pad`, `crop` | - | Resize mode (Veo 3+) |
| **Output** | `-o, --output` | path | auto | Output video path |
| | `--output-json` | path | - | Save metadata JSON |
| **Debug** | `-v, --verbose` | flag | - | Verbose output |
| | `--dry-run` | flag | - | Build but don't send |

### 7.11 Mode-Aware Smart Defaults (v2.17+)

> **Design Principle:** ลด error จากการตั้งค่าผิด โดย auto-correct ตาม mode

**Smart Validation Rules:**

| Mode | Parameter | Behavior |
|------|-----------|----------|
| **video_extension** | `--duration` | ⚠️ Auto-correct to **7** (only valid value) |
| **video_extension** | `gs://` + Gemini API | ❌ Error with solution hint |
| **reference_asset** | `--duration` | ⚠️ Auto-correct to **8** (fixed) |
| **reference_style** | `--duration` | ⚠️ Auto-correct to **8** (fixed) |
| **insert_objects** | `--model` | ⚠️ Auto-correct to **veo-3.1-generate-preview** |
| **remove_objects** | `--model` | ⚠️ Auto-correct to **veo-3.1-generate-preview** |

**Example - Auto-correction in action:**
```bash
# User tries 8 seconds for video extension
python video_gen.py "Continue" --extend-video video.mp4 --duration 8
# ⚠️ Warning: Video Extension only supports 7 seconds. Changed from 8 to 7.
# ✓ Continues with duration=7 automatically
```

### 7.12 Model-Mode Compatibility Validation (v2.19+)

> **Design Principle:** ตรวจสอบ model รองรับ mode หรือไม่ ถ้าไม่รองรับจะ auto-switch ไปใช้ model ที่เหมาะสม

**MODE_FALLBACK_MODELS:**

| Mode | Fallback Model | Reason |
|------|----------------|--------|
| **video_extension** | `veo-3.1-generate-preview` | veo-3.0 ไม่รองรับ |
| **first_last_frames** | `veo-3.1-generate-preview` | veo-3.0 ไม่รองรับ |
| **reference_asset** | `veo-3.1-generate-preview` | เฉพาะ veo-2.0-exp, veo-3.1-preview |
| **reference_style** | `veo-2.0-generate-exp` | เฉพาะ veo-2.0-exp |
| **insert_objects** | `veo-3.1-generate-preview` | Veo 3.1 supports Add Object with mask |
| **remove_objects** | `veo-3.1-generate-preview` | Veo 3.1 supports Remove Object with mask |

**Example - Model auto-switch:**
```bash
# User uses --preset budget (veo-3.0-fast) with video extension
python video_gen.py "Continue" --extend-video video.mp4 --preset budget
# ⚠️ Warning: veo-3.0-fast-generate-preview does not support video_extension. Changed to veo-3.1-generate-preview.
# ✓ Continues with compatible model automatically

# User explicitly sets incompatible model
python video_gen.py "Smooth transition" --image start.jpg --last-frame end.jpg --model veo-3.0-generate-001
# ⚠️ Warning: veo-3.0-generate-001 does not support first_last_frames. Changed to veo-3.1-generate-preview.
# ✓ Continues with compatible model automatically
```

**Priority Order:**
```
1. Smart Validation (duration, gs:// validation)  ← สูงสุด
2. Model-Mode Compatibility Validation             ← ใหม่
3. Preset Settings
4. MODE_DEFAULTS
5. Global Defaults                                 ← ต่ำสุด
```

---

## 9. API Request/Response Format

### 8.1 Request Format by Mode

**1. Text-to-Video (Basic)**
```json
{
  "instances": [{ "prompt": "TEXT_PROMPT" }],
  "parameters": { "storageUri": "gs://bucket/output/", "sampleCount": 1 }
}
```

**2. Image-to-Video**
```json
{
  "instances": [{
    "prompt": "TEXT_PROMPT",
    "image": { "gcsUri": "gs://bucket/image.jpg", "mimeType": "image/jpeg" }
  }],
  "parameters": { "storageUri": "gs://bucket/output/", "resizeMode": "pad" }
}
```

**3. First & Last Frames**
```json
{
  "instances": [{
    "prompt": "TEXT_PROMPT",
    "image": { "gcsUri": "gs://bucket/first.jpg", "mimeType": "image/jpeg" },
    "lastFrame": { "gcsUri": "gs://bucket/last.jpg", "mimeType": "image/jpeg" }
  }],
  "parameters": { "storageUri": "gs://bucket/output/" }
}
```

**4. Video Extension**
```json
{
  "instances": [{
    "prompt": "TEXT_PROMPT",
    "video": { "gcsUri": "gs://bucket/input.mp4", "mimeType": "video/mp4" }
  }],
  "parameters": { "storageUri": "gs://bucket/output/" }
}
```

**5. Reference Asset Images (up to 3)**
```json
{
  "instances": [{
    "prompt": "TEXT_PROMPT",
    "referenceImages": [
      { "image": { "bytesBase64Encoded": "BASE64_DATA", "mimeType": "image/jpeg" }, "referenceType": "asset" },
      { "image": { "bytesBase64Encoded": "BASE64_DATA2", "mimeType": "image/jpeg" }, "referenceType": "asset" }
    ]
  }],
  "parameters": { "storageUri": "gs://bucket/output/", "durationSeconds": 8 }
}
```

**6. Reference Style Image (1 only)**
```json
{
  "instances": [{
    "prompt": "TEXT_PROMPT",
    "referenceImages": [
      { "image": { "bytesBase64Encoded": "BASE64_DATA", "mimeType": "image/jpeg" }, "referenceType": "style" }
    ]
  }],
  "parameters": { "storageUri": "gs://bucket/output/" }
}
```

**7. Insert Objects**
```json
{
  "instances": [{
    "prompt": "Add a flying bird in the masked area",
    "video": { "gcsUri": "gs://bucket/video.mp4", "mimeType": "video/mp4" },
    "mask": { "gcsUri": "gs://bucket/mask.png", "mimeType": "image/png" }
  }],
  "parameters": { "storageUri": "gs://bucket/output/", "editMode": "insert" }
}
```

**8. Remove Objects**
```json
{
  "instances": [{
    "prompt": "Remove the car from the scene",
    "video": { "gcsUri": "gs://bucket/video.mp4", "mimeType": "video/mp4" },
    "mask": { "gcsUri": "gs://bucket/mask.png", "mimeType": "image/png" }
  }],
  "parameters": { "storageUri": "gs://bucket/output/", "editMode": "remove" }
}
```

### 8.2 Response Format (Long-Running Operation)

```json
{
  "name": "operations/abc123...",
  "metadata": {
    "@type": "type.googleapis.com/google.cloud.aiplatform.v1.GenerateVideoOperationMetadata"
  }
}
```

### 8.3 Complete Result Response Formats

> **v2.16 Critical Discovery:** Response format varies by Auth Method and Model!

#### Format 1: Gemini API (generatedSamples with URI)

```json
{
  "name": "operations/abc123...",
  "done": true,
  "response": {
    "generatedSamples": [{
      "video": {
        "uri": "https://generativelanguage.googleapis.com/v1beta/files/xxx:download"
      }
    }]
  }
}
```

#### Format 2: Vertex AI with OAuth (predictions with URI)

```json
{
  "name": "operations/abc123...",
  "done": true,
  "response": {
    "predictions": [{
      "video": {
        "uri": "gs://bucket-name/video.mp4"
      }
    }]
  }
}
```

#### Format 3: Vertex AI with API Key - Veo 3.x (videos with Base64) ⭐ NEW!

```json
{
  "name": "operations/abc123...",
  "done": true,
  "response": {
    "@type": "type.googleapis.com/cloud.ai.large_models.vision.GenerateVideoResponse",
    "raiMediaFilteredCount": 0,
    "videos": [{
      "bytesBase64Encoded": "AAAAIGZ0eXBpc29t..."
    }]
  }
}
```

### 8.4 Response Format by Auth Method

| Auth Method | Model | Response Field | Data Type |
|-------------|-------|----------------|-----------|
| Gemini API Key (`AIzaSy...`) | Veo 2.x | `generatedSamples[].video.uri` | URL |
| Vertex AI OAuth (`ya29...`) | Veo 2.x/3.x | `predictions[].video.uri` | GCS URI |
| **Vertex AI Key (`AQ...`)** | **Veo 3.x** | **`videos[].bytesBase64Encoded`** | **Base64** |

> **⚠️ Important:** Vertex AI API Key (`AQ...`) returns **Base64 video data directly** - not a GCS URI!
> This means no additional download step is needed, but the response is larger.

### 8.5 API Request Parameters Reference

> **📋 Discovered from official docs and testing (2026-01-23)**

| Parameter | Type | Range/Values | Description |
|-----------|------|--------------|-------------|
| `sampleCount` | int | 1-4 | Number of video files to generate (RESPONSE_COUNT) |
| `durationSeconds` | int | 4-8 | Video duration in seconds |
| `aspectRatio` | string | `16:9`, `9:16`, `1:1` | Video aspect ratio |
| `storageUri` | string | `gs://...` | GCS output path (optional) |
| `negativePrompt` | string | text | What to avoid in generation |
| `resizeMode` | string | `pad`, `fit` | How to resize input images |
| `editMode` | string | `insert`, `remove` | For mask-based editing |

> **Note:** `sampleCount` จะสร้าง video หลายไฟล์จาก prompt เดียวกัน (1-4 videos)
> **Note:** Audio is automatic for Veo 3.x models - controlled via prompt, not API parameter.

---

## 10. Input/Output Specifications

### 9.1 Input Constraints

| Input Type | Format | Requirements |
|------------|--------|--------------|
| **Image** | JPEG, PNG, WebP | 720p+ recommended, 16:9 or 9:16 aspect |
| **First/Last Frame** | JPEG, PNG, WebP | Same requirements as Image |
| **Video (Extension)** | MP4 | 1-30 seconds, 24fps, 720p/1080p |
| **Video (Editing)** | MP4, MOV, AVI, etc. | Various formats supported |
| **Mask** | PNG, JPEG, WebP | Same dimensions as video frame |
| **Reference Image** | JPEG, PNG | Up to 3 for asset, 1 for style |

### 9.2 Output Specifications

| Mode | Output Format | Duration | Resolution | Frame Rate |
|------|---------------|----------|------------|------------|
| Text-to-Video | MP4 | 4-8 sec | 720p/1080p/4K | 24fps |
| Image-to-Video | MP4 | 4-8 sec | 720p/1080p/4K | 24fps |
| First & Last Frames | MP4 | 4-8 sec | 720p/1080p | 24fps |
| Video Extension | MP4 | 7 sec (extended) | 720p | 24fps |
| Reference Images | MP4 | 8 sec (fixed) | 720p/1080p | 24fps |
| Insert/Remove Objects | MP4 | Same as input | Same as input | 24fps |

---

## 11. Polling Implementation

### 10.1 Polling Pattern

```python
def poll_for_result(operation_name, api_key, timeout=300):
    start_time = time.time()
    attempts = 0

    while time.time() - start_time < timeout:
        response = requests.get(
            f"{BASE_URL}/{operation_name}",
            params={"key": api_key}
        )

        data = response.json()

        if data.get("done"):
            return data.get("response")

        # Exponential backoff
        attempts += 1
        time.sleep(min(30, 5 * attempts))

    raise TimeoutError("Video generation timed out")
```

### 10.2 Polling Endpoint Differences

| API | Method | Endpoint |
|-----|--------|----------|
| Gemini API | GET | `/v1beta/{operation_name}?key=KEY` |
| Vertex AI | POST | `/v1/{operation_name}:fetchPredictOperation` |

**Gemini API Polling:**
```
GET https://generativelanguage.googleapis.com/v1beta/{operation_name}
Headers:
  x-goog-api-key: YOUR_API_KEY
```

**Vertex AI Polling:**
```
POST https://{LOCATION}-aiplatform.googleapis.com/v1/projects/{PROJECT_ID}/locations/{LOCATION}/publishers/google/models/{MODEL}:fetchPredictOperation
Headers:
  Authorization: Bearer ACCESS_TOKEN
Body:
  {"operationName": "projects/.../operations/..."}
```

---

## 12. Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| `404: predictLongRunning not found` | Using CLIProxyAPI endpoint | Use API Key or Service Account |
| `429: Resource exhausted` | No billing enabled | Enable GCP billing |
| `403: Billing required` | Veo needs billing | Enable billing on project |
| `400: Invalid video format` | Wrong input format | Check video codec/format |
| `500: Internal error` | Server issue | Retry with exponential backoff |

---

## 13. Key Insights

### Q: Why does Video generation require API Key and Billing?

**A:** Two factors:
1. **Method**: Video uses `predictLongRunning` which cloudcode-pa does NOT support (404)
2. **No Free Tier**: Veo (all versions) has NO free tier - billing required

### Q: What's the simplest way to generate videos?

**A:** Use API Key with billing enabled:
```bash
python video_gen.py "your prompt" --auth api-key --api-key YOUR_KEY
```
Requires: GCP project with billing enabled.

### Q: Can I use the same OAuth as Image generation?

**A:** No. CLIProxyAPI's OAuth goes to cloudcode-pa endpoint which returns 404 for `predictLongRunning`. You must use API Key or Service Account.

---

## 14. Video Metadata Design

> **Consistency with Image Generation:** Video metadata follows the same design pattern as Image generation for unified experience.

> **v2.16 Unified Format:** ทั้ง Success และ Error ใช้ schema เดียวกัน แยกแยะด้วย `status` field

### 14.1 Unified Metadata Schema (v2.25)

**Key Principle:** ไฟล์ metadata ใช้ชื่อ `metadata_*.json` เสมอ ไม่ว่าจะ success หรือ error

> **v2.25 Update:** เพิ่ม `project_id`, `storage_uri`, `location` ใน `command_args` เพื่อรองรับ Smart Defaults System
> **Reference:** [design.md Section 18](./design.md#18-smart-defaults-system-metadata-driven)

```json
{
  "version": "2.25",
  "type": "video",
  "status": "SUCCESS",
  "timestamp": "2026-01-18T10:30:00.000Z",
  "command_args": {
    "prompt": "A sunset over mountains",
    "model": "veo-3.1-generate-preview",
    "duration": 5,
    "aspect_ratio": "16:9",
    "resolution": "720p",
    "auth_method": "api-key",
    "mode": "text_to_video",
    "project_id": "gen-lang-client-0344941103",
    "storage_uri": "gs://gen-lang-client-0344941103-media-output/videos/",
    "location": "us-central1"
  },
  "generation": {
    "mode": "text_to_video",
    "model": "veo-3.1-generate-preview",
    "duration_seconds": 5,
    "aspect_ratio": "16:9",
    "resolution": "720p",
    "has_audio": true,
    "frame_rate": 24
  },
  "input_files": {
    "image": null,
    "last_frame": null,
    "reference_assets": [],
    "reference_style": null,
    "video": null,
    "extend_video": null,
    "mask": null
  },
  "output": {
    "filename": "video_20260118_103000_0.mp4",
    "path": "/path/to/generated_videos/video_20260118_103000_0.mp4",
    "file_size_bytes": 1234567,
    "mime_type": "video/mp4"
  },
  "api_response": {
    "operation_name": "operations/abc123...",
    "video_uri": "https://...",
    "processing_time_seconds": 45.2
  },
  "cost_estimate": {
    "duration_seconds": 5,
    "price_per_second": 0.15,
    "estimated_cost_usd": 0.75,
    "model_tier": "veo-3.1"
  },
  "prompt": "A sunset over mountains",
  "download_status": "completed",
  "google_api_response": null
}
```

**New Fields in command_args (v2.25):**

| Field | Type | Description | Used By Smart Defaults |
|-------|------|-------------|:----------------------:|
| `project_id` | string | GCP project ID used for this generation | ✅ |
| `storage_uri` | string | GCS storage URI (if used) | ✅ |
| `location` | string | GCP region (e.g., us-central1) | ✅ |

### 14.2 Error Metadata Schema (v2.16)

**Same file naming:** ใช้ `metadata_*.json` เหมือนกัน ไม่แยก `error_*.json`

```json
{
  "version": "2.16",
  "type": "video",
  "status": "ERROR",
  "timestamp": "2026-01-19T07:23:01.521Z",
  "error": {
    "type": "NO_VIDEO_IN_RESPONSE",
    "message": "No video in response",
    "details": {
      "result_keys": ["name", "done", "response"],
      "response_keys": ["@type", "raiMediaFilteredCount", "videos"],
      "formats_checked": ["Format 1", "Format 2", "Format 3"]
    }
  },
  "command_args": {
    "prompt": "...",
    "model": "veo-3.1-generate-preview",
    "duration": 8
  },
  "generation": {
    "mode": "text_to_video",
    "model": "veo-3.1-generate-preview"
  },
  "input_files": { "image": null },
  "output": { "filename": null, "path": null },
  "api_response": {
    "operation_name": "operations/...",
    "processing_time_seconds": 76.12
  },
  "google_api_response": {
    "@type": "type.googleapis.com/...",
    "raiMediaFilteredCount": 0,
    "videos": [{
      "bytesBase64Encoded": "[BASE64_DATA: 123456 chars, ~92592 bytes]"
    }]
  }
}
```

### 14.3 Status Field Values

| Status | Description | Has `error` field? | Has `output` file? |
|--------|-------------|:------------------:|:------------------:|
| `SUCCESS` | Video generated successfully | ❌ No | ✅ Yes |
| `ERROR` | Generation failed | ✅ Yes | ❌ No |

### 14.4 google_api_response Field

> **Purpose:** Debug API issues โดยเก็บ response จาก Google ไว้ใน metadata

**For Success:**
- Usually `null` (ไม่จำเป็นต้องเก็บ)
- หรือเก็บ summary เฉพาะ key info

**For Error:**
- **Critical field** สำหรับ debugging
- Base64 data ถูก strip ออก แทนด้วย `[BASE64_DATA: X chars]`
- เก็บ structure ทั้งหมดเพื่อวิเคราะห์ format

### 14.5 Metadata Fields Comparison

```
Image Metadata              Video Metadata
├── timestamp          →    ├── timestamp
├── command_args       →    ├── command_args
├── prompt             →    ├── prompt
├── images[]           →    ├── output (single video)
│   ├── filename       →    │   ├── filename
│   ├── path           →    │   ├── path
│   ├── mime_type      →    │   ├── mime_type
│   └── file_size      →    │   └── file_size_bytes
│                      →    ├── generation (Video-specific)
│                      →    │   ├── mode
│                      →    │   ├── duration_seconds
│                      →    │   ├── has_audio
│                      →    │   └── frame_rate
│                      →    ├── input_files (Video-specific)
│                      →    │   ├── image
│                      →    │   ├── reference_assets[]
│                      →    │   └── video
│                      →    ├── api_response
│                      →    │   ├── operation_name
│                      →    │   └── processing_time_seconds
│                      →    └── cost_estimate
│                      →        ├── price_per_second
│                      →        └── estimated_cost_usd
```

### 14.6 Auto-Save Location

```
generated_videos/
├── video_20260118_103000_0.mp4
├── metadata_20260118_103000.json    ← Auto-saved metadata
├── video_20260118_103500_0.mp4
└── metadata_20260118_103500.json
```

### 14.7 Unified Naming Convention

> **Consistency with Image Generation:** Both image and video use the same naming pattern.

| Component | Image Gen | Video Gen |
|-----------|-----------|-----------|
| **Media File** | `image_{timestamp}_{index}.png` | `video_{timestamp}_{index}.mp4` |
| **Metadata** | `metadata_{timestamp}.json` | `metadata_{timestamp}.json` |
| **Custom Output** | N/A | Uses `-o` but metadata stays `metadata_{ts}.json` |

**Example:**
```
# Default naming (no -o flag)
video_20260118_103000_0.mp4
metadata_20260118_103000.json

# Custom naming (-o custom_video.mp4)
custom_video.mp4
metadata_20260118_103000.json  ← timestamp metadata still created
```

### 14.8 CLI Options for Metadata

| Option | Description | Default |
|--------|-------------|---------|
| `--output-json PATH` | Save metadata to specific path | None |
| `--no-metadata` | Disable auto-save metadata | Auto-save ON |
| `--include-api-response` | Include full API response | Summary only |

### 14.9 Cost Estimation Table

| Model | Price/second | 5 sec Video | 8 sec Video |
|-------|--------------|-------------|-------------|
| Veo 2.0 | $0.35 | $1.75 | $2.80 |
| Veo 3.0 | $0.15-0.40 | $0.75-2.00 | $1.20-3.20 |
| Veo 3.0 Fast | $0.15 | $0.75 | $1.20 |
| Veo 3.1 | $0.15-0.60 | $0.75-3.00 | $1.20-4.80 |
| Veo 3.1 Fast | $0.15 | $0.75 | $1.20 |

### 14.10 Implementation Status

| Feature | Image Gen | Video Gen | Status |
|---------|:---------:|:---------:|:------:|
| Auto-save metadata | ✅ | ✅ | **Done** |
| command_args tracking | ✅ | ✅ | **Done** |
| File size tracking | ✅ | ✅ | **Done** |
| Prompt in metadata | ✅ | ✅ | **Done** |
| Input files tracking | N/A | ✅ | **Done** |
| Cost estimation | N/A | ✅ | **Done** |
| Processing time | N/A | ✅ | **Done** |
| **Unified status field** | N/A | ✅ | **Done (v2.16)** |
| **Error metadata** | N/A | ✅ | **Done (v2.16)** |
| **google_api_response** | N/A | ✅ | **Done (v2.16)** |

> **Implemented in video_gen.py v2.16** - Unified SUCCESS/ERROR metadata format with detailed API response logging

---

## 15. Cost Tracking & Display

### 15.1 Design Philosophy

> **Claude Code as Director:** หนึ่งในจุดแข็งของ claude-code-media-generator คือการให้ Claude Code ควบคุมและติดตาม cost ได้อย่างโปร่งใส ช่วยให้ user ตัดสินใจได้ดีขึ้นก่อน/ระหว่าง/หลังการ generate

### 15.2 Cost Display During Generation

```
CLI Output Example:

🎬 Generating video...
├─ Model: veo-3.1-generate-preview
├─ Duration: 8 seconds
├─ Estimated Cost: $2.80 - $4.80 (depending on model tier)
│
├─ ⏳ Processing... (67.68 seconds)
│
├─ ✅ Video generated successfully!
├─ Output: generated_videos/video_20260118_132218_0.mp4
├─ Size: 5.0 MB
└─ 💰 Actual Cost: $2.80
```

### 15.3 Cost Calculation Formula

```python
def calculate_cost(duration_seconds: int, model: str) -> dict:
    """Calculate generation cost based on model and duration."""

    PRICING = {
        "veo-2.0-generate-001": {"min": 0.35, "max": 0.35},
        "veo-3.0-generate-001": {"min": 0.15, "max": 0.40},
        "veo-3.0-fast-generate-001": {"min": 0.15, "max": 0.15},
        "veo-3.1-generate-preview": {"min": 0.15, "max": 0.60},
        "veo-3.1-fast-generate-preview": {"min": 0.15, "max": 0.15},
    }

    price = PRICING.get(model, {"min": 0.35, "max": 0.35})

    return {
        "duration_seconds": duration_seconds,
        "price_per_second": price["max"],  # Use max for estimate
        "estimated_cost_min": round(price["min"] * duration_seconds, 2),
        "estimated_cost_max": round(price["max"] * duration_seconds, 2),
        "estimated_cost_usd": round(price["max"] * duration_seconds, 2),
        "model_tier": model.split("-")[0] + "-" + model.split("-")[1]
    }
```

### 15.4 Detailed Pricing Table

| Model | Min $/sec | Max $/sec | 5 sec | 8 sec | 10 sec |
|-------|-----------|-----------|-------|-------|--------|
| **veo-2.0-generate-001** | $0.35 | $0.35 | $1.75 | $2.80 | $3.50 |
| **veo-3.0-generate-001** | $0.15 | $0.40 | $0.75-$2.00 | $1.20-$3.20 | $1.50-$4.00 |
| **veo-3.0-fast-generate-001** | $0.15 | $0.15 | $0.75 | $1.20 | $1.50 |
| **veo-3.1-generate-preview** | $0.15 | $0.60 | $0.75-$3.00 | $1.20-$4.80 | $1.50-$6.00 |
| **veo-3.1-fast-generate-preview** | $0.15 | $0.15 | $0.75 | $1.20 | $1.50 |

### 15.5 Cost Estimate CLI Option (Proposed)

```bash
# Preview cost without generating
python video_gen.py --prompt "A butterfly" --duration 8 --estimate-cost

Output:
💰 Cost Estimate (without generation):
├─ Model: veo-2.0-generate-001
├─ Duration: 8 seconds
├─ Estimated Cost: $2.80
└─ Note: Actual cost may vary based on model tier
```

### 15.6 Session Cost Aggregation (Future)

```
# After multiple generations in a session
📊 Session Cost Summary:
├─ Videos Generated: 3
├─ Total Duration: 24 seconds
├─ Total Cost: $8.40
│
├─ Breakdown:
│   ├─ video_20260118_131726_0.mp4: $2.80 (veo-2.0, 8s)
│   ├─ video_20260118_132218_0.mp4: $2.80 (veo-3.1, 8s)
│   └─ video_20260118_140000_0.mp4: $2.80 (veo-2.0, 8s)
└─ Average Cost/Video: $2.80
```

### 15.7 Cost in Metadata Schema

```json
{
  "cost_estimate": {
    "duration_seconds": 8,
    "price_per_second": 0.35,
    "estimated_cost_usd": 2.80,
    "model_tier": "veo-2.0",
    "currency": "USD",
    "pricing_date": "2026-01-18"
  }
}
```

### 15.8 Implementation Status

| Feature | Status | Notes |
|---------|:------:|-------|
| Cost in metadata | ✅ **Done** | Saved in `cost_estimate` field |
| CLI cost display | 🔲 Planned | Show during generation |
| `--estimate-cost` flag | 🔲 Planned | Preview cost without generating |
| Session aggregation | 🔲 Future | Track costs across session |
| Cost warnings | 🔲 Future | Warn when cost exceeds threshold |

> **Note:** Cost tracking ช่วยให้ Claude Code ทำหน้าที่เป็น "Director" ที่ควบคุม budget และแนะนำ model ที่เหมาะสมตาม cost-performance tradeoff

---

## 16. Cross-References

| Document | Content |
|----------|---------|
| [design.md](./design.md) | Main design, system overview, authentication |
| [image.design.md](./image.design.md) | Image generation details |
| [README.md](./README.md) | Quick start guide |

### 16.1 External Reference Links

> **Official Google Documentation:** Links ที่ใช้ในการศึกษาและพัฒนา

| Topic | URL | Description |
|-------|-----|-------------|
| **Video Extension (Vertex AI)** | https://docs.cloud.google.com/vertex-ai/generative-ai/docs/video/extend-a-veo-video | วิธี extend video ผ่าน Vertex AI + GCS |
| **First & Last Frames** | https://docs.cloud.google.com/vertex-ai/generative-ai/docs/video/generate-videos-from-first-and-last-frames | วิธีใช้ first/last frames ใน Vertex AI |
| **Gemini API Video** | https://ai.google.dev/gemini-api/docs/video | Video generation ผ่าน Gemini API |
| **Stack Overflow: Veo 3.1** | https://stackoverflow.com/questions/79796261/how-to-correctly-structure-the-video-object-for-veo-3-1-endpoint | วิธี structure video object สำหรับ extension |

### 16.2 Key Findings from References

จาก documentation และ Stack Overflow:

1. **Video Extension (Gemini API)**
   - ต้องใช้ `"video": { "uri": "..." }` จาก previous Veo generation
   - ไม่รองรับ `bytesBase64Encoded` หรือ `gcsUri`
   - URI มีอายุ 2 วัน

2. **Video Extension (Vertex AI)**
   - ใช้ `"video": { "gcsUri": "gs://..." }` ได้
   - ต้อง upload video ไป GCS ก่อน
   - ไม่จำกัดอายุ (ขึ้นอยู่กับ GCS retention)

3. **First & Last Frames**
   - Gemini API: รองรับ inline base64 images
   - Vertex AI: รองรับ GCS URI

---


> 📜 **Changelog:** See [changelog/video.changelog.md](./changelog/video.changelog.md)

---

## Appendix A: Reserved - ADC (Application Default Credentials) Implementation

> ⚠️ **Status: Reserved for Future Implementation**
>
> ADC is currently disabled to simplify the authentication flow.
> This appendix documents how to implement ADC if needed in the future.

### A.1 What is ADC?

ADC (Application Default Credentials) คือวิธีการ authenticate โดยใช้ credentials ที่ gcloud CLI จัดการให้

```
ADC Flow:

1. User runs: gcloud auth application-default login
   ↓
2. Browser opens → User logs in with Google Account
   ↓
3. Token is saved to:
   ~/.config/gcloud/application_default_credentials.json
   ↓
4. Application reads token from this file automatically
```

### A.2 ADC vs Service Account Comparison

| Aspect | ADC | Service Account |
|--------|-----|-----------------|
| **Login Method** | Interactive (browser) | Non-interactive (JSON file) |
| **Token Location** | `~/.config/gcloud/` | Custom path (user specified) |
| **Key File Required** | ❌ No | ✅ Yes |
| **Suitable For** | Development | Production |
| **CI/CD Friendly** | ❌ No (needs browser) | ✅ Yes |
| **Security** | User credentials | Service credentials |

### A.3 Why ADC is Disabled (v1.0)

1. **Redundancy**: ADC และ Service Account ใช้ Vertex AI endpoint เหมือนกัน
2. **User Confusion**: มี option มากเกินไปทำให้สับสน
3. **Simplicity First**: v1.0 เน้นความง่าย - 2 methods พอ
4. **Coverage**: Service Account ครอบคลุม use cases ทั้งหมดอยู่แล้ว

### A.4 Future Implementation Guide

ถ้าต้องการเปิด ADC ในอนาคต ทำตามขั้นตอนนี้:

#### Step 1: Add CLI Argument

```python
# ใน argparse
parser.add_argument(
    '--auth',
    choices=['api-key', 'service-account', 'adc'],  # เพิ่ม 'adc'
    default='api-key',
    help='Authentication method'
)
```

#### Step 2: Update Endpoint Selection Logic

```python
def get_endpoint(auth_method: str, model: str, project_id: str, location: str = "us-central1") -> str:
    """Select endpoint based on auth method."""

    if auth_method == "api-key":
        return f"https://generativelanguage.googleapis.com/v1beta/models/{model}:predictLongRunning"

    elif auth_method in ["service-account", "adc"]:  # ADC ใช้ Vertex AI เหมือน Service Account
        return f"https://{location}-aiplatform.googleapis.com/v1/projects/{project_id}/locations/{location}/publishers/google/models/{model}:predictLongRunning"

    else:
        raise ValueError(f"Unsupported auth method: {auth_method}")
```

#### Step 3: Implement ADC Token Loading

```python
import google.auth
import google.auth.transport.requests

def get_adc_token() -> str:
    """
    Get access token from Application Default Credentials.

    Prerequisites:
    - User must run: gcloud auth application-default login
    - Token file exists at: ~/.config/gcloud/application_default_credentials.json
    """
    try:
        credentials, project = google.auth.default(
            scopes=['https://www.googleapis.com/auth/cloud-platform']
        )

        # Refresh token if needed
        request = google.auth.transport.requests.Request()
        credentials.refresh(request)

        return credentials.token

    except google.auth.exceptions.DefaultCredentialsError as e:
        raise RuntimeError(
            "ADC not configured. Run: gcloud auth application-default login"
        ) from e
```

#### Step 4: Update Request Headers

```python
def get_headers(auth_method: str, api_key: str = None, access_token: str = None) -> dict:
    """Get request headers based on auth method."""

    if auth_method == "api-key":
        return {
            "Content-Type": "application/json",
            "x-goog-api-key": api_key
        }

    elif auth_method in ["service-account", "adc"]:
        return {
            "Content-Type": "application/json",
            "Authorization": f"Bearer {access_token}"
        }
```

#### Step 5: Main Function Update

```python
def main():
    args = parse_args()

    if args.auth == "api-key":
        headers = get_headers("api-key", api_key=args.api_key)

    elif args.auth == "service-account":
        token = get_service_account_token(args.credentials)
        headers = get_headers("service-account", access_token=token)

    elif args.auth == "adc":
        token = get_adc_token()
        headers = get_headers("adc", access_token=token)
```

### A.5 Required Dependencies for ADC

```bash
pip install google-auth google-auth-httplib2
```

### A.6 User Prerequisites for ADC

```bash
# 1. Install gcloud CLI
# https://cloud.google.com/sdk/docs/install

# 2. Login with gcloud
gcloud auth login

# 3. Set up Application Default Credentials
gcloud auth application-default login

# 4. Verify token file exists
ls ~/.config/gcloud/application_default_credentials.json
```

### A.7 CLI Usage Example (When Enabled)

```bash
# Future usage (when ADC is enabled)
python video_gen.py "A sunset over mountains" \
  --auth adc \
  --project-id YOUR_PROJECT
```

---

> 📌 **Note:** This appendix is documentation only.
> The ADC code is NOT implemented in v1.0.
> Refer to Section 3.5 for currently supported methods.

---

> Full history: [video.changelog.md](../changelog/video.changelog.md)
