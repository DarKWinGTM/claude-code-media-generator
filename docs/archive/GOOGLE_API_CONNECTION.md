# 🔌 CLIProxyAPI → Google AI API Connection Details

## 📋 วิธีการเชื่อมต่อ CLIProxyAPI ไปยัง Google AI API

เอกสารนี้อธิบายวิธีการเชื่อมต่อของ CLIProxyAPI ไปยัง Google AI API สำหรับ image generation

---

## 🔑 Authentication Flow

### Step 1: OAuth Token จาก CCS

CLIProxyAPI ดึง OAuth token จาก CCS:

**Token Location:**
```
~/.ccs/cliproxy/auth/gemini-user@gmail.com-all.json
```

**Token Structure:**
```json
{
  "token": {
    "access_token": "ya29.a0Aa7pCA98eIgxJGjCbQB5...",
    "refresh_token": "1//0gY0PBupvqkz1CgYIARAAGBASNwF...",
    "expires_in": 3599,
    "expiry": "2025-12-30T00:15:37.120939187+07:00",
    "token_type": "Bearer"
  },
  "email": "user@gmail.com",
  "type": "gemini"
}
```

### Step 2: HTTP Request ไปยัง Google AI API

CLIProxyAPI สร้าง HTTP request พร้อม OAuth token:

**Request Format:**
```http
POST https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-001:predict
Content-Type: application/json
Authorization: Bearer ya29.a0Aa7pCA98eIgxJGjCbQB5...

{
  "instances": [
    {
      "prompt": "A cute cat playing with a ball"
    }
  ],
  "parameters": {
    "sampleCount": 1,
    "aspectRatio": "1:1"
  }
}
```

---

## 🔄 Complete Request Flow

### 1. Client → CLIProxyAPI

**Request:**
```http
POST http://127.0.0.1:8317/v1/chat/completions
Authorization: Bearer ccs-internal-managed
Content-Type: application/json

{
  "model": "gemini-3-pro-image-preview",
  "messages": [{"role": "user", "content": "A cute cat"}],
  "modalities": ["image", "text"],
  "image_config": {"aspect_ratio": "1:1", "image_size": "1K"}
}
```

**CLIProxyAPI actions:**
1. เห็น `ccs-internal-managed` → รู้ว่าต้องดึง OAuth token
2. Query CCS สำหรับ actual OAuth token
3. Translate OpenRouter format → Google Imagen format

### 2. CLIProxyAPI → CCS (Internal Token Query)

**Internal process:**
```
CLIProxyAPI reads: ~/.ccs/cliproxy/auth/gemini-*.json
Extract: access_token (ya29.a0Aa7pCA98e...)
```

### 3. CLIProxyAPI → Google AI API

**Actual HTTP Request:**
```http
POST https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-001:predict
Authorization: Bearer ya29.a0Aa7pCA98eIgxJGjCbQB5...
Content-Type: application/json

{
  "instances": [{"prompt": "A cute cat"}],
  "parameters": {"sampleCount": 1, "aspectRatio": "1:1"}
}
```

### 4. Google AI API → CLIProxyAPI

**Response:**
```json
{
  "predictions": [
    {
      "bytesBase64Encoded": true,
      "mimeType": "image/png",
      "data": "iVBORw0KGgoAAAANSUhEUgAA..."
    }
  ]
}
```

### 5. CLIProxyAPI → Client

**Translated Response:**
```json
{
  "choices": [
    {
      "message": {
        "images": [
          {
            "image_url": {
              "url": "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAA..."
            }
          }
        ]
      }
    }
  ]
}
```

---

## 📐 Format Translation Details

### OpenRouter → Google Imagen

| OpenRouter Field | Google Imagen Field | Transformation |
|-----------------|-------------------|----------------|
| `model` | URL path | `gemini-3-pro-image-preview` → `/v1beta/models/imagen-3.0-generate-001:predict` |
| `messages[].content` | `instances[].prompt` | Extract user message text |
| `modalities` | - | Determines if image generation |
| `image_config.aspect_ratio` | `parameters.aspectRatio` | `"1:1"` → `"1:1"` |
| `image_config.image_size` | - | May affect quality |

### Response Translation

| Google Field | OpenRouter Field | Transformation |
|-------------|-----------------|----------------|
| `predictions[].data` | `choices[].message.images[].image_url.url` | Add `data:image/png;base64,` prefix |
| `predictions[].mimeType` | - | Used for data URI prefix |

---

## 🌐 Google AI API Endpoints

### Text Generation (Gemini)
```
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent
```

### Image Generation (Imagen)
```
POST https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-001:predict
```

### Alternative Image Generation
```
POST https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-002:predict
POST https://generativelanguage.googleapis.com/v1beta/models/imagen-4.0-generate-001:predict
```

---

## 🔐 Authentication Details

### OAuth Bearer Token

**Format:**
```
Authorization: Bearer ya29.a0Aa7pCA98eIgxJGjCbQB5...
```

**Characteristics:**
- Token ประมาณ 200-400 characters
- เริ่มต้นด้วย `ya29.`
- มีอายุ ~1 ชั่วโมง (3599 seconds)
- CCS จะ auto-refresh เมื่อใกล้หมดอายุ

### Token Refresh Flow

```
1. CLIProxyAPI detects token expiration
2. CCS refreshes token automatically
3. New token stored in ~/.ccs/cliproxy/auth/
4. CLIProxyAPI uses new token for next request
```

---

## 🧩 Implementation Architecture

### CLIProxyAPI Internal Components

```
┌─────────────────────────────────────────────────────────────┐
│  CLIProxyAPI (Go Server)                                   │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │  HTTP Server (port 8317)                             │  │
│  │  - Receives OpenRouter format requests               │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │  Request Translator                                  │  │
│  │  - OpenRouter → Provider format                     │  │
│  │  - Extracts modalities, image_config                │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │  OAuth Manager                                       │  │
│  │  - Reads tokens from CCS                            │  │
│  │  - Manages token refresh                            │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │  HTTP Client                                         │  │
│  │  - Sends requests to Google AI API                  │  │
│  │  - Adds Bearer token to headers                     │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │  Response Translator                                 │  │
│  │  - Provider format → OpenRouter format               │  │
│  │  - Wraps base64 data in image_url.url               │  │
│  └──────────────────┬───────────────────────────────────┘  │
└─────────────────────┼───────────────────────────────────────┘
                      │
                      ↓
                 Google AI API
```

---

## 📊 Request/Response Examples

### Image Generation Request

**Client sends:**
```json
{
  "model": "gemini-3-pro-image-preview",
  "messages": [{"role": "user", "content": "A futuristic city"}],
  "modalities": ["image", "text"],
  "image_config": {
    "aspect_ratio": "16:9",
    "image_size": "1K"
  }
}
```

**CLIProxyAPI translates to:**
```json
{
  "instances": [{"prompt": "A futuristic city"}],
  "parameters": {
    "sampleCount": 1,
    "aspectRatio": "16:9"
  }
}
```

**CLIProxyAPI sends to Google:**
```http
POST https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-001:predict
Authorization: Bearer ya29.a0Aa7pCA98e...
Content-Type: application/json
```

---

## ✅ Key Points Summary

1. **Authentication:** CLIProxyAPI ใช้ OAuth Bearer token จาก CCS
2. **Token Source:** `~/.ccs/cliproxy/auth/gemini-*.json`
3. **API Endpoint:** `https://generativelanguage.googleapis.com/v1beta/models/...`
4. **Header Format:** `Authorization: Bearer ya29.a0Aa...`
5. **Format Translation:** เกิดขึ้นใน CLIProxyAPI (OpenRouter ↔ Google)
6. **Direct Connection:** CLIProxyAPI เชื่อมต่อโดยตรงไป Google API (ไม่ผ่าน CCS)

---

## 🔍 Debugging Connection Issues

### Check OAuth Token
```bash
cat ~/.ccs/cliproxy/auth/gemini-*.json
```

### Test CLIProxyAPI
```bash
curl -X POST http://127.0.0.1:8317/v1/chat/completions \
  -H "Authorization: Bearer ccs-internal-managed" \
  -H "Content-Type: application/json" \
  -d '{"model":"gemini-3-pro-image-preview","messages":[{"role":"user","content":"test"}],"modalities":["image","text"],"image_config":{"aspect_ratio":"1:1","image_size":"1K"}}'
```

### Verify Google API Access
```bash
curl -X POST "https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-001:predict" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"instances":[{"prompt":"test"}],"parameters":{"sampleCount":1}}'
```

---

**Created:** 2026-01-14
**Purpose:** Document CLIProxyAPI → Google AI API connection details
