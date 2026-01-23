# 🎬 Claude Code Media Generator

> **AI-powered video and image generation using Google Veo & Imagen APIs**

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## ✨ Features

### 8 Video Generation Modes

| # | Mode | Input | Description |
|---|------|-------|-------------|
| 1 | **Text-to-Video** | Text prompt | Generate from text only |
| 2 | **Image-to-Video** | Image + Text | Animate single image |
| 3 | **First & Last Frames** | 2 Images + Text | Interpolate between keyframes |
| 4 | **Video Extension** | Video + Text | Continue existing video (+7 sec) |
| 5 | **Reference Asset** | 1-3 Images + Text | Subject preservation |
| 6 | **Reference Style** | 1 Image + Text | Style transfer |
| 7 | **Insert Objects** | Video + Mask + Text | Add objects (Veo 3.1 only) |
| 8 | **Remove Objects** | Video + Mask + Text | Remove objects (Veo 3.1 only) |

### Tool Status

| Feature | Status | Tool |
|---------|--------|------|
| **Video Generation** | ✅ Ready | `video_gen.py` |
| **Image Generation** | ✅ Ready | `image_gen.py` |
| **Video Extension** | ✅ Ready | `video_gen.py` |
| **GCS Integration** | ✅ Ready | Built-in |
| **Claude Code Skill** | 🚧 Planned | `./skill/` |
| **MCP Server** | 🚧 Planned | `./mcp/` |

### Supported Models

**Video (Veo)**
- `veo-3.1-generate-preview` (default) - Audio support, latest features
- `veo-3.1-fast-generate-preview` - Fast generation
- `veo-3.0-*`, `veo-2.0-*` variants

**Image (Gemini)**
- `gemini-3-pro-image-preview` (default) - Best quality

---

## 🚀 Quick Start

### Prerequisites

- Python 3.8+
- Google Cloud account with Vertex AI API enabled
- API Key (Gemini or Vertex AI)

### Installation

```bash
git clone https://github.com/yourusername/claude-code-media-generator.git
cd claude-code-media-generator
pip install requests google-auth
```

### Setup API Key

**Option 1: Environment Variable**
```bash
export GOOGLE_API_KEY="AIzaSy..."  # Gemini API
# or
export GOOGLE_API_KEY="AQ..."      # Vertex AI API
```

**Option 2: Command Line**
```bash
python video_gen.py "Your prompt" --api-key YOUR_API_KEY
```

---

## 📖 Usage

### Video Generation

```bash
# Basic video generation
python video_gen.py "A cat playing with a ball in a sunny garden"

# With preset (quick/quality/extend/budget)
python video_gen.py "A cinematic sunset" --preset quality

# Specify duration and resolution
python video_gen.py "Ocean waves" --duration 8 --resolution 1080p

# Save to specific output
python video_gen.py "Mountain landscape" -o my_video.mp4
```

### Video Extension

```bash
# Extend existing video by 7 seconds
python video_gen.py "Continue the scene" --extend-video source.mp4
```

### Image-to-Video

```bash
# Animate an image
python video_gen.py "The bird flies away" --image bird.jpg

# With first and last frame
python video_gen.py "Flower blooms" --image bud.jpg --last-frame flower.jpg
```

### Reference Asset/Style

```bash
# Use reference images for subject consistency (up to 3 images)
python video_gen.py "Character walks through forest" --reference-asset character.jpg

# Apply artistic style from reference image
python video_gen.py "City at sunset" --reference-style monet.jpg
```

### Insert/Remove Objects

```bash
# Insert objects using mask
python video_gen.py "Add a bird flying" --video scene.mp4 --mask sky_area.png --mask-mode insert

# Remove objects using mask
python video_gen.py "Remove the car" --video street.mp4 --mask car_area.png --mask-mode remove
```

### Image Generation

```bash
# Basic image generation
python image_gen.py "A futuristic city at night"

# With aspect ratio
python image_gen.py "Portrait photo" --aspect-ratio 9:16
```

### Presets

| Preset | Model | Duration | Resolution | Use Case |
|--------|-------|----------|------------|----------|
| `quick` | veo-3.1-fast | 5 sec | 720p | Drafts, testing |
| `quality` | veo-3.1 | 8 sec | 1080p | Production |
| `extend` | veo-3.1 | 7 sec | 720p | Video extension |
| `budget` | veo-3.0-fast | 5 sec | 720p | Cost-effective |

---

## 💰 Pricing (Estimated)

| Model | Price/Second |
|-------|-------------|
| veo-3.1-generate-preview | $0.35 |
| veo-3.1-fast-generate-preview | $0.15 |
| veo-3.0-generate-preview | $0.25 |
| veo-3.0-fast-generate-preview | $0.15 |
| veo-2.0-generate-preview | $0.35 |

**Example:** 5-second video with veo-3.1 = $1.75

---

## 📁 Project Structure

```
claude-code-media-generator/
├── video_gen.py          # Video generation CLI
├── image_gen.py          # Image generation CLI
├── check_api.py          # API verification tool
├── README.md             # This file
├── TODO.md               # Development roadmap
├── .gitignore            # Git ignore rules
├── design/               # Design documents
│   ├── design.md         # Master design
│   ├── video.design.md   # Video generation design
│   ├── image.design.md   # Image generation design
│   └── ...
├── docs/                 # Additional documentation
├── mcp/                  # MCP server (planned)
└── skill/                # Claude Code skill (planned)
```

---

## 🔧 Advanced Usage

### GCS Storage Output

```bash
# Output directly to GCS bucket
python video_gen.py "Your prompt" --storage-uri gs://your-bucket/output/

# Upload existing video to GCS
python video_gen.py --upload-gcs local_video.mp4
```

### Smart Validation

The tool automatically corrects common mistakes:

```bash
# Duration auto-corrected for video extension (must be 7 sec)
python video_gen.py "Continue" --extend-video video.mp4 --duration 10
# ⚠️ Warning: Video Extension only supports 7 seconds. Changed from 10 to 7.

# Model auto-switched for incompatible modes
python video_gen.py "Continue" --extend-video video.mp4 --preset budget
# ⚠️ Warning: veo-3.0-fast does not support video_extension. Changed to veo-3.1.
```

### Dry Run

```bash
# Preview request without sending
python video_gen.py "Your prompt" --dry-run
```

---

## 📚 Documentation

### 📖 Online Documentation

**Full documentation:** [https://darkwingtm.github.io/claude-code-media-generator/](https://darkwingtm.github.io/claude-code-media-generator/)

### 🌿 Branch Structure

| Branch | Purpose | Content |
|--------|---------|---------|
| **main** | Public release | README.md, TODO.md |
| **pages** | Documentation source | MkDocs files, workflows |
| **gh-pages** | Built documentation | Auto-generated HTML |

```
main branch (you are here)
├── README.md          ← Project overview
└── TODO.md            ← Development roadmap

pages branch (documentation source)
├── .github/workflows/docs.yml  ← GitHub Actions
├── mkdocs.yml                  ← MkDocs config
└── docs/                       ← Markdown files
    ├── index.md
    ├── getting-started/
    ├── video/
    ├── image/
    ├── cli/
    └── guides/

gh-pages branch (auto-generated)
└── [Built HTML site]
```

### 📄 Local Documentation

- [TODO](./TODO.md) - Development roadmap

---

## 🤝 Contributing

Contributions are welcome! Please read the design documents before making changes.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 🙏 Acknowledgments

- Google Vertex AI for Veo and Imagen models
- Anthropic Claude for AI assistance
