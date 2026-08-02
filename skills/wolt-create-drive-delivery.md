---
name: Create a Wolt Drive delivery
description: Price a Wolt Drive delivery with a shipment promise and create the delivery from it.
api: https://developer.wolt.com/docs/wolt-drive/endpoints
operations: [create_shipment_promise, create_delivery, cancel_delivery]
---

# Create a Wolt Drive delivery

Use this to get a binding price/ETA and dispatch a last-mile delivery.

## Auth
- Send `Authorization: Bearer <Merchant Key>` on every request.
- Base URL: production `https://daas-public-api.wolt.com`, development `https://daas-public-api.development.dev.woltapi.com`.

## Steps
1. Get a promise: `POST /v1/venues/{venue_id}/shipment-promises` with dropoff street/city/post_code/lat/lon, `min_preparation_time_minutes`, and optional `scheduled_dropoff_time`. Read the returned `shipment_promise_id` (promises expire).
2. Create the delivery: `POST /v1/venues/{venue_id}/deliveries` with `shipment_promise_id`, dropoff/recipient, `parcels`, optional `cash` and `handshake_delivery`. Read `wolt_order_reference_id`.
3. If needed before courier acceptance: `PATCH /order/{wolt_order_reference_id}/status/cancel`.

## Rules
- Pass a unique `merchant_order_reference_id` — reusing one returns `DUPLICATE_ORDER` (this is the idempotency key).
- Handle `SHIPMENT_PROMISE_NOT_FOUND` by generating a new promise; handle `DROPOFF_OUTSIDE_OF_DELIVERY_AREA` before creating.
- Subscribe to Drive webhooks (order.received … order.delivered), verifying the HS256 JWT `token` with your client_secret.
