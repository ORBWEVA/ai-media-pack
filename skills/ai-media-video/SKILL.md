---
name: ai-media-video
description: Generate video via Veo 3 (or other video models through kie.ai). Handles prompt-to-script-to-clip, aspect-ratio variations, duration targeting. Use when the user wants to create video ads, product demos, social clips, marketing video, explainers, or variant tests. Triggers on "generate video", "make a video ad", "Veo 3", "product demo video", "/ai-media:video".
---

# /ai-media:video — Video generation via Veo 3

LLM-generated video is getting usable for short, focused clips — 3-10 seconds of product demo, social hook, or ad variant. For longer content you're still better off with a hybrid approach (AI generates B-roll, humans record VO).

## Inputs

Ask for:
1. **Goal** — what's the video for? (Ad, product demo, social hook, explainer, mood piece)
2. **Duration** — 3s / 5s / 10s / 15s
3. **Aspect ratio** — 16:9 (YouTube), 9:16 (TikTok/Shorts/Reels), 1:1 (feed), 4:5 (Instagram)
4. **Brand references** — link to past brand video or describe tone (energetic, calm, technical, warm)
5. **ICP** — who is this for? Shapes tone, pacing, visual density

## Workflow

1. **Brief → script** — turn the goal into a 1-2 sentence visual brief. Veo 3 takes natural-language prompts; avoid bullet lists.
2. **Generate 3 variants** — same prompt, different seeds. Veo 3 output varies a lot; one good take out of 3 is normal.
3. **Pick winner** — side-by-side compare. Judge on: clarity (can a cold viewer parse it in 3 seconds?), brand fit, CTA readability.
4. **Variations** — once you have a winner, generate aspect-ratio variants for each platform.
5. **Post-process** — add brand overlay, CTA text, music via ffmpeg or a video editor (out of scope for this skill).

## Prompt template

```
{duration}-second {aspect_ratio} {tone} video showing {subject} {action}
in {setting}. Camera: {shot_type}. Mood: {mood}. No text overlay
(we add it in post). Style: {reference_or_style_words}.
```

Example:
```
5-second 9:16 energetic video showing a young Korean woman
confidently speaking Japanese to a shopkeeper in Shinjuku.
Camera: over-shoulder medium shot, natural handheld motion.
Mood: everyday empowerment. No text overlay. Style: cinematic,
warm evening light, shallow depth of field.
```

## API call (kie.ai)

```bash
curl -X POST https://api.kie.ai/v1/videos/generations \
  -H "Authorization: Bearer $KIE_AI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "veo-3",
    "prompt": "...",
    "duration": 5,
    "aspect_ratio": "9:16",
    "n": 3
  }'
```

Returns a polling URL or direct MP4s depending on tier.

## Output format

```
## Video generation — {goal}

### Brief
5-second 9:16 energetic Korean-uni-student + shopkeeper scene for
Kora Lingo's "real conversations" campaign.

### 3 takes generated (cost: $0.XX × 3 = $X.XX)
- Take 1: [URL] — hand gestures too stiff
- Take 2: [URL] — ✓ selected — great eye contact, lighting perfect
- Take 3: [URL] — camera motion too fast

### Next
- Generate 16:9 variant of Take 2 for YouTube preroll
- Add brand overlay + CTA in post (ffmpeg command attached)
```

## Verification Status (v0.1 — 2026-04-14) — ⚠ Verify before use

**Confident in:** the workflow shape (brief → prompt → generate 3 variants → pick winner → aspect-ratio variants), the prompt template structure (subject, action, setting, camera, mood, style), common failure modes of AI video (temporal consistency, motion realism).

**Needs verification:**
- Veo 3 availability via kie.ai — not confirmed
- API endpoint `https://api.kie.ai/v1/videos/generations` — **guessed, likely wrong**
- Request fields (`duration`, `aspect_ratio`, `n`) — guessed; kie.ai may use different names
- Typical Veo 3 output length limits — stated as ≤ 10s but may be longer/shorter
- Pricing cited in `/ai-media:cost` — guessed

**Next author pass must:** validate against kie.ai's current video API, refresh model list, test a real generation end-to-end, record actual cost + latency.
