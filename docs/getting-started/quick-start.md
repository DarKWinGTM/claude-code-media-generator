# Quick Start

Generate your first video and image in minutes.

---

## Prerequisites

- :white_check_mark: Python 3.8+ installed
- :white_check_mark: Dependencies installed (`pip install requests google-auth`)
- :white_check_mark: API key obtained (see [Authentication](authentication.md))

---

## :movie_camera: Video Generation

### Your First Video

```bash
# Set API key
export GOOGLE_API_KEY="AIzaSy..."

# Generate a simple video
python video_gen.py "A cat walking in a garden"
```

Output:
```
🎬 Video Generation via Veo API
Mode: text_to_video
Model: veo-3.1-generate-preview
...
✅ Video saved: generated_videos/video_20260123_123456_0.mp4
```

### With Options

```bash
# Higher quality, longer duration
python video_gen.py "A cat walking in a garden" \
  --model veo-3.1-generate-preview \
  --duration 8 \
  --aspect-ratio 16:9

# Use preset
python video_gen.py "A cat walking" --preset quality
```

---

## :frame_with_picture: Image Generation

### Your First Image

```bash
# Generate an image
python image_gen.py "A futuristic city at sunset"
```

Output:
```
🖼️ Image Generation via Gemini
Model: gemini-3-pro-image-preview
...
✅ Image saved: generated_images/image_20260123_123456.png
```

### With Options

```bash
# Different aspect ratio and resolution
python image_gen.py "A futuristic city" \
  --aspect-ratio 16:9 \
  --image-size 2K
```

---

## :moneybag: Cost Estimation

Before generating, check the cost:

```bash
# Video cost estimate
python video_gen.py "prompt" --estimate-cost

# Image cost estimate
python image_gen.py "prompt" --estimate-cost
```

---

## :control_knobs: Using Presets

Presets provide optimized settings for different use cases:

| Preset | Use Case | Model | Duration |
|--------|----------|-------|----------|
| `quick` | Fast preview | veo-3.1-fast | 5s |
| `quality` | Best quality | veo-3.1-generate | 8s |
| `extend` | Video extension | veo-2.0 | 8s |
| `budget` | Cost saving | veo-2.0 | 5s |

```bash
python video_gen.py "A cat walking" --preset quality
```

---

## :mag: Check Your API

Verify your API key works:

```bash
python check_api.py --key "$GOOGLE_API_KEY"
```

---

## Common First Issues

### "Billing not enabled"
- Video generation requires billing
- Free tier only for images (Gemini 2.0 Flash)

### "Model not found"
- Check model name spelling
- Use `python check_api.py` to list available models

### "Rate limit exceeded"
- Wait a few seconds and retry
- Consider using `--preset budget`

---

## Next Steps

- [Generation Modes](../video/modes.md) - Explore all 8 video modes
- [Video Models](../video/models.md) - Compare Veo 2.0 vs 3.1
- [Presets](../guides/presets.md) - Learn about presets
- [GCS Storage](../guides/gcs-storage.md) - Set up cloud storage
