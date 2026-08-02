---
name: Fulfill a Wolt Marketplace order
description: Receive a Wolt Marketplace order webhook and drive it through accept, ready, and delivered.
api: https://developer.wolt.com/docs/api
operations: [accept_order, mark_order_ready, mark_order_delivered]
---

# Fulfill a Wolt Marketplace order

Use this to move a marketplace order from received to delivered.

## Auth
- Obtain a bearer JWT via OAuth 2.0 from `https://integrations-authentication-service.wolt.com/oauth2/token`
  (HTTP Basic with client_id:client_secret, `grant_type=authorization_code`, scope `offline`).
- Send `Authorization: Bearer {access_token}` on every request. Access tokens last one hour; refresh with the single-use refresh token.

## Steps
1. Receive the `CREATED` webhook (verify the `WOLT-SIGNATURE` HMAC-SHA256 header against the raw body with your client secret). Read `order.id` and `order.resource_url`.
2. Accept: `PUT /orders/{orderId}/accept`.
3. When prepared: `PUT /orders/{orderId}/ready`.
4. When handed to the courier / delivered: `PUT /orders/{orderId}/delivered`.

## Rules
- Do not retry 400/401/404/422 responses; wait before retrying 429; retry 5xx with exponential backoff.
- Error envelope is `{ error_code, reason, details }` — see errors/wolt-problem-types.yml.
