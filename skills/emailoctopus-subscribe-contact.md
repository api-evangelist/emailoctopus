---
name: Subscribe a contact to an EmailOctopus list
description: >-
  Add or update a subscriber on an EmailOctopus list, with custom fields and tags,
  without creating duplicates. Use this for newsletter sign-ups, CRM syncs, and any
  flow where the same email address may arrive more than once.
api: openapi/emailoctopus-contact-api-openapi.yml
operations:
  - api_lists_get
  - api_lists_list_idcontacts_put
  - api_lists_list_idcontacts_post
  - api_lists_list_idcontacts_contact_id_get
generated: '2026-08-13'
method: generated
source: openapi/_original/emailoctopus-v2-openapi.json + https://emailoctopus.com/api-documentation/v2
---

# Subscribe a contact to an EmailOctopus list

## Before you start

- Base URL is `https://api.emailoctopus.com`. There is **no version segment** in the path.
- Every request carries `Authorization: Bearer {api_key}`. Create a key at
  <https://api.emailoctopus.com/developer/api-keys/create>.
- A key created before **2024-10-07** is a legacy v1 key and will fail v2 with
  `401 unauthorized` / `"Invalid key."`. Generate a new key rather than debugging the request.
- Any request with a JSON body must send `Content-Type: application/json`, or you get
  `415 unsupported-media-type`.

## Steps

1. **Find the list.** Call `api_lists_get` (`GET /lists`) and pick the list whose `name`
   matches. Keep its `id` — it is a bare UUID with no type prefix, so store it alongside
   the fact that it is a *list* id.
   - This is a paged collection: results are in `data`, and the next page cursor is at
     `paging.next.starting_after`. Pass it back as the `starting_after` query parameter.
     Never parse or reconstruct the cursor — EmailOctopus documents it as opaque and
     subject to change.

2. **Check the list's custom fields before you send any.** The list object returned by
   `api_lists_get` carries a `fields` array. Each field has a `tag` (the key you write to),
   a `label`, and a `type` of `text`, `number` or `date`. Writing to a `tag` that does not
   exist on that list is a validation failure, not a silent no-op.

3. **Upsert the contact.** Call `api_lists_list_idcontacts_put`
   (`PUT /lists/{list_id}/contacts`) with the email address, fields and tags.
   - Use **PUT, not POST**. `api_lists_list_idcontacts_post` returns `409 already-exists`
     when the address is already on the list. The PUT is an upsert — the EmailOctopus
     reference names it explicitly as the fix for `already-exists`.
   - `fields` is an object keyed by the field `tag` from step 2.
   - `status` accepts `pending`, `subscribed` or `unsubscribed`. If the list has
     `double_opt_in: true`, send `pending` and let EmailOctopus run the confirmation, unless
     you have your own recorded consent.

4. **Read back only if you need the assigned id.** `api_lists_list_idcontacts_contact_id_get`
   (`GET /lists/{list_id}/contacts/{contact_id}`) returns the stored record. The upsert
   response already contains it, so an immediate read-back is usually wasted quota.

## Rules this API enforces

- **There is no idempotency key.** EmailOctopus documents no `Idempotency-Key` header and
  no client request key. Safe retries come from method semantics only: replaying the PUT
  upsert converges; replaying the POST creates a `409`. Build retries around the PUT.
- **Rate limit is a token bucket**: 10 requests/second sustained, burst of 100. On
  exhaustion you get `429 too-many-requests`; read `X-RateLimit-Retry-After` to decide when
  to retry, and back off exponentially. Remaining tokens are reported in
  `X-RateLimiting-Remaining` (note the inconsistent header naming — both spellings are the
  provider's own).
- **Errors are RFC 7807** — `{type, title, detail, status}` served as `application/json`
  (not `application/problem+json`). Branch on the `type` URI, never on the prose in `detail`.
  On `422 unprocessable-content`, read the `errors[]` array: each entry carries a JSON
  Pointer (`pointer`) to the offending attribute plus a human `detail`.
- **`403 access-denied`** means the key belongs to a different account than the list. Compare
  the last four characters of the key against the account's API keys — that is the check
  EmailOctopus documents.
- **`422 out-of-limits`** means the account's plan subscriber cap would be exceeded. This is a
  billing condition, not a bad request; do not retry it.

## Do not

- Do not bulk-load contacts one at a time through this flow. For bulk work use
  `api_lists_list_idcontactsbatch_put` (see the batch skill) or the dashboard import, which
  the provider states is faster and not subject to the API rate limit.
- Do not poll for contact changes. Use webhooks (`contact.created`, `contact.updated`,
  `contact.unsubscribed`, ...) — see `asyncapi/emailoctopus-webhooks.yml`.
