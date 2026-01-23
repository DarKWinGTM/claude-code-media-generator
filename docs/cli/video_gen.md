# CLI Reference: video_gen.py

Complete command-line reference for video generation.

---

## Synopsis

```bash
python video_gen.py PROMPT [OPTIONS]
```

---

## Arguments

### Required

| Argument | Description |
|----------|-------------|
| `PROMPT` | Text description of the video to generate |

---

## Options

### Model & Output

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--model` | string | veo-3.1-generate-preview | Model to use |
| `--duration` | int | 5 | Video duration (5-8 seconds) |
| `--aspect-ratio` | string | 16:9 | 16:9, 9:16, or 1:1 |
| `--output` | path | generated_videos/ | Output directory |

### Presets

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--preset` | string | - | quick, quality, extend, budget |

### Authentication

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--key` | string | $GOOGLE_API_KEY | API key or OAuth token |
| `--project` | string | default | Config project name |

### Images

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--image` | path | - | First frame image |
| `--last-frame` | path | - | Last frame image |
| `--reference-image` | PATH:TYPE | - | Reference image (asset/style) |

### Video Extension

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--extend-video` | path/URI | - | Video to extend |
| `--storage-uri` | gs:// | - | GCS bucket for uploads |

### Mask Operations

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--video` | path | - | Source video for mask ops |
| `--mask` | path | - | Mask image |
| `--mask-mode` | string | - | insert or remove |
| `--resize-mode` | string | - | Resize mode for mask |

### Cost & Metadata

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--estimate-cost` | flag | - | Show cost without generating |
| `--no-metadata` | flag | - | Don't save metadata file |

### Debug

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--verbose` | flag | - | Show detailed output |
| `--dry-run` | flag | - | Show request without sending |

---

## Examples

### Basic

```bash
# Simple text-to-video
python video_gen.py "A cat walking in a garden"

# With options
python video_gen.py "A sunset over mountains" \
  --duration 8 \
  --aspect-ratio 16:9
```

### Using Presets

```bash
# Fast preview
python video_gen.py "Quick test" --preset quick

# Best quality
python video_gen.py "Final render" --preset quality

# Video extension
python video_gen.py "Continue scene" --preset extend --extend-video input.mp4
```

### Image-to-Video

```bash
# Animate an image
python video_gen.py "The character starts walking" --image hero.png

# With first and last frames
python video_gen.py "Transition effect" \
  --image start.jpg \
  --last-frame end.jpg
```

### Reference Images

```bash
# Preserve subject (asset)
python video_gen.py "Person waving" \
  --reference-image avatar.png:asset

# Apply style
python video_gen.py "Cityscape" \
  --reference-image painting.jpg:style \
  --model veo-2.0-exp
```

### Video Extension

```bash
# Extend with auto-upload
python video_gen.py "Continue the action" \
  --extend-video previous.mp4 \
  --storage-uri gs://my-bucket/videos \
  --model veo-2.0
```

### Mask Operations

```bash
# Insert object
python video_gen.py "Add a bird" \
  --video scene.mp4 \
  --mask bird_area.png \
  --mask-mode insert

# Remove object
python video_gen.py "Remove watermark" \
  --video scene.mp4 \
  --mask watermark.png \
  --mask-mode remove
```

---

## Environment Variables

| Variable | Description |
|----------|-------------|
| `GOOGLE_API_KEY` | Default API key |
| `GOOGLE_APPLICATION_CREDENTIALS` | Service account JSON path |

---

## Config File

Create `config.json` for multi-project support:

```json
{
  "main_projects": {
    "default": {
      "api_key": "AIzaSy...",
      "gcs_bucket": "my-bucket"
    }
  }
}
```

```bash
python video_gen.py "prompt" --project default
```

---

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Invalid arguments |
| 3 | API error |

---

## See Also

- [Video Overview](../video/overview.md) - Video generation guide
- [Generation Modes](../video/modes.md) - All 8 modes explained
- [Presets](../guides/presets.md) - Preset configurations
