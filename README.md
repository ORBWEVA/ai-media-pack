# ORBWEVA AI-Media Pack

Claude Code skills for automating AI-generated media pipelines — video, image, voice — primarily via [**kie.ai**](https://kie.ai), a unified API aggregator for models like Veo 3, Nano Banana, Flux, ElevenLabs, and others.

**Who this is for:** founders who need to generate marketing video, product images, voiceovers, and social-ready assets at scale, without juggling five API dashboards.

## Installation

```bash
npx @orbweva/academy@latest --track accelerator --pack ai-media
```

Or manually:

```bash
git clone --depth 1 https://github.com/ORBWEVA/ai-media-pack.git /tmp/ai-media-pack
cp -r /tmp/ai-media-pack/skills/* ~/.claude/skills/
```

## Skills

| Command | What it does |
|---|---|
| `/ai-media:setup` | Wire kie.ai into your project — API key in `.env`, model selection, sanity test call |
| `/ai-media:video` | Video generation via Veo 3 — prompt → script → clip → variations |
| `/ai-media:image` | Image generation via Nano Banana / Flux — batch runs, brand-consistent prompts |
| `/ai-media:voice` | TTS / voice cloning via kie.ai voice models |
| `/ai-media:pipeline` | End-to-end n8n pipeline: trigger → generate → post-process → publish |
| `/ai-media:cost` | Cost estimate before committing to a batch generation |

## Why kie.ai (not direct provider APIs)

- **One API, many models** — Veo 3, Nano Banana, Flux, Imagen, Sora, ElevenLabs, etc. Swap models without rewriting code.
- **Unified billing** — one invoice, one budget, no rate-limit juggling
- **Beta access** — kie.ai often fronts new models faster than individual providers open their APIs

If you prefer direct provider APIs, the skill workflows work the same way — just change the base URL and auth header.

## License

[CC BY-NC-SA 4.0](LICENSE). © 2026 ORBWEVA.
