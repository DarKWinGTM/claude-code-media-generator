# Reference Images

Guide to using reference images for subject preservation and style transfer.

---

## Overview

Reference images help the AI understand what you want:

- **Reference Asset** - Preserve subject identity (face, product, character)
- **Reference Style** - Apply visual style (art style, color palette)

---

## Unified Format (v2.24)

Use `--reference-image PATH:TYPE` format:

```bash
# Asset type (default if not specified)
--reference-image avatar.png:asset

# Style type
--reference-image painting.jpg:style

# Without type (defaults to asset)
--reference-image avatar.png
```

---

## Reference Asset

Preserve subject identity across the video.

### Single Reference

```bash
python video_gen.py "A person waving hello" \
  --reference-image avatar.png:asset
```

### Multiple References (Max 3)

Use multiple images of the same subject from different angles:

```bash
python video_gen.py "A person dancing" \
  --reference-image front.png:asset \
  --reference-image side.png:asset \
  --reference-image back.png:asset
```

### Best Practices

- :white_check_mark: Use clear, well-lit photos
- :white_check_mark: Show the subject from different angles
- :white_check_mark: Keep consistent lighting across references
- :x: Don't mix different subjects
- :x: Don't use blurry images

---

## Reference Style

Apply visual style from a reference image.

```bash
python video_gen.py "A cityscape at sunset" \
  --reference-image impressionist_painting.jpg:style \
  --model veo-2.0-exp
```

### Requirements

!!! warning "Model Requirement"
    - Only supported on `veo-2.0-exp` model
    - Maximum 1 style image per request
    - Cannot mix with asset references

### Best Practices

- :white_check_mark: Use distinctive art styles
- :white_check_mark: Choose high-quality style references
- :white_check_mark: Match style to content theme
- :x: Don't use photos as style (use asset instead)

---

## API Limitations

### Cannot Mix Types

The API does not support mixing asset and style in the same request:

```bash
# ❌ This will fail
python video_gen.py "A person" \
  --reference-image avatar.png:asset \
  --reference-image painting.jpg:style

# Error: "does not support this mix"
```

### Solution: Use One Type

```bash
# ✅ Assets only
python video_gen.py "A person dancing" \
  --reference-image front.png:asset \
  --reference-image side.png:asset

# ✅ Style only
python video_gen.py "A landscape" \
  --reference-image watercolor.jpg:style
```

---

## Limits Summary

| Type | Maximum | Model Support |
|------|---------|---------------|
| Asset | 3 images | All Veo models |
| Style | 1 image | veo-2.0-exp only |
| Mixed | :x: Not allowed | - |

---

## With Video Extension

You can combine reference images with video extension:

```bash
python video_gen.py "The person continues walking" \
  --extend-video previous.mp4 \
  --reference-image avatar.png:asset \
  --model veo-2.0
```

---

## Legacy Parameters (Deprecated)

The following are deprecated but still work:

```bash
# Deprecated - still works
--reference-asset avatar.png
--reference-style painting.jpg

# Recommended - use instead
--reference-image avatar.png:asset
--reference-image painting.jpg:style
```

---

## Troubleshooting

### "Reference type not supported"
- Check model compatibility
- Style only works with veo-2.0-exp

### "Too many references"
- Maximum 3 asset images
- Maximum 1 style image

### "Mixed types not supported"
- Use only asset OR only style in one request
- Cannot combine both

---

## Learn More

- [Generation Modes](modes.md) - All generation modes
- [Video Models](models.md) - Model compatibility
- [Video Extension](extension.md) - Combining with extension
