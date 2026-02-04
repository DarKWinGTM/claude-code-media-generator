# Installation

## System Requirements

- **Python** 3.8+
- **pip** package manager
- **Google Account** with billing enabled (for video generation)

---

## Install Dependencies

```bash
# Core dependencies
pip install requests google-auth google-auth-oauthlib

# For GCS uploads (optional)
pip install google-cloud-storage
```

---

## Download Scripts

```bash
# Clone the repository
git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
cd claude-code-media-generator

# Or download individual scripts
curl -O https://raw.githubusercontent.com/DarKWinGTM/claude-code-media-generator/main/video_gen.py
curl -O https://raw.githubusercontent.com/DarKWinGTM/claude-code-media-generator/main/image_gen.py
curl -O https://raw.githubusercontent.com/DarKWinGTM/claude-code-media-generator/main/check_api.py
```

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
├── config.example.json # Example config
├── design/             # Design documents
├── changelog/          # Version history
└── generated_videos/   # Output directory
```

---

## Verify Installation

```bash
# Check Python version
python3 --version

# Test video_gen.py help
python video_gen.py --help

# Test API connectivity (requires API key)
python check_api.py --key "YOUR_API_KEY"
```

---

## Next Steps

- [Authentication](authentication.md) - Set up your API key
- [Quick Start](quick-start.md) - Generate your first video
- [Skill Installation](../guides/skill-installation.md) - Install Claude Code Skill
