# CLI Reference: image_gen.py

Complete command-line reference for image generation.

---

## Synopsis

```bash
python image_gen.py PROMPT [OPTIONS]
```

---

## Arguments

### Required

| Argument | Description |
|----------|-------------|
| `PROMPT` | Text description of the image to generate |

---

## Options

### Model & Output

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--model` | string | gemini-3-pro-image-preview | Model to use |
| `--aspect-ratio` | string | 1:1 | 1:1, 16:9, 9:16, 4:3, 3:4 |
| `--image-size` | string | 1K | 1K or 2K |
| `--output` | path | generated_images/ | Output directory |

### Authentication

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--project` | string | default | Config project name |

### Context

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--doc` | path | - | Document for context |

### Cost

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--estimate-cost` | flag | - | Show cost without generating |

### Debug

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--show-defaults` | flag | - | Show current defaults and exit (v1.4) |

---

## Examples

### Basic

```bash
# Simple generation
python image_gen.py "A futuristic city at sunset"

# With aspect ratio
python image_gen.py "A cat sitting" --aspect-ratio 16:9

# High resolution
python image_gen.py "Detailed landscape" --image-size 2K
```

### Social Media

```bash
# Instagram square
python image_gen.py "Coffee art" --aspect-ratio 1:1

# Instagram story
python image_gen.py "Mountains" --aspect-ratio 9:16

# Twitter header
python image_gen.py "Abstract pattern" --aspect-ratio 16:9
```

### With Document Context

```bash
python image_gen.py "System architecture diagram" --doc README.md
```

### Cost Estimation

```bash
python image_gen.py "Test image" --estimate-cost
```

---

## Aspect Ratio Guide

| Ratio | Resolution (1K) | Best For |
|-------|-----------------|----------|
| 1:1 | 1024x1024 | Square images, avatars |
| 16:9 | 1820x1024 | Widescreen, desktop |
| 9:16 | 1024x1820 | Mobile, stories |
| 4:3 | 1365x1024 | Traditional photo |
| 3:4 | 1024x1365 | Portrait |

---

## Environment Variables

| Variable | Description |
|----------|-------------|
| `GOOGLE_API_KEY` | Default API key (for direct API usage) |

---

## Output

Images are saved with timestamp:

```
generated_images/
└── image_20260123_123456.png
```

---

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Invalid arguments |

---

## See Also

- [Image Generation](../image/index.md) - Image generation guide
- [Authentication](../getting-started/authentication.md) - API key setup
