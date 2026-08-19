---
name: Search for companies and contacts, and replay a past response
description: >-
  Discover companies and people by criteria when you have no D-U-N-S Number
  yet, and retrieve the verbatim response D&B returned at an earlier moment
  for audit and dispute defence.
api: openapi/dun-and-bradstreet-search-api-openapi.yml
apis:
  - openapi/dun-and-bradstreet-authentication-api-openapi.yml
  - openapi/dun-and-bradstreet-search-api-openapi.yml
  - openapi/dun-and-bradstreet-research-api-openapi.yml
operations:
  - generateAccessToken
  - searchCompaniesByCriteria
  - searchContacts
  - getAuditRecord
base_url: https://plus.dnb.com
generated: '2026-08-13'
method: generated
source: openapi/_original/dnb-direct-plus-openapi-original.yml
---

# Search for companies and contacts, and replay a past response

## Step 0 — token

`generateAccessToken` (`POST /v3/token`), HTTP Basic with consumer
key/secret, body `{"grant_type":"client_credentials"}`.

## Search companies (`searchCompaniesByCriteria`)

`GET /v1/search/criteria`

- `searchTerm`
- `countryISOAlpha2Code`
- `pageNumber`, `pageSize` — this is the only genuinely paged surface in
  Direct+, and it is page-number paging, not cursor paging.

The response gives `candidatesReturnedQuantity`, `candidatesMatchedQuantity`
and `searchCandidates[].organization`. Use `candidatesMatchedQuantity` — not
the length of the array — to decide whether to page again.

**Search is discovery, not resolution.** If you have a specific company in
hand, use `cleanseMatch` instead: it returns a confidence code and match
grade, and search does not.

## Search contacts (`searchContacts`)

`GET /v1/search/contact`

- `searchTerm`, `countryISOAlpha2Code`, `pageSize`
- Note there is **no `pageNumber`** on this operation — you can size the
  result set but not walk it.

Each candidate carries `contact.fullName`, `contact.jobTitles[].title` and the
`organization` the person belongs to.

Contact data is personal data. Handle it under D&B's data-transparency terms
and your own privacy obligations; do not cache it beyond what you need.

## Replay a past response (`getAuditRecord`)

`GET /v2/audit/duns/{duns}` — note this is `/v2`, not `/v1`.

- `duns` — path parameter
- `transactionTimestamp` — **required** query parameter

Returns `originalResponse`: the exact payload D&B served for that D-U-N-S at
that moment. This is the operation to reach for when you must prove what a
credit or risk decision was based on. Record the
`transactionDetail.transactionTimestamp` from every decision-time call so you
can replay it later — without that timestamp this operation is unusable.

`404` means there is no audit record for that D-U-N-S and timestamp pair.

## Rules

- Entitlement is contractual: `401` with `errorCode` `00004` can mean "your
  contract does not include this product" even when the token is valid.
- No idempotency key; these are all reads, so retries are safe in practice.
- Log `transactionDetail.transactionID` and `x-request-id` on failure.
