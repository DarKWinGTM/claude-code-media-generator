# 🏗️ System Architecture: CLIProxyAPI Image & Video Generator

## Document Overview

**Version:** 2.1
**Last Updated:** 2026-01-14
**Purpose:** System structure and component architecture documentation

**Documentation v2.1 Changes:**
- Consolidated with information from research and workflow documents
- Aligned with README.md and design.md structure

---

## 📋 Table of Contents

1. [System Overview](#system-overview)
2. [Component Architecture](#component-architecture)
3. [Data Flow](#data-flow)
4. [Technology Stack](#technology-stack)
5. [Deployment Architecture](#deployment-architecture)
6. [Authentication Flow](#authentication-flow)
7. [API Integration](#api-integration)
8. [File System Structure](#file-system-structure)

---

## 🎯 System Overview

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│  Client Layer                                               │
│                                                             │
│  - image_gen.py (Python CLI)                               │
│  - video_gen.py (Planned)                                  │
│  - MCP Tool (Planned)                                      │
└────────────────┬────────────────────────────────────────────┘
                 │ HTTP POST http://127.0.0.1:8317/v1/chat/completions
                 ↓
┌─────────────────────────────────────────────────────────────┐
│  CLIProxyAPI (Main Request Handler)                         │
│  localhost:8317                                             │
│                                                             │
│  - Request translation                                      │
│  - OAuth management                                         │
│  - Provider routing                                         │
│  - Response translation                                     │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ├─────────────────────────────────────┐
                 │ Token Query (Internal Only)         │
                 ↓                                     ↓
┌──────────────────────────┐              ┌──────────────────────────────┐
│  CCS (Extension)          │              │  Google AI API              │
│  ~/.ccs/cliproxy/auth/    │              │  - Imagen API               │
│                          │              │  - Veo API (Planned)        │
│  - OAuth token storage    │              │                              │
│  - Auto-refresh           │              │  - Image generation         │
│  - Profile management     │              │  - Video generation         │
└──────────────────────────┘              └──────────────────────────────┘
```

### Core Principles

1. **CLIProxyAPI as Main Handler** - All requests go through CLIProxyAPI
2. **CCS as Token Provider** - CCS only provides OAuth tokens (not in request load path)
3. **Consistent Interface** - Image and video use same format
4. **OAuth Authentication** - CCS internal managed tokens

---

## 🧩 Component Architecture

### 1. CLIProxyAPI (Main Request Handler)

**Location:** localhost:8317
**Language:** Go
**Purpose:** OAuth Proxy Server + Format Translator

**Responsibilities:**
- Receive client requests
- Detect `ccs-internal-managed` authentication
- Query CCS for OAuth tokens
- Translate request formats (Client → Google)
- Route to appropriate providers
- Handle async operations (video polling)
- Translate response formats (Google → Client)

**Key Functions:**
```
- Format Translation
  - CLIProxyAPI format → Google Imagen/Veo format

- Authentication Management
  - Detect ccs-internal-managed
  - Query CCS for actual OAuth tokens

- Provider Routing
  - gemini-3-pro-image-preview → Imagen API
  - veo-3.1-generate-preview → Veo API

- Response Processing
  - Sync responses (Image)
  - Async polling (Video)
```

### 2. CCS (Claude Code Switch)

**Location:** ~/.ccs/
**Type:** Extension
**Purpose:** Profile Manager + Token Provider

**Responsibilities:**
- Store OAuth tokens securely
- Auto-refresh tokens before expiry
- Manage multiple provider profiles
- Provide `ccs-internal-managed` authentication method

**Token Storage Structure:**
```
~/.ccs/cliproxy/auth/
├── gemini-user@gmail.com-all.json
├── antigravity-user@gmail.com.json
└── kiro-aws-user@gmail.com.json
```

**Token Format:**
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

### 3. Client Layer

#### image_gen.py (Current)

**Language:** Python
**Status:** ✅ Working
**Purpose:** Generate images via CLIProxyAPI

**Features:**
- CLI argument parsing
- Request payload construction
- Response parsing
- Base64 image decoding
- File output management
- Metadata tracking

**Request Flow:**
```
python image_gen.py "A cute cat" --aspect-ratio 16:9 --image-size 2K
  ↓
Build CLIProxyAPI payload
  ↓
POST to http://127.0.0.1:8317/v1/chat/completions
  ↓
Parse response & save image
```

#### MCP Tool (Planned)

**Type:** Model Context Protocol Server
**Status:** 🚧 Phase 2
**Purpose:** Integrate image/video generation with Claude Code CLI

**Benefits:**
- Seamless Claude Code integration
- Direct file path returns
- Multi-step workflow support
- No need to leave Claude Code

---

## 🔄 Data Flow

### Image Generation (Synchronous)

```
Step 1: User Request
  python image_gen.py "A sunset" --aspect-ratio 16:9
  ↓
Step 2: Build Request
  payload = {
    "model": "gemini-3-pro-image-preview",
    "messages": [{"role": "user", "content": "A sunset"}],
    "modalities": ["image", "text"],
    "image_config": {
      "aspect_ratio": "16:9",
      "image_size": "1K"
    }
  }
  ↓
Step 3: Send to CLIProxyAPI
  POST http://127.0.0.1:8317/v1/chat/completions
  Headers: Authorization: Bearer ccs-internal-managed
  ↓
Step 4: CLIProxyAPI Processing
  - Detect ccs-internal-managed
  - Query CCS for OAuth token
  - Translate to Google Imagen format
  ↓
Step 5: Call Google Imagen API
  POST https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-001:predict
  Headers: Authorization: Bearer ya29.a0Aa...
  ↓
Step 6: Wait for Response (5-30 seconds)
  - Google API processes request
  - Returns base64 image data
  ↓
Step 7: Response Translation
  Google format → CLIProxyAPI format
  ↓
Step 8: Client Processing
  - Parse JSON response
  - Extract base64 data
  - Decode and save as PNG
  - Save metadata
  ↓
Output: ./generated_images/image_YYYYMMDD_HHMMSS.png
```

### Video Generation (Planned - Asynchronous)

```
Step 1: User Request
  python video_gen.py "A cat playing" --duration 8
  ↓
Step 2: Build Request
  payload = {
    "model": "veo-3.1-generate-preview",
    "messages": [{"role": "user", "content": "A cat playing"}],
    "modalities": ["video", "text"],
    "video_config": {
      "duration_seconds": "8",
      "aspect_ratio": "16:9",
      "resolution": "720p"
    }
  }
  ↓
Step 3: Send to CLIProxyAPI
  POST http://127.0.0.1:8317/v1/chat/completions
  ↓
Step 4: CLIProxyAPI Processing
  - Query CCS for OAuth token
  - Translate to Google Veo format
  ↓
Step 5: Call Google Veo API (Long-Running)
  POST .../veo-3.1-generate-preview:predictLongRunning
  ↓
Step 6: Receive Operation Name
  {
    "name": "operations/veo-3.1-generate-preview/123456789",
    "done": false
  }
  ↓
Step 7: Poll Operation (1-5 minutes)
  GET .../operations/{operation_name}
  - Wait 10 seconds between polls
  - Check if "done": true
  ↓
Step 8: Download Video
  - Extract video URI from response
  - Download video file
  - Convert to base64
  ↓
Step 9: Response Translation
  Google format → CLIProxyAPI format
  ↓
Step 10: Client Processing
  - Parse JSON response
  - Extract base64 data
  - Decode and save as MP4
  ↓
Output: ./generated_videos/video_YYYYMMDD_HHMMSS.mp4
```

---

## 🔧 Technology Stack

### Client Layer

| Component | Technology | Version | Purpose |
|-----------|-----------|---------|---------|
| image_gen.py | Python | 3.x | Image generation client |
| video_gen.py | Python | 3.x | Video generation client (planned) |
| MCP Server | Python/TypeScript | - | Claude Code integration (planned) |

### Server Layer

| Component | Technology | Version | Purpose |
|-----------|-----------|---------|---------|
| CLIProxyAPI | Go | Latest | OAuth proxy + format translator |
| CCS | Shell/Python | - | Profile + token management |

### API Layer

| Provider | API | Authentication | Models |
|----------|-----|----------------|--------|
| Google AI | Imagen API | OAuth 2.0 | gemini-3-pro-image-preview, gemini-2.5-flash-image |
| Google AI | Veo API | OAuth 2.0 | veo-3.1-generate-preview (planned) |

### Data Formats

| Purpose | Format | Notes |
|---------|--------|-------|
| Request (Client) | CLIProxyAPI format | OpenRouter-compatible |
| Request (Google) | Google native format | instances/parameters |
| Response (Client) | CLIProxyAPI format | choices/message/images |
| Image Output | PNG | Base64 encoded |
| Video Output | MP4 | Base64 encoded |

---

## 🚀 Deployment Architecture

### Development Environment

```
Local Machine
├── CLIProxyAPI Server
│   ├── Port: 8317
│   ├── Location: localhost
│   └── Status: Running
├── CCS Extension
│   ├── Location: ~/.ccs/
│   └── Config: ~/.ccs/config.yaml
├── Client Scripts
│   ├── image_gen.py (working)
│   └── video_gen.py (planned)
└── Output Directories
    ├── ./generated_images/
    └── ./generated_videos/ (planned)
```

### Configuration Files

| File | Location | Purpose |
|------|----------|---------|
| CCS Config | ~/.ccs/config.yaml | Main CCS configuration |
| Gemini Settings | ~/.ccs/gemini.settings.json | Gemini profile settings |
| OAuth Tokens | ~/.ccs/cliproxy/auth/*.json | OAuth token storage |
| CCS Auth | ~/.ccs/cliproxy/auth/gemini-*.json | Gemini OAuth tokens |

### Network Flow

```
Client (localhost)
    ↓
CLIProxyAPI (localhost:8317)
    ↓
Google AI API (generativelanguage.googleapis.com)
    ↓
Response back through same path
```

---

## 🔐 Authentication Flow

### Special Token: `ccs-internal-managed`

**NOT a real token** - It's a special identifier that tells CLIProxyAPI:
- "Don't use this header as actual authentication"
- "Query CCS for the real OAuth token instead"
- "CCS will handle token refresh automatically"

### Authentication Chain

```
Step 1: Client sends request
  Headers: Authorization: Bearer ccs-internal-managed
  ↓
Step 2: CLIProxyAPI receives request
  - Sees "ccs-internal-managed"
  - Recognizes need for OAuth token
  ↓
Step 3: CLIProxyAPI queries CCS
  - Reads: ~/.ccs/cliproxy/auth/gemini-*.json
  - Extracts: access_token
  ↓
Step 4: CCS provides OAuth token
  - Returns: ya29.a0Aa7pCA98eIgxJGjCbQB5...
  - Includes: expiry information
  ↓
Step 5: CLIProxyAPI calls Google API
  Headers: Authorization: Bearer ya29.a0Aa...
  ↓
Step 6: Google validates token
  - OAuth 2.0 validation
  - Grants access if valid
  ↓
Step 7: Request processed
  - Image/video generation
  - Response returned
```

### Token Refresh Flow

```
CLIProxyAPI detects token expiration
  ↓
Auto-refresh triggered
  ↓
CCS uses refresh_token
  ↓
New access_token generated
  ↓
Updated in ~/.ccs/cliproxy/auth/
  ↓
CLIProxyAPI uses new token
```

---

## 🌐 API Integration

### Google Imagen API

**Endpoint:** `https://generativelanguage.googleapis.com/v1beta/models/imagen-3.0-generate-001:predict`

**Request Format:**
```json
{
  "instances": [{
    "prompt": "A cute cat playing with a ball"
  }],
  "parameters": {
    "sampleCount": 1,
    "aspectRatio": "1:1"
  }
}
```

**Response Format:**
```json
{
  "predictions": [{
    "bytesBase64Encoded": true,
    "mimeType": "image/png",
    "data": "iVBORw0KGgoAAAANSUhEUgAA..."
  }]
}
```

### Google Veo API (Planned)

**Endpoint:** `https://generativelanguage.googleapis.com/v1beta/models/veo-3.1-generate-preview:predictLongRunning`

**Request Format:**
```json
{
  "instances": [{
    "prompt": "A cinematic shot of a lion in the savannah"
  }],
  "parameters": {
    "aspectRatio": "16:9",
    "resolution": "720p",
    "durationSeconds": "8"
  }
}
```

**Initial Response:**
```json
{
  "name": "operations/veo-3.1-generate-preview/123456789",
  "done": false
}
```

**Final Response (after polling):**
```json
{
  "name": "operations/...",
  "done": true,
  "response": {
    "generateVideoResponse": {
      "generatedSamples": [{
        "video": {
          "uri": "https://generativelanguage.googleapis.com/v1beta/..."
        }
      }]
    }
  }
}
```

---

## 📁 File System Structure

### Project Structure

```
claude-code-image-generator/
├── image_gen.py                 # Image generation client (working)
├── video_gen.py                 # Video generation client (planned)
├── design.md                    # Design document with roadmaps
├── architecture.md              # This file - system architecture
├── README.md                    # Project overview
├── issue/                       # GitHub issues
│   └── veo-video-generation-support.md
├── generated_images/            # Output directory
│   ├── image_YYYYMMDD_HHMMSS_0.png
│   └── metadata_YYYYMMDD_HHMMSS.json
└── generated_videos/            # Output directory (planned)
    └── video_YYYYMMDD_HHMMSS.mp4
```

### CCS Directory Structure

```
~/.ccs/
├── config.yaml                  # Main CCS configuration
├── gemini.settings.json         # Gemini profile settings
└── cliproxy/
    └── auth/
        ├── gemini-user@gmail.com-all.json
        ├── antigravity-user@gmail.com.json
        └── kiro-aws-user@gmail.com.json
```

---

## 🔑 Key Architecture Decisions

### 1. CLIProxyAPI as Main Handler

**Decision:** All requests flow through CLIProxyAPI
**Rationale:** Centralized authentication, format translation, and routing
**Impact:** Simplifies client code, consistent interface

### 2. CCS as Token Provider Only

**Decision:** CCS not in request load path
**Rationale:** Separation of concerns, CCS focuses on token management
**Impact:** Cleaner architecture, CCS can be updated independently

### 3. Synchronous Image, Asynchronous Video

**Decision:** Different processing models for image vs video
**Rationale:** Image generation is fast (<30s), video takes longer (1-5min)
**Impact:** Different client handling patterns, better user experience

### 4. OAuth Authentication

**Decision:** Use OAuth 2.0 with CCS token management
**Rationale:** No hardcoded API keys, auto-refresh, multi-provider support
**Impact:** Secure, scalable, provider-agnostic

---

## 📊 Performance Considerations

### Image Generation

| Metric | Expected Value | Notes |
|--------|----------------|-------|
| Generation Time | 5-30 seconds | Depends on model and size |
| Output Size | 100KB - 2MB | Base64 encoded |
| Success Rate | ~95% | With proper error handling |
| Rate Limits | As configured in CCS | Provider-dependent |

### Video Generation (Expected)

| Metric | Expected Value | Notes |
|--------|----------------|-------|
| Generation Time | 1-5 minutes | Async polling required |
| Output Size | 5-20MB | Base64 encoded |
| Success Rate | ~90% | More complex than images |
| Polling Interval | 10 seconds | Optimal balance |

---

## 🔒 Security Architecture

### OAuth Token Security

**Storage:**
- Location: `~/.ccs/cliproxy/auth/`
- Format: Encrypted JSON
- Permissions: User-readable only

**Token Lifecycle:**
- Lifetime: ~1 hour (3600 seconds)
- Auto-refresh: Handled by CCS
- Rotation: Automatic on expiry

**Best Practices:**
- Never log OAuth tokens
- Never commit tokens to version control
- Use `.gitignore` for auth files
- Monitor usage to avoid quota exhaustion

---

## ✅ Summary

**Architecture Principles:**

1. **CLIProxyAPI** - Main request handler and format translator
2. **CCS** - Token provider and profile manager (not in request path)
3. **OAuth** - Secure authentication with auto-refresh
4. **Consistency** - Same interface for image and video
5. **Separation** - Clear boundaries between components

**Request Flow:**
```
Client → CLIProxyAPI → Google API
          ↑
    CCS (token only)
```

**Key Points:**
- Request load does NOT pass through CCS
- CCS provides OAuth tokens only
- CLIProxyAPI handles all translation and routing
- Image generation is synchronous
- Video generation requires async polling

---

**Document Status:** ✅ Active
**Last Reviewed:** 2026-01-14
**Next Review:** After MCP Tool implementation
