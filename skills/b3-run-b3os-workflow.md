---
name: Create, publish and run a B3OS workflow
description: >-
  Operate the B3OS Workflow API to build a cross-chain automation from actions
  and triggers, validate it, publish it, run it, and watch its runs.
api: openapi/b3-b3os-workflow-openapi.json
operations:
  - GET /v1/actions
  - GET /v1/actions/{type}
  - POST /v1/actions/{type}/test
  - GET /v1/triggers
  - POST /v1/workflows/validate
  - POST /v1/workflows
  - POST /v1/workflows/{id}/publish
  - POST /v1/workflows/{id}/run
  - GET /v1/workflows/{id}/runs
  - GET /v1/runs
generated: '2026-07-22'
method: generated
---

# Create, publish and run a B3OS workflow

Base URL: `https://api.b3os.org/v1`. Auth: API-key header (request a key via
https://docs.b3.fun/sdk/api-key-request); see `authentication/b3-authentication.yml`.
Runs consume Compute Units (CU) — an HTTP `402` means insufficient CU balance
(the x402 payment surface).

## Steps

1. **Discover building blocks.** `GET /v1/actions` lists available action types
   (filter by tags); `GET /v1/actions/{type}` returns one definition.
   `GET /v1/triggers` lists available workflow triggers.
2. **Test an action first.** `POST /v1/actions/{type}/test` runs an action with a
   custom payload without creating a workflow. Read-only checks can use
   `POST /v1/action-proxy/{type}/query` (no CU charge).
3. **Validate before saving.** `POST /v1/workflows/validate` checks a workflow
   definition without persisting it — fix `400` validation errors here.
4. **Create the workflow.** `POST /v1/workflows` with the validated definition.
   Content changes on `PUT /v1/workflows/{id}` create a new draft version.
5. **Publish.** `POST /v1/workflows/{id}/publish` promotes the draft (validates
   cron intervals). Pause/resume with `POST /v1/workflows/{id}/pause` and
   `POST /v1/workflows/{id}/resume`.
6. **Run and observe.** `POST /v1/workflows/{id}/run` (optional payload) starts a
   run; `GET /v1/workflows/{id}/runs` and `GET /v1/runs` list runs;
   `GET /v1/workflows/{id}/runs/stream` streams run events.

## Rules

- Pagination is offset/limit on list endpoints (`conventions/b3-conventions.yml`).
- No idempotency-key contract exists — do not blind-retry `POST /v1/workflows/{id}/run`;
  check `GET /v1/runs` for the run you just started before retrying.
- Errors are plain JSON with HTTP status codes, not RFC 9457
  (`errors/b3-problem-types.yml`); `402` = CU balance, top up before retrying.
- Test on testnet first: claim testnet tokens via `POST /v1/faucet/claim`
  (`sandbox/b3-sandbox.yml`).
