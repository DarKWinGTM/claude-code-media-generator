# Skill Configuration

Complete guide to configuring the `/generative` skill.

---

## Quick Setup

The easiest way to configure the skill is using the setup wizard:

```bash
/generative config setup
```

This interactive wizard guides you through:

1. **API Type Selection** - Choose Gemini API or Vertex AI
2. **API Key Setup** - Enter your authentication key
3. **Project Configuration** - Set Google Cloud project ID
4. **gcloud CLI** - Optional installation for Vertex AI

---

## Configuration Methods

### Method 1: Setup Wizard (Recommended)

```bash
/generative config setup
```

Interactive, beginner-friendly, handles all settings.

### Method 2: Direct Commands

```bash
# Setup Gemini API
/generative config gemini

# Setup Vertex AI
/generative config vertex

# Install gcloud CLI
/generative config gcloud
```

### Method 3: View Current Config

```bash
/generative config show
```

---

## API Types

### Gemini API (Easier)

Best for: Quick start, simple usage, image generation

```bash
/generative config gemini
```

**Requirements:**
- Google AI Studio API key
- No Google Cloud project needed for images
- Billing required for video generation

**Get API Key:**
1. Go to [Google AI Studio](https://aistudio.google.com/)
2. Click "Get API Key"
3. Create or select a project
4. Copy the key

### Vertex AI (Full Features)

Best for: Production use, GCS storage, advanced features

```bash
/generative config vertex
```

**Requirements:**
- Google Cloud project with billing enabled
- Service account or gcloud CLI authentication
- Vertex AI API enabled

**Setup Steps:**
1. Create Google Cloud project
2. Enable Vertex AI API
3. Enable billing
4. Create service account (or use gcloud CLI)
5. Configure with `/generative config vertex`

---

## Configuration File

The skill stores configuration in `config.json`:

```json
{
  "api_type": "gemini",
  "api_key": "AIzaSy...",
  "project_id": "my-project-123",
  "region": "us-central1",
  "default_model": "veo-3.1-generate-preview",
  "storage_uri": "gs://my-project-media-output/"
}
```

**Location:** Current working directory

---

## Configuration Options

### Core Settings

| Setting | Description | Example |
|---------|-------------|---------|
| `api_type` | API type | `gemini` or `vertex` |
| `api_key` | Authentication key | `AIzaSy...` |
| `project_id` | Google Cloud project | `my-project-123` |
| `region` | Vertex AI region | `us-central1` |

### Default Settings

| Setting | Description | Default |
|---------|-------------|---------|
| `default_model` | Default video model | `veo-3.1-generate-preview` |
| `default_preset` | Default preset | `quick` |
| `storage_uri` | GCS storage URI | *(optional)* |

---

## GCS Storage Configuration

For video extension and cloud storage:

### Bucket Naming Convention

!!! danger "Important: Bucket Naming"
    When using Vertex AI API Key, your GCS bucket **MUST** follow this pattern:

    ```
    gs://YOUR_PROJECT_ID-media-output/
    ```

### Setup GCS

```bash
# Configure storage URI
/generative config storage gs://my-project-id-media-output/videos/
```

Or add to `config.json`:

```json
{
  "storage_uri": "gs://my-project-id-media-output/videos/"
}
```

---

## Environment Variables

You can also use environment variables:

```bash
# Set API key
export GOOGLE_API_KEY="AIzaSy..."

# Set project ID
export GOOGLE_CLOUD_PROJECT="my-project-123"

# Set region
export GOOGLE_CLOUD_REGION="us-central1"
```

**Priority Order:**
1. Command-line arguments (highest)
2. `config.json` file
3. Environment variables
4. Default values (lowest)

---

## Reset Configuration

To reset all settings to defaults:

```bash
/generative config reset
```

This will:
- Remove `config.json`
- Clear cached settings
- Require re-configuration

---

## Troubleshooting

### "Invalid API key"

```bash
# Verify your API key
/generative config show

# Re-run setup
/generative config setup
```

### "Permission denied"

For Vertex AI:
```bash
# Ensure gcloud is authenticated
gcloud auth application-default login

# Check project permissions
gcloud projects get-iam-policy YOUR_PROJECT_ID
```

### "Model not found"

```bash
# Check available models
/generative info

# Ensure Vertex AI API is enabled
gcloud services enable aiplatform.googleapis.com
```

### "Billing not enabled"

Video generation requires billing:
1. Go to Google Cloud Console
2. Enable billing for your project
3. Link a payment method

---

## Security Best Practices

### 1. Protect Your API Key

```bash
# Don't commit config.json to git
echo "config.json" >> .gitignore
```

### 2. Use Environment Variables in CI/CD

```yaml
# GitHub Actions example
env:
  GOOGLE_API_KEY: ${{ secrets.GOOGLE_API_KEY }}
```

### 3. Rotate Keys Regularly

For production use, rotate API keys periodically:
1. Generate new key in Google AI Studio
2. Update configuration
3. Revoke old key

---

## Next Steps

- [Commands Reference](commands.md) - All skill commands
- [Examples](examples.md) - Usage examples
- [GCS Storage Guide](../guides/gcs-storage.md) - Detailed GCS setup
- [Authentication](../getting-started/authentication.md) - API key details
