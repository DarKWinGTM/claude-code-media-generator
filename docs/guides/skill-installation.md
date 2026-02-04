# Claude Code Skill Installation

Install the `/generative` skill to use AI-assisted video and image generation directly in Claude Code.

---

## What Gets Installed

| Component | Location | Purpose |
|-----------|----------|---------|
| `SKILL.md` | `~/.claude/skills/generative/` | Skill definition |
| `video_gen.py` | Working directory | Video generation CLI |
| `image_gen.py` | Working directory | Image generation CLI |
| `config.py` | Working directory | Configuration module |
| `video_utils.py` | Working directory | Video utilities |

---

## Installation

=== ":material-linux: Linux / macOS"

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

=== ":material-microsoft-windows: Windows (PowerShell)"

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

=== ":material-console: Windows (CMD)"

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

---

## Verify Installation

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

---

## Configuration

After installation, configure your API using the built-in setup wizard:

=== ":material-wizard-hat: Setup Wizard"

    ```bash
    # Start guided setup wizard
    /generative config setup
    ```

    The wizard will guide you through:

    1. **API Type Selection** - Gemini API or Vertex AI
    2. **API Key Setup** - Enter your key
    3. **Project Configuration** - Set project ID
    4. **gcloud CLI** - Optional installation for Vertex AI

=== ":material-cog: Manual Setup"

    ```bash
    # Configure Gemini API
    /generative config gemini

    # Configure Vertex AI
    /generative config vertex

    # Install gcloud CLI (optional)
    /generative config gcloud
    ```

=== ":material-eye: View Current Config"

    ```bash
    # Show current configuration
    /generative config show
    ```

---

## Directory Structure After Installation

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

---

## Skill Commands Reference

### Main Commands

| Command | Description |
|---------|-------------|
| `/generative image "prompt"` | Generate image from text |
| `/generative video "prompt"` | Generate video from text |
| `/generative info` | Show capabilities overview |
| `/generative config` | Configuration management |
| `/generative help` | Detailed help system |

### Help Topics

| Command | Description |
|---------|-------------|
| `/generative help image` | Image generation guide |
| `/generative help video` | Video generation guide |
| `/generative help examples` | 10+ usage examples |
| `/generative help modes` | All 8 video modes explained |

### Config Sub-commands

| Command | Description |
|---------|-------------|
| `/generative config setup` | Guided setup wizard |
| `/generative config show` | Show current settings |
| `/generative config gemini` | Setup Gemini API |
| `/generative config vertex` | Setup Vertex AI |
| `/generative config gcloud` | Setup gcloud CLI |
| `/generative config reset` | Reset to defaults |

---

## Updating the Skill

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

## Troubleshooting

### Skill Not Recognized

!!! warning "Unknown skill: generative"

    Claude Code caches the skill list at startup. After installing a new skill:

    1. **Restart Claude Code** completely
    2. Try `/generative info` again
    3. If still not working, verify the file exists:
       ```bash
       ls ~/.claude/skills/generative/SKILL.md
       ```

### Python Scripts Not Found

!!! warning "ModuleNotFoundError or FileNotFoundError"

    Make sure the Python scripts are in your **current working directory**:

    ```bash
    ls -la video_gen.py image_gen.py config.py video_utils.py
    ```

    If missing, copy them from the cloned repository.

### API Key Issues

!!! warning "Invalid API key or Permission denied"

    Run the setup wizard to configure your API:

    ```bash
    /generative config setup
    ```

---

## Next Steps

- [Authentication](../getting-started/authentication.md) - API key details
- [Video Generation](../video/overview.md) - All 8 video modes
- [GCS Storage](gcs-storage.md) - Cloud storage setup
