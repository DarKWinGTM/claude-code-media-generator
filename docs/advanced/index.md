# Advanced Usage

For power users, automation, and CI/CD integration.

---

## When to Use Direct Scripts

While the `/generative` skill is recommended for most users, direct scripts are better for:

| Use Case | Why Use Scripts |
|----------|----------------|
| **Automation** | Shell scripts, cron jobs, batch processing |
| **CI/CD** | GitHub Actions, Jenkins, automated pipelines |
| **Custom Workflows** | Complex multi-step processes |
| **Headless Servers** | No Claude Code available |
| **Fine-Grained Control** | Specific parameters for each call |

---

## Available Scripts

| Script | Purpose | Documentation |
|--------|---------|---------------|
| `video_gen.py` | Video generation CLI | [video_gen.py Reference](video_gen.md) |
| `image_gen.py` | Image generation CLI | [image_gen.py Reference](image_gen.md) |
| `check_api.py` | API verification tool | [check_api.py Reference](check_api.md) |

---

## Quick Comparison

### Skill (Recommended for Interactive Use)

```bash
/generative video "A sunset over the ocean"
```

### Script (For Automation)

```bash
python video_gen.py "A sunset over the ocean" \
  --model veo-3.1-generate-preview \
  --duration 8 \
  --aspect-ratio 16:9 \
  --preset quality
```

---

## Basic Script Usage

### Video Generation

```bash
# Simple text-to-video
python video_gen.py "A cat playing in a garden"

# With options
python video_gen.py "A cat playing in a garden" \
  --model veo-3.1-generate-preview \
  --duration 8 \
  --preset quality
```

### Image Generation

```bash
# Simple text-to-image
python image_gen.py "A futuristic city at sunset"

# With options
python image_gen.py "A futuristic city at sunset" \
  --aspect-ratio 16:9 \
  --image-size 2K
```

### API Check

```bash
# Verify API connectivity
python check_api.py --key "$GOOGLE_API_KEY"

# List available models
python check_api.py --key "$GOOGLE_API_KEY" --list-models
```

---

## Environment Setup

### Required Environment Variables

```bash
# Set API key
export GOOGLE_API_KEY="AIzaSy..."

# Optional: GCS storage
export GCS_STORAGE_URI="gs://my-project-media-output/videos/"

# Optional: Project ID (for Vertex AI)
export GOOGLE_CLOUD_PROJECT="my-project-123"
```

### Using config.json

Create `config.json` in your working directory:

```json
{
  "api_key": "AIzaSy...",
  "project_id": "my-project-123",
  "storage_uri": "gs://my-project-media-output/videos/"
}
```

---

## Automation Examples

### Bash Script

```bash
#!/bin/bash
# generate_batch.sh

PROMPTS=(
  "A sunrise over mountains"
  "Ocean waves at sunset"
  "City skyline at night"
)

for prompt in "${PROMPTS[@]}"; do
  python video_gen.py "$prompt" --preset quick
  sleep 5  # Rate limiting
done
```

### Python Script

```python
#!/usr/bin/env python3
# batch_generate.py

import subprocess
import time

prompts = [
    "A sunrise over mountains",
    "Ocean waves at sunset",
    "City skyline at night",
]

for prompt in prompts:
    subprocess.run([
        "python", "video_gen.py",
        prompt,
        "--preset", "quick"
    ])
    time.sleep(5)  # Rate limiting
```

### GitHub Actions

```yaml
# .github/workflows/generate-videos.yml
name: Generate Videos

on:
  workflow_dispatch:
    inputs:
      prompt:
        description: 'Video prompt'
        required: true

jobs:
  generate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: pip install requests google-auth

      - name: Generate video
        env:
          GOOGLE_API_KEY: ${{ secrets.GOOGLE_API_KEY }}
        run: |
          python video_gen.py "${{ inputs.prompt }}" --preset quick

      - name: Upload artifact
        uses: actions/upload-artifact@v4
        with:
          name: generated-video
          path: generated_videos/
```

---

## Advanced Features

### Video Extension Pipeline

```bash
# Step 1: Generate initial video
python video_gen.py "Opening scene of a story" \
  --storage-uri "gs://my-project-media-output/videos/"

# Step 2: Extend the video
python video_gen.py "Continue the story" \
  --mode extend \
  --video "gs://my-project-media-output/videos/video_001.mp4" \
  --storage-uri "gs://my-project-media-output/videos/"
```

### Reference Image Workflow

```bash
# Generate with subject preservation
python video_gen.py "This character walking in a park" \
  --mode reference_asset \
  --reference-image ./character.png
```

### Batch Processing with Logging

```bash
#!/bin/bash
# batch_with_logging.sh

LOG_FILE="generation_log.txt"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

echo "=== Batch Generation Started: $TIMESTAMP ===" >> $LOG_FILE

while IFS= read -r prompt; do
  echo "Generating: $prompt" >> $LOG_FILE
  python video_gen.py "$prompt" --preset quick 2>&1 >> $LOG_FILE
  echo "---" >> $LOG_FILE
done < prompts.txt

echo "=== Batch Complete ===" >> $LOG_FILE
```

---

## Error Handling

### Common Exit Codes

| Code | Meaning | Action |
|------|---------|--------|
| `0` | Success | Continue |
| `1` | General error | Check logs |
| `2` | Invalid arguments | Check command |
| `3` | API error | Check API key/quota |
| `4` | File not found | Check paths |

### Retry Logic

```bash
#!/bin/bash
# retry_generation.sh

MAX_RETRIES=3
RETRY_DELAY=10

for i in $(seq 1 $MAX_RETRIES); do
  python video_gen.py "$1" --preset quick
  if [ $? -eq 0 ]; then
    echo "Success!"
    exit 0
  fi
  echo "Attempt $i failed, retrying in ${RETRY_DELAY}s..."
  sleep $RETRY_DELAY
done

echo "All attempts failed"
exit 1
```

---

## Performance Tips

### 1. Use Presets

```bash
# Quick tests
python video_gen.py "test" --preset quick

# Final production
python video_gen.py "final" --preset quality
```

### 2. Rate Limiting

```bash
# Add delays between requests
sleep 5  # 5 seconds between calls
```

### 3. Parallel Processing (with caution)

```bash
# Run multiple generations in parallel
python video_gen.py "prompt 1" --preset quick &
python video_gen.py "prompt 2" --preset quick &
wait
```

!!! warning "API Rate Limits"
    Be mindful of API rate limits when running parallel requests. Check your quota in Google Cloud Console.

---

## Next Steps

- [video_gen.py Reference](video_gen.md) - Full video CLI documentation
- [image_gen.py Reference](image_gen.md) - Full image CLI documentation
- [check_api.py Reference](check_api.md) - API verification tool
- [Automation Guide](automation.md) - Detailed automation patterns
