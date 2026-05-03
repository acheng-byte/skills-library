---
name: generate-video
description: Generate videos using AI Gateway API. Use when the user asks to create, generate, or produce videos, or work with video generation.
allowed-tools: Bash, Read
---

`````
# Video Generation Skill

Generate videos from text descriptions using the AI Gateway JavaScript SDK with support for multiple AI video models.

## Overview

This Skill enables Claude to generate videos from text prompts using the official Trickle AI Gateway JavaScript SDK. The SDK provides a clean API for video generation with automatic status polling and error handling.

## Prerequisites

**Required Environment Variable:**
- `AI_GATEWAY_API_KEY`: Your AI Gateway API key

If this environment variable is not set, the script will fail with an error message asking you to provide it.

## Quick Start

### Generate a Video

Use the JavaScript SDK script to generate videos from text descriptions:

```bash
node scripts/generate_video_sdk.js "A peaceful forest scene with morning mist, camera slowly moving forward"
```

## Supported Models

### Video Generation Models

| Model | Provider | Duration Support | Aspect Ratio Support | Best For |
|-------|----------|------------------|---------------------|----------|
| `byteplus/dreamina-seedance-2-0` | BytePlus | 6, 8, 10 seconds | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Latest Seedance 2.0 generation |
| `byteplus/seedance-1-0-pro` | BytePlus | 6, 8, 10 seconds | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Pro-quality with flexible ratios (recommended for Seedance) |
| `byteplus/seedance-1-0-lite` | BytePlus | 6, 8, 10 seconds | 16:9, 4:3, 1:1, 3:4, 9:16, 21:9 | Faster lite version |
| `google/veo-3.1-generate-preview` | Google | 6, 8, 10 seconds | 16:9, 9:16 | Latest Google video AI  |
| `google/veo-3.1-fast-generate-preview` | Google | 6, 8, 10 seconds | 16:9, 9:16 | Fast video generation |


## API Parameters

### Video Generation

- `prompt` (required): Text description of the desired video
- `model` (required): Video model to use (see Supported Models above)
- `duration` (optional): Video duration in seconds
  - Google Veo models: 6, 8, 10 seconds
  - BytePlus Seedance models: 6, 8, 10 seconds
  - Default: 5 seconds
- `aspectRatio` (optional): Video aspect ratio
  - Google Veo models support: 16:9, 9:16
  - BytePlus Seedance models support: 16:9, 4:3, 1:1, 3:4, 9:16, 21:9
  - Default: "16:9"
- `size` (optional): Video resolution (e.g., "1280x720", "1920x1080")
- `user` (optional): User identifier for tracking

## Usage

### generate_video_sdk.js

Generate videos using the official Trickle AI Gateway JavaScript SDK. This script uses the built-in `generateVideo()` method with automatic status polling.

**Usage:**
```bash
node scripts/generate_video_sdk.js "prompt" [--model MODEL] [--duration SECONDS] [--aspect-ratio RATIO] [--output OUTPUT] [--timeout SECONDS]
```

**Options:**
- `--model MODEL`: Video model to use (default: google/veo-3.1-generate-preview)
- `--duration SECONDS`: Video duration in seconds (default: 6)
- `--aspect-ratio RATIO`: Video aspect ratio (default: 16:9)
- `--output PATH`: Output file path (default: generated_video.mp4)
- `--timeout SECONDS`: Maximum wait time in seconds (default: 300)

**Features:**
- Uses official SDK with built-in polling and error handling
- Automatic status updates during generation
- Clean and simple API
- Full TypeScript type support
- Progress bar for video download

**Examples:**
```bash
# Basic usage with Google Veo (recommended)
node scripts/generate_video_sdk.js "A serene lake at sunset with gentle ripples"

# Using BytePlus Seedance with vertical video
node scripts/generate_video_sdk.js \
  "A dancer performing contemporary dance in a studio" \
  --duration 8 \
  --model "byteplus/seedance-1-0-pro" \
  --aspect-ratio "9:16" \
  --output "dance.mp4"

# High-quality with longer duration
node scripts/generate_video_sdk.js \
  "A peaceful forest scene with morning mist" \
  --duration 10 \
  --model "google/veo-3.1-generate-preview" \
  --aspect-ratio "16:9" \
  --output "forest.mp4"
```

## How Video Generation Works

Video generation is an **asynchronous process**:

1. Submit a video generation request → Receive a video `id`
2. SDK automatically polls the video status using the `id`
3. When status is `succeeded`, the SDK returns the video URL
4. Script downloads the video to the specified output file

The SDK handles this process automatically with the `generateVideo()` method, which includes:
- Initial delay before first status check (default: 35 seconds)
- Automatic polling every 5 seconds
- Status update callbacks for progress tracking
- Configurable timeout (default: 5 minutes)

## Implementation Notes

**When implementing video generation tasks:**

1. **Use the bundled SDK script** (`generate_video_sdk.js`) for video generation tasks

2. **Check for API Key** at the start - the script will automatically check and provide helpful error messages

3. **Use the SDK for custom implementations**:
   ```javascript
   const { createTrickleAIGatewayClient } = require('../lib/trickle-ai-gateway-client.js');

   const client = createTrickleAIGatewayClient({
     apiKey: process.env.AI_GATEWAY_API_KEY,
     baseUrl: 'https://ai-gateway.trickle-lab.tech'
   });

   const videoStatus = await client.generateVideo({
     model: 'google/veo-3.1-generate-preview',
     prompt: 'Your video description',
     duration: 6,
     aspectRatio: '16:9'
   });
   ```

4. **Handle asynchronous processing**: The SDK's `generateVideo()` method handles polling automatically

5. **Save generated videos** to appropriate file paths and inform the user

6. **Set reasonable timeouts**: Video generation can take several minutes depending on duration and model

## SDK Integration

The skill includes the Trickle AI Gateway JavaScript SDK bundled in the `lib/` directory:
- `lib/trickle-ai-gateway-client.js` - CommonJS build for Node.js
- `lib/trickle-ai-gateway-client.d.ts` - TypeScript type definitions

### Using the SDK in Custom Code

```javascript
const { createTrickleAIGatewayClient } = require('./lib/trickle-ai-gateway-client.js');

// Create client
const client = createTrickleAIGatewayClient({
  apiKey: process.env.AI_GATEWAY_API_KEY,
  baseUrl: 'https://ai-gateway.trickle-lab.tech'
});

// Generate video with automatic polling
const videoStatus = await client.generateVideo(
  {
    model: 'google/veo-3.1-generate-preview',
    prompt: 'A beautiful sunset over mountains',
    duration: 8,
    aspectRatio: '16:9'
  },
  {
    initialDelay: 35000,
    pollInterval: 5000,
    timeout: 300000,
    onStatusUpdate: (status) => {
      console.log('Status:', status.status);
    }
  }
);

console.log('Video URL:', videoStatus.url);
```

### Error Handling

The SDK and script include comprehensive error handling for:
- Missing API key
- Network failures
- Invalid responses
- Generation failures
- Timeout conditions
- File I/O errors

Errors include helpful messages to guide troubleshooting.

## Model Selection Guide

### When to Use Each Model

- **Google Veo 3.1 (Recommended)**: Best for general-purpose video generation with excellent quality and reliability. Use `google/veo-3.1-generate-preview` for standard generation or `google/veo-3.1-fast-generate-preview` for faster results.

- **BytePlus Seedance (Recommended for Seedance)**: Ideal when you need flexible aspect ratios beyond 16:9 and 9:16. Use `byteplus/seedance-1-0-pro` for best quality or `byteplus/seedance-1-0-lite` for faster iteration. `byteplus/dreamina-seedance-2-0` is also available as the latest Seedance 2.0 model.

## Best Practices

1. **Be Specific in Prompts**: Include scene details, camera movements, lighting conditions, and desired visual style. More detail leads to better results.

2. **Choose the Right Model**:
   - Start with Google Veo 3.1 for general use
   - Use Seedance when you need non-standard aspect ratios

3. **Respect Duration Limits**: Match duration to model capabilities:
   - Veo models: 6, 8, or 10 seconds
   - Seedance models: 6, 8, or 10 seconds

4. **Set Appropriate Aspect Ratios**:
   - Use 16:9 for landscape/horizontal videos (YouTube, web)
   - Use 9:16 for vertical videos (mobile, TikTok, Instagram Stories)
   - Use other ratios (1:1, 3:4, 21:9) with Seedance models only

5. **Allow Sufficient Timeout**: Video generation typically takes 1-5 minutes. Set timeout to at least 300 seconds (5 minutes), or 600 seconds for longer videos.

6. **Save Outputs with Descriptive Names**: Use meaningful file names that reflect the content and model used (e.g., `forest_scene_veo.mp4`).

7. **Batch Processing**: Use the batch script for multiple videos, but be aware of longer total processing time. Process similar videos together to optimize workflow.

## Troubleshooting

### "AI_GATEWAY_API_KEY environment variable is required"
Set the environment variable before running scripts:
```bash
export AI_GATEWAY_API_KEY="your-api-key-here"
```

### Timeout Errors
Video generation can take time. Increase the timeout value:
```bash
node scripts/generate_video_sdk.js "prompt" --timeout 600
```

### Network Errors
Check your internet connection and verify the API Gateway is accessible.

### Invalid Model Errors
Ensure you're using a valid model name from the supported models list above.

### Generation Failed Errors
Check the error message for details. Common causes include:
- Invalid or unclear prompts
- Requested duration too long for the model
- API service issues
`````
