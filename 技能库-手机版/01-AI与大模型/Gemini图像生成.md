# Nano Banana Pro (Gemini 3 Pro Image)

Generate or edit images using Gemini 3 Pro Image via AI Gateway or direct API.

## Quick Start

Generate an image:

  --prompt "your image description" \
  --filename "output.png" \
  --resolution 1K
```

## Usage Examples

### Generate Image

  --prompt "a cute cat sitting on a wooden floor" \
  --filename "cat.png"
```

### Edit Image (requires GEMINI_API_KEY)

**Note**: Image editing currently requires direct Gemini API access.

export GEMINI_API_KEY="your-gemini-key"
  --prompt "make it more colorful" \
  --filename "cat-colorful.png" \
  -i "cat.png"
```

### Multi-image Composition (up to 14 images, requires GEMINI_API_KEY)

  --prompt "combine these into one scene" \
  --filename "combined.png" \
  -i img1.png -i img2.png -i img3.png
```

## API Key Configuration

The skill automatically detects API keys in this priority order:

1. **`AI_GATEWAY_API_KEY`** (recommended) - Uses AI Gateway
2. **`GEMINI_API_KEY`** - Direct Gemini API access
3. **`--api-key` argument** - Manual key override

### Recommended: AI Gateway (default)

export AI_GATEWAY_API_KEY="your-gateway-key"  # Usually pre-configured
```

✅ No additional dependencies required
✅ Cost-efficient and unified API management
❌ Image editing not currently supported

### Alternative: Direct Gemini API

export GEMINI_API_KEY="your-gemini-key"
```

✅ Supports image editing and multi-image composition

## Options

- `--prompt, -p`: Image description (required)
- `--filename, -f`: Output filename (required)
- `--resolution, -r`: Resolution (`1K`, `2K`, `4K`, default: `1K`)
- `--input-image, -i`: Input image(s) for editing (up to 14, requires GEMINI_API_KEY)
- `--api-key, -k`: Manual API key override

## Notes

- Resolutions: `1K` (default), `2K`, `4K`
- Use descriptive filenames with timestamps: `2024-12-31-cat.png`
- The script outputs a `MEDIA:` line for OpenClaw integration
- AI Gateway mode works with standard Python 3, no extra tools needed