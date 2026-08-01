---
name: Subscribe to Samora AI call webhooks
description: Discover event types, create a signed webhook subscription for call events, and verify delivered payloads.
api: openapi/samora-ai-openapi.yml
operations: [listWebhookEvents, createWebhook, getWebhook, listWebhooks, updateWebhook, deleteWebhook]
---

# Subscribe to Samora AI call webhooks

Use this to receive real-time call events instead of polling.

## Auth
Send the organization API key in the `X-API-Key` header. Base URL `https://api.samora.ai`.

## Steps
1. **Discover events** — `listWebhookEvents` (`GET /v2/webhooks/events`). Available events: `CALL_STARTED`, `CALL_FINISHED`, `CALL_FAILED`.
2. **Create subscription** — `createWebhook` (`POST /v2/webhooks`) with your HTTPS `url`, the `events` you want, and optional `data_options`. The response includes a **signing secret** — store it securely.
3. **Verify deliveries** — for each incoming POST to your endpoint, verify the signature using the signing secret before trusting the payload. On `CALL_FINISHED`, the payload references presigned transcript/recording URLs (valid 60 minutes).
4. **Manage** — `listWebhooks`, `getWebhook`, `updateWebhook`, `deleteWebhook` (`/v2/webhooks/{webhook_id}`) to inspect, change, or remove subscriptions.

## Rules
- Always verify the signature; reject unsigned or mismatched payloads.
- Return 2xx quickly and process asynchronously so deliveries are not retried unnecessarily.
