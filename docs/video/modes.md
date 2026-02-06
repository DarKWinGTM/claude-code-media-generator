# Video Generation Modes

All 8 video generation modes explained with examples.

---

## Text-to-Video

Generate video from text prompt only.

**Usage:**
```bash
python video_gen.py "A cat walking in a garden"
```

**Parameters:**

- `prompt` - Description of the video (required)
- `--duration` - 5-8 seconds (default: 5)
- `--aspect-ratio` - 16:9, 9:16, 1:1 (default: 16:9)

**Best for:** Simple scenes, abstract concepts, quick prototypes

---

## Image-to-Video

Animate a static image into video.

**Usage:**
```bash
python video_gen.py "The cat starts dancing" --image cat.jpg
```

**Parameters:**

- `prompt` - Description of motion (required)
- `--image` - First frame image (required)

**Supported formats:** JPEG, PNG, WebP

**Best for:** Animating photos, product demos, character animation

---

## First & Last Frames

Create video that interpolates between two images.

**Usage:**
```bash
python video_gen.py "Smooth transition" \
  --image start.jpg \
  --last-frame end.jpg
```

**Parameters:**

- `--image` - First frame (required)
- `--last-frame` - Last frame (required)

**Best for:** Morphing effects, transitions, storytelling

---

## Video Extension

Extend an existing video with new content.

**Usage:**
```bash
python video_gen.py "The cat runs faster" \
  --extend-video previous_video.mp4 \
  --model veo-2.0
```

**Parameters:**

- `--extend-video` - Source video to extend (required)
- `--storage-uri` - GCS bucket for upload (recommended)

!!! warning "Requirements"
    - Source video MUST be 24fps
    - Use GCS URI or local file (external URLs not supported)
    - Use model with video_extension support (veo-2.0)

**Best for:** Creating longer videos, continuing scenes

See [Video Extension](extension.md) for detailed guide.

---

## Reference Asset

Use up to 3 reference images to preserve subject identity.

**Usage:**
```bash
# Single reference
python video_gen.py "A person waving" \
  --reference-image avatar.png:asset

# Multiple references (same subject, different angles)
python video_gen.py "A person waving" \
  --reference-image front.png:asset \
  --reference-image side.png:asset \
  --reference-image back.png:asset
```

**Parameters:**

- `--reference-image PATH:asset` - Reference image with type
- Maximum 3 asset images

**Best for:** Consistent character, product videos

See [Reference Images](reference-images.md) for detailed guide.

---

## Reference Style

Apply visual style from a reference image.

**Usage:**
```bash
python video_gen.py "A cityscape" \
  --reference-image painting.jpg:style \
  --model veo-2.0-exp
```

**Parameters:**

- `--reference-image PATH:style` - Style reference image
- Maximum 1 style image
- Only supported on `veo-2.0-exp` model

**Best for:** Style transfer, artistic effects

---

## Insert Objects

Add new objects to existing video using mask.

**Usage:**
```bash
python video_gen.py "Add a flying bird" \
  --video source.mp4 \
  --mask bird_area.png \
  --mask-mode insert
```

**Parameters:**

- `--video` - Source video (required)
- `--mask` - Mask image defining insert area (required)
- `--mask-mode insert` - Set mode to insert (required)

**Best for:** Adding elements, VFX

---

## Remove Objects

Remove objects from existing video using mask.

**Usage:**
```bash
python video_gen.py "Remove the watermark" \
  --video source.mp4 \
  --mask watermark_area.png \
  --mask-mode remove
```

**Parameters:**

- `--video` - Source video (required)
- `--mask` - Mask image defining remove area (required)
- `--mask-mode remove` - Set mode to remove (required)

**Best for:** Removing unwanted elements, cleanup

---

## Mode Detection

The script automatically detects mode based on provided arguments:

| Arguments | Detected Mode |
|-----------|---------------|
| prompt only | text_to_video |
| prompt + --image | image_to_video |
| prompt + --image + --last-frame | first_and_last_frames |
| prompt + --extend-video | video_extension |
| prompt + --reference-image:asset | reference_asset |
| prompt + --reference-image:style | reference_style |
| prompt + --video + --mask-mode insert | insert_objects |
| prompt + --video + --mask-mode remove | remove_objects |

---

## Model Compatibility

| Mode | veo-3.1 | veo-3.1-fast | veo-2.0 | veo-2.0-exp |
|------|---------|--------------|---------|-------------|
| text_to_video | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| image_to_video | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| first_and_last | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| video_extension | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| reference_asset | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| reference_style | :x: | :x: | :x: | :white_check_mark: |
| insert_objects | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| remove_objects | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |

---

## Learn More

- [Video Overview](overview.md) - Back to overview
- [Video Models](models.md) - Detailed model comparison
- [Reference Images](reference-images.md) - Reference image guide
- [Video Extension](extension.md) - Video extension guide
