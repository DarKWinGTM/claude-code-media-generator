# Using Skills

The `/generative` skill provides AI-assisted video and image generation directly in Claude Code - the easiest way to use this project.

---

## Why Use Skills?

| Method | Ease of Use | Best For |
|--------|-------------|----------|
| :material-star: **Skill** (`/generative`) | :material-check-all: Easiest | Most users, quick generation |
| Script (`python video_gen.py`) | :material-check: Manual | Automation, CI/CD, advanced users |

!!! tip "Recommended"
    If you're using Claude Code, the `/generative` skill is the **recommended** way to generate media. It handles everything automatically - no need to remember command-line arguments.

---

## What is the /generative Skill?

The `/generative` skill is a Claude Code skill that:

- :material-magic-staff: **Understands natural language** - Just describe what you want
- :material-cog-sync: **Auto-configures** - Sets up optimal parameters automatically
- :material-shield-check: **Handles errors** - Provides helpful error messages
- :material-lightning-bolt: **Quick access** - Type `/generative` and go

---

## Quick Start

### 1. Install the Skill

```bash
# Clone repository
git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
cd claude-code-media-generator

# Install skill
mkdir -p ~/.claude/skills/generative
cp .claude/skills/generative/SKILL.md ~/.claude/skills/generative/

# Install dependencies
pip install requests google-auth

# Restart Claude Code
```

### 2. Configure API

```bash
# In Claude Code, run:
/generative config setup
```

The wizard will guide you through API configuration.

### 3. Generate Media

```bash
# Generate an image
/generative image "A beautiful sunset over mountains"

# Generate a video
/generative video "Ocean waves crashing on the beach"
```

---

## Skill vs Script Comparison

### :material-star: Using Skill (Recommended)

```bash
# Natural language - skill figures out the rest
/generative video "Create a cinematic sunset with warm colors"

# The skill automatically:
# - Chooses appropriate model (veo-3.1)
# - Sets optimal duration (5-8s)
# - Applies quality settings
# - Handles API calls
```

### Using Script (Advanced)

```bash
# Manual - you specify everything
python video_gen.py "A cinematic sunset" \
  --model veo-3.1-generate-preview \
  --duration 8 \
  --aspect-ratio 16:9 \
  --preset quality
```

---

## What Gets Installed

| Component | Location | Purpose |
|-----------|----------|---------|
| `SKILL.md` | `~/.claude/skills/generative/` | Skill definition (1,500+ lines) |
| `video_gen.py` | Working directory | Video generation CLI |
| `image_gen.py` | Working directory | Image generation CLI |
| `config.py` | Working directory | Configuration module |
| `video_utils.py` | Working directory | Video utilities |

---

## Next Steps

- [Agent (Optional)](agent.md) - Auto-detect media creation intent
- [Commands Reference](commands.md) - All `/generative` commands
- [Examples](examples.md) - Usage examples
- [Configuration](configuration.md) - Setup and configuration options
