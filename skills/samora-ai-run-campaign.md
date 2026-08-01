---
name: Run a Samora AI outbound calling campaign
description: Create an outbound campaign, add recipients, start it, and monitor its calls, respecting the campaign state machine.
api: openapi/samora-ai-openapi.yml
operations: [createCampaign, addScheduledCalls, startCampaign, listCampaignCalls, stopCampaign, cancelCampaign, getCampaign]
---

# Run a Samora AI outbound calling campaign

Use this to launch and manage a bulk outbound calling campaign for an agent.

## Auth
Send the organization API key in the `X-API-Key` header. Base URL `https://api.samora.ai`.

## Steps
1. **Create** — `createCampaign` (`POST /v1/external/campaigns/{agent_id}`) with `campaign_name` (<= 120 chars) and optional `retry_config` (`max_retries` 1-5, `retry_delay_minutes` 1-1440, `retry_on` any of `UNANSWERED`, `REJECTED`, `VOICEMAIL`, `FAILED`). New campaigns start in `DRAFT`.
2. **Add recipients** — `addScheduledCalls` (`POST .../{campaign_id}/scheduled-calls`) — up to 5000 numbers per request, up to 4 KiB `call_variables` per number. Allowed only while the campaign is `DRAFT` or `PAUSED`.
3. **Start** — `startCampaign` (`POST .../{campaign_id}/start`). Allowed from `DRAFT` or `PAUSED`; moves toward `IN_PROGRESS`.
4. **Monitor** — `listCampaignCalls` (`GET .../{campaign_id}/calls`, `page_size` <= 100) and `getCampaign` for status. Campaign statuses: `DRAFT`, `SCHEDULED`, `IN_PROGRESS`, `PAUSED`, `FINISHED`, `FAILED`, `CANCELLED`.
5. **Control** — `stopCampaign` only from `IN_PROGRESS`; `cancelCampaign` unless already terminal.

## Rules
- Respect the state machine: adding recipients or starting outside `DRAFT`/`PAUSED`, or stopping outside `IN_PROGRESS`, returns `409`.
- There is no idempotency key — avoid duplicate create/add retries on network errors; re-fetch with `getCampaign`/`listCampaignCalls` to reconcile before retrying.
