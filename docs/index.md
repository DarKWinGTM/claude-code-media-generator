---
hide:
  - navigation
  - toc
---

<div class="hero-section" markdown>

# :sparkles: Claude Code Media Generator

<p style="font-size: 1.4rem; opacity: 0.9; margin-bottom: 0.5rem; font-weight: 500;">
AI-Powered Video & Image Generation
</p>

<p style="font-size: 1rem; opacity: 0.7; margin-bottom: 2.5rem;">
Create stunning videos and images using Google Veo & Imagen APIs
</p>

<div style="display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap;" markdown>

[Get Started :rocket:](getting-started/installation.md){ .md-button .md-button--primary }
[View on GitHub :material-github:](https://github.com/DarKWinGTM/claude-code-media-generator){ .md-button }
[Documentation :material-book-open-variant:](video/overview.md){ .md-button }

</div>

</div>

---

## :zap: Why Claude Code Media Generator?

<div class="grid cards" markdown>

-   :movie_camera:{ .lg .middle } **8 Video Generation Modes**

    ---

    From text-to-video to advanced video extension, style transfer, and object manipulation. All powered by Google Veo 3.1.

    [:octicons-arrow-right-24: Explore all modes](video/modes.md)

-   :art:{ .lg .middle } **Reference Image System**

    ---

    Use up to 3 reference images for subject preservation or apply style transfer from any image.

    [:octicons-arrow-right-24: Learn more](video/reference-images.md)

-   :cloud:{ .lg .middle } **Cloud-Native Architecture**

    ---

    Direct GCS storage integration. Auto-upload for seamless video extension workflows.

    [:octicons-arrow-right-24: GCS Setup Guide](guides/gcs-storage.md)

-   :zap:{ .lg .middle } **Smart Presets**

    ---

    Quick, Quality, Extend, Budget — choose the right balance of speed, quality, and cost.

    [:octicons-arrow-right-24: View presets](guides/presets.md)

</div>

---

## :clapper: Video Generation Modes

<div class="grid cards" markdown>

-   :abc: **Text-to-Video**

    Generate video from text prompt only

-   :frame_with_picture: **Image-to-Video**

    Animate a single image with AI

-   :film_frames: **First & Last Frames**

    Interpolate between two keyframes

-   :fast_forward: **Video Extension**

    Add 7 more seconds to any video

-   :bust_in_silhouette: **Reference Asset**

    Preserve subject across generations

-   :paintbrush: **Reference Style**

    Apply artistic style from an image

-   :sparkles: **Insert Objects**

    Add objects using mask (Veo 3.1)

-   :wastebasket: **Remove Objects**

    Remove objects using mask (Veo 3.1)

</div>

[:octicons-arrow-right-24: View all 8 modes in detail](video/modes.md){ .md-button }

---

## :rocket: Quick Start

=== ":package: Install"

    ```bash
    # Clone the repository
    git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
    cd claude-code-media-generator

    # Install dependencies
    pip install requests google-auth
    ```

=== ":key: Setup API"

    ```bash
    # Option 1: Environment variable
    export GOOGLE_API_KEY="your-api-key"

    # Option 2: Command line
    python video_gen.py "prompt" --api-key "your-key"
    ```

=== ":movie_camera: Generate Video"

    ```bash
    # Simple text-to-video
    python video_gen.py "A cat playing in a sunlit garden"

    # With quality preset
    python video_gen.py "Cinematic sunset over mountains" --preset quality

    # With GCS storage
    python video_gen.py "Your prompt" \
        --storage-uri "gs://PROJECT_ID-media-output/videos/"
    ```

=== ":framed_picture: Generate Image"

    ```bash
    # Simple text-to-image
    python image_gen.py "A futuristic city at sunset"

    # With aspect ratio
    python image_gen.py "Portrait of a robot" --aspect-ratio 9:16
    ```

---

## :warning: Important: GCS Bucket Naming

!!! danger "Mandatory Naming Convention"

    When using Vertex AI API Key, your GCS bucket **MUST** follow this exact pattern:

    ```
    gs://YOUR_PROJECT_ID-media-output/
    ```

    Using any other bucket name will result in **permission denied** errors. This is a security boundary enforced by Vertex AI.

**Example:**

```bash
# ✅ Correct pattern
--storage-uri "gs://gen-lang-client-0344941103-media-output/videos/"

# ❌ Wrong - will fail
--storage-uri "gs://my-custom-bucket/videos/"
```

[:octicons-arrow-right-24: Full GCS Setup Guide](guides/gcs-storage.md){ .md-button }

---

## :moneybag: Pricing Overview

| Model | Type | Price/Second | 5-sec Video |
|:------|:-----|:------------|:------------|
| **veo-3.1-generate-preview** | Standard | $0.35 | $1.75 |
| **veo-3.1-fast-generate-preview** | Fast | $0.15 | $0.75 |
| **veo-3.0-generate-preview** | Previous Gen | $0.25 | $1.25 |
| **veo-2.0-generate-preview** | Legacy | $0.35 | $1.75 |

!!! tip "Cost Saving Tip"
    Use `--preset budget` to automatically select the most cost-effective model for your use case.

---

## :chart_with_upwards_trend: Project Status

| Component | Version | Status |
|:----------|:--------|:-------|
| **video_gen.py** | v2.26 | :white_check_mark:{ .status-ready } Production Ready |
| **image_gen.py** | v1.3 | :white_check_mark:{ .status-ready } Stable |
| **check_api.py** | v2.1 | :white_check_mark:{ .status-ready } Stable |
| **Documentation** | v3.8 | :white_check_mark:{ .status-ready } Updated |
| **Claude Code Skill** | — | :construction:{ .status-planned } Phase 3 |
| **MCP Server** | — | :construction:{ .status-planned } Phase 3 |

---

## :books: Explore Documentation

<div class="grid cards" markdown>

-   :material-download:{ .lg .middle } **Getting Started**

    ---

    Installation, authentication setup, and creating your first video in minutes.

    [:octicons-arrow-right-24: Start here](getting-started/installation.md)

-   :movie_camera:{ .lg .middle } **Video Generation**

    ---

    All 8 modes, model comparison, reference images, and advanced features.

    [:octicons-arrow-right-24: Video docs](video/overview.md)

-   :framed_picture:{ .lg .middle } **Image Generation**

    ---

    Text-to-image with Gemini & Imagen models, aspect ratios, and templates.

    [:octicons-arrow-right-24: Image docs](image/index.md)

-   :material-console:{ .lg .middle } **CLI Reference**

    ---

    Complete command-line documentation for all tools.

    [:octicons-arrow-right-24: CLI docs](cli/video_gen.md)

</div>

---

<div style="text-align: center; padding: 3rem 0;">

<p style="opacity: 0.6; margin-bottom: 1.5rem;">Made with :purple_heart: using Google Vertex AI</p>

<div style="display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap;">

[:material-github: Star on GitHub](https://github.com/DarKWinGTM/claude-code-media-generator){ .md-button }
[:material-bug: Report Issue](https://github.com/DarKWinGTM/claude-code-media-generator/issues){ .md-button }

</div>

</div>
