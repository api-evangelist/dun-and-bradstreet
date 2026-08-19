---
name: Batch match and enrich a file of companies
description: >-
  Submit a file of company records to Direct+, poll it to completion, and
  download the matched and enriched results. Use this instead of looping
  cleanseMatch when you have more records than your rate ceiling allows.
api: openapi/dun-and-bradstreet-data-file-api-openapi.yml
apis:
  - openapi/dun-and-bradstreet-authentication-api-openapi.yml
  - openapi/dun-and-bradstreet-data-file-api-openapi.yml
operations:
  - generateAccessToken
  - submitBatchFile
  - getBatchFileStatus
  - downloadBatchFileResults
base_url: https://plus.dnb.com
generated: '2026-08-13'
method: generated
source: openapi/_original/dnb-direct-plus-openapi-original.yml
---

# Batch match and enrich a file of companies

## When to use this

Use the batch surface when the record count would blow through the per-token
concurrency and rate ceiling on `cleanseMatch`. It trades latency for volume:
submit once, poll, download.

## Step 0 — token

`generateAccessToken` (`POST /v3/token`), HTTP Basic with consumer
key/secret, body `{"grant_type":"client_credentials"}`. Send
`Authorization: Bearer <token>` on everything below.

## Step 1 — submit (`submitBatchFile`)

`POST /v1/file` — **`multipart/form-data`**, not JSON.

Parts:

- `file` — the binary payload
- `processId` — the Direct+ process identifier (match, enrich, and so on)
- `productId` — the product to run

Returns **`202 Accepted`** with `fileId`, `submissionTimestamp` and
`status: PENDING`. `202` means queued, not done. Persist `fileId`
immediately — it is the only handle you get.

## Step 2 — poll (`getBatchFileStatus`)

`GET /v1/file/{fileId}/status`

Returns `status` from the enum `PENDING | PROCESSING | COMPLETED | FAILED`,
plus `processedRecordCount` and `totalRecordCount`.

- Poll with backoff. Use the two counts to show real progress rather than
  polling blind.
- `FAILED` is terminal. Do not resubmit automatically — there is no
  idempotency key, so a blind resubmit can double-bill a contracted
  transaction entitlement. Surface the failure and let a human decide.

## Step 3 — download (`downloadBatchFileResults`)

`GET /v1/file/{fileId}/download`

Only call this once `status` is `COMPLETED`. A `404` here means the `fileId`
is unknown or the results have aged out.

## Watch the maintenance window

D&B announces Multi Process / batch platform maintenance on
<https://status.dnb.com/> rather than in a developer changelog. During those
windows batch transactions return `500` or `504`, and D&B's own guidance is
that jobs submitted inside the window **will fail** — do not submit, do not
poll status, do not pull records. Subscribe to the status RSS feed
(<https://status.dnb.com/pages/62b1a852dc9f2012ebbcef23/rss>) and gate your
scheduler on it. See `changelog/dun-and-bradstreet-changelog.yml`.

## Rules

- No idempotency contract — a resubmitted file is a second billable job.
- `403` means quota or entitlement exhausted; escalate rather than retry.
- Log `transactionDetail.transactionID` and the `x-request-id` header on
  failure.
