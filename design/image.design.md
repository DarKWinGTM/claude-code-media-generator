# 🖼️ Image Generation Design Document

## Version: 1.9
## Date: 2026-02-04
## Parent Document: [design.md](./design.md)

---

## 📋 Document Overview

เอกสารนี้อธิบายรายละเอียดการ Generate Image ผ่าน Google AI APIs รวมถึง:
- Connection Methods ที่รองรับ
- Endpoints และ API Methods
- สิ่งที่ทำได้/ไม่ได้
- ข้อจำกัดและ Pricing

> **Note:** สำหรับ Overview ทั้งระบบ ดูที่ [design.md](./design.md)
> **Note:** สำหรับ Video Generation ดูที่ [video.design.md](./video.design.md)

---

## 1. API Method

### 1.1 Core Method: `generateContent`

```
Image Generation ใช้ API Method: generateContent

Request Type:  Synchronous (รอผลทันที)
Response Time: ~3-10 seconds
Payload Format: { contents: [...] }
```

### 1.2 Why Synchronous?

- Image processing ใช้เวลาน้อย (~3-10 วินาที)
- สามารถรอ response ได้ทันทีโดยไม่ต้อง polling
- ต่างจาก Video ที่ต้องใช้ `predictLongRunning` (async)

---

## 2. Endpoint Support Matrix

### 2.1 Which Endpoints Support Image Generation?

| Endpoint | generateContent | Image Models |
|----------|-----------------|--------------|
| `cloudcode-pa.googleapis.com` | ✅ Supported | ✅ Yes |
| `generativelanguage.googleapis.com` | ✅ Supported | ✅ Yes |
| `aiplatform.googleapis.com` | ✅ Supported | ✅ Yes |

**Key Finding:** Image generation (`generateContent`) works on ALL endpoints.

### 2.2 Endpoint Details

```
ENDPOINT CAPABILITIES FOR IMAGE

1. cloudcode-pa.googleapis.com (CLIProxyAPI/Built-in OAuth)
   ├─ generateContent: ✅ Supported
   ├─ Auth: Built-in OAuth (cloud-platform scope)
   ├─ Billing: ❌ Not required (for Gemini 2.0 Flash)
   └─ Use Case: Development, Free tier

2. generativelanguage.googleapis.com (Gemini API)
   ├─ generateContent: ✅ Supported
   ├─ Auth: API Key
   ├─ Billing: Depends on model
   └─ Use Case: Simple integration

3. aiplatform.googleapis.com (Vertex AI)
   ├─ generateContent: ✅ Supported
   ├─ Auth: OAuth / Service Account
   ├─ Billing: ✅ Required
   └─ Use Case: Enterprise, Production
```

---

## 3. Authentication Methods

### 3.1 Methods Comparison for Image

| Method | Endpoint | Billing Required | Recommended For |
|--------|----------|:----------------:|-----------------|
| **Built-in OAuth** | cloudcode-pa | ❌ No (Gemini 2.0 Flash) | Development |
| **Gemini API Key** (`AIzaSy...`) | generativelanguage | ❌ No (Gemini 2.0 Flash) | Simple projects |
| **Vertex AI API Key** (`AQ...`) | aiplatform | ✅ Yes | Video/Image Gen* |
| **OAuth2 Token** (`ya29...`) | aiplatform | ✅ Yes | Full Vertex access |
| **Service Account** | aiplatform | ✅ Yes | Enterprise |

> **⚠️ Note on Vertex AI API Keys (`AQ...`):**
> - ✅ Works with `predictLongRunning` (async image/video generation)
> - ❌ Does NOT work with `generateContent`, List Models, etc.
> - For `generateContent` method (sync image gen via Gemini), use OAuth2 or Gemini API Key
> - See [check.design.md](./check.design.md) for API Key compatibility details

### 3.2 Recommended: Built-in OAuth (CLIProxyAPI)

```
Image Generation via CLIProxyAPI (Recommended for Free)

image_gen.py request
    │
    ▼
CLIProxyAPI (localhost:8317)
    │ Uses built-in OAuth with cloud-platform scope
    ▼
cloudcode-pa.googleapis.com/v1internal:generateContent
    │ Method supported ✅
    ▼
Gemini Model (gemini-2.0-flash)
    │ Has FREE tier ✅
    ▼
✅ IMAGE GENERATED

Success factors:
1. Method = generateContent (supported by cloudcode endpoint)
2. Model = Gemini 2.0 Flash (has FREE tier for image output)
3. OAuth scope = cloud-platform (accepted by cloudcode endpoint)
```

> **Reference:** ดูรายละเอียด Authentication ที่ [design.md - Section: Authentication](./design.md#authentication-methods)

---

## 4. Image Models & Pricing

### 4.1 Available Models

| Model | Status | Paid Price | Quality |
|-------|--------|------------|---------|
| **Gemini 3 Pro Image** | ✅ Supported | $0.134/image | Best (Default) |
| Imagen 3 | 🔲 Not Implemented | $0.03/image | Requires Vertex AI |
| Imagen 4 | 🔲 Not Implemented | $0.02-0.06/image | Requires Vertex AI |

> **⚠️ Deprecated:** `gemini-2.5-flash-image` is no longer supported (error: unknown provider)
> **Note:** Imagen models require Vertex AI endpoint and are not yet implemented in `image_gen.py`

### 4.2 Recommended Model

```
Default Model:   gemini-3-pro-image-preview (Best quality, always works)
Future:          Imagen 3/4 (requires Vertex AI implementation)
```

---

## 5. What Works / Doesn't Work

### 5.1 ✅ What Works

| Feature | Via CLIProxyAPI | Via API Key | Via Service Account |
|---------|-----------------|-------------|---------------------|
| Text-to-Image | ✅ | ✅ | ✅ |
| Image-to-Image | ✅ | ✅ | ✅ |
| Style Transfer | ✅ | ✅ | ✅ |

### 5.2 ❌ Limitations

| Limitation | Reason |
|------------|--------|
| Imagen models | 🔲 Not implemented - requires Vertex AI endpoint |
| Batch generation | ต้อง loop เอง, API ไม่รองรับ batch |
| High-res (4K+) | บาง models จำกัดที่ 1024x1024 |

---

## 6. CLI Usage Examples

### 6.1 Via CLIProxyAPI

```bash
# Simple text-to-image
python image_gen.py "A beautiful sunset over mountains"

# Default model is gemini-3-pro-image-preview
python image_gen.py "A cat wearing a hat"
```

### 6.2 Via API Key

```bash
# Using API Key
python image_gen.py "A futuristic city" --auth api-key --api-key YOUR_KEY
```

### 6.3 Via Service Account (Enterprise)

```bash
python image_gen.py "Professional product photo" \
  --auth service-account \
  --credentials /path/to/sa.json \
  --project-id YOUR_PROJECT
```

---

## 7. Request/Response Format

### 7.1 Request Format

```json
{
  "contents": [{
    "parts": [{
      "text": "Generate an image of a sunset over mountains"
    }]
  }],
  "generationConfig": {
    "responseModalities": ["image", "text"],
    "imageSizeOption": "1024x1024"
  }
}
```

### 7.2 Response Format

```json
{
  "candidates": [{
    "content": {
      "parts": [{
        "inlineData": {
          "mimeType": "image/png",
          "data": "base64-encoded-image-data..."
        }
      }]
    }
  }]
}
```

---

## 8. Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| `403: ACCESS_TOKEN_SCOPE_INSUFFICIENT` | Wrong scope for endpoint | Use correct auth method |
| `429: Resource exhausted` | Rate limit exceeded | Wait and retry |
| `400: Invalid image size` | Unsupported resolution | Use 1024x1024 or smaller |
| `500: Internal error` | Server issue | Retry with exponential backoff |
| `Timeout` | Request took too long | API request timeout is 300s |

### 8.1 Timeout Configuration

| Operation | Timeout | Notes |
|-----------|---------|-------|
| URL Download | 60s | Download reference images from URL |
| API Request | **300s (5 min)** | Main generation request |

> **Note:** Timeout increased from 120s to 300s in v1.3 for better reliability with slower responses.

---

## 9. Key Insights

### Q: What model should I use?

**A:** Use `gemini-3-pro-image-preview`:
```bash
python image_gen.py "your prompt here"
```
This is the default and only supported model. Previously available models (`gemini-2.5-flash-image`) are deprecated.

---

## 10. Cost Tracking & Display

### 10.1 Design Philosophy

> **Claude Code as Director:** Project นี้ต้องการให้ Claude Code ควบคุม cost อย่างโปร่งใส แม้ image generation บางส่วนจะฟรี แต่ก็ควรมีการ track ไว้เพื่อวางแผน

### 10.2 Pricing Table

| Model | Free Tier | Paid Price | Notes |
|-------|:---------:|------------|-------|
| **Gemini 3 Pro Image** | ❌ | $0.134/image | Default model |
| Imagen 3 | ❌ | $0.03/image | Specialized |
| Imagen 4 | ❌ | $0.02-0.06/image | Latest, variable pricing |

> **⚠️ Deprecated:** `gemini-2.5-flash-image` is no longer supported

### 10.3 Cost Calculation

```python
def calculate_image_cost(model: str, count: int = 1) -> dict:
    """Calculate image generation cost."""

    PRICING = {
        "gemini-3-pro-image-preview": 0.134,  # Default
        "imagen-3": 0.03,
        "imagen-4": 0.04,  # Average
    }

    price = PRICING.get(model, 0.134)  # Default to gemini-3 price

    return {
        "model": model,
        "count": count,
        "price_per_image": price,
        "total_cost_usd": round(price * count, 4),
        "is_free_tier": False  # No free tier available
    }
```

### 10.4 Cost Display Example

```
🖼️ Generating image...
├─ Model: gemini-3-pro-image-preview
├─ 💰 Cost: $0.134/image
│
├─ ✅ Image generated successfully!
├─ Output: generated_images/image_20260121_140000_0.png
└─ Size: 1.2 MB
```

### 10.5 Cost Comparison: Image vs Video

| Type | Typical Cost | Notes |
|------|--------------|-------|
| Image (Default) | $0.134 | Gemini 3 Pro Image |
| Image (Imagen) | $0.02-0.06 | Per image |
| Video 5s | $0.75-3.00 | Per video |
| Video 8s | $1.20-4.80 | Per video |

> **Insight:** Image generation ยังคง cost-effective เมื่อเทียบกับ video ดังนั้น Claude Code สามารถแนะนำ user ให้ใช้ image ก่อนถ้าไม่จำเป็นต้องใช้ video

### 10.6 Implementation Status

| Feature | Status | Notes |
|---------|:------:|-------|
| Cost calculation | ✅ **Done** | In metadata |
| Free tier detection | ✅ **Done** | Auto-detect |
| CLI cost display | 🔲 Planned | Show during generation |
| Model recommendation | 🔲 Future | Cost-based suggestions |

---

## 11. Planned Features

### 11.1 Multi-Tool Workflows (via Skill Orchestration)

> **Status:** ✅ Available (via Smart Skill)
> **Updated:** 2026-02-04

**Design Decision:** Template features like `closing-frame` are NOT implemented as CLI flags.
Instead, Smart Skill orchestrates multi-tool workflows:

**Example: Video Closing Credits Workflow**

```
User Request: "Create closing credits for my video"
  ↓
Skill Phase 1: Generate closing frame image
  → python image_gen.py "Video credits frame with host avatars" \
      --image avatar1.jpg avatar2.jpg \
      -o closing.png
  ↓
Skill Phase 2: Extend video with closing frame
  → python video_gen.py "Fade transition to credits" \
      --extend-video main.mp4 \
      --last-frame closing.png
```

**Benefits:**
- No code changes needed in image_gen.py or video_gen.py
- Flexible workflows managed by Smart Skill
- Same capabilities, simpler implementation
- Reference: [generate-video SKILL.md](../.claude/skills/generate-video/SKILL.md)

---

## 12. Cross-References

| Document | Content |
|----------|---------|
| [design.md](./design.md) | Main design, system overview, authentication |
| [video.design.md](./video.design.md) | Video generation details |
| [README.md](./README.md) | Quick start guide |

---

## 13. Version History

> Full history: [image.changelog.md](../changelog/image.changelog.md)
