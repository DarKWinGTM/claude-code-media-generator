# Video Generation Design Document

## Version: 3.2
## Date: 2026-01-18

---

## 1. Authentication & Endpoint Architecture

### 1.1 Google API Endpoints Overview

| Endpoint | URL | Purpose | Supports Video (Veo)? |
|----------|-----|---------|----------------------|
| **Gemini API** | `generativelanguage.googleapis.com` | Public Gemini API | ✅ Yes |
| **Vertex AI** | `{region}-aiplatform.googleapis.com` | Enterprise GCP API | ✅ Yes |
| **Cloud Code Assist** | `cloudcode-pa.googleapis.com` | Internal IDE API | ❌ No |

### 1.2 OAuth Scope Requirements

| Scope | Works with Gemini API? | Works with Vertex AI? | Works with Cloud Code? |
|-------|------------------------|----------------------|------------------------|
| `generative-language.*` | ✅ Yes | ❌ No | ❌ No |
| `cloud-platform` | ❌ No | ✅ Yes | ✅ Yes |

### 1.3 Built-in OAuth Client Limitation

The built-in OAuth client (`681255809395-oo8ft2oprdrnp9e3aqf6av3hmdib135j.apps.googleusercontent.com`) only has:
- `cloud-platform` scope
- `userinfo.email` scope
- `userinfo.profile` scope

**It does NOT have `generative-language.*` scope registered**, so it cannot access `generativelanguage.googleapis.com` directly.

---

## 2. Authentication Methods (3 รูปแบบ)

### 2.1 API Key (Recommended - Simplest)

```
Authentication: x-goog-api-key header
Endpoint: generativelanguage.googleapis.com
Billing Required: No (free tier available)
Video Support: ✅ Yes
```

**Usage:**
```bash
python video_gen.py "prompt" --auth api-key --api-key YOUR_KEY
```

### 2.2 Built-in OAuth (--login)

```
Authentication: Bearer token with cloud-platform scope
Endpoint: aiplatform.googleapis.com (Vertex AI) - FORCED
Billing Required: Yes
Video Support: ✅ Yes
```

**Why Vertex AI is forced:**
- Built-in OAuth only has `cloud-platform` scope
- `generativelanguage.googleapis.com` requires `generative-language.*` scope
- `aiplatform.googleapis.com` accepts `cloud-platform` scope

**Usage:**
```bash
# First login
python video_gen.py --login

# Then generate
python video_gen.py "prompt" --auth oauth --project-id YOUR_PROJECT
```

### 2.3 Service Account

```
Authentication: Bearer token
Endpoint: aiplatform.googleapis.com (Vertex AI)
Billing Required: Yes
Video Support: ✅ Yes
```

**Usage:**
```bash
python video_gen.py "prompt" --auth service-account --credentials sa.json --project-id YOUR_PROJECT
```

---

## 3. CLIProxyAPI Architecture Discovery

### 3.1 How CLIProxyAPI Routes Requests

CLIProxyAPI has multiple executors for different endpoints:

```
gemini_executor.go         → generativelanguage.googleapis.com (needs API key or special OAuth)
gemini_vertex_executor.go  → aiplatform.googleapis.com (Vertex AI)
gemini_cli_executor.go     → cloudcode-pa.googleapis.com (Cloud Code Assist)
```

### 3.2 Cloud Code Assist Endpoint

**File:** `gemini_cli_executor.go`

```go
const (
    codeAssistEndpoint      = "https://cloudcode-pa.googleapis.com"
    codeAssistVersion       = "v1internal"
)

var geminiOAuthScopes = []string{
    "https://www.googleapis.com/auth/cloud-platform",
    "https://www.googleapis.com/auth/userinfo.email",
    "https://www.googleapis.com/auth/userinfo.profile",
}
```

**Supported Actions:**
- `generateContent` ✅
- `streamGenerateContent` ✅
- `countTokens` ✅
- `predictLongRunning` ❌ (404 Not Found)

### 3.3 Why Image Generation Works via CLIProxyAPI

When using CLIProxyAPI for image generation (e.g., `gemini-3-pro-image-preview`):

```
Request → CLIProxyAPI (localhost:8317)
       → gemini_cli_executor.go
       → cloudcode-pa.googleapis.com/v1internal:generateContent
       → Success (uses cloud-platform scope)
```

### 3.4 Why Video Generation Fails via CLIProxyAPI

The `cloudcode-pa.googleapis.com` endpoint does not support `predictLongRunning`:

```
Request → cloudcode-pa.googleapis.com/v1internal:predictLongRunning
       → 404 Not Found
```

---

## 4. Video Generation Flow

### 4.1 With API Key (Recommended for Free Tier)

```
video_gen.py --auth api-key
     ↓
x-goog-api-key header
     ↓
generativelanguage.googleapis.com/v1beta/models/{model}:predictLongRunning
     ↓
Long-running operation (poll for result)
     ↓
Video file saved
```

### 4.2 With Built-in OAuth (Requires Billing)

```
video_gen.py --auth oauth
     ↓
Built-in OAuth (cloud-platform scope)
     ↓
FORCED → Vertex AI endpoint
     ↓
{region}-aiplatform.googleapis.com/v1/projects/{project}/locations/{location}/publishers/google/models/{model}:predictLongRunning
     ↓
Long-running operation (poll for result)
     ↓
Video file saved
```

---

## 5. Project Requirements for Vertex AI

### 5.1 Enable Billing

```bash
# Visit:
https://console.developers.google.com/billing/enable?project=YOUR_PROJECT
```

### 5.2 Enable Vertex AI API

```bash
gcloud services enable aiplatform.googleapis.com --project=YOUR_PROJECT
```

### 5.3 Required IAM Roles

- `roles/aiplatform.user` - For predictions
- `roles/storage.objectViewer` - For accessing generated videos

---

## 6. Token Cache Structure

### 6.1 Location

```
~/.video_gen_tokens.json
```

### 6.2 Schema

```json
{
  "access_token": "ya29.xxx",
  "refresh_token": "1//xxx",
  "token_type": "Bearer",
  "expiry": "2026-01-17T14:03:50.124143+00:00",
  "client_id": "681255809395-xxx.apps.googleusercontent.com",
  "client_secret": "GOCSPX-xxx",
  "email": "user@gmail.com",
  "project_id": "project1,project2,project3"
}
```

---

## 7. Error Handling Matrix

| Error | Cause | Solution |
|-------|-------|----------|
| `403: ACCESS_TOKEN_SCOPE_INSUFFICIENT` | Using cloud-platform scope with Gemini API | Use Vertex AI endpoint or API key |
| `403: This API method requires billing` | Vertex AI needs billing enabled | Enable billing on GCP project |
| `403: Vertex AI API has not been used` | API not enabled | Enable Vertex AI API |
| `401: UNAUTHENTICATED` | Token expired | Re-login with `--login` |
| `404: Not Found` | Wrong endpoint for action | Use correct endpoint for the API |
| `400: model requires GCP billing enabled` | Veo 2.0 needs GCP billing | Enable billing or use Veo 3.x |
| `429: Resource exhausted / quota exceeded` | No API quota for Veo | Enable GCP billing (Veo has no free tier) |

---

## 8. Veo API Quota Analysis (CRITICAL)

### 8.1 Free Tier Availability

| Model | Free Tier (API) | Paid Tier |
|-------|-----------------|-----------|
| **Veo 3.1** | ❌ Not available | $0.15-$0.60/second |
| **Veo 3** | ❌ Not available | $0.15-$0.40/second |
| **Veo 2** | ❌ Not available | $0.35/second |
| Gemini 2.5 Flash | ✅ Free | Pay-as-you-go |
| Imagen 3 | ❌ Not available | $0.03/image |

**Key Finding:** Veo (all versions) has NO free tier for API access.

### 8.2 Google AI Pro Subscription vs API Access

```
Google AI Pro / Pro Family Subscription
├── App-based Access (UI) ✅ INCLUDED
│   ├── gemini.google.com
│   ├── aistudio.google.com
│   └── Veo quota: 3-5 videos/day
│
└── API Access (generativelanguage.googleapis.com) ❌ NOT INCLUDED
    ├── Separate billing system
    ├── Pay-as-you-go pricing
    └── Veo: No free tier
```

### 8.3 Why OAuth with `generative-language.peruserquota` Gets 429

Even with correct OAuth authentication and `generative-language.peruserquota` scope:
- Authentication: ✅ Succeeds
- API Quota for Veo: ❌ 0 (no free tier)
- Result: `429 Resource Exhausted`

### 8.4 Solutions for Video Generation

| Solution | Cost | Complexity | Video Quota |
|----------|------|------------|-------------|
| **Gemini App (UI)** | $19.99/mo (Pro) | Low | 3-5/day |
| **AI Studio (UI)** | Free (Pro) | Low | 3-5/day |
| **API with GCP Billing** | Pay-as-you-go | Medium | Unlimited |
| **API Key** | Pay-as-you-go | Low | Unlimited |

---

## 9. Recommended Usage Patterns

### 9.1 For Free Usage (API Key)

```bash
export GOOGLE_API_KEY="your-api-key"
python video_gen.py "A sunset over mountains" --auth api-key
```

### 9.2 For Enterprise/Billing Enabled (OAuth + Vertex)

```bash
# One-time login
python video_gen.py --login

# Generate videos
python video_gen.py "A sunset over mountains" --auth oauth --project-id YOUR_PROJECT
```

### 9.3 For Automation (Service Account)

```bash
python video_gen.py "A sunset over mountains" \
  --auth service-account \
  --credentials /path/to/service-account.json \
  --project-id YOUR_PROJECT
```

---

## 10. Architecture Diagram

```
VIDEO GENERATION ARCHITECTURE (3 Authentication Methods)

video_gen.py
  │
  ├── Authentication Methods (ทุก method ต้อง Billing สำหรับ Veo)
  │
  ├─ 1. API Key (Recommended)
  │    ↓
  │    Gemini API (generativelanguage.googleapis.com)
  │    ↓
  │    Veo Model (video gen)
  │
  ├─ 2. Built-in OAuth (Human User / Enterprise)
  │    ↓
  │    Vertex AI (aiplatform.googleapis.com)
  │    ↓
  │    Veo Model (video gen)
  │
  └─ 3. Service Account (Automation / CI-CD)
       ↓
       Vertex AI (aiplatform.googleapis.com)
       ↓
       Veo Model (video gen)

Note: Method 2 & 3 ไปที่ Vertex AI เหมือนกัน
      ต่างกันแค่วิธี authentication (Browser vs JSON Key)
```

---

## 11. Utility Tools

### 11.1 check_api.py - API Checker Tool

เครื่องมือตรวจสอบ API Key, models, และ quota

**Features:**
- List available models (Veo, Imagen, Gemini)
- Check API quota status
- Test specific model access
- Display useful links

**Usage:**

```bash
# Full check (default)
python check_api.py --api-key YOUR_KEY

# List models only
python check_api.py --api-key YOUR_KEY --list-models

# Filter by type (veo, imagen, gemini, embedding)
python check_api.py --api-key YOUR_KEY --filter veo

# Check quota only
python check_api.py --api-key YOUR_KEY --check-quota

# Test specific model
python check_api.py --api-key YOUR_KEY --test-model veo-3.1-generate-preview

# Show details
python check_api.py --api-key YOUR_KEY --details

# Output as JSON
python check_api.py --api-key YOUR_KEY --json
```

**Output Example:**

```
============================================================
                  Gemini API Checker v1.0
============================================================

## Available Models
Veo (Video) (5)
  ✓ veo-3.1-generate-preview
  ✓ veo-3.1-fast-generate-preview
  ...

## Testing API Quota
✓ Text generation: OK
      Response: API working
      Tokens used: 48

## Checking Veo Model Access
✓ veo-3.1-generate-preview: Available
ℹ Veo models are visible, but generation requires billing enabled

## Summary & Links
  • Check usage: https://ai.dev/rate-limit
  • API pricing: https://ai.google.dev/gemini-api/docs/pricing
```

---

## 12. Image vs Video Generation Architecture

### 12.1 API Method Difference (Critical)

| Feature | 🖼️ Image Generation | 🎬 Video Generation |
|---------|---------------------|---------------------|
| **API Method** | `generateContent` | `predictLongRunning` |
| **Response Type** | Synchronous (immediate) | Asynchronous (polling) |
| **Processing Time** | ~3-10 seconds | ~30-120 seconds |
| **Payload Format** | `{ contents: [...] }` | `{ instances: [...] }` |

**Why Different?**
- Image: Fast processing → Synchronous response possible
- Video: Long processing → Must use async long-running operation

### 12.2 Endpoint Capabilities Matrix

```
ENDPOINT SUPPORT FOR API METHODS

cloudcode-pa.googleapis.com (CLIProxyAPI/Built-in OAuth)
├─ generateContent:       ✅ Supported
├─ streamGenerateContent: ✅ Supported
├─ countTokens:           ✅ Supported
└─ predictLongRunning:    ❌ 404 NOT FOUND

generativelanguage.googleapis.com (Gemini API - API Key)
├─ generateContent:       ✅ Supported
├─ streamGenerateContent: ✅ Supported
├─ countTokens:           ✅ Supported
└─ predictLongRunning:    ✅ Supported

aiplatform.googleapis.com (Vertex AI - OAuth/Service Account)
├─ generateContent:       ✅ Supported
├─ streamGenerateContent: ✅ Supported
├─ predict:               ✅ Supported
└─ predictLongRunning:    ✅ Supported
```

### 12.3 Free Tier Availability

| Model Type | Model | Free Tier | Paid Price |
|------------|-------|-----------|------------|
| 🖼️ Image | Gemini 2.0 Flash (image output) | ✅ **FREE** | $0.039/image |
| 🖼️ Image | Gemini 2.5 Flash Image | ❌ No | $0.039/image |
| 🖼️ Image | Gemini 3 Pro Image | ❌ No | $0.134/image |
| 🖼️ Image | Imagen 3 | ❌ No | $0.03/image |
| 🖼️ Image | Imagen 4 | ❌ No | $0.02-0.06/image |
| 🎬 Video | Veo 2.0 | ❌ No | $0.35/second |
| 🎬 Video | Veo 3.0 | ❌ No | $0.15-0.40/second |
| 🎬 Video | Veo 3.1 | ❌ No | $0.15-0.60/second |

**Key Finding:** Only Gemini 2.0 Flash has FREE image generation. ALL video models require billing.

### 12.4 Why Image Works Without API Key

```
Image Generation via CLIProxyAPI (Built-in OAuth)

image_gen.py request
    │
    ▼
CLIProxyAPI (localhost:8317)
    │ Uses built-in OAuth with cloud-platform scope
    ▼
cloudcode-pa.googleapis.com/v1internal:generateContent
    │ Method supported ✅
    ▼
Gemini Model (gemini-2.0-flash / gemini-3-pro-image-preview)
    │ Has FREE tier ✅
    ▼
✅ IMAGE GENERATED

Success factors:
1. Method = generateContent (supported by cloudcode endpoint)
2. Model = Gemini 2.0 Flash (has FREE tier for image output)
3. OAuth scope = cloud-platform (accepted by cloudcode endpoint)
```

### 12.5 Why Video Fails via CLIProxyAPI

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

### 12.6 Complete Media Generation Flow

```
MEDIA GENERATION ARCHITECTURE

YOUR APPLICATION
  │
  ├── Authentication Layer
  │
  ├─ API Key (Simple)
  │    ↓
  │    generativelanguage.googleapis.com
  │    ├─ generateContent  ✅
  │    └─ predictLongRun   ✅
  │         ↓
  │         ├─ 🖼️ IMAGE: gemini-2.0-flash (FREE ✅)
  │         ├─ 🖼️ IMAGE: gemini-2.5-flash-img (PAID)
  │         ├─ 🖼️ IMAGE: imagen-3/4 (PAID)
  │         └─ 🎬 VIDEO: veo-2.0/3.0/3.1 (PAID)
  │
  ├─ Built-in OAuth (CLIProxyAPI)
  │    ↓
  │    cloudcode-pa.googleapis.com
  │    ├─ generateContent  ✅
  │    └─ predictLongRun   ❌ (404)
  │         ↓
  │         ├─ 🖼️ IMAGE: gemini-2.0-flash (FREE ✅) → Works!
  │         └─ 🎬 VIDEO: Not supported (method 404)
  │
  └─ Service Account (Enterprise)
       ↓
       aiplatform.googleapis.com
       ├─ generateContent  ✅
       └─ predictLongRun   ✅
            ↓
            ├─ 🖼️ IMAGE: All models (PAID)
            └─ 🎬 VIDEO: veo-2.0/3.0/3.1 (PAID)
```

### 12.7 Summary Comparison

| Factor | 🖼️ Image (Gemini 2.0 Flash) | 🎬 Video (Veo) |
|--------|------------------------------|----------------|
| **API Method** | `generateContent` | `predictLongRunning` |
| **Response Type** | Synchronous | Asynchronous |
| **cloudcode-pa Support** | ✅ Yes | ❌ No (404) |
| **Free Tier** | ✅ Yes | ❌ No |
| **Can use CLIProxyAPI** | ✅ Yes | ❌ No |
| **Requires API Key** | ❌ Optional | ✅ Yes (or Vertex) |
| **Requires Billing** | ❌ No (Gemini 2.0 Flash) | ✅ Yes (always) |

### 12.8 Key Insights

**Q: Why does Image generation work without API Key?**

**A:** Three factors combined:
1. **Method**: Image uses `generateContent` which cloudcode-pa supports
2. **Endpoint**: CLIProxyAPI uses cloudcode-pa.googleapis.com with OAuth
3. **Free Tier**: Gemini 2.0 Flash has free tier for image generation

**Q: Why does Video generation require API Key and Billing?**

**A:** Two factors:
1. **Method**: Video uses `predictLongRunning` which cloudcode-pa does NOT support (404)
2. **No Free Tier**: Veo (all versions) has NO free tier - billing required

---

## 13. Version History

| Version | Date | Changes |
|---------|------|---------|
| 3.2 | 2026-01-18 | Simplified to 3 auth methods (removed Custom OAuth), clarified Billing requirement |
| 3.1 | 2026-01-18 | Added Image vs Video architecture section with API method analysis |
| 3.0 | 2026-01-18 | Added check_api.py utility tool, updated documentation |
| 2.9 | 2026-01-18 | Added Veo API quota analysis, subscription vs API access |
| 2.8 | 2026-01-17 | Added design documentation, OAuth scope analysis |
| 2.7 | 2026-01-16 | Added built-in OAuth with Vertex AI forced endpoint |
| 2.0 | 2026-01-15 | Initial 8-mode video generation support |
