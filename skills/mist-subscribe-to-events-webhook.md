---
name: Subscribe to Mist events via webhook
description: Create an org- or site-level webhook subscription for Mist event topics, test it, and inspect delivery history.
api: openapi/mist-openapi-original.json
operations: [listWebhookTopics, createOrgWebhook, listOrgWebhooks, pingOrgWebhook, searchOrgWebhooksDeliveries]
---

# Subscribe to Mist events via webhook

Stream real-time Mist telemetry (audits, alarms, device events, location/occupancy) to your endpoint.

## Auth
- `Authorization: Token {apitoken}` on every request. Use the regional host matching your portal.

## Steps
1. **Discover topics** — `listWebhookTopics` (`GET /api/v1/const/webhook_topics`) for the valid topic strings (e.g. `audits`, `alarms`, `device-events`, `occupancy`, `location`).
2. **Create the subscription** — `createOrgWebhook` (`POST /api/v1/orgs/{org_id}/webhooks`) with `url`, `topics[]`, and a `secret` for signature verification. (Use `createSiteWebhook` for site scope.)
3. **List & confirm** — `listOrgWebhooks` (`GET /api/v1/orgs/{org_id}/webhooks`) to capture the `webhook_id`.
4. **Test delivery** — `pingOrgWebhook` (`POST /api/v1/orgs/{org_id}/webhooks/{webhook_id}/ping`) to send a test event to your endpoint.
5. **Audit deliveries** — `searchOrgWebhooksDeliveries` (`GET /api/v1/orgs/{org_id}/webhooks/{webhook_id}/events/search`) to inspect recent delivery attempts and debug failures.

## Rules
- Mist POSTs JSON event batches; verify the payload signature using the `secret` you set.
- See `asyncapi/mist-webhooks.yml` for the topic catalog and streaming (WebSocket) alternative.
- Handle at-least-once delivery — dedupe on event id; a failed HTTP 2xx from your endpoint triggers Mist retries.
