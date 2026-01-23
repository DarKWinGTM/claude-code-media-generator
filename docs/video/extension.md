# Video Extension

Extend existing videos with AI-generated content.

---

## Overview

Video extension allows you to continue an existing video with new AI-generated content. The AI analyzes the last frames and generates continuation based on your prompt.

---

## Requirements

### Critical Requirements

| Requirement | Details |
|-------------|---------|
| **Frame Rate** | Source video MUST be 24fps |
| **Model** | Must use veo-2.0 or veo-2.0-exp |
| **Source** | Local file or GCS URI (NOT external URLs) |

### Why 24fps?

The Veo API only accepts 24fps videos for extension. Videos with different frame rates will fail with "fps mismatch" error.

---

## Basic Usage

```bash
# Extend a local video
python video_gen.py "The cat runs faster" \
  --extend-video previous_video.mp4 \
  --model veo-2.0

# Extend with GCS storage
python video_gen.py "Continue the scene" \
  --extend-video previous_video.mp4 \
  --storage-uri gs://my-bucket/videos \
  --model veo-2.0
```

---

## Source Options

### Local File

```bash
python video_gen.py "Continue scene" \
  --extend-video /path/to/video.mp4
```

The script will:

1. Check if video is 24fps
2. Upload to GCS if `--storage-uri` provided
3. Use base64 encoding if no GCS

### GCS URI

```bash
python video_gen.py "Continue scene" \
  --extend-video gs://my-bucket/videos/source.mp4
```

Direct GCS URI is faster - no upload needed.

---

## With Reference Images

Combine extension with reference images:

```bash
python video_gen.py "The person continues walking" \
  --extend-video previous.mp4 \
  --reference-image avatar.png:asset \
  --model veo-2.0
```

---

## With Last Frame

Specify where the video should end:

```bash
python video_gen.py "Transform to sunset" \
  --extend-video daytime.mp4 \
  --last-frame sunset.jpg \
  --model veo-2.0
```

---

## Workflow: Creating Long Videos

Chain multiple extensions to create longer videos:

```bash
# Step 1: Generate initial video
python video_gen.py "A person walking" --preset quality
# Output: video_001.mp4

# Step 2: Extend first video
python video_gen.py "The person starts running" \
  --extend-video video_001.mp4 --model veo-2.0
# Output: video_002.mp4

# Step 3: Extend again
python video_gen.py "The person jumps over obstacle" \
  --extend-video video_002.mp4 --model veo-2.0
# Output: video_003.mp4

# Combine with ffmpeg
ffmpeg -i "concat:video_001.mp4|video_002.mp4|video_003.mp4" -c copy final.mp4
```

---

## Common Issues

### "fps mismatch"

**Problem:** Source video is not 24fps

**Solution:** Convert to 24fps using ffmpeg:

```bash
ffmpeg -i input.mp4 -r 24 -c:v libx264 output_24fps.mp4
```

### "video is empty"

**Problem:** External URL used (not supported)

**Solution:** Download video first, then use local file:

```bash
# Download video
curl -o local_video.mp4 "https://example.com/video.mp4"

# Convert to 24fps if needed
ffmpeg -i local_video.mp4 -r 24 local_24fps.mp4

# Use local file
python video_gen.py "Continue" --extend-video local_24fps.mp4
```

### "Model does not support video_extension"

**Problem:** Using veo-3.1 models

**Solution:** Use veo-2.0:

```bash
python video_gen.py "Continue" \
  --extend-video input.mp4 \
  --model veo-2.0
```

---

## Auto-Upload Feature

When `--storage-uri` is provided, local files are automatically uploaded:

```bash
python video_gen.py "Continue scene" \
  --extend-video local_video.mp4 \
  --storage-uri gs://my-bucket/uploads
```

Flow:
```
Local file → Upload to GCS → Use gcsUri in API → Generate → Download
```

---

## Best Practices

### Do

- :white_check_mark: Use Veo-generated videos (always 24fps)
- :white_check_mark: Use `--storage-uri` for reliable uploads
- :white_check_mark: Use `--model veo-2.0` for extension
- :white_check_mark: Keep prompts consistent with source video

### Don't

- :x: Use external URLs directly
- :x: Use veo-3.1 models for extension
- :x: Use videos with non-24fps frame rate
- :x: Create drastic scene changes

---

## Planned Features (v2.25)

Future version will support:

```bash
# Planned: Auto-download and convert external URLs
python video_gen.py "Continue" \
  --extend-video-from-url "https://example.com/video.mp4"
```

This will:

1. Download video from URL
2. Convert to 24fps using ffmpeg
3. Upload to GCS
4. Proceed with extension

---

## Learn More

- [Generation Modes](modes.md) - All generation modes
- [Video Models](models.md) - Model comparison
- [Reference Images](reference-images.md) - Using references with extension
- [GCS Storage](../guides/gcs-storage.md) - Setting up GCS
