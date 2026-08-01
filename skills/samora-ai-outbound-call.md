---
name: Place and track an outbound Samora AI voice call
description: Trigger a single outbound voice-agent call and poll its status until it finishes, then retrieve the transcript and recording.
api: openapi/samora-ai-openapi.yml
operations: [triggerCall, getCall]
---

# Place and track an outbound Samora AI call

Use this to place one outbound call with a Samora AI voice agent and follow it to completion.

## Auth
All requests are server-to-server. Send the organization API key in the `X-API-Key`
header. Never expose the key in browser or mobile clients. Base URL `https://api.samora.ai`.

## Steps
1. **Trigger the call** — `triggerCall` (`POST /v1/call/trigger`) with body `{ "agent_id": "<uuid>", "to_number": "<E.164>" }`. The number must be E.164 (e.g. `+919876543210`). The response returns `call_id` and `status`.
2. **Poll status** — `getCall` (`GET /v1/call/{call_id}`) until `status` reaches a terminal value: `CALL_FINISHED`, `UNANSWERED`, or `REJECTED`. In-flight values are `PENDING`, `TRIGGERED`, `ONGOING`.
3. **Collect artifacts** — on `CALL_FINISHED`, read `transcript_url` and `recording_url` from the call. These are presigned URLs valid for 60 minutes — download promptly.

## Rules
- On `401`, the API key is missing/invalid. On `429`, back off and retry.
- Prefer subscribing a webhook (see the webhook skill) over tight polling for production volume.
