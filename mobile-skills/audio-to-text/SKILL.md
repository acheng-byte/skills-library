---
name: audio-to-text
description: "使用 OpenAI 语音识别将音频文件转录为文字，支持多种格式输出和说话人分离。当用户说「音频转文字」「语音识别」「转录」「听写」时触发。"
---

# Audio Transcribe

## Workflow
1. Collect inputs: audio file path(s), desired response format (text/json/diarized_json), optional language hint, and any known speaker references.
2. Verify `OPENAI_API_KEY` is set. If missing, ask the user to set it locally (do not ask them to paste the key).
4. Validate the output: transcription quality, speaker labels, and segment boundaries; iterate with a single targeted change if needed.
5. Save outputs under `output/transcribe/` when working in this repo.

## Decision rules
- Default to `gpt-4o-mini-transcribe` with `--response-format text` for fast transcription.
- If the user wants speaker labels or diarization, use `--model gpt-4o-transcribe-diarize --response-format diarized_json`.
- If audio is longer than ~30 seconds, keep `--chunking-strategy auto`.
- Prompting is not supported for `gpt-4o-transcribe-diarize`.

## Output conventions
- Use `output/transcribe/<job-id>/` for evaluation runs.
- Use `--out-dir` for multiple files to avoid overwriting.

## Dependencies (install if missing)
Prefer `uv` for dependency management.

```
```
If `uv` is unavailable:
```
```

## Environment
- `OPENAI_API_KEY` must be set for live API calls.
- If the key is missing, instruct the user to create one in the OpenAI platform UI and export it in their shell.
- Never ask the user to paste the full key in chat.

## Skill path (set once)

export CODEX_HOME="${CODEX_HOME:-$HOME/.codex}"
```

User-scoped skills install under `$CODEX_HOME/skills` (default: `~/.codex/skills`).

## Reference map
- `references/api.md`: supported formats, limits, response formats, and known-speaker notes.