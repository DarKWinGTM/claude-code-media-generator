---
hide:
  - navigation
  - toc
---

<div class="hero-section" markdown>

# :material-movie-star: Claude Code Media Generator

<p style="font-size: 1.4rem; opacity: 0.9; margin-bottom: 0.5rem; font-weight: 500;">
AI-Powered Video & Image Generation
</p>

<p style="font-size: 1rem; opacity: 0.7; margin-bottom: 2.5rem;">
Create stunning videos and images using Google Veo & Imagen APIs
</p>

<div style="display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap;" markdown>

[Get Started :material-rocket-launch:](getting-started/installation.md){ .md-button .md-button--primary }
[View on GitHub :material-github:](https://github.com/DarKWinGTM/claude-code-media-generator){ .md-button }
[Documentation :material-book-open-variant:](video/overview.md){ .md-button }

</div>

</div>

---

## :material-lightning-bolt: Why Claude Code Media Generator?

<div class="grid cards" markdown>

-   :material-video-vintage:{ .lg .middle } **8 Video Generation Modes**

    ---

    From text-to-video to advanced video extension, style transfer, and object manipulation. All powered by Google Veo 3.1.

    [:material-arrow-right: Explore all modes](video/modes.md)

-   :material-palette:{ .lg .middle } **Reference Image System**

    ---

    Use up to 3 reference images for subject preservation or apply style transfer from any image.

    [:material-arrow-right: Learn more](video/reference-images.md)

-   :material-cloud-upload:{ .lg .middle } **Cloud-Native Architecture**

    ---

    Direct GCS storage integration. Auto-upload for seamless video extension workflows.

    [:material-arrow-right: GCS Setup Guide](guides/gcs-storage.md)

-   :material-flash:{ .lg .middle } **Smart Presets**

    ---

    Quick, Quality, Extend, Budget — choose the right balance of speed, quality, and cost.

    [:material-arrow-right: View presets](guides/presets.md)

</div>

---

## :material-movie-open: Video Generation Modes

<div class="grid cards" markdown>

-   :material-text-box: **Text-to-Video**

    Generate video from text prompt only

-   :material-image-move: **Image-to-Video**

    Animate a single image with AI

-   :material-filmstrip-box-multiple: **First & Last Frames**

    Interpolate between two keyframes

-   :material-movie-plus: **Video Extension**

    Add 7 more seconds to any video

-   :material-account-box: **Reference Asset**

    Preserve subject across generations

-   :material-brush: **Reference Style**

    Apply artistic style from an image

-   :material-shape-plus: **Insert Objects**

    Add objects using mask (Veo 3.1)

-   :material-eraser: **Remove Objects**

    Remove objects using mask (Veo 3.1)

</div>

[:material-arrow-right: View all 8 modes in detail](video/modes.md){ .md-button }

---

## :material-rocket-launch-outline: Quick Start

=== ":material-package-variant: Install"

    ```bash
    # Clone the repository
    git clone https://github.com/DarKWinGTM/claude-code-media-generator.git
    cd claude-code-media-generator

    # Install dependencies
    pip install requests google-auth
    ```

=== ":material-key-variant: Setup API"

    ```bash
    # Option 1: Environment variable
    export GOOGLE_API_KEY="your-api-key"

    # Option 2: Command line
    python video_gen.py "prompt" --api-key "your-key"
    ```

=== ":material-video-box: Generate Video"

    ```bash
    # Simple text-to-video
    python video_gen.py "A cat playing in a sunlit garden"

    # With quality preset
    python video_gen.py "Cinematic sunset over mountains" --preset quality

    # With GCS storage
    python video_gen.py "Your prompt" \
        --storage-uri "gs://PROJECT_ID-media-output/videos/"
    ```

=== ":material-image-plus: Generate Image"

    ```bash
    # Simple text-to-image
    python image_gen.py "A futuristic city at sunset"

    # With aspect ratio
    python image_gen.py "Portrait of a robot" --aspect-ratio 9:16
    ```

---

## :material-alert-circle: Important: GCS Bucket Naming

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

[:material-arrow-right: Full GCS Setup Guide](guides/gcs-storage.md){ .md-button }

---

## :material-cash-multiple: Pricing Overview

| Model | Type | Price/Second | 5-sec Video |
|:------|:-----|:------------|:------------|
| **veo-3.1-generate-preview** | Standard | $0.35 | $1.75 |
| **veo-3.1-fast-generate-preview** | Fast | $0.15 | $0.75 |
| **veo-3.0-generate-preview** | Previous Gen | $0.25 | $1.25 |
| **veo-2.0-generate-preview** | Legacy | $0.35 | $1.75 |

!!! tip "Cost Saving Tip"
    Use `--preset budget` to automatically select the most cost-effective model for your use case.

---

## :material-chart-line: Project Status

| Component | Version | Status |
|:----------|:--------|:-------|
| **video_gen.py** | v2.26 | :material-check-circle:{ .status-ready } Production Ready |
| **image_gen.py** | v1.3 | :material-check-circle:{ .status-ready } Stable |
| **check_api.py** | v2.1 | :material-check-circle:{ .status-ready } Stable |
| **Documentation** | v3.8 | :material-check-circle:{ .status-ready } Updated |
| **Claude Code Skill** | — | :material-progress-wrench:{ .status-planned } Phase 3 |
| **MCP Server** | — | :material-progress-wrench:{ .status-planned } Phase 3 |

---

## :material-bookshelf: Explore Documentation

<div class="grid cards" markdown>

-   :material-download:{ .lg .middle } **Getting Started**

    ---

    Installation, authentication setup, and creating your first video in minutes.

    [:material-arrow-right: Start here](getting-started/installation.md)

-   :material-video-vintage:{ .lg .middle } **Video Generation**

    ---

    All 8 modes, model comparison, reference images, and advanced features.

    [:material-arrow-right: Video docs](video/overview.md)

-   :material-image:{ .lg .middle } **Image Generation**

    ---

    Text-to-image with Gemini & Imagen models, aspect ratios, and templates.

    [:material-arrow-right: Image docs](image/index.md)

-   :material-console:{ .lg .middle } **CLI Reference**

    ---

    Complete command-line documentation for all tools.

    [:material-arrow-right: CLI docs](cli/video_gen.md)

</div>

---

<div style="text-align: center; padding: 3rem 0;" markdown>

Made with :material-heart:{ .heart-icon } using Google Vertex AI

<div style="display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap;" markdown>

[Star on GitHub :material-github:](https://github.com/DarKWinGTM/claude-code-media-generator){ .md-button }
[Report Issue :material-bug:](https://github.com/DarKWinGTM/claude-code-media-generator/issues){ .md-button }

</div>

</div>
