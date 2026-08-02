---
name: Manage Tweed platform webhooks
description: Create, update, activate, and verify webhook subscriptions on the Tweed API (V2).
api: openapi/tweed-api-v2-openapi-original.json
operations: [getPlatformWebhooks, addPlatformWebhook, updatePlatformWebhook, setActivePlatformWebhook, deletePlatformWebhook]
generated: '2026-07-21'
method: generated
---

# Manage Tweed platform webhooks

Base URL: `https://api-v2.prod.paytweed.com`, bearer-authenticated.

## Steps

1. **Inspect existing subscriptions** — `GET /v1/platform-webhooks`
   (`getPlatformWebhooks`).
2. **Create one** — `POST /v1/platform-webhooks` (`addPlatformWebhook`) with
   `url` (HTTPS-only, publicly reachable), `events` (at least one, e.g.
   `CHECKOUT_STATUS_UPDATED`), and a `secret` signing key stored securely on
   your side.
3. **Rotate or repoint** — `PUT /v1/platform-webhooks/{id}`
   (`updatePlatformWebhook`) to change `url`, `events`, or `secret`.
4. **Pause/resume without deleting** — `POST /v1/platform-webhooks/{id}/set-active`
   (`setActivePlatformWebhook`) with `isActive: true|false`.
5. **Remove** — `DELETE /v1/platform-webhooks/{id}` (`deletePlatformWebhook`).

## Rules

- Verify every delivery: compute `sha256=` HMAC of the raw body with your
  secret and compare (timing-safe) against `X-Hub-Signature-256`.
- Deduplicate on `X-Tweed-Delivery`; route multi-endpoint setups on
  `X-Tweed-Hook-ID`.
