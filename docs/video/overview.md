# Video Generation Overview

Generate AI videos using Google Veo models with 8 different generation modes.

---

## Supported Models

| Model | Audio | Extension | Quality | Cost |
|-------|-------|-----------|---------|------|
| **veo-3.1-generate-preview** | :white_check_mark: Yes | :x: No | Best | $$$ |
| **veo-3.1-fast-generate-preview** | :white_check_mark: Yes | :x: No | Good | $$ |
| **veo-2.0** | :x: No | :white_check_mark: Yes | Good | $$ |
| **veo-2.0-exp** | :x: No | :white_check_mark: Yes | Good | $$ |

!!! tip "Recommended"
    Use `veo-3.1-generate-preview` for best quality with audio.

---

## Generation Modes

| Mode | Description | Required Inputs |
|------|-------------|-----------------|
| [Text-to-Video](modes.md#text-to-video) | Generate from text only | Prompt |
| [Image-to-Video](modes.md#image-to-video) | Animate from image | Prompt + Image |
| [First & Last Frames](modes.md#first-last-frames) | Interpolate two images | Prompt + 2 Images |
| [Video Extension](extension.md) | Extend existing video | Prompt + Video |
| [Reference Asset](reference-images.md#reference-asset) | Preserve subject | Prompt + 1-3 Images |
| [Reference Style](reference-images.md#reference-style) | Apply style | Prompt + Style Image |
| [Insert Objects](modes.md#insert-objects) | Add to video | Prompt + Video + Mask |
| [Remove Objects](modes.md#remove-objects) | Remove from video | Prompt + Video + Mask |

---

## Quick Examples

```bash
# Text-to-Video
python video_gen.py "A cat walking in a garden"

# Image-to-Video
python video_gen.py "The cat starts running" --image cat.jpg

# Video Extension
python video_gen.py "Continue the scene" --extend-video previous.mp4

# Reference Asset (preserve subject)
python video_gen.py "A person dancing" --reference-image avatar.png:asset
```

---

## Output Specifications

| Setting | Options |
|---------|---------|
| **Duration** | 5s, 6s, 7s, 8s (default: 5s) |
| **Aspect Ratio** | 16:9, 9:16, 1:1 |
| **Resolution** | 720p (16:9), 1280x720 |
| **Frame Rate** | 24 fps |
| **Format** | MP4 |

---

## API Method

Video generation uses `predictLongRunning` (asynchronous):

```
Request → Operation ID → Poll for completion → Download video
```

Typical generation time: 60-180 seconds

---

## Cost Overview

| Model | Per Video (5s) |
|-------|---------------|
| veo-3.1-generate-preview | ~$0.50 |
| veo-3.1-fast-generate-preview | ~$0.25 |
| veo-2.0 | ~$0.20 |

!!! info "Cost Estimation"
    Use `--estimate-cost` to preview cost before generating.

---

## Learn More

- [Generation Modes](modes.md) - All 8 modes in detail
- [Video Models](models.md) - Model comparison
- [Reference Images](reference-images.md) - Using reference images
- [Video Extension](extension.md) - Extending videos
