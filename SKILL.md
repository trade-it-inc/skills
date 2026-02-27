---
name: trade-it
description: Implement Trade It embedded SDK integrations in a web app. Use when asked to add Trade It connect or trade modals, wire a server route that calls Trade It POST /api/session/url, or integrate @trade-it/react into an existing React codebase.
---

# Trade It SDK Integration

## Overview

Use this skill to embed Trade It into a partner application to create an OOTB stock, crypto, or options trading experience.

Assume the user is (or is becoming) a Trade It partner and stores per-user Trade It access tokens server-side. If OAuth token storage is not implemented yet, scaffold the integration points and leave clear TODOs instead of inventing fake auth.

## Workflow

1. Detect the stack.
- Confirm the app has a React frontend (or a place where React can render the Trade It modal).
- Find the server-side boundary where the current user's stored Trade It token can be accessed.
- If the app is not React-based, stop and recommend either Trade Links or a custom wrapper around `@trade-it/core`.

2. Add a partner server route for session URLs.
- Create a server-side route in the partner app that accepts a local request for `connect` or `trade`.
- Read the current user's stored Trade It access token server-side.
- Call Trade It `POST <TRADE_IT_API_URL>/api/session/url` with `Authorization: Bearer <user trade it access token>`.
- Use request bodies:
  - `{ "target": "connect" }`
  - `{ "target": "connect", "brokerageId": <number> }`
  - `{ "target": "trade" }`
- Return the Trade It JSON response unchanged when possible.
- Add a `TRADE_IT_API_URL` env var if the app does not already have one. Default to `https://api.tradeit.app` if unavailable.

3. Add the client-side SDK integration.
- Install `@trade-it/react`.
- Render one shared `TradeItModal`.
- For connect flows:
  - fetch a connect URL from the partner server
  - call `tradeIt.openConnect({ launch: { mode: 'connect', url } })`
- For trade flows:
  - fetch a trade URL template from the partner server
  - call `tradeIt.openTrade({ launch: { mode: 'trade', url, config } })`

4. Map trade configuration correctly.
- Use SDK enums instead of hard-coded strings.
- Use full config property names (`tradeType`, `orderType`, `timeInForce`, `positionEffect`, etc.).
- For options or multi-leg trades, use `tradeType: TradeType.MultiLeg` and provide `legs`.

5. Validate the finished integration.
- Connect modal opens from the partner UI.
- Trade modal opens in the requested mode (`simple` or options/multi-leg).
- Missing or expired sessions can be recovered by requesting a fresh session URL.

## Implementation Rules

- Never call Trade It session URL endpoints directly from browser code.
- Never expose `client_secret` in client-side code.
- Prefer returning the full `url` from the partner server rather than rebuilding URLs client-side.
- Keep route names and patterns idiomatic to the partner's stack.
- Reuse the partner's existing modal/state/fetch patterns where possible.
- If there is no trusted server-side source for the current user's Trade It token, stop and document that as the blocker.

## Required Deliverables

When using this skill, produce:

- the server route or handler that proxies session URL requests
- client-side modal wiring for connect and/or trade
- any required env var additions
- a short note showing where the user's Trade It token must come from

## References

- Read `references/api-contract.md` for request/response shapes, session URL flows, and SDK launch patterns.
- Read `references/trade-config.md` for `launch.config` fields, enums, and trade examples.
