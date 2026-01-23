# Workflow: Image Generation via Claude Code CLI + CLIProxyAPI + Gemini

## Complete Architecture Flow

```
User Terminal
  ↓
  python image_gen.py "prompt" --model gemini-3-pro-image-preview
  ↓
┌─────────────────────────────────────────────────────────┐
│  image_gen.py (Python Script)                            │
│  - Parse CLI arguments                                   │
│  - Build OpenRouter-compatible payload                   │
│  - Send POST request to CLIProxyAPI                      │
└─────────────────────────────────────────────────────────┘
  ↓
  HTTP POST to http://127.0.0.1:8317/v1/chat/completions
  Headers: Authorization: Bearer ccs-internal-managed
  ↓
┌─────────────────────────────────────────────────────────┐
│  CLIProxyAPI (localhost:8317)                           │
│  - OAuth Proxy Server                                   │
│  - Manages authentication tokens                         │
│  - Routes to Gemini/AntiGravity providers              │
└─────────────────────────────────────────────────────────┘
  ↓
  Retrieves OAuth access token from ~/.ccs/cliproxy/auth/
  ↓
┌─────────────────────────────────────────────────────────┐
│  CCS (Claude Code Switch)                                │
│  - Profile manager                                       │
│  - Internal token management                            │
│  - Auto-refresh OAuth tokens                            │
└─────────────────────────────────────────────────────────┘
  ↓
  Uses stored OAuth tokens (Google/Gemini/AntiGravity)
  ↓
┌─────────────────────────────────────────────────────────┐
│  Gemini Image Generation API                             │
│  Model: gemini-3-pro-image-preview                      │
│  OR: gemini-2.5-flash-image                             │
│                                                          │
│  Request Format (OpenRouter):                           │
│  {                                                       │
│    "model": "gemini-3-pro-image-preview",              │
│    "messages": [{"role": "user", "content": "prompt"}], │
│    "modalities": ["image", "text"],                      │
│    "image_config": {                                     │
│      "aspect_ratio": "1:1",                             │
│      "image_size": "1K"                                 │
│    }                                                     │
│  }                                                       │
└─────────────────────────────────────────────────────────┘
  ↓
  Google Imagen API processes request
  ↓
  Generate image using Imagen 3/4 model
  ↓
┌─────────────────────────────────────────────────────────┐
│  Response Format (OpenRouter-compatible):               │
│  {                                                       │
│    "choices": [{                                        │
│      "message": {                                       │
│        "images": [{                                     │
│          "image_url": {                                 │
│            "url": "data:image/png;base64,..."           │
│          }                                               │
│        }]                                                │
│      }                                                   │
│    }]                                                    │
│  }                                                       │
└─────────────────────────────────────────────────────────┘
  ↓
  CLIProxyAPI returns response with base64 image data
  ↓
┌─────────────────────────────────────────────────────────┐
│  image_gen.py Processing                                │
│  - Parse JSON response                                  │
│  - Extract base64 image data                            │
│  - Decode base64 to binary                              │
│  - Save as PNG file                                     │
│  - Save metadata with prompt                             │
└─────────────────────────────────────────────────────────┘
  ↓
  Output: ./generated_images/
    - image_YYYYMMDD_HHMMSS_0.png
    - metadata_YYYYMMDD_HHMMSS.json (includes prompt)
```

## Component Interactions

### 1. User → image_gen.py
```
Input:
  - Prompt (text description)
  - Model selection (gemini-3-pro-image-preview or gemini-2.5-flash-image)
  - Aspect ratio (1:1, 3:4, 4:3, 9:16, 16:9)
  - Image size (1K, 2K, 4K)

Output:
  - PNG image file
  - Metadata JSON with prompt
```

### 2. image_gen.py → CLIProxyAPI
```
Endpoint: http://127.0.0.1:8317/v1/chat/completions
Method: POST
Headers:
  - Authorization: Bearer ccs-internal-managed
  - Content-Type: application/json

Body: OpenRouter-compatible request with modalities and image_config
```

### 3. CLIProxyAPI → CCS
```
Authentication: ccs-internal-managed
Token Source: ~/.ccs/cliproxy/auth/gemini-*.json
Token Type: OAuth 2.0 Bearer token
Provider: Google (Gemini/AntiGravity)
```

### 4. CCS → Gemini API
```
API: Google Imagen API
Endpoint: https://generativelanguage.googleapis.com/
Authentication: OAuth 2.0
Models:
  - gemini-3-pro-image-preview (Imagen 3)
  - gemini-2.5-flash-image (Imagen 2.5 Flash)
```

### 5. Gemini API → CLIProxyAPI
```
Response: OpenRouter-compatible format
Image Format: Base64-encoded PNG
MIME Type: data:image/png;base64
```

### 6. CLIProxyAPI → image_gen.py
```
Response Format:
  {
    "choices": [{
      "message": {
        "images": [{
          "image_url": {
            "url": "data:image/png;base64,iVBORw0KG..."
          }
        }]
      }
    }]
  }
```

## Data Flow Summary

```
┌──────────────┐
│ User Command │
└──────┬───────┘
       │ "prompt text"
       ↓
┌──────────────────┐
│ image_gen.py     │ → Build payload
└──────┬───────────┘
       │ HTTP POST + ccs-internal-managed
       ↓
┌──────────────────┐
│ CLIProxyAPI      │ → Get OAuth token
└──────┬───────────┘
       │ Bearer token
       ↓
┌──────────────────┐
│ Gemini Imagen    │ → Generate image
│   API            │
└──────┬───────────┘
       │ Base64 image
       ↓
┌──────────────────┐
│ CLIProxyAPI      │ → Return response
└──────┬───────────┘
       │ JSON with base64
       ↓
┌──────────────────┐
│ image_gen.py     │ → Decode & save
└──────┬───────────┘
       │ PNG file + metadata.json
       ↓
┌──────────────────┐
│ File System      │ → ./generated_images/
└──────────────────┘
```

## Key Configuration Files

| File | Purpose |
|------|---------|
| `~/.ccs/config.yaml` | CCS profile configuration |
| `~/.ccs/gemini.settings.json` | Gemini profile settings |
| `~/.ccs/cliproxy/auth/*.json` | OAuth tokens |
| `image_gen.py` | Main script |
| `./generated_images/metadata_*.json` | Prompt and image metadata |

## Authentication Flow

```
1. image_gen.py sends: Authorization: Bearer ccs-internal-managed
2. CLIProxyAPI receives ccs-internal-managed token
3. CLIProxyAPI queries CCS for actual OAuth token
4. CCS retrieves from ~/.ccs/cliproxy/auth/gemini-*.json
5. CLIProxyAPI uses OAuth token to call Gemini API
6. Gemini API validates OAuth token
7. Image generation proceeds
8. Response returns through same path
```

## Model Options

| Model | Quality | Speed | Best For |
|-------|---------|-------|----------|
| `gemini-3-pro-image-preview` | High | Slower | Detailed artwork |
| `gemini-2.5-flash-image` | Medium | Faster | Quick iterations |

## Configuration Options

### Aspect Ratios
- `1:1` - Square (default)
- `3:4` - Portrait
- `4:3` - Standard
- `9:16` - Mobile portrait
- `16:9` - Widescreen

### Image Sizes
- `1K` - ~1024px (fastest, default)
- `2K` - ~2048px (balanced)
- `4K` - ~4096px (highest quality)
