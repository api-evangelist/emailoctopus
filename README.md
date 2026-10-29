# EmailOctopus

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

EmailOctopus is an affordable email-marketing platform for newsletters, campaigns, automations and audience management, built on a low-cost sending model that undercuts the incumbent ESPs. Its v2 REST API is served from a single host, https://api.emailoctopus.com, with no version segment in the path, and is authenticated with a Bearer API key. The API covers subscriber lists, contacts, per-list custom fields and tags, campaigns and three campaign report projections, plus an operation to enqueue a contact into an automation. EmailOctopus publishes a real OpenAPI 3.1.0 document (25 operations, 19 schemas) at its v2 reference URL, documents an RFC 7807 error envelope with dereferenceable type URIs, cursor pagination, a token-bucket rate limit of 10 requests per second with a burst of 100, and an HMAC-SHA256-signed webhook surface carrying eight contact event types. It ships no official client SDK, no CLI and no MCP server.

**Areas:** Email · Email Marketing · Newsletters · Campaigns · Automation · Contacts · List · Marketing · Webhook · Transactional Email · Subscriber Management · Reporting

## EmailOctopus on APIs.io

Everything profiled for EmailOctopus is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/emailoctopus/](https://apis.io/providers/emailoctopus/)** — or go straight to one of the APIs or properties below.

### APIs (6)

- [EmailOctopus Automation API](https://apis.io/apis/emailoctopus/emailoctopus-automation-api/) — Enqueue an existing contact into an EmailOctopus automation. Write-only — the v2 API exposes no operation to list or read automations, so the automation id…
- [EmailOctopus Campaign API](https://apis.io/apis/emailoctopus/emailoctopus-campaign-api/) — Read campaigns and their three report projections — summary counters, per-link clicks, and per-contact engagement rows. Read-only: campaigns cannot be…
- [EmailOctopus Contact API](https://apis.io/apis/emailoctopus/emailoctopus-contact-api/) — Manage subscribers on a list: create, read, update, delete, upsert by email address, and update many at once through the batch endpoint. The upsert (PUT…
- [EmailOctopus Field API](https://apis.io/apis/emailoctopus/emailoctopus-field-api/) — Define the per-list custom fields contact records are written against. Fields are keyed by a stable tag slug and typed text, number or date, with optional…
- [EmailOctopus List API](https://apis.io/apis/emailoctopus/emailoctopus-list-api/) — Create and manage subscriber lists, the aggregate root of the EmailOctopus data model. Fields, tags and contacts are all owned by a list and addressed…
- [EmailOctopus Tag API](https://apis.io/apis/emailoctopus/emailoctopus-tag-api/) — Create, rename, delete and list the tags used to segment contacts on a list. Tags are keyed by slug rather than an id. 4 operation(s) in the EmailOctopus v2…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### AsyncAPI (1)

Event-driven and webhook contracts.

- [Emailoctopus Webhooks](https://apis.io/asyncapis/emailoctopus/emailoctopus-webhooks/)

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [emailoctopus](https://apis.io/servers/emailoctopus/emailoctopus/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Emailoctopus Agentic Access](https://apis.io/agentic-access/emailoctopus/emailoctopus-agentic-access/)

#### Collections (7)

Postman and Open Collection request collections.

- [EmailOctopus v2 API](https://apis.io/collections/emailoctopus/open-emailoctopus/)
- [EmailOctopus v2 Automation API](https://apis.io/collections/emailoctopus/open-emailoctopus-automation-api/)
- [EmailOctopus v2 Automation Campaign API](https://apis.io/collections/emailoctopus/open-emailoctopus-campaign-api/)
- [EmailOctopus v2 Automation Contact API](https://apis.io/collections/emailoctopus/open-emailoctopus-contact-api/)
- [EmailOctopus v2 Automation Field API](https://apis.io/collections/emailoctopus/open-emailoctopus-field-api/)
- [EmailOctopus v2 Automation List API](https://apis.io/collections/emailoctopus/open-emailoctopus-list-api/)
- [EmailOctopus v2 Automation Tag API](https://apis.io/collections/emailoctopus/open-emailoctopus-tag-api/)

#### Overlays (6)

OpenAPI overlays, including conversational phrasing for each operation.

- [Emailoctopus Automation Overlay](https://apis.io/overlays/emailoctopus/emailoctopus-automation-overlay/)
- [Emailoctopus Campaign Overlay](https://apis.io/overlays/emailoctopus/emailoctopus-campaign-overlay/)
- [Emailoctopus Contact Overlay](https://apis.io/overlays/emailoctopus/emailoctopus-contact-overlay/)
- [Emailoctopus Field Overlay](https://apis.io/overlays/emailoctopus/emailoctopus-field-overlay/)
- [Emailoctopus List Overlay](https://apis.io/overlays/emailoctopus/emailoctopus-list-overlay/)
- [Emailoctopus Tag Overlay](https://apis.io/overlays/emailoctopus/emailoctopus-tag-overlay/)

#### Security (2)

Authentication, trust centre, and vulnerability disclosure.

- [Emailoctopus Authentication](https://apis.io/security/emailoctopus/emailoctopus-authentication/)
- [Emailoctopus Domain Security](https://apis.io/security/emailoctopus/emailoctopus-domain-security/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Emailoctopus Plans Pricing](https://apis.io/plans/emailoctopus/emailoctopus-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Emailoctopus Rate Limits](https://apis.io/rate-limits/emailoctopus/emailoctopus-rate-limits/)

#### FinOps (1)

What the API costs to run, by operation.

- [Emailoctopus Finops](https://apis.io/finops/emailoctopus/emailoctopus-finops/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://emailoctopus.com)
- [Documentation](https://emailoctopus.com/api-documentation/v2)
- [Getting started](https://emailoctopus.com/api-documentation/v2#section/Authentication)
- [API reference](https://emailoctopus.com/api-documentation/v2)
- [Pricing](https://emailoctopus.com/pricing)
- [Sign up](https://emailoctopus.com/account/sign-up)
- [Support](https://help.emailoctopus.com)
- [Blog](https://emailoctopus.com/blog)
- [Terms of service](https://emailoctopus.com/legal/terms)
- [Privacy policy](https://emailoctopus.com/legal/privacy)
- [Status](https://status.emailoctopus.com)

## API Evangelist rating

**Kin Score 57.3** — strong · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 66.1 |
| Contract quality | 59.4 |
| Contract governance | 4.5 |
| Operational transparency | 55.3 |
| Developer ergonomics | 50.6 |
| Access clarity | 76.3 |

**Agent readiness 34.9** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | verified |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | yes |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | partial |
| Operation Distinctiveness | distinct |

**Regulatory alignment 20.8** — Telecommunications

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/emailoctopus/](https://apis.io/providers/emailoctopus/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for EmailOctopus: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/emailoctopus/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
