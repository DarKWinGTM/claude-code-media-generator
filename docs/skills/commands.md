# Skill Commands Reference

Complete reference for all `/generative` skill commands.

---

## Main Commands

| Command | Description |
|---------|-------------|
| `/generative image "prompt"` | Generate image from text |
| `/generative video "prompt"` | Generate video from text |
| `/generative info` | Show capabilities overview |
| `/generative config` | Configuration management |
| `/generative help` | Detailed help system |

---

## Image Generation

### Basic Usage

```bash
/generative image "A beautiful sunset over mountains"
```

### With Options

```bash
# Specify aspect ratio
/generative image "A portrait of a robot" --aspect-ratio 9:16

# Higher resolution
/generative image "Landscape photography" --image-size 2K

# Multiple images
/generative image "Abstract art" --count 4
```

### Image Parameters

| Parameter | Options | Default |
|-----------|---------|---------|
| `--aspect-ratio` | `1:1`, `16:9`, `9:16`, `4:3`, `3:4` | `1:1` |
| `--image-size` | `1K`, `2K` | `1K` |
| `--count` | `1-4` | `1` |
| `--model` | `gemini-2.0-flash`, `imagen-3.0` | Auto |

---

## Video Generation

### Basic Usage

```bash
/generative video "Ocean waves crashing on the beach"
```

### With Options

```bash
# Higher quality
/generative video "Cinematic sunset" --preset quality

# Specific duration
/generative video "A cat walking" --duration 8

# With aspect ratio
/generative video "Vertical video for social media" --aspect-ratio 9:16
```

### Video Parameters

| Parameter | Options | Default |
|-----------|---------|---------|
| `--preset` | `quick`, `quality`, `extend`, `budget` | `quick` |
| `--duration` | `5`, `6`, `7`, `8` | `5` |
| `--aspect-ratio` | `16:9`, `9:16` | `16:9` |
| `--model` | See [Video Models](../video/models.md) | `veo-3.1-generate-preview` |

---

## Help System

### Main Help

```bash
/generative help
```

### Topic-Specific Help

| Command | Description |
|---------|-------------|
| `/generative help image` | Image generation guide |
| `/generative help video` | Video generation guide |
| `/generative help examples` | 10+ usage examples |
| `/generative help modes` | All 8 video modes explained |
| `/generative help models` | Model comparison |
| `/generative help presets` | Preset configurations |

---

## Configuration Commands

### Setup Wizard

```bash
/generative config setup
```

Guided setup that helps you configure:

1. **API Type** - Gemini API or Vertex AI
2. **API Key** - Your authentication key
3. **Project ID** - Google Cloud project (for Vertex AI)
4. **gcloud CLI** - Optional installation

### Other Config Commands

| Command | Description |
|---------|-------------|
| `/generative config show` | Show current settings |
| `/generative config gemini` | Setup Gemini API |
| `/generative config vertex` | Setup Vertex AI |
| `/generative config gcloud` | Setup gcloud CLI |
| `/generative config reset` | Reset to defaults |

---

## Info Command

```bash
/generative info
```

Shows:

- Available models
- Supported features
- Current configuration status
- API connection status

---

## Advanced Video Modes

For advanced video generation modes, use specific parameters:

### Image-to-Video

```bash
/generative video "Animate this image" --image /path/to/image.jpg
```

### Video Extension

```bash
/generative video "Continue this video" --extend /path/to/video.mp4
```

### Reference Style

```bash
/generative video "Apply this style" --style-image /path/to/style.jpg
```

### Reference Asset

```bash
/generative video "Use this character" --reference-image /path/to/character.jpg
```

---

## Tips & Best Practices

### 1. Be Descriptive

```bash
# Good - descriptive prompt
/generative video "A golden retriever running through a sunlit meadow, slow motion, cinematic"

# Less effective - vague prompt
/generative video "dog running"
```

### 2. Use Presets

```bash
# Quick preview
/generative video "My prompt" --preset quick

# Best quality
/generative video "My prompt" --preset quality
```

### 3. Check Costs First

```bash
# Estimate cost before generating
/generative video "My prompt" --estimate-cost
```

---

## Next Steps

- [Examples](examples.md) - Real usage examples
- [Configuration](configuration.md) - Detailed setup guide
- [Video Modes](../video/modes.md) - All 8 video generation modes
