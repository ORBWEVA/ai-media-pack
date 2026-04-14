---
name: ai-media-pipeline
description: Build an end-to-end n8n workflow for AI media generation — trigger, generate, post-process, store, publish. Use when the user wants to automate a recurring content pipeline (daily social posts, weekly video ads, on-demand image generation from a form submission). Triggers on "media pipeline", "n8n workflow", "automate content generation", "/ai-media:pipeline".
---

# /ai-media:pipeline — n8n workflow for AI media generation

Orchestrate `/ai-media:video`, `/ai-media:image`, `/ai-media:voice` into a recurring workflow. Use n8n (students already install the n8n MCP in Week 0).

## Common pipeline shapes

### Daily social post pipeline
```
Cron (daily 9am) → Pick prompt from Airtable/Notion/Supabase →
Generate 3 image variants via /ai-media:image →
Pick winner (manual approval step OR auto-pick first) →
Post to Instagram/X via their APIs →
Log cost + engagement to Supabase
```

### On-demand image from form
```
Webhook (form submission) → Extract prompt fields →
/ai-media:image with user-supplied params →
Upload result to Supabase Storage →
Return signed URL to user via webhook response
```

### Weekly video ad batch
```
Cron (Monday 6am) → Pull 5 ad prompts from Notion database →
/ai-media:video for each (3 variants × 5 prompts = 15 takes) →
Human review step (Slack notification with URLs) →
On approval: upload selected to Meta Ads / Google Ads via APIs →
Summary report to Slack
```

## Workflow

1. **Pick the trigger** — Cron (recurring), Webhook (on-demand), Manual (user-triggered test)
2. **Pick the source of prompts** — static list, CMS (Notion/Airtable), Supabase table, LLM-generated from a topic seed
3. **Generate** — call kie.ai via HTTP Request node (n8n has native support). Use env var for `KIE_AI_API_KEY`, never inline.
4. **Post-process** — ffmpeg subflow if needed (crop, watermark, add audio, CTA overlay)
5. **Store** — Supabase Storage, S3, Cloudflare R2. Record metadata (prompt used, model, cost, timestamp) in a DB table.
6. **Publish** — platform APIs (Meta Graph API, X API, TikTok Content Posting, YouTube Data, LinkedIn)
7. **Monitor** — log costs, alerts on failures, retry on transient errors

## n8n node map (template)

```
[Cron / Webhook trigger]
  ↓
[Set node — load prompt batch]
  ↓
[Split In Batches — process one at a time to stay under rate limits]
  ↓
[HTTP Request — POST to kie.ai]
  ↓
[IF — did generation succeed?]
  ↓ yes
[HTTP Request — download asset]
  ↓
[Supabase — upload to storage]
  ↓
[Supabase — insert metadata row]
  ↓
[HTTP Request — post to platform]
  ↓
[Slack — notify success]
  ↓ no
[Slack — alert failure; retry logic]
```

## Output

A deployable n8n workflow JSON, ready to import via n8n's UI or API.

## Notes

Skeleton (v0.1). Will grow with per-platform posting templates (Meta, TikTok, YouTube, LinkedIn) and cost-monitoring dashboards.
