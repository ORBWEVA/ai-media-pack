---
name: ai-media-image
description: Generate images via Nano Banana, Flux, or Imagen through kie.ai. Handles batch runs, brand-consistent prompts, variations, and aspect-ratio sets. Use when the user wants product shots, marketing images, social graphics, hero imagery, blog post illustrations, or email header images. Triggers on "generate image", "make image", "product shot", "hero image", "/ai-media:image".
---

# /ai-media:image — Image generation via Nano Banana / Flux

Primary model default: `nano-banana` (fast + cheap for iteration). Switch to `flux-pro-1.1` for final assets that need to hold up at high resolution.

## Inputs

Ask for:
1. **Goal** — hero image, product shot, social graphic, blog illustration, email header
2. **Aspect ratio** — 1:1 (feed), 4:5 (IG), 9:16 (story), 16:9 (hero), 3:2 (blog), custom
3. **Brand palette + style references** — 1-3 reference images or described style
4. **Content** — specific subject, props, setting

## Brand-consistent prompts

Don't just write a prompt — wrap it with brand fingerprint:

```
{subject}, {setting}, {camera/composition},
{lighting style — matches brand guidelines},
{color palette — use brand hex codes as descriptors:
  "#1F6FEB deep blue accent, #F5F5F5 off-white background"},
{negative prompt: no text, no logos, no people unless specified}
```

## Workflow

1. **Draft prompt** — using the template above
2. **Generate 4-8 variants** — `n: 4` or `n: 8`, iterate in low-res
3. **Pick winner** — judge on brand fit first, aesthetic second
4. **Upscale** — run the winner through `flux-pro-1.1` at final resolution (2048x2048 or higher)
5. **Variants for platforms** — same subject, different aspect ratios for feed / story / hero

## API call

```bash
curl -X POST https://api.kie.ai/v1/images/generations \
  -H "Authorization: Bearer $KIE_AI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "nano-banana",
    "prompt": "...",
    "size": "1024x1024",
    "n": 4
  }'
```

## Common pitfalls

| Problem | Fix |
|---|---|
| Image has weird text artifacts | Add "no text, no logos" to negative prompt |
| Hands / faces distorted | Crop to exclude, or switch to `flux-pro-1.1` which handles anatomy better |
| Colors wash out | Specify brand hex codes in prompt, not just color names |
| Everything looks stock-photo-generic | Add specific lighting + composition notes; reference a photographer's style |

## Output format

```
## Image generation — {goal}

### Prompt used
"Korean woman mid-20s ordering at a Japanese ramen shop, natural evening
light, steam rising from bowl, camera over her shoulder, warm color grade,
#1F6FEB navy jacket for brand accent, shallow depth of field. No text, no logos."

### 4 variants generated (cost: $0.XX × 4 = $X.XX)
- Variant 1: [URL] — ✓ selected
- Variant 2: [URL] — anatomy off
- Variant 3: [URL] — too stylized
- Variant 4: [URL] — good mood, weak composition

### Upscaled final
[URL] — 2048x2048 via flux-pro-1.1, ready for web use

### Platform variants
- 1:1 hero: [URL]
- 9:16 story: [URL]
- 16:9 YouTube thumbnail: [URL]
```

## Notes

Skeleton (v0.1).
