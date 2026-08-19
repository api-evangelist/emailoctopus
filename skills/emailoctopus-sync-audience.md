---
name: Sync an audience into EmailOctopus in bulk
description: >-
  Build a list with its custom fields and tags, then keep it in sync with an external
  source of truth using the batch contact endpoint and cursor pagination, staying inside
  the token-bucket rate limit.
api: openapi/emailoctopus-list-api-openapi.yml
operations:
  - api_lists_post
  - api_lists_list_idfields_post
  - api_lists_list_idtags_post
  - api_lists_list_idcontactsbatch_put
  - api_lists_list_idcontacts_get
  - api_lists_list_idcontacts_contact_id_delete
generated: '2026-08-13'
method: generated
source: openapi/_original/emailoctopus-v2-openapi.json + https://emailoctopus.com/api-documentation/v2
---

# Sync an audience into EmailOctopus in bulk

## Shape of the model

A **list** owns everything. Fields, tags and contacts are all addressed through
list-scoped paths (`/lists/{list_id}/...`) and never globally. Fields and tags are keyed
by a `tag` slug, not a UUID; contacts are keyed by a UUID `id` but are also addressable by
email address through the upsert. See `data-model/emailoctopus-data-model.yml`.

## Steps

1. **Create the list.** `api_lists_post` (`POST /lists`) with a `name` and
   `double_opt_in`. Returns `201` with the list `id`. Store it.

2. **Define custom fields before importing anyone.** For each column you intend to sync,
   call `api_lists_list_idfields_post` (`POST /lists/{list_id}/fields`) with `label`, `tag`,
   `type` (`text`, `number` or `date`) and, optionally, `choices` and a `fallback`. The
   `tag` you set here is the key you will write contact data under — choose it once and
   keep it stable, because renaming it later is a `PUT` to
   `api_lists_list_idfields_tag_put` and every existing consumer breaks.

3. **Create the tags you segment on.** `api_lists_list_idtags_post`
   (`POST /lists/{list_id}/tags`). Creating a tag that already exists returns
   `409 already-exists` — check the list's `tags` array first, or handle the 409 as a
   success.

4. **Push contacts in batches.** `api_lists_list_idcontactsbatch_put`
   (`PUT /lists/{list_id}/contacts/batch`) updates multiple contacts in one request. Prefer
   this to a loop of single upserts: one request against a bucket of 100 tokens goes much
   further than a hundred.

5. **Reconcile by paging the list.** `api_lists_list_idcontacts_get`
   (`GET /lists/{list_id}/contacts`) returns at most **100** contacts per page in `data`.
   Follow `paging.next.url` (or pass `paging.next.starting_after` as `starting_after`) until
   `paging.next` is absent. Diff against your source of truth.

6. **Remove what no longer belongs.** `api_lists_list_idcontacts_contact_id_delete`
   (`DELETE /lists/{list_id}/contacts/{contact_id}`) returns `204`. Deleting removes the
   record entirely; if the person opted out, set `status: unsubscribed` through the upsert
   instead so the suppression is preserved.

## Rate-limit budget

The bucket holds **100 tokens** and refills at **10 per second**. A full-list reconcile of
50,000 contacts is 500 page requests — roughly 50 seconds at the sustained rate. Plan the
sync as a steady stream, not a burst, and treat `X-RateLimiting-Remaining` as the signal to
slow down before you hit `429 too-many-requests`. The provider explicitly says a dashboard
**import** is faster than the API for a first bulk load and is not subject to these limits.

## Rules

- No idempotency key exists. A batch PUT is safe to replay because it is an upsert; a
  `POST /lists/{list_id}/contacts` is not.
- `422 out-of-limits` means the sync would push the account past its plan's subscriber cap.
  Stop and surface it — retrying will not help. See `plans/emailoctopus-plans-pricing.yml`.
- `422 unprocessable-content` carries an `errors[]` array with a JSON Pointer per failure.
  In a batch this is how you find *which* row failed — log the pointer, not just the status.
- Campaigns are **read-only** over the API. This flow can build and maintain the audience,
  but it cannot create or send a campaign; that happens in the dashboard.
