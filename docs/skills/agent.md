# Generative Media Navigator Agent

The **Generative Media Navigator** is a Claude Code agent that automatically detects when you want to create videos or images, then invokes the `/generative` skill for you.

---

## Skill + Agent Architecture

| Component | Purpose | Type | Install |
|-----------|---------|------|---------|
| `/generative` Skill | Execute generation commands | Skill | ✅ Required |
| Generative Media Navigator | Auto-detect intent, invoke skill | Agent | ✅ Recommended |

!!! success "Recommended Setup"
    Install both the Skill and Agent for the complete experience. The agent provides natural language understanding on top of the skill's execution capabilities.

!!! info "Dependency"
    The agent requires the `/generative` skill to be installed first. See [Skill Installation](../getting-started/installation.md).

---

## How It Works

```
User: "สร้างวิดีโอแมวเดินบนดวงจันทร์"
  ↓
Agent detects: video intent, prompt = "cat walking on the moon"
  ↓
Agent invokes: /generative video "cat walking on the moon"
  ↓
Result returned to user
```

### Intent Detection

The agent recognizes these patterns:

**Video Keywords:**

| Thai | English |
|------|---------|
| สร้างวิดีโอ, ทำวิดีโอ | create video, make video |
| อยากได้วิดีโอ | I want a video |
| generate video | generate video |

**Image Keywords:**

| Thai | English |
|------|---------|
| สร้างรูป, ทำรูป, สร้างภาพ | create image, make image |
| อยากได้รูป | I want an image |
| generate image | generate image |

---

## Installation

### Prerequisites

- ✅ Claude Code installed
- ✅ `/generative` skill installed (see [Installation](../getting-started/installation.md))

### Linux / macOS

```bash
# Create agents directory
mkdir -p ~/.claude/agents

# Copy agent file from repository
cp .claude/agents/generative-media-navigator.md ~/.claude/agents/

# Restart Claude Code
```

### Windows (PowerShell)

```powershell
# Create agents directory
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\agents"

# Copy agent file
Copy-Item ".\.claude\agents\generative-media-navigator.md" `
   -Destination "$env:USERPROFILE\.claude\agents\"

# Restart Claude Code
```

---

## Verification

After installation, test with natural language:

```
You: "สร้างวิดีโอแมวเดินบนดวงจันทร์"
Agent: [Detects video intent → invokes /generative video "cat walking on the moon"]

You: "Create an image of a beautiful sunset"
Agent: [Detects image intent → invokes /generative image "beautiful sunset"]

You: "Make a video showing ocean waves"
Agent: [Detects video intent → invokes /generative video "ocean waves"]
```

---

## When Agent Does NOT Act

The agent will **not** auto-invoke when:

- Asking about previous generated content ("วิดีโอที่สร้างเมื่อกี้เป็นไง")
- Troubleshooting or checking status
- Request is ambiguous (will ask for clarification)
- User explicitly says they don't want to generate

---

## Directory Structure

After full installation (Skill + Agent):

```
~/.claude/
├── agents/
│   └── generative-media-navigator.md   ← Agent (optional)
└── skills/
    └── generative/
        └── SKILL.md                      ← Skill (required)
```

---

## Agent Configuration

The agent uses these settings:

| Setting | Value | Description |
|---------|-------|-------------|
| `name` | `generative-media-navigator` | Unique identifier |
| `tools` | `Bash, Read, Glob, Grep, AskUserQuestion, Skill` | Available tools |
| `model` | `inherit` | Uses parent model |
| `color` | `purple` | UI indicator color |

---

## Updating

```bash
# Pull latest changes
cd claude-code-media-generator
git pull

# Update agent file
# Linux/macOS:
cp .claude/agents/generative-media-navigator.md ~/.claude/agents/

# Windows (PowerShell):
Copy-Item ".\.claude\agents\generative-media-navigator.md" `
   -Destination "$env:USERPROFILE\.claude\agents\" -Force

# Restart Claude Code
```

---

## Related

- [Skill Overview](overview.md) - About the `/generative` skill
- [Commands](commands.md) - All skill commands
- [Configuration](configuration.md) - API setup
