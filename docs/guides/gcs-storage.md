# GCS Storage Guide

Google Cloud Storage integration for video generation.

---

## Why Use GCS?

| Benefit | Description |
|---------|-------------|
| **Video Extension** | Required for extending videos reliably |
| **Larger Files** | No base64 size limits |
| **Faster Processing** | Direct GCS-to-API transfer |
| **Persistent Storage** | Keep generated videos in cloud |

---

## Setup

### 1. Create a GCS Bucket

```bash
# Install gcloud CLI if needed
# https://cloud.google.com/sdk/docs/install

# Create bucket
gsutil mb gs://your-bucket-name

# Set location (optional)
gsutil mb -l us-central1 gs://your-bucket-name
```

### 2. Set Permissions

For Vertex AI to access your bucket:

```bash
# Grant access to Vertex AI service account
gsutil iam ch serviceAccount:service-PROJECT_NUMBER@gcp-sa-aiplatform.iam.gserviceaccount.com:objectViewer gs://your-bucket-name
```

---

## Usage

### With video_gen.py

```bash
# Generate video and store in GCS
python video_gen.py "A sunset" --storage-uri gs://your-bucket/videos

# Extend video (auto-uploads local file to GCS)
python video_gen.py "Continue scene" \
  --extend-video local_video.mp4 \
  --storage-uri gs://your-bucket/videos
```

### With Config File

Add to `config.json`:

```json
{
  "main_projects": {
    "default": {
      "api_key": "AIzaSy...",
      "gcs_bucket": "gs://your-bucket/videos"
    }
  }
}
```

Then use automatically:

```bash
python video_gen.py "A sunset"  # Uses config GCS bucket
```

---

## Storage URI Format

```
gs://BUCKET_NAME/PATH/

Examples:
gs://my-videos/
gs://my-videos/generated/
gs://my-project-bucket/ai-videos/2026/
```

---

## Auto-Upload Feature

When you provide `--storage-uri`:

1. Local files are uploaded to GCS before API call
2. Generated videos are stored in GCS
3. Videos are also downloaded locally

```
Local file → Upload to GCS → API uses gcsUri → Generated in GCS → Download local
```

---

## Checking Your Bucket

```bash
# List contents
gsutil ls gs://your-bucket/

# Check file details
gsutil ls -l gs://your-bucket/video.mp4

# Download file
gsutil cp gs://your-bucket/video.mp4 ./local_copy.mp4
```

---

## Cost Considerations

| Service | Cost |
|---------|------|
| Storage | ~$0.02/GB/month |
| Upload | Free |
| Download | ~$0.12/GB |
| Operations | Minimal |

!!! tip "Auto-Delete Old Files"
    Set up lifecycle rules to auto-delete old files

    ```bash
    # Create lifecycle rule (delete after 30 days)
    gsutil lifecycle set lifecycle.json gs://your-bucket
    ```

---

## Troubleshooting

### "Permission denied"

```bash
# Check bucket permissions
gsutil iam get gs://your-bucket

# Add yourself as owner
gsutil iam ch user:your@email.com:objectAdmin gs://your-bucket
```

### "Bucket not found"

```bash
# Verify bucket exists
gsutil ls gs://your-bucket

# Create if missing
gsutil mb gs://your-bucket
```

### "gsutil not found"

```bash
# Install Google Cloud SDK
curl https://sdk.cloud.google.com | bash

# Or use pip
pip install google-cloud-storage
```

---

## Alternative: REST API

If gsutil isn't available, the script can use REST API:

```python
# Handled automatically in video_gen.py
# Uses google-cloud-storage library
```

---

## Learn More

- [Video Extension](../video/extension.md) - Why GCS is important
- [Authentication](../getting-started/authentication.md) - GCS authentication
- [Troubleshooting](troubleshooting.md) - Common issues
