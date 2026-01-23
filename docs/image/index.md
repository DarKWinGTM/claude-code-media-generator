# Image Generation

Generate AI images using Google Gemini and Imagen models.

---

## Overview

Image generation uses `generateContent` API method (synchronous) and supports text-to-image generation with various aspect ratios and resolutions.

---

## Supported Models

| Model | Quality | Cost per Image |
|-------|---------|----------------|
| **gemini-3-pro-image-preview** | Best (Default) | $0.134 |
| imagen-3 | Good | $0.03 |
| imagen-4 | Good | $0.04 |

!!! tip "Default Model"
    `gemini-3-pro-image-preview` is the default model for best quality.

---

## Basic Usage

```bash
# Simple image generation
python image_gen.py "A futuristic city at sunset"

# With options
python image_gen.py "A cat sitting on a couch" \
  --aspect-ratio 16:9 \
  --image-size 2K
```

---

## Command Line Options

| Option | Values | Default | Description |
|--------|--------|---------|-------------|
| `--aspect-ratio` | 1:1, 16:9, 9:16, 4:3, 3:4 | 1:1 | Image aspect ratio |
| `--image-size` | 1K, 2K | 1K | Image resolution |
| `--model` | gemini-3-pro-image-preview | Default | Model to use |
| `--estimate-cost` | Flag | - | Show cost without generating |
| `--project` | Project name | default | Config project to use |

---

## Aspect Ratios

| Ratio | Best For |
|-------|----------|
| `1:1` | Social media, avatars, icons |
| `16:9` | Widescreen, desktop wallpaper |
| `9:16` | Mobile, stories, vertical video |
| `4:3` | Traditional photo format |
| `3:4` | Portrait photos |

---

## Examples

### Social Media

```bash
# Instagram square
python image_gen.py "A coffee cup with latte art" --aspect-ratio 1:1

# Instagram story
python image_gen.py "Mountain landscape" --aspect-ratio 9:16
```

### Desktop Wallpaper

```bash
python image_gen.py "Abstract geometric patterns, purple and blue" \
  --aspect-ratio 16:9 \
  --image-size 2K
```

### Documentation/Diagrams

```bash
python image_gen.py "Architecture diagram for microservices" \
  --doc README.md
```

---

## Cost Estimation

Preview cost before generating:

```bash
python image_gen.py "A beautiful sunset" --estimate-cost
```

Output:
```
💰 Cost Estimate:
   Model: gemini-3-pro-image-preview
   Count: 1
   Price: $0.134 per image
   Total: $0.134
```

---

## Output

Images are saved to `generated_images/` directory:

```
generated_images/
├── image_20260123_123456.png
├── image_20260123_123500.png
└── ...
```

---

## Using with Documents

Generate images based on document content:

```bash
python image_gen.py "Diagram of the system" --doc design.md
```

The script reads the document and incorporates context into the prompt.

---

## API Differences from Video

| Aspect | Image | Video |
|--------|-------|-------|
| **API Method** | generateContent | predictLongRunning |
| **Response** | Synchronous | Asynchronous (polling) |
| **Free Tier** | :white_check_mark: Gemini 2.0 Flash | :x: None |
| **Generation Time** | ~5-15 seconds | ~60-180 seconds |

---

## Troubleshooting

### "Unknown provider"

Some models may not be available:

```bash
# Use default model instead
python image_gen.py "prompt"  # Uses gemini-3-pro-image-preview
```

### "Rate limit exceeded"

Wait a few seconds between requests or reduce frequency.

### Image not generated

- Check API key is valid
- Ensure prompt follows content guidelines
- Try simplifying the prompt

---

## Learn More

- [Quick Start](../getting-started/quick-start.md) - Getting started guide
- [Authentication](../getting-started/authentication.md) - API key setup
- [CLI Reference: image_gen.py](../cli/image_gen.md) - Full CLI reference
