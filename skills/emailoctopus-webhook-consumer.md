---
name: Consume EmailOctopus webhooks safely
description: >-
  Receive EmailOctopus contact events on your own endpoint, verify the HMAC signature,
  handle batched payloads and retries, and enqueue an automation in response.
api: openapi/emailoctopus-automation-api-openapi.yml
operations:
  - api_automations_automation_idqueue_post
  - api_lists_list_idcontacts_contact_id_get
generated: '2026-08-13'
method: generated
source: https://help.emailoctopus.com/article/314-webhooks + openapi/_original/emailoctopus-v2-openapi.json
---

# Consume EmailOctopus webhooks safely

## What arrives

EmailOctopus POSTs JSON to an HTTPS endpoint you register in the dashboard. There is **no
API to manage webhooks** — registration is dashboard-only, and a team may have at most
**2 endpoints**.

The body is an **array**, not a single object. Up to **1000 events** per request, because
EmailOctopus buffers for up to a minute before delivering. Code that reads
`body.type` instead of iterating will silently drop events.

Event types (8): `contact.created`, `contact.updated`, `contact.deleted`,
`contact.unsubscribed`, `contact.bounced`, `contact.complained`, `contact.opened`,
`contact.clicked`.

Each event: `id` (uuid), `type`, `list_id`, `contact_id`, `contact_email_address`,
`occurred_at` (ISO 8601), and optionally `contact_fields`, `contact_tags`,
`contact_status` (`PENDING` / `SUBSCRIBED` / `UNSUBSCRIBED` / `BOUNCED` / `COMPLAINED` —
note these are UPPERCASE here, while the REST API returns lowercase `pending` /
`subscribed` / `unsubscribed`) and `campaign_id` on campaign-driven events.

## Steps

1. **Verify the signature before parsing.** Compute `HMAC-SHA256` over the **raw request
   body** using the webhook's secret as the key, hex-encode it, and prefix `sha256=`.
   Compare against the `EmailOctopus-Signature` header with a constant-time comparison.
   Reject on mismatch. Read the body as raw bytes — re-serializing the JSON first will
   change the digest.

2. **Acknowledge fast, process asynchronously.** Return 2xx as soon as the signature
   verifies and the payload is durably queued. EmailOctopus retries a failed delivery **up
   to 9 times over roughly 10 days** with escalating delays, so a slow handler turns one
   burst into ten.

3. **Deduplicate on `id`.** Retries redeliver the same events. The event `id` is the
   dedupe key; `occurred_at` alone is not, because a batch can carry many events with
   close timestamps.

4. **Do not assume ordering.** Events are buffered and batched. Reconcile against
   `occurred_at`, and when current state matters, read it back with
   `api_lists_list_idcontacts_contact_id_get` rather than replaying the event stream.

5. **Filter at the source.** When registering the webhook you can select which event types
   to receive and optionally **exclude events generated via the API or an import**. Use
   that exclusion when your own writes would otherwise echo back and trigger a sync loop —
   this is the single most useful setting on the whole surface.

6. **React by enqueueing an automation.** `api_automations_automation_idqueue_post`
   (`POST /automations/{automation_id}/queue`) puts a contact into an existing automation.
   It returns `204`. The `automation_id` must come from the dashboard — the API has no
   operation to list automations, so it must be configured, not discovered.

## Rules

- The enqueue endpoint returns `409` when the contact is already queued, and `422` when the
  payload fails validation. Neither is retryable as-is.
- The enqueue has **no idempotency key**. Because a webhook can be redelivered, dedupe on
  the event `id` *before* enqueueing, or the same contact enters the automation twice.
- HTTPS is required for the receiving endpoint; a plain-HTTP URL cannot be registered.
- Webhook delivery is not covered by the REST rate limit, but your responses to
  EmailOctopus should stay fast regardless.
