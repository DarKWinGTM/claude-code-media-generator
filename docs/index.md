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

!!! warning "🚧 Under Development"
    **Core features are production-ready.** Currently focusing on documentation improvements and planned features design.

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

=== ":material-star: Skill (Recommended)"

    The easiest way - use the `/generative` skill in Claude Code:

    ```bash
    # Generate an image
    /generative image "A beautiful sunset over mountains"

    # Generate a video
    /generative video "Ocean waves crashing on the beach"

    # With options
    /generative video "Cinematic sunset" --preset quality
    ```

    [:material-arrow-right: Learn more about Skills](skills/overview.md){ .md-button .md-button--primary }

=== ":material-console: Script"

    For automation and advanced users:

    ```bash
    # Option 1: Environment variable
    export GOOGLE_API_KEY="your-api-key"

    # Generate video
    python video_gen.py "A cat playing in a sunlit garden"

    # With quality preset
    python video_gen.py "Cinematic sunset over mountains" --preset quality
    ```

    [:material-arrow-right: Advanced Usage](advanced/index.md){ .md-button }

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
| **video_gen.py** | v2.27 | :material-check-circle:{ .status-ready } Production Ready |
| **image_gen.py** | v1.4 | :material-check-circle:{ .status-ready } Stable |
| **check_api.py** | v2.1 | :material-check-circle:{ .status-ready } Stable |
| **Documentation** | v3.10 | :material-check-circle:{ .status-ready } Updated |
| **Claude Code Skill** | v3.2.1 | :material-check-circle:{ .status-ready } Available |
| **Agent** | v1.0 | :material-check-circle:{ .status-ready } Available |
| **MCP Server** | — | :material-progress-wrench:{ .status-planned } Planned |

---

## :material-map-marker-path: Development Roadmap

<div class="grid cards" markdown>

-   :material-check-circle:{ .lg .middle style="color: #22c55e" } **Phase 1: Core Features** ✅

    ---

    - 8 video generation modes
    - Image generation (Imagen/Gemini)
    - Smart validation & auto-correction
    - GCS storage integration

-   :material-check-circle:{ .lg .middle style="color: #22c55e" } **Phase 2: Enhanced Workflows** ✅

    ---

    - Video extension from URLs
    - Auto FPS conversion (24fps)
    - Reference image system
    - Smart defaults from metadata

-   :material-check-circle:{ .lg .middle style="color: #22c55e" } **Phase 3: Claude Code** ✅

    ---

    - `/generative` unified skill
    - Help system (5 topics)
    - Config wizard
    - Agent for auto-detection

-   :material-clipboard-list:{ .lg .middle style="color: #eab308" } **Phase 4: Planned Features**

    ---

    - **Remix Mode** (`--remix video.mp4`)
    - Outpainting (`--outpaint left,right`)
    - Camera Control (`--camera pan-left`)
    - Audio Control (style/mood)

</div>

---

## :material-bookshelf: Explore Documentation

<div class="grid cards" markdown>

-   :material-magic-staff:{ .lg .middle } **Using Skills**

    ---

    The easiest way to generate media - `/generative` skill for Claude Code.

    [:material-arrow-right: Start with Skills](skills/overview.md)

-   :material-download:{ .lg .middle } **Getting Started**

    ---

    Installation, authentication setup, and creating your first video in minutes.

    [:material-arrow-right: Get Started](getting-started/installation.md)

-   :material-video-vintage:{ .lg .middle } **Video Generation**

    ---

    All 8 modes, model comparison, reference images, and advanced features.

    [:material-arrow-right: Video docs](video/overview.md)

-   :material-image:{ .lg .middle } **Image Generation**

    ---

    Text-to-image with Gemini & Imagen models, aspect ratios, and templates.

    [:material-arrow-right: Image docs](image/index.md)

-   :material-console:{ .lg .middle } **Advanced Usage**

    ---

    Direct scripts, automation, CI/CD integration for power users.

    [:material-arrow-right: Advanced docs](advanced/index.md)

-   :material-book-open-variant:{ .lg .middle } **Guides**

    ---

    GCS storage, presets, troubleshooting, and more.

    [:material-arrow-right: View guides](guides/gcs-storage.md)

</div>

---

## :material-star-shooting: Star History

<a href="https://star-history.com/#DarKWinGTM/claude-code-media-generator&Date">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=DarKWinGTM/claude-code-media-generator&type=Date&theme=dark" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=DarKWinGTM/claude-code-media-generator&type=Date" />
   <img alt="Star History Chart" src="https://api.star-history.com/svg?repos=DarKWinGTM/claude-code-media-generator&type=Date" />
 </picture>
</a>

---

<div style="text-align: center; padding: 3rem 0;" markdown>

Made with :material-heart:{ .heart-icon } using Google Vertex AI

<div style="display: flex; gap: 1rem; justify-content: center; flex-wrap: wrap;" markdown>

[Star on GitHub :material-github:](https://github.com/DarKWinGTM/claude-code-media-generator){ .md-button }
[Report Issue :material-bug:](https://github.com/DarKWinGTM/claude-code-media-generator/issues){ .md-button }

</div>

</div>
