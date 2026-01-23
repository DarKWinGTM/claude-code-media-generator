# 🎬 Feature Request: Add Veo Video Generation Support (Similar to Image Generation)

## 📋 Summary

Add support for Google's **Veo 3.1 video generation models** to CLIProxyAPI, following the same architecture pattern as the existing `gemini-3-pro-image-preview` image generation support.

This would enable users to generate videos through CLIProxyAPI using the same client format with `modalities: ["video", "text"]` and `video_config`, just like how image generation currently works.

**GitHub Issue:** https://github.com/router-for-me/CLIProxyAPIPlus/issues/94

---

## 🎯 Current State (Image Generation)

**Currently working perfectly:**
```python
# image_gen.py - Image Generation (✅ Working)
POST http://127.0.0.1:8317/v1/chat/completions
Authorization: Bearer ccs-internal-managed

{
  "model": "gemini-3-pro-image-preview",
  "messages": [{"role": "user", "content": "A cute cat"}],
  "modalities": ["image", "text"],
  "image_config": {
    "aspect_ratio": "1:1",
    "image_size": "1K"
  }
}
```

**Response:**
```json
{
  "choices": [{
    "message": {
      "images": [{
        "image_url": {"url": "data:image/png;base64,..."}
      }]
    }
  }]
}
```

---

## 💻 Reference Implementation (image_gen.py)

**CLIProxyAPI ใช้ format translation mechanism ของตัวเอง** - ไม่ได้ผ่าน OpenRouter

### Key Code Sections

**1. Authentication (lines 41-44)**
```python
HEADERS = {
    "Authorization": "Bearer ccs-internal-managed",
    "Content-Type": "application/json"
}
```

**2. Request Payload (lines 94-107)**
```python
payload = {
    "model": model,
    "messages": [
        {
            "role": "user",
            "content": prompt
        }
    ],
    "modalities": ["image", "text"],
    "image_config": {
        "aspect_ratio": aspect_ratio,
        "image_size": image_size
    }
}
```

**3. Response Handling (lines 129-189)**
```python
result = response.json()

if result.get("choices"):
    message = result["choices"][0].get("message", {})

    if message.get("images"):
        for i, image_obj in enumerate(message["images"]):
            image_url = image_obj.get("image_url", {}).get("url", "")

            if image_url:
                # Save base64 image data
                saved_path = save_image(image_url, output_dir, i)
```

---

## 🎬 Requested Feature (Video Generation)

**Desired behavior:**
```python
# video_gen.py - Video Generation (🎯 Desired)
POST http://127.0.0.1:8317/v1/chat/completions
Authorization: Bearer ccs-internal-managed

{
  "model": "veo-3.1-generate-preview",
  "messages": [{"role": "user", "content": "A cat playing with a ball"}],
  "modalities": ["video", "text"],
  "video_config": {
    "duration_seconds": "8",
    "aspect_ratio": "16:9",
    "resolution": "720p"
  }
}
```

**Expected Response:**
```json
{
  "choices": [{
    "message": {
      "videos": [{
        "video_url": {"url": "data:video/mp4;base64,..."}
      }]
    }
  }]
}
```

---

## 📊 Google Veo API Details

### Official REST API Format

**Endpoint:**
```http
POST https://generativelanguage.googleapis.com/v1beta/models/veo-3.1-generate-preview:predictLongRunning
```

**Request Format (Google Native):**
```json
{
  "instances": [{
    "prompt": "A cinematic shot of a majestic lion in the savannah"
  }],
  "parameters": {
    "aspectRatio": "16:9",
    "resolution": "720p",
    "durationSeconds": "8"
  }
}
```

**Response (Async Operation):**
```json
{
  "name": "operations/veo-3.1-generate-preview/123456789",
  "done": false,
  "metadata": {
    "@type": "type.googleapis.com/google.api.Operation"
  }
}
```

**Poll for Completion:**
```http
GET https://generativelanguage.googleapis.com/v1beta/{operation_name}
```

**Final Response:**
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

### Supported Veo Models

| Model | Type | Resolution | Duration |
|-------|------|------------|----------|
| `veo-3.1-generate-preview` | Preview | 720p, 1080p | 4s, 6s, 8s |
| `veo-3.1-fast-generate-preview` | Fast Preview | 720p, 1080p | 4s, 6s, 8s |
| `veo-3.0-generate-001` | Stable | 720p, 1080p | 8s |
| `veo-3.0-fast-generate-001` | Fast Stable | 720p, 1080p | 8s |

### Key Parameters

| Parameter | Type | Values | Description |
|-----------|------|--------|-------------|
| `prompt` | string | - | Text description for video |
| `aspectRatio` | string | `"16:9"`, `"9:16"` | Video aspect ratio |
| `resolution` | string | `"720p"`, `"1080p"` | Video resolution |
| `durationSeconds` | string | `"4"`, `"6"`, `"8"` | Video length in seconds |
| `negativePrompt` | string | - | What NOT to include |

---

## 🏗️ Proposed Implementation

### 1. Client Format (Input) - CLIProxyAPI Format

**Video Generation Request:**
```json
{
  "model": "veo-3.1-generate-preview",
  "messages": [{"role": "user", "content": "Prompt text here"}],
  "modalities": ["video", "text"],
  "video_config": {
    "duration_seconds": "8",
    "aspect_ratio": "16:9",
    "resolution": "720p",
    "negative_prompt": "cartoon, drawing, low quality"
  }
}
```

### 2. Translation Logic (Go)

**CLIProxyAPI Format → Google Veo Format:**
```go
func translateVideoRequest(req CLIProxyAPIRequest) VeoRequest {
    config := req.VideoConfig

    return VeoRequest{
        Instances: []VeoInstance{
            {Prompt: extractPrompt(req.Messages)},
        },
        Parameters: VeoParameters{
            AspectRatio: translateAspectRatio(config.AspectRatio),
            Resolution: config.Resolution,
            DurationSeconds: config.DurationSeconds,
            NegativePrompt: config.NegativePrompt,
        },
    }
}
```

### 3. Response Translation

**Google Veo → CLIProxyAPI Format:**
```go
func translateVideoResponse(resp VeoResponse) CLIProxyAPIResponse {
    video := resp.Response.GenerateVideoResponse.GeneratedSamples[0].Video

    return CLIProxyAPIResponse{
        Choices: []Choice{
            {
                Message: Message{
                    Videos: []Video{
                        {
                            VideoURL: VideoURL{
                                URL: "data:video/mp4;base64," + video.VideoBytes,
                            },
                        },
                    },
                },
            },
        },
    }
}
```

### 4. Async Operation Handling

**Video generation is asynchronous** - needs polling:

```go
func waitForVideoOperation(operationName string) (*VeoResponse, error) {
    for {
        resp := getOperation(operationName)

        if resp.Done {
            return resp.Response, nil
        }

        time.Sleep(10 * time.Second)
    }
}
```

### 5. Model Routing

**Add to routing table:**
```go
var modelRoutes = map[string]string{
    // Image models (existing)
    "gemini-3-pro-image-preview": "imagen-3.0-generate-001",

    // Video models (new)
    "veo-3.1-generate-preview": "veo-3.1-generate-preview:predictLongRunning",
    "veo-3.1-fast-generate-preview": "veo-3.1-fast-generate-preview:predictLongRunning",
    "veo-3.0-generate-001": "veo-3.0-generate-001:predictLongRunning",
    "veo-3.0-fast-generate-001": "veo-3.0-fast-generate-001:predictLongRunning",
}
```

---

## 📐 Architecture Comparison

### Image Generation (Current)

```
Client Request (CLIProxyAPI format)
    ↓
CLIProxyAPI Translator
    ↓
Query CCS for OAuth token
    ↓
Google Imagen API (synchronous)
    ↓
CLIProxyAPI Translator
    ↓
Client Response (CLIProxyAPI format)
```

### Video Generation (Proposed)

```
Client Request (CLIProxyAPI format)
    ↓
CLIProxyAPI Translator
    ↓
Query CCS for OAuth token
    ↓
Google Veo API (asynchronous)
    ↓
CLIProxyAPI Poller (poll until done)
    ↓
CLIProxyAPI Translator
    ↓
Client Response (CLIProxyAPI format)
```

---

## 🔧 Key Differences from Image Generation

| Aspect | Image Generation | Video Generation |
|--------|-----------------|-------------------|
| **Response Type** | Synchronous | Asynchronous (Long-running operation) |
| **Processing Time** | ~5-30 seconds | 1-5 minutes |
| **Polling Required** | No | Yes |
| **Response Format** | Direct base64 | URI + async polling |
| **Output Size** | ~100KB-2MB | ~5-20MB |
| **Endpoint** | `:predict` | `:predictLongRunning` |

---

## ✅ Acceptance Criteria

### Must Have

- [ ] Support `veo-3.1-generate-preview` model
- [ ] Accept CLIProxyAPI format with `modalities: ["video", "text"]`
- [ ] Accept `video_config` parameters:
  - `duration_seconds`
  - `aspect_ratio`
  - `resolution`
  - `negative_prompt`
- [ ] Translate CLIProxyAPI → Google Veo format
- [ ] Handle async operation (polling mechanism)
- [ ] Translate response back to CLIProxyAPI format
- [ ] Return video as base64 data URI (`data:video/mp4;base64,...`)
- [ ] Use OAuth authentication from CCS (same as image)
- [ ] Route through `gemini` provider channel

### Nice to Have

- [ ] Support `veo-3.1-fast-generate-preview` (faster generation)
- [ ] Support `veo-3.0-generate-001` (stable version)
- [ ] Support image-to-video generation
- [ ] Support video extension
- [ ] Progress reporting during polling

---

## 📚 References

### Related Discussion

**Discussion #378:** [Image gen not supported/enabled for gemini-3-pro-image-preview?](https://github.com/router-for-me/CLIProxyAPI/discussions/378#discussion-9192920)

This discussion contains working code examples for image generation via CLIProxyAPI, which served as the reference implementation for this video generation feature request.

### Reference Implementation

**File:** `image_gen.py`

**Key Sections:**
- **Lines 41-44:** CCS OAuth authentication headers
- **Lines 94-107:** Request payload structure
- **Lines 129-189:** Response parsing and image extraction

### Google Official Documentation

- **Video API Docs:** https://ai.google.dev/gemini-api/docs/video
- **REST API:** `POST /v1beta/models/veo-3.1-generate-preview:predictLongRunning`
- **Async Operations:** Poll `GET /v1beta/{operation_name}` until `done: true`

---

## 📝 Additional Notes

### Authentication

Video generation should use the **same OAuth authentication** as image generation:
- Token source: `~/.ccs/cliproxy/auth/gemini-*.json`
- Header: `Authorization: Bearer ccs-internal-managed`
- Provider: `gemini` channel

### Compatibility

- **Backward compatible:** Does not break existing image generation
- **Same endpoint:** `/v1/chat/completions`
- **Same auth method:** `ccs-internal-managed`
- **New modality:** `video` (in addition to existing `image`, `text`)

### Error Handling

- Handle operation timeout
- Handle quota exceeded
- Handle polling failures
- Handle video generation failures

---

## 🙏 Thank You

This feature would enable video generation capabilities through CLIProxyAPI using the same clean, consistent interface that makes image generation so easy to use!

---

**Labels:** `enhancement`, `video generation`, `veo`, `feature request`
**Priority:** Medium
**Complexity:** Medium (requires async polling, similar architecture to existing image generation)
**Status:** Open - https://github.com/router-for-me/CLIProxyAPIPlus/issues/94
