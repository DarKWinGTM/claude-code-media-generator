# CLI Reference: check_api.py

Complete command-line reference for API verification.

---

## Synopsis

```bash
python check_api.py [OPTIONS]
```

---

## Description

API Checker verifies API connectivity, lists available models, and checks quota information for both Gemini API and Vertex AI endpoints.

---

## Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `--key` | string | $GOOGLE_API_KEY | API key or OAuth token |
| `--location` | string | us-central1 | Vertex AI region |
| `--project-id` | string | - | GCP project ID |
| `--verbose` | flag | - | Show detailed output |

---

## API Key Detection

The script auto-detects endpoint based on key format:

| Prefix | Endpoint | Authentication |
|--------|----------|----------------|
| `AIzaSy...` | Gemini API | API Key works |
| `AQ.Ab8R...` | Vertex AI | OAuth2 required! |
| `ya29....` | Both | OAuth2 token |

---

## Examples

### Check Gemini API

```bash
# Using environment variable
export GOOGLE_API_KEY="AIzaSy..."
python check_api.py

# Using flag
python check_api.py --key "AIzaSy..."
```

### Check Vertex AI

```bash
# Get OAuth token first
TOKEN=$(gcloud auth application-default print-access-token)

# Check with token
python check_api.py --key "$TOKEN" --project-id my-project
```

### List Models

```bash
python check_api.py --key "$API_KEY" --verbose
```

---

## Output

```
🔍 API Checker v2.1
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📍 Endpoint: Gemini API (generativelanguage.googleapis.com)
🔑 Key Type: Gemini API Key

✅ Connection successful!

📋 Available Models:
   • gemini-2.0-flash-exp
   • gemini-2.0-flash
   • veo-3.1-generate-preview
   • veo-3.1-fast-generate-preview
   • veo-2.0
   ...
```

---

## Endpoint Compatibility

| Feature | Gemini API | Vertex AI |
|---------|------------|-----------|
| API Key Auth | :white_check_mark: | :x: |
| OAuth2 Auth | :white_check_mark: | :white_check_mark: |
| Service Account | :x: | :white_check_mark: |
| List Models | :white_check_mark: | :warning: OAuth only |
| Generate Video | :white_check_mark: | :white_check_mark: |

---

## Important Notes

### Vertex AI Keys Don't Work for Listing

!!! warning "Authentication Limitation"
    Vertex AI keys (`AQ...`) require OAuth for model listing.

```bash
# This will fail for model listing
python check_api.py --key "AQ.Ab8R..."  # Vertex AI key
# Error: 401 Unauthorized

# Use OAuth token instead
TOKEN=$(gcloud auth application-default print-access-token)
python check_api.py --key "$TOKEN"
```

### Video Generation Still Works

!!! info "Generation vs Listing"
    Even if model listing fails, video generation may still work with Vertex AI keys.

```bash
# Listing fails
python check_api.py --key "AQ..."  # ❌

# But video generation works!
python video_gen.py "prompt" --key "AQ..."  # ✅
```

---

## Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success - connection verified |
| 1 | Error - connection failed |

---

## See Also

- [Authentication](../getting-started/authentication.md) - Authentication methods
- [Quick Start](../getting-started/quick-start.md) - Getting started
