# 🎨 Design Document: CLIProxyAPI Image & Video Generation

## 📋 Document Overview

**Version:** 3.9
**Last Updated:** 2026-02-08
**Status:** Active Development
**Author:** DarKWinGTM

---

## 📚 Documentation Structure

> **This is the MAIN design document.** For detailed specifications, see related documents below.

### Related Documents

| Document | Description | Content |
|----------|-------------|---------|
| **[image.design.md](./image.design.md)** | 🖼️ Image Generation Details | Endpoints, methods, limitations, pricing |
| **[video.design.md](./video.design.md)** | 🎬 Video Generation Details | 9 modes, endpoints, methods, limitations |
| **[skill.design.md](./skill.design.md)** | 🎯 Smart Skill System | `/generative` skill design, workflows |
| **[pages.design.md](./pages.design.md)** | 🌐 GitHub Pages Design | UI/UX, MkDocs Material, Metro Cyberpunk theme |
| **[config.design.md](./config.design.md)** | ⚙️ Config System | Multi-project config, Smart Defaults |
| **[mcp.design.md](./mcp.design.md)** | 🔧 MCP Tools Specification | Future MCP integration plan |
| **[gcsdb.design.md](./gcsdb.design.md)** | 🗄️ GCS Module (Planning) | Future GCS module extraction |
| **[agent.design.md](./agent.design.md)** | 🤖 Agent Design | Generative Media Navigator agent |
| **[testing.design.md](./testing.design.md)** | 🧪 Checkpoint Testing | 48 test cases, installation, validation |
| **[README.md](../README.md)** | 📖 Quick Start | Installation and basic usage |

### Quick Navigation

| Topic | Location |
|-------|----------|
| **System Requirements** | [Section: System Requirements](#system-requirements) |
| System Architecture | [Section: System Architecture](#-system-architecture) |
| Authentication (3 Methods) | [Section: Authentication](#authentication-methods) |
| **OAuth 2.0 Client Types** | [Section: Understanding OAuth 2.0](#-understanding-oauth-20-client-types) |
| Image Generation | [image.design.md](./image.design.md) |
| Video Generation (9 Modes) | [video.design.md](./video.design.md) |
| **Smart Skill System** | [skill.design.md](./skill.design.md) |
| **GitHub Pages** | [pages.design.md](./pages.design.md) |
| **Cloud Storage (GCS)** | [Section: Cloud Storage](#️-google-cloud-storage-gcs-integration) |
| **Cost Overview** | [Section: Cost Overview](#-cost-overview) |
| **Config System** | [config.design.md](./config.design.md) |
| **Smart Defaults (Metadata)** | [Section 18: Smart Defaults](#18-smart-defaults-system-metadata-driven) |
| **Checkpoint Testing** | [testing.design.md](./testing.design.md) |
| API Endpoints | [Section: API Details (Summary)](#-api-details-summary) |
| Error Handling | [Section: Error Handling](#error-handling) |

### Critical Discoveries (Development Journey)

> ⚠️ **Key Learning:** ระหว่างการพัฒนา เราค้นพบความแตกต่างสำคัญระหว่าง Image และ Video:

| Factor | 🖼️ Image | 🎬 Video |
|--------|----------|----------|
| **API Method** | `generateContent` | `predictLongRunning` |
| **cloudcode-pa Support** | ✅ Works | ❌ 404 NOT FOUND |
| **CLIProxyAPI (Built-in OAuth)** | ✅ Works | ❌ Cannot use |
| **Free Tier** | ✅ Gemini 2.0 Flash | ❌ None (all Veo) |
| **Billing Required** | ❌ Optional | ✅ Always |

**Insight:** cloudcode-pa.googleapis.com (CLIProxyAPI endpoint) ถูกออกแบบมาสำหรับ Code Assist ไม่ใช่ Media Generation จึงไม่รองรับ `predictLongRunning` method

---

**Documentation v3.1 Changes (2026-01-18):**
- **Added Cost Overview Section:** Quick cost reference table, cost tracking features status, cost-based recommendations
- **Updated Quick Navigation:** Added Cost Overview link

**Documentation v3.0 Changes (2026-01-18):**
- **Major Refactoring:** Split detailed API specifications into separate documents
- **Removed ~1500 lines** of duplicate content from design.md
- **Created image.design.md** - Complete Image Generation specifications (273 lines)
- **Created video.design.md v2.0** - Complete Video Generation specifications (664 lines)
- **Updated Quick Navigation** - Links now point to detail documents
- **Renamed Section:** "🔌 Direct API Connection Details (Verified)" → "🔌 API Details (Summary)"
- **Added Summary Tables** with links to detail documents
- **Document Structure:** design.md = Overview + Links → detail documents = Complete specs

**Documentation v2.7 Changes:**
- Added **Complete Video Generation Modes (All Veo Features)** - 8 modes based on official documentation
- Added **New CLI Parameters (v2.7)**: `--last-frame`, `--reference-asset`, `--reference-style`, `--video`, `--mask`, `--mask-mode`, `--resize-mode`
- Added **Mode Detection Logic** - Python function to auto-detect generation mode from CLI arguments
- Added **API Request Format by Mode** - Complete JSON examples for all 8 modes
- Added **Model Compatibility Matrix** - Which features work with which Veo models
- Added **Input Constraints** table - Format and requirements for each input type
- Added **Output Specifications** table - Duration, resolution, frame rate per mode
- Added **CLI Examples for All Modes** - Complete bash examples for all 8 generation modes
- **New Features from Documentation:**
  - First & Last Frames mode (video interpolation between two images)
  - Reference Asset images (up to 3 images for subject preservation)
  - Reference Style image (style transfer from 1 image)
  - Insert Objects (add objects using mask + prompt)
  - Remove Objects (remove objects using mask + prompt)

**Documentation v2.6 Changes:**
- Added **Authentication Compatibility Matrix** - Critical info: API Key works ONLY with Gemini API
- Added **Endpoint Selection Logic** - Auto-select endpoint based on auth method
- Added **Endpoint Mapping by Auth Method** table
- Added **Polling Endpoint Differences** - Gemini API uses GET, Vertex AI uses POST
- Added **GCP Project Required** and **Billing** rows to API comparison table
- Clarified that OAuth methods work with BOTH endpoints, API Key is Gemini-only
- Added **Authentication Methods Overview** table with Auth Type, Login Style, Interactive, Token Auto-Refresh
- Added **Detailed Authentication Method Explanations** for each method:
  - API Key: Static Key (NOT OAuth), no login, Gemini API only
  - CLIProxyAPI OAuth: OAuth 2.0 browser login, token structure documentation
  - Service Account: OAuth 2.0 JWT Bearer, JSON key file, no interactive login
  - ADC (gcloud): OAuth 2.0 browser login via gcloud SDK, token storage location
- Added **Authentication Comparison Summary** visual diagram
- Added **Video Extension (Continue Video) Design** section:
  - Video Extension Modes table (Text-to-Video, Image-to-Video, Video Extension)
  - CLI Parameters for Video Extension (`--extend-video`, `--extend-from`)
  - Video Extension Flow diagram
  - CLI Examples including chain extensions
  - Video Extension Limitations
  - Video URI Management with `--output-json`
  - Complete Generation Modes Summary table

**Documentation v2.5 Changes:**
- **Major Design Decision:** CLIProxyAPI OAuth token is now the PRIMARY authentication source for Direct Mode
- **Explicit Mode Selection:** No silent fallback - user must explicitly choose auth method if default fails
- Added **5 Authentication Modes** with clear CLI parameters
- Added **CLIProxyAPI OAuth Token Sync** as Priority 1 in Credential Discovery System
- Added `load_cliproxy_token()` function with full token file structure documentation
- Updated **Authentication Priority Order** table with 4 levels (CLIProxyAPI → Service Account → ADC → API Key)
- Updated **CLI Interface Design** with `--mode` and `--auth` parameters
- Added **Error Handling** section with explicit options for users
- Token path: `~/.ccs/cliproxy/auth/gemini-{email}-{project}.json`
- Auto-refresh support for expired tokens using refresh_token
- Scopes verified: `cloud-platform` covers Vertex AI Video Generation

**Documentation v2.4 Changes:**
- Added **🔌 Direct API Connection Details (Verified)** comprehensive section
- Added **API Comparison Table** (Gemini API vs Vertex AI)
- Added **Complete Request/Response Examples** for Text-to-Video, Image-to-Video, Video Extension
- Added **Video Extension Structure** (verified from Stack Overflow - uses `video.uri` not `bytesBase64Encoded`)
- Added **Parameters Reference Table** with all supported parameters and default values
- Added **Veo 3.1 Special Features** (Native Audio, Prompt Tips)
- Added **Authentication Implementation** with OAuth as PRIMARY, API Key as SECONDARY
- Added **Authentication Priority Order** table (Service Account → ADC → API Key)
- Added **Unified Credential Discovery System** code for video_gen.py
- Added **OAuth vs API Key Comparison** table
- Updated **Polling Implementation Pattern** to support both OAuth and API Key
- Added **Error Handling Reference** table with all HTTP error codes
- Added **Implementation Checklist** for video generation feature

**Documentation v2.3 Changes:**
- Added **Direct Google API Integration References** section
- Documented **Vertex AI Authentication** methods (Service Account, ADC, API Key)
- Added **Veo 3.1 Video Generation** official documentation links
- Included **Implementation Strategy** for dual-mode authentication in single script
- Added **CLI Interface Design** with `--auth` parameter specification
- Documented **Credential Discovery Order** (CLI → ENV → Default paths)
- Added Stack Overflow references for Veo endpoint structure
- Documented fallback strategy since CCS doesn't support video generation yet
- **Key Design Decision:** Single script (`video_gen.py`) with dual-mode support instead of separate scripts

---

## 🎯 Project Concept

**CLIProxyAPI Image/Video Generator** เป็นระบบสาธิตและเครื่องมือสำหรับสร้างภาพและวิดีโอผ่าน CLIProxyAPI โดยใช้ Google AI models (Imagen & Veo) ผ่านการแปลง format ของ CLIProxyAPI

### Core Philosophy

- **CLIProxyAPI as Translator:** แปลง client requests → Google API format
- **CCS as Token Provider:** ให้ OAuth tokens เท่านั้น (ไม่อยู่ใน request load path)
- **Consistent Interface:** Image และ Video ใช้ format เดียวกัน
- **OAuth Authentication:** ใช้ CCS internal managed tokens
- **Multimodal Flexibility:** รองรับ Text, Single Image, และ Multiple Images เป็น Input

### System Requirements

| Requirement | Minimum | Required For | Notes |
|-------------|---------|--------------|-------|
| **Python** | 3.8+ | All scripts | Recommended: 3.10+ |
| **pip packages** | `requests` | All scripts | `pip install requests` |
| **gcloud CLI** | Latest | GCS, OAuth | [Install Guide](#prerequisites-gcloud-cli--gsutil) |
| **gsutil** | Included with gcloud | GCS download | Bundled with gcloud SDK |
| **Billing enabled** | - | Video (Veo) | No free tier for video |

**Quick Install:**
```bash
# Python dependencies
pip install requests

# gcloud CLI (Ubuntu)
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
sudo apt-get update && sudo apt-get install google-cloud-cli

# Setup
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
```

> **Note:** Video Generation กับ GCS ต้องการ gcloud CLI สำหรับ download | Image Generation ไม่ต้องการ gcloud

---

## 🔐 Authentication Methods (3 รูปแบบ)

> **สรุป:** ทุก method ต้อง Billing สำหรับ Video (Veo) | Image สามารถใช้ Free tier ได้

### Quick Comparison

| # | Method | Endpoint | Image | Video | Billing |
|---|--------|----------|:-----:|:-----:|:-------:|
| 1 | **API Key** | generativelanguage | ✅ | ✅ | Video only |
| 2 | **Built-in OAuth** | cloudcode-pa | ✅ | ❌ 404 | - |
| 3 | **Service Account** | aiplatform | ✅ | ✅ | ✅ Always |

### Method Details

| Method | Best For | Setup Complexity |
|--------|----------|------------------|
| **API Key** | Simple projects, Development | ★☆☆☆☆ (1 min) |
| **Built-in OAuth** | Image only, Free tier | ★★☆☆☆ (5 min) |
| **Service Account** | Enterprise, CI/CD, Automation | ★★★☆☆ (15 min) |

### Recommendation

```
For Image Generation:
→ Built-in OAuth (CLIProxyAPI) - Free, no setup

For Video Generation:
→ API Key - Simplest, requires billing
→ Service Account - Enterprise, requires billing
```

> **Note:** รายละเอียดเพิ่มเติมดูที่ [Section: Authentication Methods Overview](#authentication-methods-overview)

---

## 🔑 Understanding OAuth 2.0 Client Types

> **Key Concept:** ความเข้าใจความแตกต่างระหว่าง OAuth 2.0 Client Types ช่วยให้เลือกวิธี Authentication ได้ถูกต้อง

### OAuth 2.0 Client IDs คืออะไร?

OAuth 2.0 Client ID คือ "App ที่ขอ User Login" - สร้างใน Google Cloud Console เพื่อให้ App ของเราสามารถขอ permission จาก User ผ่าน Google Sign-in ได้

### 2 รูปแบบของ OAuth Client

| ประเภท | คำอธิบาย | ตัวอย่าง |
|--------|----------|----------|
| **Built-in OAuth Client** | Client ID/Secret ที่ Developer ฝังไว้ใน code แล้ว | CLIProxyAPI, gcloud CLI |
| **Custom OAuth Client** | สร้างเองใน Google Cloud Console | App ที่เราพัฒนาเอง |

### Built-in OAuth Client (CLIProxyAPI)

```
CLIProxyAPI มี Built-in OAuth Client ครบชุด:

Developer (Anthropic/Google) สร้างไว้ให้แล้ว:
    ├── OAuth 2.0 Client ID      ✅ ฝังใน code
    ├── OAuth 2.0 Client Secret  ✅ ฝังใน code
    └── OAuth Consent Screen     ✅ ตั้งค่าไว้แล้ว

User Flow:
    รัน cliproxy login gemini
        ↓
    Browser เปิด Google Sign-in
        ↓
    User กดอนุญาต (Consent)
        ↓
    Token เก็บใน ~/.ccs/cliproxy/auth/
        ↓
    ใช้งาน API ได้เลย ✅
```

### สิ่งที่ User ไม่ต้องทำ (เมื่อใช้ Built-in OAuth)

| ขั้นตอน | ต้องทำเอง? | หมายเหตุ |
|---------|:---------:|----------|
| สร้าง Google Cloud Project | ❌ | Developer ทำให้แล้ว |
| สร้าง OAuth 2.0 Client ID | ❌ | ฝังใน code แล้ว |
| ตั้งค่า Consent Screen | ❌ | ตั้งค่าไว้แล้ว |
| Manage Redirect URIs | ❌ | กำหนดไว้แล้ว |
| เปิด Billing | ❌ | Free tier (สำหรับ Image) |

### Custom OAuth Client (สร้างเอง)

ถ้าต้องการสร้าง OAuth Client เอง ต้องทำขั้นตอนเหล่านี้:

```
สร้างเองใน Google Cloud Console:

1. สร้าง Google Cloud Project
    ↓
2. ไป APIs & Services → Credentials
    ↓
3. Create OAuth 2.0 Client ID
   - Application type: Desktop app
   - Download client_secret.json
    ↓
4. ตั้งค่า OAuth Consent Screen
   - App name, logo
   - Scopes ที่ต้องการ
   - Test users (ถ้าเป็น External)
    ↓
5. นำ Client ID/Secret ไปใช้ใน App
```

### เปรียบเทียบ OAuth Client กับ Service Account

| Feature | OAuth Client (User Login) | Service Account |
|---------|:-------------------------:|:---------------:|
| **ใครเป็นคนใช้** | Human User | Application/Server |
| **Login Method** | Browser Sign-in | JSON Key File |
| **Interactive** | ✅ ต้อง User กด | ❌ Automated |
| **Token Storage** | Browser + App Cache | ไม่มี (ใช้ JWT) |
| **Use Case** | CLI tools, Desktop apps | CI/CD, Server-side |
| **Billing** | User's account | Service account's project |

### สรุป: เลือกใช้แบบไหน?

```
ต้องการใช้งานแบบไหน?
    │
    ├─ แค่ Login แล้วใช้งาน (ไม่ต้อง setup)
    │   → ใช้ Built-in OAuth (CLIProxyAPI)
    │   → เหมาะกับ: Development, Testing, Personal use
    │
    ├─ ต้องการควบคุม App เอง
    │   → สร้าง Custom OAuth Client
    │   → เหมาะกับ: Production app ที่พัฒนาเอง
    │
    └─ ต้องการ Automation (CI/CD, Server)
        → ใช้ Service Account
        → เหมาะกับ: Enterprise, Production, Scheduled jobs
```

---

## 🔍 API Key Verification & Model Listing

> **⚠️ MOVED:** Documentation สำหรับ `check_api.py` ย้ายไปที่ [check.design.md](./check.design.md)
>
> **เหตุผล:** เพื่อป้องกันความสับสนระหว่าง:
> - `check_api.py` operations (List Models) - `AQ...` keys ❌ ไม่ทำงาน
> - `video_gen.py` operations (Video Gen) - `AQ...` keys ✅ ทำงานได้!

### Quick Reference: API Key by Tool

| Tool | Operation | `AIzaSy...` | `AQ...` | `ya29...` |
|------|-----------|:-----------:|:-------:|:---------:|
| **video_gen.py** | Video Generation | ✅ | ✅ | ✅ |
| **image_gen.py** | Image Generation | ✅ | ✅ | ✅ |
| **check_api.py** | List Models | ✅ | ❌ | ✅ |

> **สำคัญ:** `AQ...` keys ทำงานกับ video/image generation แต่ไม่ทำงานกับ List Models!
>
> ดูรายละเอียด:
> - Video Generation: [video.design.md](./video.design.md)
> - API Checker: [check.design.md](./check.design.md)

---


> 📜 **Changelog:** See [changelog/changelog.master.md](./changelog/changelog.master.md)

---

## 🏗️ System Architecture

### High-Level Flow Diagram

```
┌─────────────────────────────────────────────────────────────┐
│  Client Layer                                              │
│                                                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │ image_gen.py │  │ video_gen.py │  │  MCP Tool    │      │
│  │   (Updated)  │  │ (Proposed)   │  │  (Planned)   │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│                                                             │
│  - Send CLIProxyAPI format requests                         │
│  - Handle Multimodal Inputs (Text + N Images)               │
│  - Use "ccs-internal-managed" auth indicator                │
│  - Manage MIME Types (GIF -> JPEG mapping)                  │
│                                                             │
└────────────────┬────────────────────────────────────────────┘
                 │ HTTP POST http://127.0.0.1:8317/v1/chat/completions
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  CLIProxyAPI (Main Request Handler) ✅                       │
│  localhost:8317                                             │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ 1. Receive Request (CLIProxyAPI format)             │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │ 2. Detect "ccs-internal-managed" → Need OAuth Token│  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │ 3. Query CCS for OAuth Token                        │  │
│  │    ~/.ccs/cliproxy/auth/gemini-*.json               │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │ 4. Translate Format                                 │  │
│  │    CLIProxyAPI → Google API format                  │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │ 5. Send to Google API                              │  │
│  │    (Imagen for image, Veo for video)               │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │ 6. Handle Response                                 │  │
│  │    - Image: Synchronous (direct base64)             │  │
│  │    - Video: Async polling required                  │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │ 7. Translate Response                              │  │
│  │    Google API → CLIProxyAPI format                  │  │
│  └──────────────────┬───────────────────────────────────┘  │
│                     │                                       │
│  ┌──────────────────▼───────────────────────────────────┐  │
│  │ 8. Return to Client                                │  │
│  └──────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                 │
                 │ Internal Query (Token Only)
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  CCS (Extension)                                           │
│  ~/.ccs/cliproxy/auth/                                      │
│                                                             │
│  - OAuth Token Storage                                     │
│  - Auto-refresh Tokens                                     │
│  - NOT in request load path                               │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔄 Design Flow Diagrams

### Image Generation Flow (Updated Implementation)

```
User Request: "Generate a hybrid creature" + [cat.jpg, dog.png]
    ↓
┌─────────────────────────────────────────────────────────────┐
│  image_gen.py (Client)                                     │
│                                                             │
│  1. Parse Arguments                                         │
│     - Prompt: "Generate a hybrid creature"                  │
│     - Images: ["cat.jpg", "dog.png"]                        │
│                                                             │
│  2. Process Images                                          │
│     - Loop through input_images                             │
│     - Download (if URL) or Read (if local)                  │
│     - Encode base64                                         │
│     - Detect MIME type (Logic: GIF -> JPEG)                 │
│                                                             │
│  3. Construct Payload                                       │
│     payload = {                                             │
│       "model": "gemini-3-pro-image-preview",                │
│       "messages": [{                                        │
│          "role": "user",                                    │
│          "content": [                                       │
│             {"type": "text", "text": "..."},                │
│             {"type": "image_url", ...},                     │
│             {"type": "image_url", ...}                      │
│          ]                                                  │
│       }],                                                   │
│       "image_config": {"aspect_ratio": "1:1"}               │
│     }                                                       │
└────────────────┬────────────────────────────────────────────┘
                 │ POST http://127.0.0.1:8317/v1/chat/completions
                 │ Headers: Authorization: Bearer ccs-internal-managed
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  CLIProxyAPI                                                │
│                                                             │
│  1. Detect ccs-internal-managed                           │
│  2. Get OAuth token: ~/.ccs/cliproxy/auth/gemini-*.json │
│  3. Translate → Google Imagen format                       │
│  4. POST to Google Imagen API                              │
│  5. Wait ~5-30 seconds (synchronous)                       │
│  6. Receive base64 image                                   │
│  7. Translate → Client format                              │
└────────────────┬────────────────────────────────────────────┘
                 │ Return {"choices": [{"message": {"images": [...]}}]}
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  Parse Response & Save Image                               │
│                                                             │
│  - Extract base64 data                                     │
│  - Decode & save as PNG                                    │
│  - Output: ./generated_images/image_YYYYMMDD_HHMMSS.png    │
└─────────────────────────────────────────────────────────────┘
```

### Video Generation Flow (Proposed)

```
User Request: "Generate a cat playing video"
    ↓
┌─────────────────────────────────────────────────────────────┐
│  video_gen.py (Client - to be implemented)                 │
│                                                             │
│  payload = {                                                │
│    "model": "veo-3.1-generate-preview",                    │
│    "messages": [{"role": "user", "content": "A cat..."}],   │
│    "modalities": ["video", "text"],                        │
│    "video_config": {                                        │
│      "duration_seconds": "8",                              │
│      "aspect_ratio": "16:9",                               │
│      "resolution": "720p"                                  │
│    }                                                        │
│  }                                                          │
└────────────────┬────────────────────────────────────────────┘
                 │ POST http://127.0.0.1:8317/v1/chat/completions
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  CLIProxyAPI                                                │
│                                                             │
│  1. Detect ccs-internal-managed                           │
│  2. Get OAuth token from CCS                               │
│  3. Translate → Google Veo format                         │
│  4. POST to :predictLongRunning endpoint                  │
│  5. Receive operation name (async started)                 │
│  6. Poll operation every 10 seconds                        │
│  7. Wait 1-5 minutes (asynchronous)                       │
│  8. Download video when done                               │
│  9. Translate → Client format                              │
└────────────────┬────────────────────────────────────────────┘
                 │ Return {"choices": [{"message": {"videos": [...]}}]}
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  Parse Response & Save Video                               │
│                                                             │
│  - Extract base64 data                                     │
│  - Decode & save as MP4                                    │
│  - Output: ./generated_videos/video_YYYYMMDD_HHMMSS.mp4    │
└─────────────────────────────────────────────────────────────┘
```

### MCP Tool Integration Flow

```
Claude Code CLI User
    ↓
User: "Generate an image of a sunset"
    ↓
┌─────────────────────────────────────────────────────────────┐
│  MCP Server (image-gen-mcp)                                │
│  Port: 3000 (configurable)                                  │
│                                                             │
│  Tool: generate_image                                      │
│  Parameters:                                               │
│    - prompt: string (required)                             │
│    - aspect_ratio: "1:1" | "16:9" | ... (optional)         │
│    - image_size: "1K" | "2K" | "4K" (optional)             │
│    - model: "gemini-3-pro-image-preview" (optional)        │
└────────────────┬────────────────────────────────────────────┘
                 │ Call CLIProxyAPI internally
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  CLIProxyAPI → Google Imagen API                           │
│  (Same flow as image_gen.py)                               │
└────────────────┬────────────────────────────────────────────┘
                 │ Return image data
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  MCP Server Response:                                       │
│  {                                                         │
│    "success": true,                                        │
│    "image_path": "/path/to/image.png",                     │
│    "metadata": {                                           │
│      "prompt": "...",                                      │
│      "model": "...",                                       │
│      "timestamp": "...",                                    │
│      "size_bytes": 123456                                  │
│    }                                                       │
│  }                                                         │
└─────────────────────────────────────────────────────────────┘
```

---

## 📁 Current Implementation

### Working Prototype: image_gen.py

**Status:** ✅ Fully Functional (v2.2)
**Location:** `claude-code-image-generator/image_gen.py`

**Features:**
- Generate images via CLIProxyAPI
- Support multiple models (gemini-3-pro-image-preview, gemini-2.5-flash-image)
- Configurable aspect ratios (1:1, 3:4, 4:3, 9:16, 16:9)
- Configurable image sizes (1K, 2K, 4K)
- Auto-save to ./generated_images/
- Metadata tracking (JSON)
- Colorful terminal output
- **Multiple Image Inputs (New in v2.2):** Support for referencing multiple images
- **URL Support (New in v2.2):** Directly download referenced images from URLs

**Usage:**
```bash
# Basic usage
python image_gen.py "A cute cat"

# With options
python image_gen.py "A sunset" --aspect-ratio 16:9 --image-size 2K

# Multiple images (Local + URL)
python image_gen.py "Fusion style" --image local_sketch.png https://example.com/style.jpg

# GIF Input (Automatically handled as JPEG MIME)
python image_gen.py "Analyze movement" --image animation.gif
```

**Key Code Sections:**
- Authentication (lines 41-44)
- Multiple Image Processing (loop in `generate_image`)
- MIME Type Detection (GIF -> JPEG logic)
- Response handling

---

## ✅ Phase 3: Claude Code Skills (Completed)

> **Status:** ✅ Implemented (2026-02-03)
> **Location:** `.claude/skills/`

### Overview

Claude Code Skills ช่วยให้ผู้ใช้สามารถ generate video/image ผ่าน slash commands ได้โดยตรง

### Skills Implemented

| Skill | Command | Features |
|-------|---------|----------|
| **generate-video** | `/generate-video "prompt"` | Interactive pre-flight checks, API key setup, 8 modes |
| **generate-image** | `/generate-image "prompt"` | Aspect ratio, image size, CCS auth |

### File Structure

```
.claude/skills/
├── generate-video/
│   └── SKILL.md        # 194 lines - Full video generation
└── generate-image/
    └── SKILL.md        # 82 lines - Image generation
```

### Key Features

1. **YAML Frontmatter** - Pre-approved tool permissions
2. **Interactive Setup** - AskUserQuestion for API key configuration
3. **Pre-flight Checks** - Verify API key, endpoint, config before execution
4. **Preset Support** - quick, quality, extend, budget presets

### Usage Example

```bash
# Generate video
/generate-video "A cat playing with a ball" --preset quality

# Generate image
/generate-image "A futuristic city" --aspect-ratio 16:9
```

---

## 🚧 Future Development: MCP Tool

### Design: image-gen-mcp

**Purpose:** Convert image_gen.py into MCP Tool for seamless Claude Code integration

**MCP Tool Definition:**
```typescript
{
  "name": "generate_image",
  "description": "Generate images via CLIProxyAPI using Google Imagen models",
  "inputSchema": {
    "type": "object",
    "properties": {
      "prompt": {
        "type": "string",
        "description": "Text description of the image to generate"
      },
      "aspect_ratio": {
        "type": "string",
        "enum": ["1:1", "3:4", "4:3", "9:16", "16:9"],
        "description": "Image aspect ratio (default: 1:1)"
      },
      "image_size": {
        "type": "string",
        "enum": ["1K", "2K", "4K"],
        "description": "Image resolution (default: 1K)"
      },
      "model": {
        "type": "string",
        "enum": ["gemini-3-pro-image-preview", "gemini-2.5-flash-image"],
        "description": "Model to use (default: gemini-3-pro-image-preview)"
      }
    },
    "required": ["prompt"]
  }
}
```

**Implementation Plan:**

1. **Create MCP Server Structure**
   ```
   image-gen-mcp/
   ├── server.py          # Main MCP server
   ├── tools/
   │   ├── generate_image.py
   │   └── generate_video.py (future)
   ├── config.yaml         # Configuration
   └── README.md
   ```

2. **Core Features**
   - Expose `generate_image` tool
   - Support video generation (future)
   - Configuration management
   - Error handling
   - Logging

---

## 🗺️ Development Roadmap

### Phase 1: Foundation (✅ Complete)
- [x] Research CLIProxyAPI architecture
- [x] Understand Google Imagen API
- [x] Create image_gen.py prototype
- [x] Document authentication flow
- [x] Create GitHub issue for video support
- [x] Consolidate documentation

### Phase 1.5: Enhancement (✅ Complete)
- [x] Support URL inputs
- [x] Support Multiple Image inputs
- [x] Fix MIME type compatibility issues (GIF)

### Phase 2: MCP Tool Integration (Current)
- [ ] Design MCP server architecture
- [ ] Implement `generate_image` tool
- [ ] Add configuration management
- [ ] Test with Claude Code CLI
- [ ] Document MCP usage

### Phase 3: Video Support
- [ ] Implement video_gen.py prototype
- [ ] Add async polling logic
- [ ] Test with Veo API
- [ ] Create `generate_video` MCP tool
- [ ] Update documentation

### Phase 4: Enhanced Features
- [ ] Batch generation support
- [ ] Progress reporting for async operations
- [ ] Error handling improvements
- [ ] Configuration file support
- [ ] Web UI (optional)

---

## 🔧 Technical Specifications

### Input Handling Strategies (New)

#### MIME Type Compatibility Table

| File Extension | Internal MIME Mapping | Notes |
|----------------|-----------------------|-------|
| `.jpg`, `.jpeg` | `image/jpeg` | Native support |
| `.png` | `image/png` | Native support |
| `.webp` | `image/webp` | Supported by Gemini |
| `.gif` | `image/jpeg` | **Special Handling:** API rejects `image/gif`. Mapped to `image/jpeg` effectively sending the first frame/bytes as static image data. |

### Supported Models

#### Image Generation

| Model | Type | Resolution | Quality | Speed |
|-------|------|------------|---------|-------|
| `gemini-3-pro-image-preview` | High Quality | Up to 4K | Best | Slow (~30s) |
| `gemini-2.5-flash-image` | Fast | Up to 2K | Good | Fast (~5s) |

#### Video Generation (Proposed)

| Model | Type | Resolution | Duration | Speed |
|-------|------|------------|----------|-------|
| `veo-3.1-generate-preview` | Preview | 720p, 1080p | 4s, 6s, 8s | Medium |
| `veo-3.1-fast-generate-preview` | Fast Preview | 720p, 1080p | 4s, 6s, 8s | Fast |
| `veo-3.0-generate-001` | Stable | 720p, 1080p | 8s | Slow |
| `veo-3.0-fast-generate-001` | Fast Stable | 720p, 1080p | 8s | Fast |

### Configuration Parameters

#### Image Config

```json
{
  "aspect_ratio": "1:1 | 3:4 | 4:3 | 9:16 | 16:9",
  "image_size": "1K | 2K | 4K",
  "negative_prompt": "string (optional)"
}
```

#### Video Config

```json
{
  "duration_seconds": "4 | 6 | 8",
  "aspect_ratio": "16:9 | 9:16",
  "resolution": "720p | 1080p",
  "negative_prompt": "string (optional)"
}
```

---

## 📊 Performance Metrics

### Current Performance (image_gen.py)

| Metric | Value |
|--------|-------|
| Average Generation Time | 5-30 seconds |
| Success Rate | ~95% |
| Average Output Size | 100KB - 2MB |
| API Rate Limit | As configured in CCS |

### Expected Performance (video_gen.py)

| Metric | Expected Value |
|--------|---------------|
| Average Generation Time | 1-5 minutes |
| Success Rate | ~90% |
| Average Output Size | 5-20MB |
| Polling Interval | 10 seconds |

---

## 💰 Cost Overview

### Cost Management Philosophy

> **Claude Code as Director:** หนึ่งในจุดแข็งของ claude-code-media-generator คือการที่ Claude Code สามารถควบคุม, ติดตาม, และแนะนำเรื่อง cost ได้อย่างโปร่งใส

### Quick Cost Reference

| Type | Model | Cost | Free Tier |
|------|-------|------|:---------:|
| **Image** | Gemini 2.0 Flash | $0.00 | ✅ |
| **Image** | Gemini 3 Pro | $0.134/image | ❌ |
| **Image** | Imagen 3/4 | $0.02-0.06/image | ❌ |
| **Video 5s** | Veo 2.0 | $1.75 | ❌ |
| **Video 8s** | Veo 2.0 | $2.80 | ❌ |
| **Video 8s** | Veo 3.1 | $1.20-4.80 | ❌ |

### Cost Tracking Features

| Feature | Image | Video | Status |
|---------|:-----:|:-----:|:------:|
| Cost in metadata | ✅ | ✅ | **Done** |
| Free tier detection | ✅ | N/A | **Done** |
| CLI cost display | 🔲 | 🔲 | Planned |
| `--estimate-cost` | 🔲 | 🔲 | Planned |
| Session aggregation | 🔲 | 🔲 | Future |

### Cost-Based Recommendations

Claude Code สามารถแนะนำ strategy ตาม cost:

| Scenario | Recommendation |
|----------|---------------|
| Quick prototype | Image (Free) → then Video |
| Budget-conscious | Veo 3.0 Fast ($0.15/sec) |
| Best quality | Veo 3.1 ($0.15-0.60/sec) |
| Production | Pre-calculate costs first |

> **Detailed Cost Information:**
> - Image costs: [image.design.md - Section 10](./image.design.md#10-cost-tracking--display)
> - Video costs: [video.design.md - Section 14](./video.design.md#14-cost-tracking--display)

---

## 🔐 Security Considerations

### OAuth Token Management

- **Token Storage:** `~/.ccs/cliproxy/auth/gemini-*.json`
- **Token Refresh:** Automatic via CCS
- **Token Lifetime:** ~1 hour (3600 seconds)
- **Security:** Never log or expose tokens

### Best Practices

1. **Never commit** OAuth tokens to version control
2. **Use .gitignore** for auth files
3. **Rotate tokens** regularly
4. **Monitor usage** to avoid quota exhaustion
5. **Handle errors** gracefully

---

## ☁️ Google Cloud Storage (GCS) Integration

> **Central Feature:** Cloud Storage ใช้ร่วมกันระหว่าง image_gen.py และ video_gen.py สำหรับการจัดเก็บ output และ video extension

### Why Cloud Storage?

| Use Case | Description |
|----------|-------------|
| **Video Extension** | ต้องใช้ GCS URI (`gs://...`) เป็น input สำหรับ extend video |
| **Large File Storage** | Video files มักใหญ่ (>10MB), GCS เหมาะกว่า base64 response |
| **Persistent Storage** | เก็บ output ถาวร ไม่หายเมื่อ session จบ |
| **Sharing** | สามารถ share link ได้ง่าย |

### Response Format Control: `storageUri` Parameter

| Parameter | Response Format | Video Extension Compatible |
|-----------|-----------------|:--------------------------:|
| ไม่ระบุ `storageUri` | `bytesBase64Encoded` (base64 data) | ❌ ไม่ได้ |
| ระบุ `storageUri` | `gcsUri` (e.g., `gs://bucket/video.mp4`) | ✅ ใช้ได้ |

```
Request with storageUri
  │
  ▼
API saves video to GCS
  │
  ▼
Response contains gcsUri
  │
  ▼
Use gcsUri for --extend-video
```

### Prerequisites: gcloud CLI & gsutil

#### Installation

**Ubuntu/Debian:**
```bash
# Install Google Cloud SDK
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
sudo apt-get update && sudo apt-get install google-cloud-cli
```

**Snap (Alternative):**
```bash
sudo snap install google-cloud-cli --classic
```

**macOS:**
```bash
brew install google-cloud-sdk
```

**Windows:**
- Download: https://cloud.google.com/sdk/docs/install

#### Initial Setup

```bash
# 1. Login to Google Cloud
gcloud auth login

# 2. Set default project
gcloud config set project YOUR_PROJECT_ID

# 3. Verify configuration
gcloud config list
```

### Creating GCS Bucket

```bash
# Create bucket in us-central1 (recommended for Vertex AI)
gsutil mb -l us-central1 gs://YOUR_PROJECT_ID-media-output/

# Verify bucket created
gsutil ls

# Set bucket permissions (if needed for public access)
gsutil iam ch allUsers:objectViewer gs://YOUR_PROJECT_ID-media-output/
```

### GCS Console Explorer

- **URL:** https://console.cloud.google.com/storage/browser
- **Features:**
  - Browse buckets and objects
  - Upload/download files manually
  - Set permissions and lifecycle policies
  - Monitor storage usage

### CLI Usage with `--storage-uri`

```bash
# Video generation with GCS output
python video_gen.py "Your prompt" \
  --storage-uri "gs://YOUR_BUCKET/videos/" \
  --model veo-3.1-generate-preview

# Response will contain:
# {
#   "videos": [{
#     "gcsUri": "gs://YOUR_BUCKET/videos/generated_video.mp4"
#   }]
# }

# Use gcsUri for video extension
python video_gen.py "Continue the scene" \
  --extend-video "gs://YOUR_BUCKET/videos/generated_video.mp4"
```

### Upload to GCS with `--upload-gcs`

> **Use Case:** เมื่อใช้ base64 response (default) แต่ต้องการ upload video ไปยัง GCS หลังจาก download เสร็จ สำหรับใช้กับ Video Extension ในภายหลัง

```bash
# Generate video (base64) แล้ว upload ไป GCS อัตโนมัติ
python video_gen.py "A cat walking in garden" \
  --upload-gcs "gs://YOUR_BUCKET/videos/" \
  --model veo-3.1-generate-preview

# Output:
# ✅ Video saved: ./generated_videos/video_xxx.mp4
# 📤 Uploading to GCS
# ✅ Uploaded to GCS: gs://YOUR_BUCKET/videos/video_xxx.mp4

# ใช้ GCS URI ที่ได้สำหรับ video extension
python video_gen.py "Continue the scene" \
  --extend-video "gs://YOUR_BUCKET/videos/video_xxx.mp4"
```

**Comparison: `--storage-uri` vs `--upload-gcs`**

| Factor | `--storage-uri` | `--upload-gcs` |
|--------|-----------------|----------------|
| Response Format | GCS URI (gcsUri) | Base64 → local file |
| Local File | ต้อง download เอง | ✅ มีอัตโนมัติ |
| GCS File | ✅ API สร้างโดยตรง | ✅ Upload หลัง download |
| Setup Required | ⚠️ Service agent provision | ⚠️ Storage Object User role |
| Best For | Large files, no local needed | ต้องการทั้ง local + GCS |

**Upload Methods:**

| Method | Requirements | Fallback |
|--------|--------------|----------|
| gsutil cp | gcloud CLI installed | - |
| REST API | API Key with Storage Object User | ถ้าไม่มี gsutil |

### Permission Requirements

| Operation | Required IAM Role |
|-----------|-------------------|
| Create bucket | `storage.buckets.create` |
| Write objects | `storage.objects.create` |
| Read objects | `storage.objects.get` |
| List objects | `storage.objects.list` |

**Recommended Role:** `roles/storage.objectAdmin` (for write access)

### Troubleshooting

| Error | Cause | Solution |
|-------|-------|----------|
| `Service agents are being provisioned` | First time using GCS with Vertex AI | Wait 2-5 minutes, retry |
| `Permission denied` | API Key lacks GCS write access | Grant `storage.objectAdmin` role |
| `Bucket not found` | Bucket doesn't exist | Create bucket with `gsutil mb` |

**Service Agents Note:**
```
เมื่อใช้ storageUri ครั้งแรก Vertex AI จะ provision service agents
เพื่อเขียนไปยัง GCS bucket → รอ 2-5 นาทีแล้วลองใหม่

Error: Service agents are being provisioned...
Solution: Wait 2-5 minutes and retry
```

### Bucket Naming Convention (Mandatory Standard)

> ⚠️ **MANDATORY:** เมื่อใช้ Vertex AI API Key ต้องใช้ชื่อ bucket ตาม pattern นี้เท่านั้น

**Pattern:**
```
gs://YOUR_PROJECT_ID-media-output/
```

**Formula:**
```
GCS Bucket = YOUR_PROJECT_ID-media-output
```

**Example:**
| Component | Value |
|-----------|-------|
| Project ID | `gen-lang-client-0344941103` |
| GCS Bucket | `gen-lang-client-0344941103-media-output` |
| Storage URI | `gs://gen-lang-client-0344941103-media-output/path/` |

**Why This Pattern?**
- Vertex AI API Key **automatically** grants permissions to bucket named `YOUR_PROJECT_ID-media-output`
- Using different bucket names will cause **permission denied** errors
- This is NOT a bug - it's the designed security boundary for API Key access

**Common Mistake (Root Cause of Permission Issues):**
```bash
# ❌ WRONG - Will fail with permission denied
--storage-uri "gs://my-custom-bucket/videos/"
--storage-uri "gs://random-bucket-name/output/"

# ✅ CORRECT - Use YOUR_PROJECT_ID-media-output pattern
--storage-uri "gs://gen-lang-client-0344941103-media-output/videos/"
```

**Validation:**
```python
# GCS bucket must follow pattern: YOUR_PROJECT_ID-media-output
expected_bucket = f"{project_id}-media-output"
```

---

### Other Naming Patterns (Service Account Only)

> ⚠️ **Warning:** ชื่อ bucket แบบอื่นใช้ได้ **เฉพาะ Service Account** เท่านั้น และต้อง config IAM permission เพิ่มเติม
>
> **ไม่ทำงานกับ API Key!**

```
❌ These patterns require Service Account + IAM config:

gs://YOUR_PROJECT_ID-veo-output/
gs://YOUR_PROJECT_ID-video-generation/
gs://custom-bucket-name/

✅ Use MANDATORY pattern instead (works with API Key):

gs://YOUR_PROJECT_ID-media-output/
```

### Cross-Reference

| Feature | Document |
|---------|----------|
| Video Extension Mode | [video.design.md - Video Extension](#) |
| storageUri Parameter | [video.design.md - Parameters](#) |
| API Key vs OAuth | [Section: Authentication](#authentication-methods) |

---

## 📚 Related Documentation

### Project Documents

| Document | Description |
|----------|-------------|
| **[image.design.md](./image.design.md)** | 🖼️ Image generation details, endpoints, limitations |
| **[video.design.md](./video.design.md)** | 🎬 Video generation (8 modes), endpoints, limitations |
| **[mcp.design.md](./mcp.design.md)** | 🔧 MCP Tools specification (future) |
| **[README.md](./README.md)** | 📖 Quick start guide |
| **[./issue/](./issue/)** | 📋 GitHub issue documents |

### Archived Documents

> Files moved to `archive/` folder after content merged into main documents.

| File | Status |
|------|--------|
| `archive/DESIGN.md` | Merged into design.md |
| `archive/architecture.md` | Merged into design.md |
| `archive/AUTHENTICATION_GUIDE.md` | Merged into design.md |

### External References

- **CLIProxyAPI:** https://github.com/router-for-me/CLIProxyAPI
- **CLIProxyAPIPlus:** https://github.com/router-for-me/CLIProxyAPIPlus
- **Google Imagen API:** https://ai.google.dev/gemini-api/docs/image-generation
- **Google Veo API:** https://ai.google.dev/gemini-api/docs/video

### Official Google Documentation

| Topic | URL |
|-------|-----|
| Veo Model Reference | https://docs.cloud.google.com/vertex-ai/generative-ai/docs/model-reference/veo-video-generation |
| Vertex AI Authentication | https://docs.cloud.google.com/vertex-ai/docs/authentication |
| Gemini API Video Guide | https://ai.google.dev/gemini-api/docs/video |
| API Pricing | https://ai.google.dev/gemini-api/docs/pricing |
| Rate Limits | https://ai.google.dev/gemini-api/docs/rate-limits |

---

## 🔌 API Details (Summary)

> **For complete API specifications, see the detail documents:**
> - **Image API:** [image.design.md](./image.design.md)
> - **Video API:** [video.design.md](./video.design.md)

### Quick Reference: API Methods

| Generation | API Method | Type | Response Time |
|------------|------------|------|---------------|
| **Image** | `generateContent` | Synchronous | ~3-10 sec |
| **Video** | `predictLongRunning` | Asynchronous | ~30-120 sec |

### Quick Reference: Endpoint Support

| Endpoint | Image (`generateContent`) | Video (`predictLongRunning`) |
|----------|:-------------------------:|:----------------------------:|
| `cloudcode-pa.googleapis.com` | ✅ Works | ❌ 404 |
| `generativelanguage.googleapis.com` | ✅ Works | ✅ Works |
| `aiplatform.googleapis.com` | ✅ Works | ✅ Works |

### Quick Reference: Gemini API vs Vertex AI

| Feature | Gemini API | Vertex AI |
|---------|------------|-----------|
| **Base URL** | `generativelanguage.googleapis.com` | `{LOCATION}-aiplatform.googleapis.com` |
| **Auth** | API Key | OAuth / Service Account |
| **Best For** | Prototyping | Production, Enterprise |
| **Billing** | Pay-as-you-go (API Key) | GCP Billing Account |

> 📌 **Complete API specifications moved to detail documents.**
> The comprehensive content (Authentication Matrix, Endpoint Logic, 8 Video Modes, Request Formats, Polling Implementation) is now in:
> - [video.design.md](./video.design.md) - Video Generation API
> - [image.design.md](./image.design.md) - Image Generation API

---

## 📝 Notes

### Design Principles

1. **Simplicity First:** Easy to use, easy to understand
2. **Consistency:** Same interface for image and video
3. **Reliability:** Proper error handling and retry logic
4. **Extensibility:** Easy to add new models and features

### Known Limitations

1. **CCS Video Support:** CCS does not yet support Video Generation (Veo models)
   - **Workaround:** Direct Google Vertex AI/Gemini API integration required
   - **Impact:** Need separate authentication flow for video generation

2. **Video generation:** Implemented but requires direct API access (not via CCS)
   - Async polling mechanism implemented
   - Requires Service Account credentials or API key

3. **Batch processing:** Not supported yet
   - Single generation per request
   - No queue management

4. **Progress reporting:** Limited for long operations
   - Basic polling status display
   - No detailed progress percentage

5. **Configuration:** Requires code changes currently
   - No external config file support
   - Credentials hardcoded or environment variables

### Future Enhancements

1. **Dual-Mode Authentication (Priority)** 🔐
   - Implement `--auth {ccs|direct|auto}` parameter in `video_gen.py`
   - Add Service Account authentication support
   - Add API key authentication support
   - Implement credential discovery system (CLI → ENV → Default paths)
   - Add Vertex AI endpoint support
   - Create auto-detection logic (try CCS first, fallback to Direct)
   - Add helpful error messages for missing credentials

2. **Credential Management System** 🔑
   - Secure credential storage in `./credentials/` folder
   - Support multiple credential profiles
   - Credential validation on startup
   - Environment variable support
   - Integration with `gcloud` CLI credentials

3. **Enhanced video_gen.py** 🎬
   - Dual-mode support (CCS + Direct API)
   - Smart default mode selection
   - Consistent error handling across both modes
   - Progress reporting for both modes
   - Unified output format

4. **MCP Tool** for Claude Code integration
   - Unified tool for both image and video generation
   - Automatic authentication mode selection
   - Progress reporting via MCP protocol
   - Support both CCS and Direct API modes

5. **Web UI** for visual interaction
   - Browser-based interface
   - Real-time preview
   - Gallery management
   - Credential management UI

6. **Configuration file** for persistent settings
   - YAML/JSON config support
   - Multiple profile management (CCS, Direct API, Hybrid)
   - Credential storage (encrypted)
   - Default parameter presets

7. **Batch mode** for multiple generations
   - Queue management
   - Parallel processing
   - Batch status tracking
   - Support both authentication modes

8. **Plugin system** for extensibility
   - Custom model support
   - Post-processing filters
   - Output format converters
   - Custom authentication providers

---

## 🎯 Success Criteria

### Phase 1.5 (Enhancements)
- [x] Can use `memora.gif` and `claude-mem-logomark.webp` together in one request.
- [x] Script does not crash on `.gif` inputs.
- [x] Generated images reflect the style/content of input references.

### Phase 2 (MCP Integration)

- [ ] MCP server runs without errors
- [ ] `generate_image` tool works from Claude Code
- [ ] Configuration is flexible
- [ ] Error handling is robust
- [ ] Documentation is complete

### Phase 3 (Video Support - Dual Mode)

**Dual-Mode Authentication:**
- [ ] `--auth {ccs|direct|auto}` parameter implemented
- [ ] Service Account authentication working
- [ ] API key authentication working (Gemini API)
- [ ] Vertex AI endpoint connection working
- [ ] Credential discovery system implemented (CLI → ENV → Default)
- [ ] Auto-detection mode working (try CCS, fallback to Direct)
- [ ] Clear error messages for missing/invalid credentials

**Video Generation:**
- [x] `video_gen.py` script created (basic structure)
- [ ] CCS mode working (when CCS supports video)
- [ ] Direct API mode working (Vertex AI)
- [ ] Direct API mode working (Gemini API)
- [ ] Async polling reliable for both modes
- [ ] Operation status tracking accurate
- [ ] Timeout handling robust

**Quality & UX:**
- [ ] Error handling covers all cases (both modes)
- [ ] Performance acceptable (2-5 min generation time)
- [ ] User experience smooth
- [ ] Progress reporting clear and informative
- [ ] Output quality meets expectations
- [ ] Consistent behavior between CCS and Direct modes

**Documentation:**
- [x] Direct API integration references added
- [x] Authentication methods documented
- [x] CLI interface design documented
- [x] Credential discovery order documented
- [ ] Setup guide for Service Account credentials
- [ ] Setup guide for API key usage
- [ ] Troubleshooting guide for common issues
- [ ] Migration guide from CCS-only to dual-mode

### Phase 4 (Unified Experience)

- [ ] Both `image_gen.py` and `video_gen.py` support dual-mode
- [ ] Automatic routing based on model capabilities
- [ ] Consistent error handling across both scripts
- [ ] Shared credential management system
- [ ] Shared configuration file support
- [ ] Comprehensive testing suite (unit + integration)
- [ ] Performance benchmarks for both modes

---

## 17) Git Workflow & Branch Strategy

### 17.1 Branch Architecture

**⚠️ CRITICAL: Two separate branches with different purposes**

| Branch | Purpose | Location | Content |
|--------|---------|----------|---------|
| **main** | Source code, design docs, changelogs | Project root | Python scripts, design/, changelog/, README.md |
| **pages** | MkDocs documentation site | Git worktree at `./pages/` | docs/, mkdocs.yml, overrides/ |

### 17.2 Git Worktree Setup

```
claude-code-media-generator/     # main branch
├── design/                      # ✅ Track in main
├── changelog/                   # ✅ Track in main
├── src/                         # ✅ Track in main
├── README.md                    # ✅ Track in main
└── pages/                       # ⚠️ Git worktree (pages branch)
    ├── docs/                    # ✅ Track in pages ONLY
    ├── mkdocs.yml               # ✅ Track in pages ONLY
    └── overrides/               # ✅ Track in pages ONLY
```

### 17.3 ⛔ PROHIBITED Actions

| Action | Why Prohibited |
|--------|----------------|
| `git add pages/*` ใน main branch | pages/ เป็น worktree แยก ไม่ควร track ใน main |
| `git add -f pages/*` ใน main branch | แม้ใช้ `-f` ก็ห้าม เพราะจะทำให้ files ซ้ำซ้อน |
| Commit `pages/docs/*` ใน main | Content อยู่ใน pages branch เท่านั้น |

### 17.4 ✅ Correct Git Workflow

**เมื่อแก้ไข CSS หรือ MkDocs content:**

```bash
# Step 1: เข้าไปใน pages worktree
cd pages/

# Step 2: ตรวจสอบว่าอยู่ถูก branch
git branch
# Output: * pages

# Step 3: Add, commit, push ใน pages branch
git add docs/stylesheets/extra.css
git commit -m "CSS v1.8.x: Description"
git push origin pages
```

**เมื่อแก้ไข design docs หรือ changelog:**

```bash
# Step 1: อยู่ที่ project root (main branch)
cd /path/to/claude-code-media-generator/

# Step 2: ตรวจสอบว่าอยู่ถูก branch
git branch
# Output: * main

# Step 3: Add, commit, push ใน main branch
git add design/pages.design.md changelog/pages.changelog.md
git commit -m "docs: Update design and changelog"
git push origin main
```

### 17.5 Branch-File Mapping

| File/Folder | ✅ Correct Branch | ❌ Wrong Branch |
|-------------|-------------------|-----------------|
| `design/*.design.md` | main | pages |
| `changelog/*.changelog.md` | main | pages |
| `README.md` | main | pages |
| `src/*.py` | main | pages |
| `pages/docs/*` | pages | main |
| `pages/mkdocs.yml` | pages | main |
| `pages/overrides/*` | pages | main |
| `pages/docs/stylesheets/extra.css` | pages | main |

### 17.6 Verification Commands

```bash
# ตรวจสอบว่า pages/ ถูก ignore ใน main
cat .gitignore | grep pages

# ตรวจสอบว่าไม่มี pages files ถูก track ใน main
git ls-files | grep "^pages/"
# Should return empty

# ตรวจสอบ worktree status
git worktree list
```

### 17.7 Recovery: ถ้า commit pages files ไป main ผิดพลาด

```bash
# Step 1: ลบ pages files ออกจาก git tracking (ไม่ลบ file จริง)
git rm --cached pages/docs/stylesheets/extra.css

# Step 2: Commit การลบ
git commit -m "chore: Remove pages/ files from main branch tracking"

# Step 3: Push
git push origin main
```

---

## 18) Smart Defaults System (Metadata-Driven)

> **Version:** 2.0 | **Status:** ✅ Implemented (Phase 2.8.3)
> **Goal:** ลดความซับซ้อนเมื่อ extend video โดยใช้ metadata จาก source video เป็น defaults อัตโนมัติ
> **Updated:** 2026-02-01

### 18.1 Concept Overview

```
New Video:      CLI → config.json[--project] → Code Defaults
Extend Video:   CLI → Source Metadata → config.json[--project] → Code Defaults
```

**Problem:** เมื่อ extend video ต้องพิมพ์ args ซ้ำทุกครั้ง (model, storage-uri, project, etc.)

**Solution:** อ่าน metadata จาก source video แล้วใช้เป็น defaults + รองรับ --project flag

### 18.2 Defaults Priority Chain (Extended)

| Priority | Source | When Used | Description |
|----------|--------|-----------|-------------|
| 1 (Highest) | CLI Arguments | Always | User explicitly sets via command line |
| 2 | Source Metadata | `--extend-video` mode | Inherits from source video's `command_args` |
| 3 | config.json `[--project]` | When `--project` specified | Selected project from multi-project config |
| 4 | config.json `[first project]` | Single project config | Auto-select if only one project exists |
| 5 (Lowest) | Code Defaults | Always | Hardcoded fallbacks in script |

### 18.3 --project Integration

**Design Decision:** `--project` เป็น optional flag ที่ทำงานร่วมกับ metadata system

```
--project Behavior:

1. If --project specified:
   → Use that project's config from config.json

2. If NOT specified + --extend-video:
   → Try to inherit project_id from source metadata
   → If metadata has project_id → use it
   → If no project_id in metadata → use config.json (first/only project)

3. If NOT specified + multi-project config:
   → Error: "Multiple projects in config. Use --project to specify."
   → Or: Auto-select first project with warning

4. If NOT specified + single project config:
   → Auto-select the only project (no warning needed)
```

### 18.4 Inheritable vs Non-Inheritable Args

| Inherit from Metadata ✅ | ไม่ Inherit ❌ | Notes |
|-------------------------|----------------|-------|
| `model` | `prompt` (ต้องใหม่เสมอ) | Model consistency for extend |
| `storage_uri` | `--extend-video` path | User specifies source |
| `project_id` | mode-specific inputs | Project context |
| `resolution` | `--image` / `--last-frame` | Keep resolution consistent |
| `aspect_ratio` | `--reference-*` paths | Keep aspect ratio |
| `api_key` (masked) | Sensitive data | Security: re-fetch from config |
| `location` | | Region consistency |

### 18.5 Metadata Schema Enhancement

**Current metadata (v2.24.1) - Missing fields:**
```json
{
  "command_args": {
    "prompt": "...",
    "model": "veo-3.1-generate-preview",
    "duration": 6,
    "aspect_ratio": "16:9",
    "resolution": "720p",
    "auth_method": "api-key",
    "mode": "text_to_video"
  }
}
```

**Enhanced metadata (v2.25) - Add these fields:**
```json
{
  "command_args": {
    "prompt": "...",
    "model": "veo-3.1-generate-preview",
    "duration": 6,
    "aspect_ratio": "16:9",
    "resolution": "720p",
    "auth_method": "api-key",
    "mode": "text_to_video",
    "project_id": "gen-lang-client-0344941103",
    "storage_uri": "gs://gen-lang-client-0344941103-media-output/videos/",
    "location": "us-central1"
  }
}
```

### 18.6 Metadata Lookup Strategy

**Current metadata naming:**
```
video_20260124_220945_0.mp4
metadata_20260124_220945.json
```

**Lookup algorithm:**
```python
def load_source_metadata(video_path: Path) -> Optional[dict]:
    """Extract and load metadata from source video."""
    # Extract timestamp from video filename
    # video_20260124_220945_0.mp4 → 20260124_220945
    match = re.search(r'(\d{8}_\d{6})', video_path.name)
    if not match:
        return None

    timestamp = match.group(1)
    metadata_path = video_path.parent / f"metadata_{timestamp}.json"

    if not metadata_path.exists():
        return None

    with open(metadata_path) as f:
        data = json.load(f)

    return data.get("command_args", {})
```

### 18.7 Smart Defaults Function

```python
def apply_smart_defaults(args: Namespace, config: ConfigLoader) -> Namespace:
    """Apply smart defaults from metadata and config."""

    sources = {}  # Track where each value came from

    # Step 1: Load source metadata (if extend mode)
    source_meta = None
    if args.extend_video:
        source_meta = load_source_metadata(Path(args.extend_video))

    # Step 2: Define inheritable fields
    inheritable = ['model', 'storage_uri', 'project_id', 'resolution',
                   'aspect_ratio', 'location']

    # Step 3: Apply priority chain for each field
    for field in inheritable:
        cli_value = getattr(args, field, None)

        if cli_value:
            sources[field] = 'CLI'
            continue

        # Try source metadata
        if source_meta and field in source_meta:
            setattr(args, field, source_meta[field])
            sources[field] = 'source metadata'
            continue

        # Try config.json
        config_value = get_config_value(config, field, args.project)
        if config_value:
            setattr(args, field, config_value)
            sources[field] = 'config.json'
            continue

        sources[field] = 'code default'

    # Step 4: Print source summary
    print_defaults_summary(sources, args)

    return args
```

### 18.8 User Experience Examples

**Example 1: First video (no extend)**
```bash
# With config.json (single project)
python video_gen.py "A cat walking"
# → All defaults from config.json automatically

# With config.json (multi-project)
python video_gen.py "A cat walking" --project my-project-1
# → Uses my-project-1 settings from config.json
```

**Example 2: Extend video (Smart Defaults)**
```bash
# Before (ต้องพิมพ์ซ้ำ):
python video_gen.py --extend-video video.mp4 -p "Continue" \
  --storage-uri gs://bucket/out/ --model veo-2.0 \
  --project my-project-1

# After (Smart Defaults v2.0):
python video_gen.py --extend-video video.mp4 -p "Continue"
# → storage_uri, model, project ดึงจาก metadata อัตโนมัติ
```

**Console output:**
```
📋 Smart Defaults Applied
  → model: veo-2.0 (from source metadata)
  → storage_uri: gs://bucket/out/ (from source metadata)
  → project_id: my-project-1 (from source metadata)
  → resolution: 720p (from source metadata)
  → location: us-central1 (from config.json)
```

> **Note:** Video extension is supported by `veo-2.0`, `veo-2.0-exp`, and `veo-3.1-generate-preview`.

### 18.9 Edge Cases

| Scenario | Behavior |
|----------|----------|
| Metadata not found | Warning → fallback to config.json |
| External URL extend | No local metadata → use config only |
| Corrupted metadata | Warning → fallback to config.json |
| CLI override | CLI args always win |
| Multi-project + no --project + no metadata | Error with hint to use --project |
| project_id in metadata not in config | Use metadata value (API key from CLI/env) |

### 18.10 Implementation Phases

| Phase | Tasks | Priority | Status |
|-------|-------|----------|--------|
| **Phase 1** | Metadata schema enhancement (add project_id, storage_uri, location) | High | 🔲 TODO |
| **Phase 2** | `load_source_metadata()` function in video_gen.py | High | 🔲 TODO |
| **Phase 3** | `apply_smart_defaults()` function in video_gen.py | High | 🔲 TODO |
| **Phase 4** | Update config.py - remove active_project logic | Medium | 🔲 TODO |
| **Phase 5** | `--show-defaults` flag (preview without running) | Low | 🔲 Future |
| **Phase 6** | Apply same system to image_gen.py | Low | 🔲 Future |

### 18.11 Config System Integration

**Remove from config.py:**
- `active_project` field in schema
- `active_project` logic in `load()` method

**Keep in config.py:**
- `--project` flag support via `set_active_project()`
- Multi-project config structure
- Environment variable fallback

**Integration Flow:**
```
User runs command
    ↓
Parse CLI args (including --project)
    ↓
If --extend-video:
    → Load source metadata
    → Extract inheritable values
    ↓
Apply smart defaults (metadata → config → code)
    ↓
Execute generation
    ↓
Save metadata (including project_id, storage_uri)
```

### 18.12 Cross-Reference

| Related | Document | Section |
|---------|----------|---------|
| Config System | [config.design.md](./config.design.md) | Schema, API Reference |
| Video Generation | [video.design.md](./video.design.md) | Metadata Schema |
| Image Generation | [image.design.md](./image.design.md) | Future integration |
| GCS Naming | [Section: Cloud Storage](#️-google-cloud-storage-gcs-integration) | Bucket naming pattern |

---

**Document Status:** ✅ Active
**Last Reviewed:** 2026-02-01
**Next Review:** After Phase 2.8.4 (--show-defaults, image_gen.py)
