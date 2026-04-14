---
name: ai-media-voice
description: Generate TTS (text-to-speech) or cloned-voice audio via kie.ai voice models (ElevenLabs, etc.). Use for voiceovers, audio ads, multi-lingual narration, podcast intros, course lesson audio, onboarding walkthroughs. Triggers on "voiceover", "TTS", "generate voice", "voice cloning", "narration", "/ai-media:voice".
---

# /ai-media:voice — TTS + voice cloning via kie.ai

## Two modes

### Mode 1: Stock voices (TTS)

Pick from kie.ai's catalog of pre-trained voices. Good for: voiceovers where brand consistency matters less than quality.

### Mode 2: Voice cloning

Clone the founder's / talent's voice from 30+ seconds of clean audio. Good for: brand-critical audio (podcast intro, course lesson, personal message at scale).

## Inputs

Ask for:
1. **Script text** — the exact words to speak
2. **Voice** — stock voice name, or "clone my voice" (if cloning, need sample files)
3. **Language** — auto-detect or specify (EN, JA, KO, ES, etc.)
4. **Style** — conversational / authoritative / warm / energetic
5. **Use case** — shapes speed + intonation defaults

## Workflow — TTS (stock voice)

```bash
curl -X POST https://api.kie.ai/v1/audio/speech \
  -H "Authorization: Bearer $KIE_AI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "elevenlabs-turbo-v2",
    "voice": "rachel",
    "input": "Welcome to Kora Lingo. In 12 weeks, you will speak Japanese confidently.",
    "response_format": "mp3"
  }' -o out.mp3
```

## Workflow — voice cloning

1. **Record sample** — 30-60 seconds of clean audio from the target voice. Single speaker, no background music, no room echo. A USB mic in a quiet room is fine.
2. **Upload to kie.ai** — POST to `/v1/voices` with the audio file. Returns a `voice_id`.
3. **Generate** — use the cloned `voice_id` in subsequent TTS calls.
4. **Test** — always generate a 10-second sample first. Cloned voices occasionally have issues on specific phonemes — catch them early.

```bash
# Upload sample
curl -X POST https://api.kie.ai/v1/voices \
  -H "Authorization: Bearer $KIE_AI_API_KEY" \
  -F "name=ryno-brand-voice" \
  -F "sample=@voice-sample.mp3"

# Generate with cloned voice
curl -X POST https://api.kie.ai/v1/audio/speech \
  -H "Authorization: Bearer $KIE_AI_API_KEY" \
  -d '{ "voice_id": "clone_xxx", "input": "..." }'
```

## Common pitfalls

| Problem | Fix |
|---|---|
| Clone sounds robotic | Sample too short or noisy — record a cleaner 60s sample |
| Pronunciation wrong on proper nouns | Use phonetic spelling: "Kora Lingo" → "Ko-rah Ling-oh" or add SSML phoneme tags |
| Emotion flat | Switch model to one that supports `style` or emotion parameters |
| Multi-lingual switching sounds weird | Generate each language segment separately and splice |

## Output format

```
## Voice generation — {use case}

### Config
- Voice: ryno-brand-voice (cloned)
- Model: elevenlabs-pro
- Language: EN
- Style: conversational

### Sample (10s, cost: $0.XX)
[URL] — ✓ approved

### Full generation (45s, cost: $0.XX)
[URL] — ready for use in onboarding video

### Next steps
- Edit with ffmpeg to trim silence, normalize volume
- Layer under video with ffmpeg -i or a video editor
```

## Notes

Skeleton (v0.1).
