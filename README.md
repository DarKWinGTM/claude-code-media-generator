<div align="center">

<!-- Hero Banner -->
<img src="https://img.shields.io/badge/Claude_Code-Media_Generator-8b5cf6?style=for-the-badge&logo=google-cloud&logoColor=white&labelColor=1e1e2e" alt="Claude Code Media Generator" height="60">

<br><br>

# 🎬 Claude Code Media Generator

### **AI-Powered Video & Image Generation**

*Generate stunning videos and images using Google Veo & Imagen APIs*

<br>

<!-- Badges Row 1 -->
[![Python](https://img.shields.io/badge/Python-3.8+-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![Vertex AI](https://img.shields.io/badge/Vertex_AI-Veo_3.1-4285F4?style=flat-square&logo=google-cloud&logoColor=white)](https://cloud.google.com/vertex-ai)
[![License](https://img.shields.io/badge/License-MIT-22c55e?style=flat-square)](LICENSE)
[![Docs](https://img.shields.io/badge/Docs-Online-8b5cf6?style=flat-square&logo=readthedocs&logoColor=white)](https://darkwingtm.github.io/claude-code-media-generator/)

<!-- Badges Row 2 -->
[![GitHub Stars](https://img.shields.io/github/stars/DarKWinGTM/claude-code-media-generator?style=flat-square&logo=github&color=yellow)](https://github.com/DarKWinGTM/claude-code-media-generator/stargazers)
[![Last Commit](https://img.shields.io/github/last-commit/DarKWinGTM/claude-code-media-generator?style=flat-square&color=blue)](https://github.com/DarKWinGTM/claude-code-media-generator/commits)

<br>

**[📖 Documentation](https://darkwingtm.github.io/claude-code-media-generator/)** · **[🚀 Quick Start](#-quick-start)** · **[💡 Examples](#-usage-examples)** · **[📋 Roadmap](TODO.md)**

</div>

---

## 🌟 Why Claude Code Media Generator?

<table>
<tr>
<td width="25%" align="center">
<h3>🚀</h3>
<b>Simple CLI</b><br>
<sub>One command to generate</sub>
</td>
<td width="25%" align="center">
<h3>🎬</h3>
<b>8 Video Modes</b><br>
<sub>Text, Image, Extension & more</sub>
</td>
<td width="25%" align="center">
<h3>☁️</h3>
<b>Cloud Native</b><br>
<sub>Direct GCS integration</sub>
</td>
<td width="25%" align="center">
<h3>🔒</h3>
<b>Secure</b><br>
<sub>OAuth & API Key support</sub>
</td>
</tr>
</table>

---

## ⚡ Features

<table>
<tr>
<td width="50%">

### 🎥 Video Generation
- **8 Generation Modes** - Text-to-video, image animation, extension & more
- **Veo 3.1 Support** - Latest model with audio generation
- **Smart Presets** - Quick, Quality, Extend, Budget
- **Auto-Validation** - Corrects common mistakes automatically

</td>
<td width="50%">

### 🖼️ Image Generation
- **Gemini & Imagen** - Multiple model support
- **Flexible Formats** - Various aspect ratios
- **High Quality** - Up to 4K resolution
- **Cost Tracking** - Built-in estimation

</td>
</tr>
<tr>
<td width="50%">

### ☁️ Cloud Integration
- **GCS Storage** - Direct output to Google Cloud
- **Auto Upload** - Seamless video extension workflow
- **REST API** - No gsutil required

</td>
<td width="50%">

### 🛡️ Developer Experience
- **Dry Run Mode** - Preview before generating
- **Detailed Logs** - Full request/response info
- **Metadata Export** - JSON tracking files

</td>
</tr>
</table>

---

## 🎬 Video Generation Modes

| Mode | Input | Description |
|:-----|:------|:------------|
| 🔤 **Text-to-Video** | Text only | Generate video from prompt |
| 🖼️ **Image-to-Video** | Image + Text | Animate a single image |
| 🎞️ **First & Last Frames** | 2 Images + Text | Interpolate keyframes |
| ➕ **Video Extension** | Video + Text | Add 7 more seconds |
| 👤 **Reference Asset** | 1-3 Images + Text | Subject preservation |
| 🎨 **Reference Style** | 1 Image + Text | Style transfer |
| ✨ **Insert Objects** | Video + Mask | Add objects (Veo 3.1) |
| 🗑️ **Remove Objects** | Video + Mask | Remove objects (Veo 3.1) |

---

## 🚀 Quick Start

### Prerequisites

```
✅ Python 3.8+
✅ Google Cloud account with Vertex AI enabled
✅ API Key (Gemini or Vertex AI)
✅ Claude Code CLI (optional - for /generative skill)
```

### Installation

```bash
# Clone the repository
git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
cd claude-code-media-generator

# Install dependencies
pip install requests google-auth
```

### Setup API Key

```bash
# Option 1: Environment variable
export GOOGLE_API_KEY="your-api-key"

# Option 2: Command line
python video_gen.py "Your prompt" --api-key YOUR_API_KEY
```

---

## 🛠️ Claude Code Skill Installation

Install the `/generative` skill to use AI-assisted video and image generation in Claude Code.

### 📦 What Gets Installed

| Component | Location | Purpose |
|-----------|----------|---------|
| `SKILL.md` | `~/.claude/skills/generative/` | Skill definition |
| `video_gen.py` | Working directory | Video generation CLI |
| `image_gen.py` | Working directory | Image generation CLI |
| `config.py` | Working directory | Configuration module |
| `video_utils.py` | Working directory | Video utilities |

### 🐧 Linux / macOS Installation

```bash
# 1. Clone the repository
git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
cd claude-code-media-generator

# 2. Install Python dependencies
pip install requests google-auth

# 3. Create skill directory and install skill
mkdir -p ~/.claude/skills/generative
cp .claude/skills/generative/SKILL.md ~/.claude/skills/generative/

# 4. Copy Python scripts to your working directory
# Option A: Copy to current project
cp video_gen.py image_gen.py config.py video_utils.py /path/to/your/project/

# Option B: Create symlinks (recommended for updates)
ln -s "$(pwd)/video_gen.py" /path/to/your/project/
ln -s "$(pwd)/image_gen.py" /path/to/your/project/
ln -s "$(pwd)/config.py" /path/to/your/project/
ln -s "$(pwd)/video_utils.py" /path/to/your/project/

# 5. Restart Claude Code to detect new skill
```

### 🪟 Windows Installation (PowerShell)

```powershell
# 1. Clone the repository
git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
cd claude-code-media-generator

# 2. Install Python dependencies
pip install requests google-auth

# 3. Create skill directory and install skill
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\generative"
Copy-Item ".\.claude\skills\generative\SKILL.md" -Destination "$env:USERPROFILE\.claude\skills\generative\"

# 4. Copy Python scripts to your working directory
Copy-Item "video_gen.py", "image_gen.py", "config.py", "video_utils.py" -Destination "C:\path\to\your\project\"

# 5. Restart Claude Code to detect new skill
```

### 🪟 Windows Installation (Command Prompt)

```cmd
:: 1. Clone the repository
git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
cd claude-code-media-generator

:: 2. Install Python dependencies
pip install requests google-auth

:: 3. Create skill directory and install skill
mkdir "%USERPROFILE%\.claude\skills\generative"
copy ".\.claude\skills\generative\SKILL.md" "%USERPROFILE%\.claude\skills\generative\"

:: 4. Copy Python scripts to your working directory
copy video_gen.py "C:\path\to\your\project\"
copy image_gen.py "C:\path\to\your\project\"
copy config.py "C:\path\to\your\project\"
copy video_utils.py "C:\path\to\your\project\"

:: 5. Restart Claude Code to detect new skill
```

### ✅ Verify Installation

After restarting Claude Code:

```bash
# Test the skill is recognized
/generative info

# Test image generation
/generative image "A beautiful sunset"

# Test video generation
/generative video "Ocean waves"

# Show help
/generative help
```

### 🔧 Configuration

After installation, configure your API:

```bash
# Start setup wizard
/generative config setup

# Or configure manually
/generative config gemini    # For Gemini API
/generative config vertex    # For Vertex AI
```

### 📁 Directory Structure After Installation

```
~/.claude/
└── skills/
    └── generative/
        └── SKILL.md           # Skill definition (1,500+ lines)

/your/project/
├── video_gen.py               # Video generation CLI
├── image_gen.py               # Image generation CLI
├── config.py                  # Configuration module
├── video_utils.py             # Video utilities
└── config.json                # Your API configuration (created by skill)
```

### 🔄 Updating the Skill

```bash
# Pull latest changes
cd claude-code-media-generator
git pull

# Update skill file
# Linux/macOS:
cp .claude/skills/generative/SKILL.md ~/.claude/skills/generative/

# Windows (PowerShell):
Copy-Item ".\.claude\skills\generative\SKILL.md" -Destination "$env:USERPROFILE\.claude\skills\generative\" -Force

# Update Python scripts (if using copies, not symlinks)
# Copy the updated files to your project directory

# Restart Claude Code
```

---

## 💡 Usage Examples

### Basic Video Generation

```bash
# Simple text-to-video
python video_gen.py "A cat playing with a ball in a sunny garden"

# With quality preset (8 sec, 1080p)
python video_gen.py "A cinematic sunset over the ocean" --preset quality

# Custom duration and resolution
python video_gen.py "Ocean waves crashing" --duration 8 --resolution 1080p
```

### Video Extension

```bash
# Extend existing video by 7 seconds
python video_gen.py "Continue the scene smoothly" --extend-video source.mp4

# Extend from URL (auto-downloads and converts)
python video_gen.py "Continue the action" \
    --extend-video-from-url "https://example.com/video.mp4" \
    --storage-uri "gs://your-project-media-output/extend/"
```

### Image-to-Video

```bash
# Animate an image
python video_gen.py "The bird takes flight" --image bird.jpg

# With first and last frame
python video_gen.py "Flower blooms beautifully" \
    --image bud.jpg --last-frame flower.jpg
```

### Reference Images

```bash
# Subject consistency (up to 3 images)
python video_gen.py "Character walks through forest" \
    --reference-image character.png:asset

# Style transfer
python video_gen.py "City at sunset" \
    --reference-image monet.jpg:style
```

### Image Generation

```bash
# Basic image
python image_gen.py "A futuristic city at night"

# With aspect ratio
python image_gen.py "Portrait photo" --aspect-ratio 9:16
```

---

## ⚙️ Presets

| Preset | Model | Duration | Resolution | Best For |
|:-------|:------|:---------|:-----------|:---------|
| `quick` | veo-3.1-fast | 5 sec | 720p | Drafts, testing |
| `quality` | veo-3.1 | 8 sec | 1080p | Production |
| `extend` | veo-3.1 | 7 sec | 720p | Video extension |
| `budget` | veo-3.0-fast | 5 sec | 720p | Cost-effective |

```bash
python video_gen.py "Your prompt" --preset quality
```

---

## ☁️ GCS Storage

> **⚠️ Important:** When using Vertex AI API Key, your GCS bucket **MUST** follow this naming pattern:

```
gs://YOUR_PROJECT_ID-media-output/
```

### Example

```bash
# Correct pattern (will work)
python video_gen.py "Your prompt" \
    --storage-uri "gs://gen-lang-client-0344941103-media-output/videos/" \
    --project-id "gen-lang-client-0344941103"

# ❌ Wrong pattern (will fail with permission denied)
# gs://my-custom-bucket/
# gs://your-project-veo-output/
```

---

## 🎯 Smart Defaults (v2.25.1)

When extending a video, settings are automatically inherited from the source video's metadata:

```bash
# Original video was created with veo-2.0, 1080p, custom project
python video_gen.py "Continue the scene" --extend-video video_20260201_123456_0.mp4

# Output:
# ℹ Found source metadata for inheritance
# ℹ Inherited model from metadata: veo-2.0
# ℹ Inherited resolution from metadata: 1080p
```

**Priority Chain:** CLI Arguments > Source Metadata > config.json > Code Defaults

---

## 💰 Pricing

| Model | Price/Second | 5-sec Video |
|:------|:------------|:------------|
| veo-3.1-generate-preview | $0.35 | $1.75 |
| veo-3.1-fast-generate-preview | $0.15 | $0.75 |
| veo-3.0-generate-preview | $0.25 | $1.25 |
| veo-2.0-generate-preview | $0.35 | $1.75 |

---

## 📁 Project Structure

```
claude-code-media-generator/
├── 🎬 video_gen.py      # Video generation CLI
├── 🖼️ image_gen.py      # Image generation CLI
├── 🔍 check_api.py      # API verification tool
├── 📄 README.md         # This file
├── 📋 TODO.md           # Development roadmap
├── 📁 design/           # Design documents
│   ├── design.md        # Main project design
│   ├── video.design.md  # Video module design
│   ├── image.design.md  # Image module design
│   └── ...
├── 📁 changelog/        # Version history
├── 📁 pages/            # MkDocs documentation (pages branch)
└── 📁 wiki/             # GitHub Wiki source
```

---

## 📖 Documentation

<table>
<tr>
<td align="center" width="25%">
<a href="https://darkwingtm.github.io/claude-code-media-generator/getting-started/installation/">
<img src="https://img.shields.io/badge/📦-Installation-blue?style=for-the-badge" alt="Installation">
</a>
<br><sub>Setup & Dependencies</sub>
</td>
<td align="center" width="25%">
<a href="https://darkwingtm.github.io/claude-code-media-generator/getting-started/authentication/">
<img src="https://img.shields.io/badge/🔐-Authentication-green?style=for-the-badge" alt="Auth">
</a>
<br><sub>API Keys & OAuth</sub>
</td>
<td align="center" width="25%">
<a href="https://darkwingtm.github.io/claude-code-media-generator/video/overview/">
<img src="https://img.shields.io/badge/🎬-Video_Guide-purple?style=for-the-badge" alt="Video">
</a>
<br><sub>All 8 Modes</sub>
</td>
<td align="center" width="25%">
<a href="https://darkwingtm.github.io/claude-code-media-generator/cli/video_gen/">
<img src="https://img.shields.io/badge/💻-CLI_Reference-orange?style=for-the-badge" alt="CLI">
</a>
<br><sub>Full Commands</sub>
</td>
</tr>
</table>

---

## 📊 Status

| Component | Version | Status |
|:----------|:--------|:-------|
| video_gen.py | v2.27 | ✅ Production Ready |
| image_gen.py | v1.4 | ✅ Stable |
| check_api.py | v2.1 | ✅ Stable |
| Documentation | v3.10 | ✅ Updated |
| Claude Code Skill | v3.2.1 | ✅ Available |
| MCP Server | - | 🚧 Planned |

---

## 🤝 Contributing

Contributions are welcome! Please read the design documents in `design/` before making changes.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

<div align="center">

### Made with ❤️ using Google Vertex AI

**[⬆️ Back to Top](#-claude-code-media-generator)**

</div>
