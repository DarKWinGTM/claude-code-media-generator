# Claude Code Media Generator

> **Generate AI videos and images using Google Veo & Imagen APIs**

Welcome to the official documentation for **claude-code-media-generator** - a CLI tool for generating videos and images using Google's Veo and Imagen models.

---

## ✨ Features

| Feature | Description |
|---------|-------------|
| :movie_camera: **Video Generation** | 8 different generation modes using Veo 2.0/3.1 |
| :framed_picture: **Image Generation** | Text-to-image using Gemini & Imagen models |
| :wrench: **Flexible Auth** | API Key, OAuth2, Service Account support |
| :cloud: **GCS Integration** | Upload videos to Google Cloud Storage |
| :moneybag: **Cost Estimation** | Built-in cost tracking and preview |
| :zap: **Presets** | Quick, Quality, Extend, Budget presets |

---

## :movie_camera: Video Generation Modes

| Mode | Description |
|------|-------------|
| **Text-to-Video** | Generate from text prompt only |
| **Image-to-Video** | Generate from first frame image |
| **First & Last Frames** | Interpolate between two images |
| **Video Extension** | Extend existing videos |
| **Reference Asset** | Use up to 3 reference images |
| **Reference Style** | Apply style from reference image |
| **Insert Objects** | Add objects using mask |
| **Remove Objects** | Remove objects using mask |

---

## :rocket: Quick Start

```bash
# Install dependencies
pip install requests google-auth google-auth-oauthlib

# Set API key
export GOOGLE_API_KEY="AIzaSy..."

# Generate a video
python video_gen.py "A cat walking in a garden"

# Generate an image
python image_gen.py "A futuristic city at sunset"
```

---

## :books: Documentation

<div class="grid cards" markdown>

-   :material-download: **Getting Started**

    ---

    Installation, authentication, and your first video

    [:octicons-arrow-right-24: Get started](getting-started/installation.md)

-   :movie_camera: **Video Generation**

    ---

    All 8 modes, models, and advanced features

    [:octicons-arrow-right-24: Learn more](video/overview.md)

-   :framed_picture: **Image Generation**

    ---

    Text-to-image with Gemini & Imagen

    [:octicons-arrow-right-24: Learn more](image/index.md)

-   :material-console: **CLI Reference**

    ---

    Complete command-line documentation

    [:octicons-arrow-right-24: Reference](cli/video_gen.md)

</div>

---

## :chart_with_upwards_trend: Project Status

| Component | Version | Status |
|-----------|---------|--------|
| video_gen.py | v2.24.1 | :white_check_mark: Stable |
| image_gen.py | v1.3 | :white_check_mark: Stable |
| check_api.py | v2.1 | :white_check_mark: Stable |
| Documentation | v2.25 | :arrows_counterclockwise: Active |

---

## :link: Links

- [GitHub Repository](https://github.com/DarKWinGTM/claude-code-media-generator)
- [Google Veo Documentation](https://cloud.google.com/vertex-ai/docs/generative-ai/video/overview)
- [Google Imagen Documentation](https://cloud.google.com/vertex-ai/docs/generative-ai/image/overview)
