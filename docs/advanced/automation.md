# Automation Guide

Complete guide to automating video and image generation.

---

## Overview

This guide covers automation patterns for:

- Batch processing
- CI/CD integration
- Scheduled generation
- Error handling and monitoring

---

## Prerequisites

Before automating, ensure:

1. **Scripts installed** - `video_gen.py`, `image_gen.py` in working directory
2. **Dependencies** - `pip install requests google-auth`
3. **API configured** - `config.json` or environment variables
4. **API quota** - Sufficient quota for planned workload

---

## Batch Processing

### Simple Batch Script

```bash
#!/bin/bash
# batch_generate.sh

set -e  # Exit on error

PROMPTS_FILE="${1:-prompts.txt}"
OUTPUT_DIR="${2:-generated_videos}"

mkdir -p "$OUTPUT_DIR"

while IFS= read -r prompt; do
  echo "Generating: $prompt"
  python video_gen.py "$prompt" \
    --preset quick \
    --output-dir "$OUTPUT_DIR"
  sleep 5  # Rate limiting
done < "$PROMPTS_FILE"

echo "Batch complete!"
```

**Usage:**
```bash
chmod +x batch_generate.sh
./batch_generate.sh prompts.txt output/
```

### prompts.txt Example

```text
A sunrise over mountains, cinematic, warm colors
Ocean waves crashing on rocks, slow motion
City skyline at night, timelapse style
```

---

## Python Automation

### Basic Automation Script

```python
#!/usr/bin/env python3
"""batch_automation.py - Automated video generation"""

import subprocess
import time
import json
import logging
from pathlib import Path
from datetime import datetime

# Setup logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('automation.log'),
        logging.StreamHandler()
    ]
)

def generate_video(prompt: str, preset: str = "quick") -> bool:
    """Generate a video and return success status."""
    try:
        result = subprocess.run(
            ["python", "video_gen.py", prompt, "--preset", preset],
            capture_output=True,
            text=True,
            timeout=300  # 5 minute timeout
        )

        if result.returncode == 0:
            logging.info(f"Success: {prompt[:50]}...")
            return True
        else:
            logging.error(f"Failed: {result.stderr}")
            return False

    except subprocess.TimeoutExpired:
        logging.error(f"Timeout: {prompt[:50]}...")
        return False
    except Exception as e:
        logging.error(f"Error: {e}")
        return False

def process_batch(prompts: list, rate_limit: int = 5) -> dict:
    """Process a batch of prompts with rate limiting."""
    results = {"success": 0, "failed": 0, "prompts": []}

    for i, prompt in enumerate(prompts, 1):
        logging.info(f"Processing {i}/{len(prompts)}")

        success = generate_video(prompt)
        results["prompts"].append({
            "prompt": prompt,
            "success": success,
            "timestamp": datetime.now().isoformat()
        })

        if success:
            results["success"] += 1
        else:
            results["failed"] += 1

        # Rate limiting
        if i < len(prompts):
            time.sleep(rate_limit)

    return results

def main():
    # Load prompts
    prompts = [
        "A sunrise over mountains, cinematic",
        "Ocean waves at golden hour",
        "City traffic timelapse, night"
    ]

    # Process batch
    results = process_batch(prompts)

    # Save results
    with open("results.json", "w") as f:
        json.dump(results, f, indent=2)

    logging.info(f"Complete: {results['success']} success, {results['failed']} failed")

if __name__ == "__main__":
    main()
```

---

## CI/CD Integration

### GitHub Actions

```yaml
# .github/workflows/video-generation.yml
name: Video Generation Pipeline

on:
  workflow_dispatch:
    inputs:
      prompts:
        description: 'JSON array of prompts'
        required: true
        default: '["A sunset", "Ocean waves"]'
      preset:
        description: 'Generation preset'
        required: false
        default: 'quick'
        type: choice
        options:
          - quick
          - quality
          - budget

jobs:
  generate:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          pip install requests google-auth

      - name: Generate videos
        env:
          GOOGLE_API_KEY: ${{ secrets.GOOGLE_API_KEY }}
        run: |
          echo '${{ inputs.prompts }}' | jq -r '.[]' | while read prompt; do
            echo "Generating: $prompt"
            python video_gen.py "$prompt" --preset ${{ inputs.preset }}
            sleep 5
          done

      - name: Upload artifacts
        uses: actions/upload-artifact@v4
        with:
          name: generated-videos
          path: generated_videos/
          retention-days: 7
```

### Jenkins Pipeline

```groovy
// Jenkinsfile
pipeline {
    agent any

    environment {
        GOOGLE_API_KEY = credentials('google-api-key')
    }

    parameters {
        text(name: 'PROMPTS', defaultValue: 'A sunset\nOcean waves', description: 'One prompt per line')
        choice(name: 'PRESET', choices: ['quick', 'quality', 'budget'], description: 'Generation preset')
    }

    stages {
        stage('Setup') {
            steps {
                sh 'pip install requests google-auth'
            }
        }

        stage('Generate') {
            steps {
                script {
                    def prompts = params.PROMPTS.split('\n')
                    prompts.each { prompt ->
                        sh """
                            python video_gen.py "${prompt}" --preset ${params.PRESET}
                            sleep 5
                        """
                    }
                }
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'generated_videos/*', fingerprint: true
            }
        }
    }
}
```

---

## Scheduled Generation

### Cron Job (Linux/macOS)

```bash
# Edit crontab
crontab -e

# Add scheduled generation (daily at 2 AM)
0 2 * * * /path/to/batch_generate.sh /path/to/prompts.txt >> /var/log/video_gen.log 2>&1
```

### Systemd Timer (Linux)

```ini
# /etc/systemd/system/video-gen.timer
[Unit]
Description=Daily Video Generation

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```

```ini
# /etc/systemd/system/video-gen.service
[Unit]
Description=Video Generation Service

[Service]
Type=oneshot
ExecStart=/path/to/batch_generate.sh
User=your-user
Environment=GOOGLE_API_KEY=your-key
WorkingDirectory=/path/to/project
```

### Windows Task Scheduler

```powershell
# Create scheduled task
$action = New-ScheduledTaskAction -Execute "python" `
    -Argument "video_gen.py 'Daily prompt' --preset quick" `
    -WorkingDirectory "C:\path\to\project"

$trigger = New-ScheduledTaskTrigger -Daily -At 2am

Register-ScheduledTask -Action $action -Trigger $trigger `
    -TaskName "VideoGeneration" -Description "Daily video generation"
```

---

## Error Handling

### Retry Logic

```python
import time
import subprocess
from functools import wraps

def retry(max_attempts=3, delay=10):
    """Decorator for retry logic."""
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts:
                        raise
                    print(f"Attempt {attempt} failed: {e}")
                    time.sleep(delay)
            return None
        return wrapper
    return decorator

@retry(max_attempts=3, delay=10)
def generate_with_retry(prompt: str):
    """Generate video with automatic retry."""
    result = subprocess.run(
        ["python", "video_gen.py", prompt, "--preset", "quick"],
        check=True
    )
    return result
```

### Error Notifications

```python
import smtplib
from email.mime.text import MIMEText

def send_error_notification(error_message: str):
    """Send email notification on error."""
    msg = MIMEText(f"Video generation failed:\n\n{error_message}")
    msg['Subject'] = 'Video Generation Error'
    msg['From'] = 'automation@example.com'
    msg['To'] = 'admin@example.com'

    with smtplib.SMTP('smtp.example.com') as server:
        server.send_message(msg)
```

---

## Monitoring

### Basic Logging

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('automation.log'),
        logging.StreamHandler()
    ]
)
```

### Metrics Collection

```python
import json
from datetime import datetime

class MetricsCollector:
    def __init__(self, metrics_file="metrics.json"):
        self.metrics_file = metrics_file
        self.metrics = {"runs": []}

    def record_run(self, prompt: str, success: bool, duration: float):
        self.metrics["runs"].append({
            "timestamp": datetime.now().isoformat(),
            "prompt": prompt,
            "success": success,
            "duration_seconds": duration
        })

    def save(self):
        with open(self.metrics_file, "w") as f:
            json.dump(self.metrics, f, indent=2)

    def get_success_rate(self) -> float:
        if not self.metrics["runs"]:
            return 0.0
        successes = sum(1 for r in self.metrics["runs"] if r["success"])
        return successes / len(self.metrics["runs"])
```

---

## Best Practices

### 1. Rate Limiting

```python
# Respect API rate limits
time.sleep(5)  # Between requests
```

### 2. Idempotency

```python
# Check if output already exists
output_path = f"generated_videos/{prompt_hash}.mp4"
if os.path.exists(output_path):
    print("Already generated, skipping")
    continue
```

### 3. Graceful Shutdown

```python
import signal
import sys

def signal_handler(sig, frame):
    print("Graceful shutdown...")
    # Cleanup code here
    sys.exit(0)

signal.signal(signal.SIGINT, signal_handler)
signal.signal(signal.SIGTERM, signal_handler)
```

### 4. Configuration Management

```python
# Use environment-specific configs
import os

CONFIG = {
    "development": {
        "preset": "quick",
        "rate_limit": 2
    },
    "production": {
        "preset": "quality",
        "rate_limit": 5
    }
}

env = os.getenv("ENVIRONMENT", "development")
config = CONFIG[env]
```

---

## Next Steps

- [Advanced Usage Overview](index.md) - All advanced features
- [video_gen.py Reference](video_gen.md) - Full CLI documentation
- [GCS Storage](../guides/gcs-storage.md) - Cloud storage setup
