# Installation

## System Requirements

- **Python** 3.8+
- **pip** package manager
- **Google Account** with billing enabled (for video generation)
- **Claude Code** (recommended for skill usage)

---

## Choose Your Installation Method

=== ":material-star: Skill (Recommended)"

    The easiest way to use this project - install the `/generative` skill for Claude Code.

    ### Step 1: Clone Repository

    ```bash
    git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
    cd claude-code-media-generator
    ```

    ### Step 2: Install Dependencies

    ```bash
    pip install requests google-auth google-auth-oauthlib
    ```

    ### Step 3: Install Skill

    ```bash
    # Create skill directory
    mkdir -p ~/.claude/skills/generative

    # Copy skill file
    cp .claude/skills/generative/SKILL.md ~/.claude/skills/generative/

    # Copy scripts to your working directory
    cp video_gen.py image_gen.py config.py video_utils.py /path/to/your/project/
    ```

    ### Step 4: Restart Claude Code

    Restart Claude Code to detect the new skill.

    ### Step 5: Verify

    ```bash
    /generative info
    ```

    [:material-arrow-right: Skill Configuration](../skills/configuration.md){ .md-button }

=== ":material-console: Script (Advanced)"

    For automation, CI/CD, or use without Claude Code.

    ### Step 1: Clone Repository

    ```bash
    git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
    cd claude-code-media-generator
    ```

    ### Step 2: Install Dependencies

    ```bash
    # Core dependencies
    pip install requests google-auth google-auth-oauthlib

    # For GCS uploads (optional)
    pip install google-cloud-storage
    ```

    ### Step 3: Verify

    ```bash
    # Check Python version
    python3 --version

    # Test video_gen.py help
    python video_gen.py --help
    ```

    [:material-arrow-right: Advanced Usage](../advanced/index.md){ .md-button }

---

## File Structure

```
claude-code-media-generator/
├── video_gen.py        # Video generation CLI
├── image_gen.py        # Image generation CLI
├── check_api.py        # API verification tool
├── config.py           # Configuration module
├── video_utils.py      # Video utilities
├── config.json         # Your configuration
├── .claude/
│   └── skills/
│       └── generative/
│           └── SKILL.md  # Skill definition (1,500+ lines)
├── generated_videos/   # Output directory
└── generated_images/   # Output directory
```

---

## Next Steps

- [Authentication](authentication.md) - Set up your API key
- [Quick Start](quick-start.md) - Generate your first video
- [Using Skills](../skills/overview.md) - Learn about the `/generative` skill
- [Agent](../skills/agent.md) - Auto-detect media creation intent (recommended)
