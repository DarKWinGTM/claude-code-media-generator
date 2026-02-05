# Skill Examples

Real-world examples of using the `/generative` skill for various use cases.

---

## Quick Examples

### Generate a Simple Image

```bash
/generative image "A serene lake at sunrise with mountains in the background"
```

**Result:** A beautiful landscape image saved to `generated_images/`

### Generate a Simple Video

```bash
/generative video "Ocean waves gently rolling onto a sandy beach"
```

**Result:** A 5-second video saved to `generated_videos/`

---

## Image Examples

### Portrait Photography

```bash
/generative image "Professional headshot of a young woman, studio lighting, neutral background" --aspect-ratio 3:4
```

### Landscape Photography

```bash
/generative image "Dramatic mountain landscape at golden hour, snow-capped peaks, wide angle" --aspect-ratio 16:9 --image-size 2K
```

### Product Photography

```bash
/generative image "Sleek smartphone on a marble surface, soft shadows, minimalist style" --aspect-ratio 1:1
```

### Abstract Art

```bash
/generative image "Abstract fluid art with vibrant blue and gold colors, swirling patterns" --count 4
```

### Social Media Content

```bash
/generative image "Motivational quote background, gradient sunset colors, minimalist" --aspect-ratio 9:16
```

---

## Video Examples

### Cinematic Scenes

```bash
/generative video "Cinematic shot of a city skyline at night, lights twinkling, slow camera pan" --preset quality --duration 8
```

### Nature & Wildlife

```bash
/generative video "A butterfly landing on a flower in a meadow, macro shot, soft bokeh background" --preset quality
```

### Product Showcase

```bash
/generative video "360-degree rotation of a luxury watch on a velvet surface, dramatic lighting" --aspect-ratio 1:1
```

### Social Media (Vertical)

```bash
/generative video "Trendy fashion outfit reveal, vertical format for Instagram Reels" --aspect-ratio 9:16 --duration 5
```

### Fast Preview

```bash
/generative video "Quick test of a sunset timelapse" --preset quick
```

### Budget-Friendly

```bash
/generative video "Simple animation for a presentation" --preset budget
```

---

## Advanced Examples

### Image-to-Video Animation

```bash
# Animate an existing image
/generative video "Animate this landscape photo with moving clouds and flowing water" --image ./my-photo.jpg
```

### Video Extension

```bash
# Extend an existing video by 7 seconds
/generative video "Continue the action smoothly" --extend ./my-video.mp4
```

### Style Transfer

```bash
# Apply artistic style from a reference image
/generative video "A city street scene in this artistic style" --style-image ./van-gogh.jpg
```

### Subject Preservation

```bash
# Use a character across multiple generations
/generative video "This character walking through a park" --reference-image ./my-character.png
```

---

## Workflow Examples

### Create Marketing Content

```bash
# Step 1: Generate hero image
/generative image "Modern SaaS dashboard interface, clean design, blue accent colors" --aspect-ratio 16:9 --image-size 2K

# Step 2: Create promotional video
/generative video "Animated showcase of the dashboard with smooth transitions" --preset quality --duration 8

# Step 3: Generate social media versions
/generative image "Same dashboard concept, vertical format" --aspect-ratio 9:16
```

### Create a Video Series

```bash
# Part 1: Opening scene
/generative video "Sunrise over a peaceful village, establishing shot" --preset quality

# Part 2: Extend the scene
/generative video "Camera slowly moves through the village streets" --extend ./part1.mp4

# Part 3: Continue story
/generative video "Arriving at a cozy café" --extend ./part2.mp4
```

### Batch Generation

```bash
# Generate multiple variations
/generative image "Abstract background for presentation" --count 4

# Pick the best one and create video version
/generative video "Animate this abstract background with subtle motion" --image ./best-image.png
```

---

## Tips for Better Results

### 1. Detailed Prompts Work Better

```bash
# Instead of:
/generative video "a dog"

# Try:
/generative video "A golden retriever puppy playing fetch in a sunny park, slow motion capture, warm afternoon light, shallow depth of field"
```

### 2. Specify Camera Movement

```bash
/generative video "Aerial drone shot flying over a tropical beach, camera slowly descending toward the water"
```

### 3. Include Lighting Details

```bash
/generative video "Portrait of a person with dramatic Rembrandt lighting, dark background, moody atmosphere"
```

### 4. Reference Artistic Styles

```bash
/generative video "Cyberpunk city at night in the style of Blade Runner, neon lights, rain-soaked streets"
```

### 5. Use Quality Keywords

```bash
/generative video "Ultra HD, 4K quality, professional cinematography, cinematic color grading"
```

---

## Cost-Conscious Examples

### Quick Tests (Lowest Cost)

```bash
# Use quick preset for testing ideas
/generative video "Test prompt" --preset quick

# Estimate cost before full quality
/generative video "My final prompt" --estimate-cost
```

### Budget Production

```bash
# Use budget preset for production
/generative video "Promotional video" --preset budget --duration 5
```

### Quality When It Matters

```bash
# Reserve quality preset for final output
/generative video "Hero video for website" --preset quality --duration 8
```

---

## Next Steps

- [Commands Reference](commands.md) - Full command documentation
- [Configuration](configuration.md) - Setup and API configuration
- [Video Modes](../video/modes.md) - All 8 video generation modes
