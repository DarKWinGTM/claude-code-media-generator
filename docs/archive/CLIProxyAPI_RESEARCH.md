# 🔍 CLIProxyAPI Research Summary

## 📊 สรุปการวิจัย CLIProxyAPI และ CLIProxyAPIPlus

### สิ่งที่ค้นพบ

**CLIProxyAPI** (mainline repository: https://github.com/router-for-me/CLIProxyAPI)
- เป็น Go-based proxy server (99.8% Go code)
- ทำหน้าที่เป็น **Format Translator** ระหว่าง OpenAI/OpenRouter format และ CLI tool formats
- ไม่ได้ host models โดยตรง แต่ทำหน้าที่ **proxy** ไปยัง CLI tools

**CLIProxyAPIPlus** (https://github.com/router-for-me/CLIProxyAPIPlus)
- เป็น fork ของ CLIProxyAPI
- เพิ่ม third-party provider support (GitHub Copilot, Kiro)
- ใช้ mainline features เหมือนกัน
- Community-maintained สำหรับ third-party providers

---

## 🏗️ สถาปัตยกรรมที่ถูกต้ออง (Confirmed)

### Component Roles

```
┌─────────────────────────────────────────────────────────────┐
│  CLIProxyAPI (localhost:8317)                               │
│                                                             │
│  หน้าที่หลัก:                                               │
│  1. Receive requests จาก clients                            │
│  2. Translate formats (OpenRouter → Provider format)        │
│  3. Manage authentication (OAuth token management)          │
│  4. Route to appropriate providers                          │
│  5. Translate responses back (Provider → OpenRouter)        │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  CCS (Claude Code Switch)                                   │
│                                                             │
│  หน้าที่หลัก:                                               │
│  1. Store OAuth tokens ใน ~/.ccs/cliproxy/auth/             │
│  2. Auto-refresh tokens                                     │
│  3. Provide tokens เมื่อ CLIProxyAPI สอบถาม               │
│  4. Manage multiple OAuth accounts                         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  Client (image_gen.py)                                      │
│                                                             │
│  หน้าที่หลัก:                                               │
│  1. Send OpenRouter-format requests to CLIProxyAPI          │
│  2. Use "ccs-internal-managed" as auth indicator            │
│  3. Process base64 image responses                          │
└─────────────────────────────────────────────────────────────┘
```

### Request Flow (Confirmed Correct)

```
Client Request Flow (Main Flow)
─────────────────────────────────

1. image_gen.py → CLIProxyAPI (localhost:8317)
   Endpoint: /v1/chat/completions
   Headers: Authorization: Bearer ccs-internal-managed
   Body: OpenRouter format with modalities + image_config

2. CLIProxyAPI รับ request
   - เห็น "ccs-internal-managed" → รู้ว่าต้องดึง OAuth token
   - Query CCS สำหรับ actual OAuth token

3. CLIProxyAPI → CCS (Internal Token Query)
   Location: ~/.ccs/cliproxy/auth/gemini-*.json
   Response: OAuth access_token (ya29.a0Aa...)

4. CLIProxyAPI → Google Imagen API
   - Translate OpenRouter format → Google Imagen format
   - Use OAuth token จาก CCS
   - Endpoint: https://generativelanguage.googleapis.com/...

5. Google API → CLIProxyAPI
   Response: base64 image data (Google format)

6. CLIProxyAPI → Client
   - Translate Google format → OpenRouter format
   - Return image data in choices[].message.images[]
```

---

## 🔑 จุดสำคัญที่ยืนยันแล้ว

### 1. CLIProxyAPI คือตัวแปลง Format (Format Translator)

**OpenRouter Format (Input)**
```json
{
  "model": "gemini-3-pro-image-preview",
  "messages": [{"role": "user", "content": "prompt"}],
  "modalities": ["image", "text"],
  "image_config": {
    "aspect_ratio": "1:1",
    "image_size": "1K"
  }
}
```

**CLIProxyAPI translates to → Google Imagen Format**
```json
{
  "instances": [{"prompt": "prompt"}],
  "parameters": {
    "sampleCount": 1,
    "aspectRatio": "1:1"
  }
}
```

### 2. CCS ไม่ใช่ Request Handler

**ที่ CCS ทำจริงๆ:**
- ✅ เก็บ OAuth tokens
- ✅ Auto-refresh tokens
- ✅ ตอบ token requests จาก CLIProxyAPI

**ที่ CCS ไม่ได้ทำ:**
- ❌ รับ requests จาก client โดยตรง
- ❌ Translate formats
- ❌ Call Google APIs
- ❌ Process responses

### 3. CLIProxyAPI Configuration

**Authentication Directory:** `~/.cli-proxy-api/`
**Port:** 8317
**Format:** OpenAI/OpenRouter-compatible

**Supported OAuth Channels:**
- gemini-cli (รองรับ image generation)
- antigravity (รองรับ image generation)
- claude, codex, qwen, iflow
- github-copilot, kiro (CLIProxyAPIPlus only)

---

## 📐 Format Translation Details

### OpenRouter → Google Imagen

| OpenRouter Field | Google Imagen Field | Notes |
|-----------------|-------------------|-------|
| `model` | Model selection | CLIProxyAPI routes to correct endpoint |
| `messages[].content` | `instances[].prompt` | Extract user message |
| `modalities` | N/A | Indicates image generation |
| `image_config.aspect_ratio` | `parameters.aspectRatio` | 1:1, 16:9, etc. |
| `image_config.image_size` | N/A | May affect quality parameters |

### Response Translation

**Google Format:**
```json
{
  "predictions": [{
    "bytesBase64Encoded": true,
    "mimeType": "image/png",
    "data": "iVBORw0KGgo..."
  }]
}
```

**OpenRouter Format:**
```json
{
  "choices": [{
    "message": {
      "images": [{
        "image_url": {
          "url": "data:image/png;base64,iVBORw0KGgo..."
        }
      }]
    }
  }]
}
```

---

## 🎯 สรุปหลักการสำคัญ

1. **CLIProxyAPI = Format Translator + OAuth Manager**
   - รับ OpenRouter format
   - แปลงเป็น provider format
   - จัดการ OAuth tokens
   - แปลง response กลับ

2. **CCS = Token Storage Only**
   - เก็บ OAuth tokens
   - ให้ tokens เมื่อ CLIProxyAPI ขอ
   - Auto-refresh tokens

3. **Request Load = Direct CLIProxyAPI**
   - Client → CLIProxyAPI → Provider
   - CCS เข้ามาเฉพาะตอนดึง token เท่านั้น

4. **Format Compatibility**
   - CLIProxyAPI ใช้ OpenRouter format เป็น standard
   - แปลงไปยัง provider-specific formats โดยอัตโนมัติ
   - ทำให้ client code ใช้ format เดียวกันทุก provider

---

## 📚 References

- **CLIProxyAPI:** https://github.com/router-for-me/CLIProxyAPI
- **CLIProxyAPIPlus:** https://github.com/router-for-me/CLIProxyAPIPlus
- **Documentation:** https://help.router-for.me/
- **OpenRouter API:** https://openrouter.ai/docs
- **Google Imagen API:** https://ai.google.dev/gemini-api/docs/image-generation

---

## ✅ ความเข้าใจที่ถูกต้อง

จากการวิจัย CLIProxyAPIPlus repository ยืนยันว่า:

1. ✅ **CLIProxyAPI เป็น main request handler** - รับ requests โดยตรงจาก client
2. ✅ **CCS เป็น token provider เท่านั้น** - ให้ OAuth tokens เมื่อ CLIProxyAPI สอบถาม
3. ✅ **Request load ไม่ผ่าน CCS** - Client → CLIProxyAPI → Google API เท่านั้น
4. ✅ **Format translation เกิดขึ้นใน CLIProxyAPI** - แปลง OpenRouter → Provider format
5. ✅ **OpenRouter format เป็น standard** - Client ใช้ format เดียวกันทุก provider

---

**Created:** 2026-01-14
**Research Source:** CLIProxyAPIPlus repository analysis
