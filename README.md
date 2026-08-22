# Dun & Bradstreet

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

API Evangelist profile of Dun & Bradstreet — a global provider of business decisioning data and analytics whose D-U-N-S Number identifies more than 500 million companies worldwide, and whose primary developer surface is the D&B Direct+ REST API platform.

This repository indexes Dun & Bradstreet's developer surface as an APIs.json index together with derived OpenAPI, JSON Schema, JSON Structure, JSON-LD, example, Spectral, Naftiko capability, vocabulary, plans, rate-limits, and FinOps artifacts.

- Provider site: https://www.dnb.com/
- Direct+ documentation: https://directplus.documentation.dnb.com/ (gated by Okta sign-in)
- Repo: https://github.com/api-evangelist/dun-and-bradstreet

## APIs documented

| API | Surface | Doc |
|---|---|---|
| D&B Direct+ API | Umbrella REST API at `https://plus.dnb.com` | [docs](https://directplus.documentation.dnb.com/) |
| D&B Direct+ Identity Resolution API | Match an input record to a D-U-N-S Number | [Identity Resolution](https://directplus.documentation.dnb.com/html/guides/IdentityResolution.html) |
| D&B Direct+ Search API | Find companies and contacts by criteria | [Search](https://directplus.documentation.dnb.com/html/guides/Search.html) |
| D&B Direct+ Enrich (Data Blocks) API | Retrieve versioned Data Blocks for a D-U-N-S | [Enrich](https://directplus.documentation.dnb.com/html/guides/EnrichmentGuide.html) |
| D&B Direct+ Multi-Process API | Match plus Enrich in a single call | [Multi-Process](https://directplus.documentation.dnb.com/html/guides/MultiProcess.html) |
| D&B Direct+ Data File (Batch) API | Asynchronous bulk submit / status / download | [Batch](https://directplus.documentation.dnb.com/html/guides/BatchUserGuide.html) |
| D&B Direct+ Monitoring API | Register portfolio, pull change notifications | [Monitoring](https://directplus.documentation.dnb.com/html/guides/Monitoring.html) |
| D&B Direct+ Research API | Retrieve historical audit responses | [Research](https://directplus.documentation.dnb.com/html/guides/Research.html) |

## Artifacts

- `openapi/dnb-direct-plus-openapi-original.yml` — OpenAPI 3.0 sketch of the Direct+ surface (token, match, search, enrich, multi-process, batch, monitoring, research).
- `examples/` — 14 request/response examples covering every Direct+ operation.
- `json-schema/` — JSON Schemas for the Organization, Match Candidate, and Monitoring Notification entities.
- `json-structure/dnb-direct-plus-organization-structure.json` — structural overview of the Direct+ organization payload.
- `json-ld/dun-and-bradstreet-context.jsonld` — JSON-LD context aligning Direct+ fields to schema.org and FIBO.
- `rules/dnb-direct-plus-rules.yml` — Spectral ruleset enforcing Direct+ conventions (lowerCamelCase, `duns` field, `/v{n}/` path prefix, Title Case tags, bearer auth).
- `capabilities/` — three Naftiko capabilities: `identity-resolution`, `master-data-sync`, `third-party-risk-onboarding`.
- `vocabulary/dun-and-bradstreet-vocabulary.yml` — controlled vocabulary for D-U-N-S, Data Blocks, Confidence Code, Match Grade, PAYDEX, Failure Score, etc.
- `plans/dun-and-bradstreet-plans-pricing.yml` — API Commons Plans 0.1 profile (contract-only subscription, no public list price, free standalone D-U-N-S registration).
- `rate-limits/dun-and-bradstreet-rate-limits.yml` — API Commons Rate Limits 0.1 profile (per-token concurrency + rps + monthly quota + portfolio size, all contract-defined and gated behind sign-in).
- `finops/dun-and-bradstreet-finops.yml` — FinOps view of cost dimensions and optimization levers for Direct+.

## Scale and context

- More than 500 million businesses in the D&B Data Cloud, keyed by a nine-digit D-U-N-S Number first issued in 1963.
- Founded in 1841 as The Mercantile Agency in New York City; today headquartered in Jacksonville, Florida.
- Taken private by Clearlake Capital in August 2025 for approximately $7.7 billion (including debt); no longer trades on NYSE.
- D&B distributes data products on the Snowflake Marketplace and integrates natively with Salesforce, Microsoft Dynamics 365, SAP, Oracle, Adobe Real-Time CDP, and HubSpot.

## Notable absences

- The Direct+ documentation portal at `directplus.documentation.dnb.com` is fully gated by Okta sign-in; no public OpenAPI download, no public Postman collection link, no public rate-limit numbers, no public list pricing.
- No public GitHub organization at `github.com/dun-and-bradstreet` (the org exists but exposes zero public repositories), so no official SDKs or sample apps.
- No public status page or RSS-based changelog discoverable outside the gated portal.

## Maintainer

Kin Lane — kin@apievangelist.com
