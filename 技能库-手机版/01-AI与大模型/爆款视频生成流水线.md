---
name: viral-video-pipeline
description: "端到端流水线，从文字提示自动生成短视频竖屏爆款内容，适合社交媒体传播。当用户说「爆款视频」「短视频生成」「刷屏视频」「自动生成视频」时触发。"
---

# Viral Video Generator

End-to-end pipeline for producing short-form vertical videos from a text prompt.

## Pipeline Overview

```
Prompt -> Script -> [Parallel Generation] -> Assembly -> Final Video
                    ├── Video Clips (Veo 3.1)
                    ├── Voiceover (Piper TTS)
                    └── Music + SFX (ffmpeg synthesis)
```

## Quick Start

# Generate a complete video
  --prompt "A capybara uses AI to build a dating website" \
  --style meme \
```

Or orchestrate manually with individual scripts (see Workflow below).

## Requirements

### Auto-installed on first run:
- **Piper voice model**: Downloaded to `/tmp/piper-voices/en_US-amy-medium.onnx`
- **ffmpeg**: Static build at `/tmp/ffmpeg-7.0.2-amd64-static/ffmpeg`

### Required:
- **AI_GATEWAY_API_KEY** environment variable (for video generation via Veo 3.1)
- **generate-video skill** installed at `~/.claude/skills/generate-video/`

## Workflow

### Step 1: Write Script

Use the script template in `references/script-templates.md`. Every video follows the 6-8 scene structure:

| Scene | Purpose | Duration |
|-------|---------|----------|
| Hook | Grab attention, relatable problem | 5-9s |
| Discovery | Introduce the solution | 6-10s |
| Demo | Show it working | 8-13s |
| Features | Key benefits (1-3 scenes) | 5-10s each |
| Payoff | Emotional resolution | 6-8s |
| CTA | Call to action | 5-9s |

Write each scene with:
- `visual_prompt`: Text prompt for AI video generation
- `voiceover`: Narration text
- `sfx`: Sound effect cue (whoosh, ding, boom, etc.)

### Step 2: Generate Assets (Parallel)

Launch all three in parallel for speed:

#### 2a. Video Clips
# For each scene:
  "VISUAL_PROMPT" \
  --model "google/veo-3.1-fast-generate-preview" \
  --duration 5 --aspect-ratio "9:16" \
  --output scene-N.mp4 --timeout 300
```

Fallback models: `google/veo-3.1-generate-preview`, `seedance/seedance-1-5-pro-i2v`

For real website/product shots, use Ken Burns effect on screenshots:
$FFMPEG -y -loop 1 -i screenshot.jpg -t $DURATION \
  -vf "scale=2160:3840,zoompan=z='min(zoom+0.001,1.3)':x='iw/2-(iw/zoom/2)':y='ih/3-(ih/zoom/3)':d=$FRAMES:s=1080x1920:fps=30,format=yuv420p" \
  -c:v libx264 -preset fast -crf 23 output.mp4
```

YouTube thumbnails: `curl -sL "https://img.youtube.com/vi/VIDEO_ID/maxresdefault.jpg"`

#### 2b. Voiceover (Piper TTS)
# Install if needed

# Generate per-scene voiceover
echo "VOICEOVER TEXT" | /home/node/.local/bin/piper \
  --model /tmp/piper-voices/en_US-amy-medium.onnx \
  --output_file scene-N-vo.wav \
  --length_scale 1.0 --sentence_silence 0.3

# Concatenate all scenes with silence gaps using Python
  scene1-vo.wav scene2-vo.wav ... --silence 0.4 --output voiceover-full.wav
```

#### 2c. Background Music + SFX
  --style upbeat --duration 60 --bpm 130 \
  --output-dir ./assets/audio/
```

Styles: `upbeat` (130 BPM), `romantic` (100 BPM), `epic` (140 BPM), `chill` (90 BPM)

Generates: `background-music.wav`, `whoosh.wav`, `boom.wav`, `ding.wav`, `record-scratch.wav`

### Step 3: Normalize Video Clips

Extend/loop each clip to match its voiceover duration:
  --clips-dir ./assets/video-clips/ \
  --audio-dir ./assets/audio/ \
  --scene-map scene-map.json \
  --output-dir ./normalized/
```

### Step 4: Assemble Final Video

  --video-dir ./normalized/ \
  --voiceover ./assets/audio/voiceover-full.wav \
  --music ./assets/audio/background-music.wav \
  --sfx-dir ./assets/audio/ \
  --scene-map scene-map.json \
  --output ./output/final.mp4
```

Assembly pipeline:
1. Iterative xfade merge (fade/slideright/slideup transitions, 0.3s)
2. Mix voiceover (vol 1.3) + music (vol 0.12) + SFX with amix + alimiter
3. Combine video + audio with AAC 192kbps
4. Apply movflags +faststart for web playback

### Output Specs

| Property | Value |
|----------|-------|
| Resolution | 1080x1920 (9:16 vertical) |
| Video codec | H.264 (libx264) |
| Frame rate | 30 fps |
| Audio codec | AAC 192kbps, 44.1kHz |
| Format | MP4 with faststart |

## Scene Map Format

```json
{
  "scenes": [
    {
      "id": 1,
      "clip": "scene1-hook.mp4",
      "voiceover": "scene1-vo.wav",
      "vo_text": "This is Gerald. Gerald is a capybara.",
      "visual_prompt": "A cute lonely capybara by a sunset pond",
      "transition": "fade",
      "sfx": [{"type": "whoosh", "offset": 0}]
    }
  ]
}
```

## Tips for Viral Content

- **Hook in 2 seconds**: First scene must grab attention immediately
- **Short sentences**: Punchy narration. Comedic pauses. Dry humor.
- **Relatable problems**: Start with pain points the audience knows
- **Show, don't tell**: Visual storytelling over text walls
- **Emotional payoff**: End with a feel-good moment before CTA
- **Under 60s for Reels**: Instagram favors 30-60s; TikTok up to 90s