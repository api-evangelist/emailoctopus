---
name: Pull EmailOctopus campaign performance
description: >-
  Read campaign results out of EmailOctopus — the summary counters, the per-link click
  report, and the per-contact engagement rows — and reconcile them against your own
  analytics.
api: openapi/emailoctopus-campaign-api-openapi.yml
operations:
  - api_campaigns_get
  - api_campaigns_campaign_id_get
  - api_campaigns_campaign_idreportssummary_get
  - api_campaigns_campaign_idreportslinks_get
  - api_campaigns_campaign_idreports_get
generated: '2026-08-13'
method: generated
source: openapi/_original/emailoctopus-v2-openapi.json + https://emailoctopus.com/api-documentation/v2
---

# Pull EmailOctopus campaign performance

## What this API can and cannot do

The Campaign surface is **read-only**. There is no create, no send, no schedule and no
delete. If a task requires sending a campaign, it cannot be done through the v2 API —
say so rather than looking for an undocumented endpoint.

## Steps

1. **List campaigns.** `api_campaigns_get` (`GET /campaigns`). Paged: results in `data`,
   cursor at `paging.next.starting_after`, 100 per page. Each campaign carries `status`
   (`draft`, `sending`, `sent`, `error`), `name`, `subject`, `to` (the lists it targets),
   `created_at` and `sent_at`.

2. **Filter to what is actually reportable.** Only `status: sent` campaigns have meaningful
   reports. A `draft` or `sending` campaign will return empty or partial counters — do not
   present those as a result.

3. **Get the headline numbers.** `api_campaigns_campaign_idreportssummary_get`
   (`GET /campaigns/{campaign_id}/reports/summary`) returns `sent`, `bounced`, `opened`,
   `clicked`, `complained` and `unsubscribed`. `bounced`, `opened` and `clicked` are
   objects, not integers — read their sub-counters rather than assuming a scalar.

4. **Get click detail per link.** `api_campaigns_campaign_idreportslinks_get`
   (`GET /campaigns/{campaign_id}/reports/links`) returns the per-URL click breakdown in
   `data`. This is the report to use for content performance.

5. **Get the per-contact rows.** `api_campaigns_campaign_idreports_get`
   (`GET /campaigns/{campaign_id}/reports`) returns individual contact engagement filtered
   by a report status. Use it to attribute behaviour to a person; it is paged like every
   other collection here, and it is the largest of the three — budget rate limit for it.

6. **Resolve the campaign itself when you need context.**
   `api_campaigns_campaign_id_get` (`GET /campaigns/{campaign_id}`) gives `from`, `content`
   and the target lists so a report can be labelled correctly.

## Rules

- **Open rates are not truth.** `opened` reflects tracking-pixel loads and is suppressed by
  privacy proxies. Report it as a directional signal, and prefer `clicked` when a decision
  depends on it.
- Reports are **retained for 30 days on the free Starter plan** and unlimited on Pro. A
  missing report on an old campaign is a plan limit, not an API failure.
- Everything here is a `GET` and therefore naturally retryable. The constraint is the
  token bucket — 10 requests/second, burst 100 — so a whole-account report pull should
  page steadily and honour `429 too-many-requests` with `X-RateLimit-Retry-After`.
- `404 not-found` on a campaign id usually means it belongs to another account; `403
  access-denied` means the key does. Check the key before the id.
- For live engagement rather than historical reporting, subscribe to the `contact.opened`,
  `contact.clicked`, `contact.bounced` and `contact.complained` webhooks instead of polling —
  see `asyncapi/emailoctopus-webhooks.yml`.
