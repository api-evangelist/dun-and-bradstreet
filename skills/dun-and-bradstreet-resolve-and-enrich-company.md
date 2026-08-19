---
name: Resolve a company to a D-U-N-S Number and enrich it
description: >-
  Turn a messy company name and country into a verified D&B D-U-N-S Number,
  then pull the licensed Data Blocks for it. This is the foundational D&B
  Direct+ flow — almost every other D&B workflow starts here.
api: openapi/dun-and-bradstreet-identity-resolution-api-openapi.yml
apis:
  - openapi/dun-and-bradstreet-authentication-api-openapi.yml
  - openapi/dun-and-bradstreet-identity-resolution-api-openapi.yml
  - openapi/dun-and-bradstreet-enrich-api-openapi.yml
operations:
  - generateAccessToken
  - cleanseMatch
  - getDataBlocksByDuns
base_url: https://plus.dnb.com
generated: '2026-08-13'
method: generated
source: openapi/_original/dnb-direct-plus-openapi-original.yml
---

# Resolve a company to a D-U-N-S Number and enrich it

## Before you start

- Direct+ is contract-gated. A syntactically perfect call still returns `401`
  with `errorCode` `00004` if the customer's contract does not license the
  product or Data Block you asked for. That is not a bug to retry.
- You need a Direct+ **consumer key** and **consumer secret**.
- Base URL is `https://plus.dnb.com`. The version segment differs per product
  — `/v3` for the token, `/v1` for match and enrich.

## Step 1 — mint a token (`generateAccessToken`)

`POST /v3/token`

- Authenticate the request with **HTTP Basic**: consumer key as the username,
  consumer secret as the password.
- Body: `{"grant_type": "client_credentials"}`
- Read `access_token` and `expirationDateTime` off the response. Cache the
  token until `expirationDateTime`; there is no refresh token, so re-run this
  exchange when it expires.
- Send `Authorization: Bearer <access_token>` on every subsequent call.

## Step 2 — resolve the company (`cleanseMatch`)

`GET /v1/match/cleanseMatch`

Required query parameters:

- `name` — the company name as you have it, however messy
- `countryISOAlpha2Code` — two-letter country code

Add every optional identifier you hold, because each one raises match
confidence: `streetAddressLine1`, `addressLocality`, `addressRegion`,
`postalCode`, `telephoneNumber`, `registrationNumber`.

Control the result set with `candidateMaximumQuantity` and
`confidenceLowerLevelThresholdValue`.

The response carries `matchCandidates[]`. **Do not blindly take the first
candidate.** Each candidate exposes
`matchQualityInformation.confidenceCode`, `.matchGrade` and
`.matchDataProfile`. Decide a confidence floor before you call, and if no
candidate clears it, return "no confident match" rather than guessing — a
wrong D-U-N-S silently poisons everything downstream.

Take `matchCandidates[n].organization.duns` — a nine-digit string.

## Step 3 — enrich (`getDataBlocksByDuns`)

`GET /v1/data/duns/{duns}`

- `duns` — path parameter, the nine digits from step 2
- `blockIDs` — **required** query parameter naming which Data Blocks (and
  which version and level of each) to return. This is how you choose the
  response shape. Ask only for blocks the contract licenses; anything else
  returns `401`/`00004`.
- `tradeUp` — optional; shifts the response from the matched entity to its
  headquarters/parent record.

The response nests everything under `organization`, with block families
appearing as `corporateLinkage`, `financials`, `principals`,
`beneficialOwnership` and `riskAssessment`.

## Shortcut

If you want steps 2 and 3 in a single round trip, use `multiProcessMatchAndEnrich`
(`POST /v1/multiProcess`) instead — see the
*match-and-enrich-in-one-call* skill.

## Rules that apply to every call

- **No idempotency.** Direct+ publishes no `Idempotency-Key` header. Do not
  assume a retried write is safe.
- **Error envelope.** Every response, success or failure, carries
  `transactionDetail.transactionID`. Errors add
  `error.errorCode` + `error.errorMessage`. It is *not* RFC 9457
  problem+json. Log `transactionID` and the `x-request-id` response header on
  every failure — those are what D&B support asks for.
- **Rate limits.** Concurrency and monthly transaction entitlements are
  contractual and not published as numbers. On `429`, honour `Retry-After`
  with exponential backoff. On `403`, stop — that is a quota or entitlement
  wall, and retrying will not clear it.
- **Pagination** does not apply here; `cleanseMatch` caps candidates rather
  than paging them.

See `conventions/dun-and-bradstreet-conventions.yml`,
`errors/dun-and-bradstreet-problem-types.yml` and
`rate-limits/dun-and-bradstreet-rate-limits.yml`.
