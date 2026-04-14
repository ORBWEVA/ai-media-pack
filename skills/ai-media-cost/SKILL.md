---
name: ai-media-cost
description: Estimate cost of a batch AI media generation run before committing. Handles per-model pricing across video, image, voice. Use before kicking off large batches or recurring pipelines to avoid overspending. Triggers on "how much will this cost", "estimate cost", "AI generation budget", "cost of batch", "/ai-media:cost".
---

# /ai-media:cost — Pre-flight cost estimate

AI media generation is cheap per unit, expensive at scale. A single Veo 3 clip is $0.25-$1.50. A daily pipeline generating 10 clips a day is $75-$450/month. This skill prevents surprise bills.

## Inputs

Ask for:
1. **Scope** — one-off batch OR recurring pipeline
2. **For each media type**: how many units × how many runs × how often
3. **Model selection** — default vs premium
4. **Budget cap** — what's the monthly ceiling

## Pricing snapshot (April 2026)

Ballpark via kie.ai — check dashboard for current rates. Prices change.

| Media | Model | Unit cost | Notes |
|---|---|---|---|
| Image | nano-banana | $0.01-$0.03 | Fast, cheap. Good for iteration. |
| Image | flux-pro-1.1 | $0.04-$0.08 | Premium quality. |
| Image | imagen-4 | $0.05-$0.10 | Google's flagship. |
| Video | veo-3 | $0.25-$1.50 | Per clip, depends on duration + quality tier |
| Video | runway-gen-4 | $0.30-$2.00 | Alternative to Veo 3 |
| Voice (TTS) | elevenlabs-turbo-v2 | $0.30 / 1K chars | ~$0.10 for a 60-second voiceover |
| Voice (cloning) | elevenlabs-pro | $0.90 / 1K chars | ~$0.30 for 60s |

## Workflow

1. Ask user for scope numbers
2. Multiply per-unit × volume × frequency
3. Show breakdown + monthly projection
4. Flag if projection exceeds budget cap — suggest downgrades (nano-banana instead of flux, shorter clips, less frequent runs)

## Example

User wants: 4 Instagram posts/day (2 image + 1 video + 1 voice), 7 days/week.

```
Daily:
- 2 images × nano-banana @ $0.02 = $0.04
- 1 image × flux-pro-1.1 @ $0.06 = $0.06
- 1 video × veo-3 (5s) @ $0.75 = $0.75
- 1 voice × elevenlabs-turbo-v2 (60s ≈ 150 chars) @ $0.05 = $0.05
Daily subtotal: $0.90

Monthly (× 30): $27.00
Annual (× 365): $328.50
```

## Output format

```
## Cost estimate — {scope}

### Assumptions
- Batch size: N units per run
- Frequency: daily / weekly / monthly
- Model mix: X% default, Y% premium

### Breakdown
[per-unit cost × volume × frequency table]

### Monthly projection: $XX.XX
### Annual projection: $XX.XX

### Budget check
✓ Within $50/mo cap
(or)
✗ Exceeds $50/mo cap by $22. Suggestions:
  - Drop flux-pro to nano-banana on 50% of images → saves $20/mo
  - Generate video 3x/week instead of daily → saves $X/mo
```

## Notes

Skeleton (v0.1). Pricing table will need updating quarterly. Consider automating rate fetch from kie.ai's pricing API once available.
