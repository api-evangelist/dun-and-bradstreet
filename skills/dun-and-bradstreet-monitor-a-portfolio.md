---
name: Monitor a portfolio of companies for change
description: >-
  Register a monitoring portfolio, add and remove D-U-N-S Numbers, and drain
  field-level change notifications. Direct+ Monitoring is pull-only — there is
  no webhook, so an agent must poll on a schedule.
api: openapi/dun-and-bradstreet-monitoring-api-openapi.yml
apis:
  - openapi/dun-and-bradstreet-authentication-api-openapi.yml
  - openapi/dun-and-bradstreet-monitoring-api-openapi.yml
operations:
  - generateAccessToken
  - createMonitoringRegistration
  - addDunsToRegistration
  - removeDunsFromRegistration
  - pullMonitoringNotifications
base_url: https://plus.dnb.com
generated: '2026-08-13'
method: generated
source: openapi/_original/dnb-direct-plus-openapi-original.yml
---

# Monitor a portfolio of companies for change

## Before you start

Get a bearer token with `generateAccessToken` (`POST /v3/token`, HTTP Basic
with consumer key/secret, body `{"grant_type":"client_credentials"}`). Send it
as `Authorization: Bearer <token>` on everything below.

You need D-U-N-S Numbers already — resolve them first with the
*resolve-and-enrich-company* skill.

## Step 1 — create the registration (`createMonitoringRegistration`)

`POST /v1/monitoring/registrations`

Body:

- `registrationReference` — **required**, and it is *your* chosen identifier.
  Every later call in this flow uses it as a path segment, so pick something
  stable and store it. There is no server-assigned id to fall back on.
- `description` — free text
- `blockIDs[]` — which Data Blocks to watch. You only get notified about
  fields inside the blocks you register, so this choice defines the whole
  signal.
- `deliveryMethod` — `SFTP` or `API_PULL`. Choose `API_PULL` for an agent
  workflow; `SFTP` drops files.

Returns `201` with `registrationReference`, `status`, `createdTimestamp`.

## Step 2 — add companies (`addDunsToRegistration`)

`POST /v1/monitoring/registrations/{registrationReference}/duns`

Body: `{"dunsList": ["123456789", ...]}` — each entry must match `^[0-9]{9}$`.

The response is a reconciliation, not a bare success: read `addedQuantity`
and, importantly, `rejectedDuns[]`, where each entry carries a `duns` and a
`reason`. **Always inspect `rejectedDuns[]`** — a partial success looks like a
success if you only check the status code.

## Step 3 — remove companies (`removeDunsFromRegistration`)

`DELETE /v1/monitoring/registrations/{registrationReference}/duns`

Same body shape. Read `removedQuantity` and `rejectedDuns[]`.

## Step 4 — drain notifications (`pullMonitoringNotifications`)

`GET /v1/monitoring/registrations/{registrationReference}/notifications`

- `maximumQuantity` — optional cap per pull.

Each entry in `notifications[]` is a single field-level change:

- `duns` — which company changed
- `changeTimestamp` — when
- `blockID` + `elementId` — exactly which attribute
- `previousValue` / `currentValue` — before and after

**This is a queue, not a page.** Pulling removes the notifications, so there
is no `pageNumber` and no cursor. Keep pulling until you get an empty batch,
and persist what you consumed before the next pull — a dropped batch is
gone.

## Polling, not pushing

There is no webhook, no SSE and no streaming on Direct+. `deliveryMethod` is
`SFTP` or `API_PULL` and that is the whole menu. Schedule the pull; do not
wait to be woken. (D&B's only public webhook is on its operational status
dashboard, which is a different thing entirely — see
`asyncapi/dun-and-bradstreet-status-webhooks.yml`.)

## Rules

- **No idempotency key.** `addDunsToRegistration` and
  `removeDunsFromRegistration` are set operations, so replaying them is
  usually harmless in effect, but D&B does not guarantee it. Never assume
  replay safety for `createMonitoringRegistration`.
- Portfolio size is capped by contract. Exceeding it fails on add.
- On `403`, stop and escalate to the account representative — it is an
  entitlement wall, not a transient error.
- Log `transactionDetail.transactionID` and the `x-request-id` header on every
  failure.
