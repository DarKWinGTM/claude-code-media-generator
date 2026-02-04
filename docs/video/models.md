# Video Models

Comparison of available Veo models.

---

## Available Models

### veo-3.1-generate-preview (Recommended)

| Feature | Value |
|---------|-------|
| **Audio** | :white_check_mark: Yes - generates with sound |
| **Quality** | Best - highest resolution |
| **Video Extension** | :white_check_mark: Supported |
| **Cost** | $$$ Higher |

**Best for:** Final production, videos with audio, video extension

```bash
python video_gen.py "A waterfall" --model veo-3.1-generate-preview
```

---

### veo-3.1-fast-generate-preview

| Feature | Value |
|---------|-------|
| **Audio** | :white_check_mark: Yes |
| **Quality** | Good - faster generation |
| **Video Extension** | :x: Not supported |
| **Cost** | $$ Medium |

**Best for:** Quick previews, drafts

```bash
python video_gen.py "A waterfall" --model veo-3.1-fast-generate-preview
# Or use preset
python video_gen.py "A waterfall" --preset quick
```

---

### veo-2.0

| Feature | Value |
|---------|-------|
| **Audio** | :x: No |
| **Quality** | Good |
| **Video Extension** | :white_check_mark: Supported |
| **Cost** | $$ Medium |

**Best for:** Video extension without audio, chaining videos

```bash
python video_gen.py "A waterfall" --model veo-2.0
# Also supports video extension
python video_gen.py "Continue scene" --extend-video input.mp4 --model veo-2.0
```

---

### veo-2.0-exp

| Feature | Value |
|---------|-------|
| **Audio** | :x: No |
| **Quality** | Good |
| **Video Extension** | :white_check_mark: Supported |
| **Reference Style** | :white_check_mark: Supported |
| **Cost** | $$ Medium |

**Best for:** Style transfer, experimental features

```bash
python video_gen.py "A cityscape" \
  --model veo-2.0-exp \
  --reference-image painting.jpg:style
```

---

## Feature Comparison Matrix

| Feature | veo-3.1 | veo-3.1-fast | veo-2.0 | veo-2.0-exp |
|---------|---------|--------------|---------|-------------|
| Audio Generation | :white_check_mark: | :white_check_mark: | :x: | :x: |
| Video Extension | :white_check_mark: | :x: | :white_check_mark: | :white_check_mark: |
| Reference Style | :x: | :x: | :x: | :white_check_mark: |
| Reference Asset | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Insert/Remove Objects | :x: | :x: | :white_check_mark: | :white_check_mark: |
| Generation Speed | Medium | Fast | Medium | Medium |
| Quality | Best | Good | Good | Good |

---

## Model Selection Guide

```
Need audio?
  → YES: veo-3.1-generate-preview (also supports extension)
  → NO: Continue below

Need video extension?
  → YES: veo-3.1-generate-preview, veo-2.0, or veo-2.0-exp
  → NO: Continue below

Need style transfer?
  → YES: veo-2.0-exp
  → NO: Continue below

Need fast preview?
  → YES: veo-3.1-fast-generate-preview
  → NO: veo-3.1-generate-preview
```

---

## Using Presets

Presets automatically select the best model:

| Preset | Model | Duration | Use Case |
|--------|-------|----------|----------|
| `quick` | veo-3.1-fast | 5s | Fast preview |
| `quality` | veo-3.1-generate | 8s | Production |
| `extend` | veo-3.1-generate | 7s | Video extension |
| `budget` | veo-3.0-fast | 5s | Cost saving |

```bash
python video_gen.py "A waterfall" --preset quality
```

---

## API Endpoints

| Model | Gemini API | Vertex AI |
|-------|------------|-----------|
| veo-3.1-generate-preview | :white_check_mark: | :white_check_mark: |
| veo-3.1-fast-generate-preview | :white_check_mark: | :white_check_mark: |
| veo-2.0 | :white_check_mark: | :white_check_mark: |
| veo-2.0-exp | :x: | :white_check_mark: |

---

## Learn More

- [Video Overview](overview.md) - Video generation overview
- [Generation Modes](modes.md) - All generation modes
- [Presets](../guides/presets.md) - Using presets
