# Presets

Quick configuration presets for common use cases.

---

## Available Presets

| Preset | Model | Duration | Use Case |
|--------|-------|----------|----------|
| `quick` | veo-3.1-fast | 5s | Fast preview, drafts |
| `quality` | veo-3.1-generate | 8s | Production, final render |
| `extend` | veo-2.0 | 8s | Video extension |
| `budget` | veo-2.0 | 5s | Cost saving |

---

## Usage

```bash
python video_gen.py "Your prompt" --preset PRESET_NAME
```

---

## Preset Details

### quick

**Purpose:** Fast preview and iteration

```bash
python video_gen.py "A cat walking" --preset quick
```

| Setting | Value |
|---------|-------|
| Model | veo-3.1-fast-generate-preview |
| Duration | 5 seconds |
| Audio | :white_check_mark: Yes |
| Speed | Fast |
| Cost | $$ Medium |

**Best for:**

- Quick concept validation
- Drafts and iterations
- Time-sensitive projects

---

### quality

**Purpose:** Best possible output

```bash
python video_gen.py "A cinematic sunset" --preset quality
```

| Setting | Value |
|---------|-------|
| Model | veo-3.1-generate-preview |
| Duration | 8 seconds |
| Audio | :white_check_mark: Yes |
| Speed | Medium |
| Cost | $$$ Higher |

**Best for:**

- Final production videos
- Portfolio pieces
- Client deliverables

---

### extend

**Purpose:** Video extension workflow

```bash
python video_gen.py "Continue the scene" \
  --preset extend \
  --extend-video previous.mp4
```

| Setting | Value |
|---------|-------|
| Model | veo-2.0 |
| Duration | 8 seconds |
| Audio | :x: No |
| Extension | :white_check_mark: Supported |
| Cost | $$ Medium |

**Best for:**

- Extending existing videos
- Creating longer sequences
- Chaining multiple clips

---

### budget

**Purpose:** Cost-effective generation

```bash
python video_gen.py "Simple animation" --preset budget
```

| Setting | Value |
|---------|-------|
| Model | veo-2.0 |
| Duration | 5 seconds |
| Audio | :x: No |
| Speed | Medium |
| Cost | $ Lower |

**Best for:**

- High volume generation
- Testing and experiments
- Budget-conscious projects

---

## Combining with Options

Presets set defaults but can be overridden:

```bash
# Use quality preset but change duration
python video_gen.py "Prompt" --preset quality --duration 5

# Use extend preset with custom model
python video_gen.py "Prompt" --preset extend --model veo-2.0-exp
```

---

## Preset Comparison

```
Quality: ████████████████████ Best output
Quick:   ██████████████░░░░░░ Good balance
Budget:  ██████████░░░░░░░░░░ Cost effective
Extend:  ████████████████░░░░ Extension focused
```

---

## Cost Comparison

| Preset | Est. Cost per Video |
|--------|---------------------|
| quality | ~$0.50 |
| quick | ~$0.25 |
| extend | ~$0.20 |
| budget | ~$0.15 |

!!! tip "Check Exact Pricing"
    Use `--estimate-cost` to see exact pricing before generating.

---

## Custom Presets

For custom configurations, use `config.json`:

```json
{
  "presets": {
    "my_custom": {
      "model": "veo-3.1-generate-preview",
      "duration": 6,
      "aspect_ratio": "9:16"
    }
  }
}
```

---

## Learn More

- [Video Models](../video/models.md) - Model details
- [CLI: video_gen.py](../cli/video_gen.md) - Full CLI reference
- [Video Extension](../video/extension.md) - Extension workflow
