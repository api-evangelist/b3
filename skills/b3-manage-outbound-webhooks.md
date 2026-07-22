---
name: Manage B3OS webhook deliveries
description: >-
  Operate the B3OS outbound/execution webhook surface — verify signatures with
  the org secret, list and inspect deliveries, and replay or retry failures.
api: openapi/b3-b3os-workflow-openapi.json
operations:
  - GET /organizations/{orgId}/webhook-secret
  - GET /v1/outbound-webhook/deliveries
  - GET /v1/outbound-webhook/deliveries/{id}
  - POST /v1/outbound-webhook/deliveries/{id}/replay
  - POST /v1/outbound-webhook/deliveries/{id}/retry
  - GET /v1/execution-webhooks
  - POST /v1/execution-webhooks
  - POST /v1/execution-webhooks/{id}/regenerate-secret
generated: '2026-07-22'
method: generated
---

# Manage B3OS webhook deliveries

Base URL: `https://api.b3os.org/v1` (the webhook-secret endpoint sits outside the
`/v1` namespace at `GET /organizations/{orgId}/webhook-secret`). Auth: API key.
Full catalog: `asyncapi/b3-b3os-webhooks.yml`.

## Steps

1. **Get the signing secret.** `GET /organizations/{orgId}/webhook-secret`
   returns the per-organization secret used to sign outbound payloads — verify
   every inbound delivery against it before trusting the payload.
2. **Register org-level endpoints.** `POST /v1/execution-webhooks` creates an
   execution-webhook endpoint; enable/disable with
   `POST /v1/execution-webhooks/{id}/enable` / `.../disable`; rotate its signing
   secret with `POST /v1/execution-webhooks/{id}/regenerate-secret`.
3. **Audit deliveries.** `GET /v1/outbound-webhook/deliveries` and
   `GET /v1/execution-webhooks/deliveries` list deliveries;
   `GET /v1/outbound-webhook/deliveries/{id}` shows one delivery's status,
   attempts and payload.
4. **Recover failures.** `POST /v1/outbound-webhook/deliveries/{id}/retry`
   requeues a failed delivery; `POST /v1/outbound-webhook/deliveries/{id}/replay`
   creates a new delivery from the same event (also available on
   execution-webhook deliveries).

## Rules

- Replay creates a **new** delivery; retry requeues the same one — pick retry for
  transient endpoint failures, replay after fixing a consumer bug.
- Rotating a secret invalidates in-flight signature checks — deploy the new
  secret to consumers before regenerating.
- Errors are plain JSON with HTTP status codes (`errors/b3-problem-types.yml`).
