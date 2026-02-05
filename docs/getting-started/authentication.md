# Authentication

Claude Code Media Generator supports multiple authentication methods for Google APIs.

!!! tip "Using Claude Code Skill?"

    If you have the `/generative` skill installed, use the built-in setup wizard:

    ```bash
    /generative config setup
    ```

    This will guide you through API configuration step-by-step.

---

## Authentication Methods Overview

| Method | Type | Best For | Interactive |
|--------|------|----------|-------------|
| **API Key** | Static Key | Quick start, Gemini API | No |
| **OAuth2 (gcloud)** | OAuth 2.0 | Full Vertex AI access | Yes (browser) |
| **Service Account** | OAuth 2.0 JWT | Server/automation | No |

---

## :key: Method 1: API Key (Recommended for Start)

### Get Your API Key

1. Go to [Google AI Studio](https://aistudio.google.com/)
2. Click **Get API Key**
3. Create a new key or use existing

### API Key Types

| Prefix | Type | Works With |
|--------|------|------------|
| `AIzaSy...` | Gemini API Key | Gemini API endpoint |
| `AQ.Ab8R...` | Vertex AI Key | Vertex AI endpoint (limited) |

### Set API Key

=== "Environment Variable"

    ```bash
    export GOOGLE_API_KEY="AIzaSy..."
    ```

=== "Command Line"

    ```bash
    python video_gen.py "prompt" --key "AIzaSy..."
    ```

=== "Config File"

    ```json
    {
      "main_projects": {
        "default": {
          "api_key": "AIzaSy..."
        }
      }
    }
    ```

---

## :lock: Method 2: OAuth2 via gcloud (Full Access)

### Setup

```bash
# Install gcloud CLI
# https://cloud.google.com/sdk/docs/install

# Login to Google Cloud
gcloud auth login

# Set application default credentials
gcloud auth application-default login

# Get access token (for testing)
gcloud auth application-default print-access-token
```

### Usage

```bash
# Get token
TOKEN=$(gcloud auth application-default print-access-token)

# Use with video_gen.py
python video_gen.py "prompt" --key "$TOKEN"
```

---

## :robot: Method 3: Service Account (Automation)

### Create Service Account

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Navigate to **IAM & Admin** → **Service Accounts**
3. Click **Create Service Account**
4. Grant roles: `Vertex AI User`, `Storage Object Admin`
5. Download JSON key file

### Usage

```bash
# Set credentials path
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/key.json"

# video_gen.py will auto-detect
python video_gen.py "prompt"
```

---

## Endpoint Compatibility Matrix

| Auth Method | Gemini API | Vertex AI |
|-------------|------------|-----------|
| API Key (`AIzaSy...`) | :white_check_mark: | :x: |
| Vertex AI Key (`AQ...`) | :x: | :warning: Limited |
| OAuth2 Token | :white_check_mark: | :white_check_mark: |
| Service Account | :x: | :white_check_mark: |

---

## Config File (Multi-Project)

```json
{
  "main_projects": {
    "default": {
      "api_key": "AIzaSy...",
      "gcs_bucket": "my-bucket",
      "project_id": "my-project"
    },
    "work": {
      "api_key": "AIzaSy...",
      "gcs_bucket": "work-bucket"
    }
  }
}
```

```bash
# Use specific project
python video_gen.py "prompt" --project work
```

---

## Troubleshooting

### "Invalid API key"
- Check key prefix: `AIzaSy...` for Gemini API
- Ensure key is not expired or revoked

### "Permission denied"
- Enable Vertex AI API in Cloud Console
- Check billing is enabled

### "Token expired"
- Re-run `gcloud auth application-default login`

---

## Next Steps

- [Quick Start](quick-start.md) - Generate your first video
- [GCS Storage](../guides/gcs-storage.md) - Set up cloud storage
- [Using Skills](../skills/overview.md) - Use the `/generative` skill
