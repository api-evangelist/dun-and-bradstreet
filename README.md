# Dun & Bradstreet

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
