---
name: ai-media-setup
description: Set up kie.ai unified AI media API (Veo 3, Nano Banana, Flux, ElevenLabs, etc.) in a project — API key in .env, model selection, sanity test call. Use when starting AI media work, onboarding a new project that needs AI-generated video/image/voice. Triggers on "set up kie.ai", "AI media setup", "configure AI generation", "/ai-media:setup".
---

# /ai-media:setup — Wire kie.ai into your project

One-time configuration. Run at the start of AI media work.

## What this does

1. Add `KIE_AI_API_KEY` to `.env` (and `.env.example`)
2. Install `fetch`-based client or `@kie/sdk` if available
3. Pick default models for video / image / voice (documented below)
4. Run a sanity test — generate one small image to confirm the key works
5. Set up a cost-tracking log (optional — defaults to on)

## Prereqs

- kie.ai account → https://kie.ai (student signs up)
- API key → dashboard → Keys → create new, copy the `kie_…` value
- Budget plan — ask the student what monthly cap they want; recommend $20-50/mo to start

## Workflow

```bash
# 1. Add key to .env
echo "KIE_AI_API_KEY=kie_xxxxxxxxxxxx" >> .env

# 2. Safe template for collaborators
echo "KIE_AI_API_KEY=" >> .env.example

# 3. Install client (if Node project)
npm install node-fetch
# or: pnpm add node-fetch

# 4. Sanity test — generate one 512x512 image
node -e "
fetch('https://api.kie.ai/v1/images/generations', {
  method: 'POST',
  headers: { Authorization: 'Bearer ' + process.env.KIE_AI_API_KEY, 'Content-Type': 'application/json' },
  body: JSON.stringify({ model: 'nano-banana', prompt: 'a single green apple on white', size: '512x512' })
}).then(r => r.json()).then(console.log);
"
```

## Default model selection

| Media | Model | Why default |
|---|---|---|
| Image (fast, cheap) | `nano-banana` | Google's efficient image model via kie.ai |
| Image (quality) | `flux-pro-1.1` | Higher fidelity for hero / product shots |
| Video (short, ads) | `veo-3` | Best quality for ≤ 10s clips |
| Voice (narration) | `elevenlabs-turbo-v2` | Natural voice, low latency |
| Voice (cloning) | `elevenlabs-pro` | Clone your own voice from samples |

Override per call if needed.

## Security

- `KIE_AI_API_KEY` is a live credential. Never commit. The `/secure-setup:init` skill (in ORBWEVA's `secure-setup` repo) already adds `.env` to `.gitignore` — verify that ran.
- If you leak the key, rotate it in kie.ai dashboard immediately.

## Notes

Skeleton (v0.1). Future: auto-detect project type (Next.js, Python, n8n) and scaffold idiomatic client code.
