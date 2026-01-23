# Troubleshooting

Common issues and solutions.

---

## Authentication Issues

### "Invalid API key"

**Problem:** API key is not valid or expired

**Solutions:**

1. Check key prefix: `AIzaSy...` for Gemini API
2. Regenerate key at [Google AI Studio](https://aistudio.google.com/)
3. Ensure key is not quoted incorrectly

```bash
# Check key format
echo $GOOGLE_API_KEY | head -c 10
# Should show: AIzaSy...
```

---

### "Permission denied" / "403 Forbidden"

**Problem:** API access not authorized

**Solutions:**

1. Enable Vertex AI API in Cloud Console
2. Check billing is enabled
3. Verify API key has correct permissions

```bash
# For Vertex AI, use OAuth instead of API key
gcloud auth application-default login
TOKEN=$(gcloud auth application-default print-access-token)
python video_gen.py "test" --key "$TOKEN"
```

---

### "401 Unauthorized"

**Problem:** Authentication failed

**Solutions:**

1. Vertex AI keys (`AQ...`) need OAuth for some operations
2. Token may be expired - refresh it

```bash
# Refresh OAuth token
gcloud auth application-default login
```

---

## Video Generation Issues

### "Model not found"

**Problem:** Specified model doesn't exist

**Solutions:**

1. Check model name spelling
2. List available models

```bash
python check_api.py --key "$GOOGLE_API_KEY"
```

**Common models:**

- `veo-3.1-generate-preview`
- `veo-3.1-fast-generate-preview`
- `veo-2.0`

---

### "Rate limit exceeded"

**Problem:** Too many requests

**Solutions:**

1. Wait 30-60 seconds between requests
2. Use `--preset budget` for lower cost
3. Batch requests during off-peak hours

---

### "Billing not enabled"

**Problem:** Video generation requires billing

**Solution:**

1. Go to [Cloud Console](https://console.cloud.google.com/)
2. Enable billing for your project
3. Link a payment method

!!! info "Free Tier"
    Image generation with Gemini 2.0 Flash is free tier, but video generation requires billing.

---

## Video Extension Issues

### "video is empty"

**Problem:** External URL used for extension

**Cause:** API doesn't support HTTP/HTTPS URLs

**Solution:** Download video first, then use local file

```bash
# Download
curl -o local.mp4 "https://example.com/video.mp4"

# Use local file
python video_gen.py "Continue" --extend-video local.mp4
```

---

### "fps mismatch"

**Problem:** Source video is not 24fps

**Solution:** Convert to 24fps using ffmpeg

```bash
# Check current fps
ffprobe -v error -select_streams v:0 -show_entries stream=r_frame_rate input.mp4

# Convert to 24fps
ffmpeg -i input.mp4 -r 24 -c:v libx264 output_24fps.mp4

# Use converted file
python video_gen.py "Continue" --extend-video output_24fps.mp4
```

---

### "Model does not support video_extension"

**Problem:** Using veo-3.1 for extension

**Solution:** Use veo-2.0 for video extension

```bash
python video_gen.py "Continue" \
  --extend-video input.mp4 \
  --model veo-2.0
```

---

## Reference Image Issues

### "Too many references"

**Problem:** Exceeded reference image limits

**Limits:**

- Maximum 3 asset images
- Maximum 1 style image
- Cannot mix asset and style

**Solution:** Reduce number of references

```bash
# Use max 3 assets
python video_gen.py "prompt" \
  --reference-image img1.png:asset \
  --reference-image img2.png:asset \
  --reference-image img3.png:asset
```

---

### "does not support this mix"

**Problem:** Mixing asset and style references

**Solution:** Use only one type per request

```bash
# ❌ Wrong
--reference-image face.png:asset --reference-image style.jpg:style

# ✅ Correct - assets only
--reference-image face1.png:asset --reference-image face2.png:asset

# ✅ Correct - style only
--reference-image painting.jpg:style
```

---

### "Reference type not supported"

**Problem:** Style reference on wrong model

**Solution:** Use `veo-2.0-exp` for style

```bash
python video_gen.py "prompt" \
  --reference-image style.jpg:style \
  --model veo-2.0-exp
```

---

## GCS Issues

### "Bucket not found"

**Problem:** GCS bucket doesn't exist

**Solution:**

```bash
# Create bucket
gsutil mb gs://your-bucket-name
```

---

### "Permission denied on bucket"

**Problem:** No access to GCS bucket

**Solution:**

```bash
# Grant access
gsutil iam ch user:your@email.com:objectAdmin gs://your-bucket
```

---

### "gsutil not found"

**Problem:** gcloud SDK not installed

**Solution:**

```bash
# Install gcloud
curl https://sdk.cloud.google.com | bash

# Or use Python package
pip install google-cloud-storage
```

---

## General Issues

### "Timeout"

**Problem:** Request took too long

**Solutions:**

1. Video generation can take 60-180 seconds
2. Check internet connection
3. Try again - may be temporary

---

### "Output file not created"

**Problem:** Video generation failed silently

**Solutions:**

1. Check `--verbose` output for errors
2. Verify output directory exists
3. Check disk space

```bash
python video_gen.py "prompt" --verbose
```

---

### "Import error: requests"

**Problem:** Missing dependencies

**Solution:**

```bash
pip install requests google-auth google-auth-oauthlib
```

---

## Getting Help

1. Check this troubleshooting guide
2. Run with `--verbose` for details
3. Check [Changelog](https://github.com/darkwingtm/claude-code-media-generator/releases) for known issues
4. Open issue on [GitHub](https://github.com/darkwingtm/claude-code-media-generator/issues)

---

## Learn More

- [Authentication](../getting-started/authentication.md) - Auth setup guide
- [Video Extension](../video/extension.md) - Extension requirements
- [GCS Storage](gcs-storage.md) - GCS configuration
