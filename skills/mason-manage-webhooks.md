---
name: Subscribe to Mason events with webhooks
description: Register, list and delete webhook subscriptions so an integration is notified of Mason changes/updates.
api: openapi/mason-webhooks-openapi.json
operations:
  - Register_a_Webhook_v1_webhook_post
  - Get_Webhook_v1_webhook_get
  - Delete_Webhook_v1_webhook__id__delete
---

# Subscribe to Mason events with webhooks

Mason delivers change/update notifications to an HTTP callback you register.
Base host: `https://api.getmason.io`. Requires an OAuth2 bearer token.

## Steps

1. **Register a webhook.** Call `Register_a_Webhook_v1_webhook_post`
   (`POST /v1/webhook`) with a `WebhookRegistrationModel`: an `event_type` and a
   `channel_details` object. `channel_details.url` is required; you may also set
   `method`, `headers`, `source_fields`, `success_codes`, `fallback` and `timeout`.
   Mason POSTs to your `url` when the subscribed event fires.
2. **List subscriptions.** Call `Get_Webhook_v1_webhook_get` (`GET /v1/webhook`),
   optionally filtering by `id` or `event_type`.
3. **Delete a subscription.** Call `Delete_Webhook_v1_webhook__id__delete`
   (`DELETE /v1/webhook/{id}`).

## Rules

- Return one of your `success_codes` from your endpoint so Mason treats delivery
  as successful; otherwise the `fallback` channel is used.
- `401` = bad/expired bearer token; `422` = invalid registration payload
  (see errors/mason-problem-types.yml).
- The public reference does not enumerate `event_type` values — coordinate the
  event name with your Mason integration. See asyncapi/mason-webhooks.yml.
