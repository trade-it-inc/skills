---
name: trade-it-skill
description: Build and troubleshoot products using Trade It's SDK, REST API, OAuth, portals, webhooks, Trade Links, or MCP client integrations. Use for developer integration work, not personal portfolio lookup or live trading through an already-connected assistant.
---

# Trade It Developer

Connect a product or AI client to users' brokerage accounts. This developer skill does not require access to the developer's own brokerage account. For personal account lookup or trading, use the Trade It MCP tools and their workflow guidance instead. For partner integrations, start with the Trade It Portal in Onboarding mode for consent, sign-in, and the first connection. Use the same portal in Connection mode for later connections. Standard redirect OAuth remains available for a custom authorization experience. Read only the references needed for the requested work.

## Start with current contracts

Inspect the application's framework, authentication, token storage, existing integration, and installed SDK version. Retrieve the relevant first-party docs before implementing endpoints, configuration, event handling, or brokerage behavior; do not rely on remembered schemas.

| Resource | Use |
| --- | --- |
| [Platform integration](https://docs.tradeit.app/sdk) | Partner setup and the portal integration lifecycle |
| [Onboarding mode](https://docs.tradeit.app/sdk/onboarding) | First-time portal creation, sign-in, and authorization events |
| [Connection mode](https://docs.tradeit.app/sdk/connect) | Subsequent connections using an existing token |
| [OAuth metadata](https://tradeit.app/.well-known/oauth-authorization-server) | Issuer and standard OAuth authorization/token endpoints |
| [REST API](https://docs.tradeit.app/api) | Operation inputs, result models, permissions, and errors |
| [OpenAPI](https://tradeit.app/openapi.json) | Machine-readable API contract, when accessible |
| [Brokerages](https://docs.tradeit.app/brokerages) | Current codes and supported capabilities |
| [Agent resources](https://tradeit.app/llm-resources) | Discovery and additional developer resources |

Use installed `@trade-it/react` / `@trade-it/core` declarations for the pinned SDK version. If docs describe a feature that is absent from the installed release or environment, identify the required upgrade or deployment. Do not claim an unreleased feature works with an older package. If a source is inaccessible, use another first-party source and state what remains unverified.

## Choose an integration

| User's goal | Choose | Read |
| --- | --- | --- |
| Verify users, get consent, and connect brokerages inside the app | **Trade It Portal in Onboarding mode**, recommended for partners | [Onboarding](references/onboarding-portal.md), [OAuth](references/oauth.md) |
| Control authentication separately from brokerage connection | Standard redirect OAuth, then connection portal | [OAuth](references/oauth.md), [Connection and trade portals](references/api-contract.md) |
| Add or reconnect a brokerage for an already-authorized user | Same portal in Connection mode using the saved access token | [Connection and trade portals](references/api-contract.md) |
| Let Trade It render order entry and review | React trade portal | [Connection and trade portals](references/api-contract.md), [Trade configuration](references/trade-config.md) |
| Read accounts/holdings or build a custom trading UI | Server-side REST API | [Backend API](references/backend-api.md), [OAuth](references/oauth.md) |
| Receive account connection and final order updates | Signed webhooks | [Webhooks](references/webhooks.md) |
| Share a prefilled trade without a partner backend | Trade Links | [Trade Links and MCP](references/other-integrations.md) |
| Build or configure an AI client with brokerage tools | Existing Trade It MCP server | [Trade Links and MCP](references/other-integrations.md) |
| Diagnose OTP, framing, callback, token, or connection failures | Follow the failed boundary | [Troubleshooting](references/troubleshooting.md) |

These surfaces can be combined. For example, onboard with the portal, read holdings through the REST API, and receive final order updates through webhooks. `@trade-it/core` provides embedded URL/types/event primitives; it is not an authenticated REST client.

## Identity and authorization

- Brokerage connections belong to one portable Trade It user. A partner receives an OAuth grant, not ownership of those connections.
- Partner clients use confidential credentials and PKCE `S256`. Bind each attempt to the authenticated user in the partner application.
- Keep client secrets, OAuth access/refresh tokens, API keys, and webhook signing secrets on the partner server. Only short-lived portal/session URLs and authorization codes cross into the browser.
- Create both portal modes through `POST https://api.tradeit.app/api/portal`. Basic client credentials plus required `oauth_options` select Onboarding mode; a valid Bearer token without `oauth_options` selects Connection mode. Optional `connect_options` works in both. There is no separate mode field or portal discovery extension.
- `oauth_options.email_hint` is optional: supply it to start email OTP after consent, or omit it to show Google, Apple, X, Discord, and email sign-in. Users can switch methods or correct their address. The hint is not verified identity; do not impersonate users or redeem identity-verification links for them.
- Onboarding permissions follow the registered partner's read-only or full ClientType. Connection mode accepts any supported valid token with `brokerage:write`, including OAuth access tokens, direct sign-in tokens, and API keys. Generated portal tokens cannot exceed the original credential's permissions. Do not add undocumented scope fields or use picker filters as authorization restrictions.
- Standard OAuth and portal authorization share code exchange and token storage. The portal delivers `{ code, state, issuer }` through async `onAuthorization`; credentials are not forwarded to generic `onEvent`.
- Resolve `onAuthorization` after token storage succeeds. Brokerage connection starts in the same iframe while exchange runs; automatic closure waits for the callback.
- Revocation prevents refresh, but an existing access token can remain valid until its expiration. Do not promise immediate invalidation.

## Trading invariants

Prefer a draft-first experience. Show the returned order and require explicit user approval before executing that exact order. Check the returned status: an account configured for automatic execution can submit during creation, so creation is not universally a harmless preview. Do not use that configuration for an unapproved draft workflow.

Keep `create_trade` and `create_options_trade` distinct. Do not substitute SDK camelCase fields for REST snake_case parameters. Use SDK enums and typed configuration for embedded trades. Treat submitted, filled, canceled, and failed states distinctly; neither a successful HTTP response nor a closed modal proves a fill.

## Implement and hand off

Fit the integration into the existing application's routes, sessions, storage, and UI. Build the requested path rather than every available surface. Implement a server boundary for authenticated API calls and token refresh before adding browser launch code. Fetch portal/session URLs immediately before opening them and use the returned URL directly.

Return the changed files, required environment/client settings, and what was checked. Identify remaining credentials or deployment prerequisites without inventing values. Validate the affected behavior in proportion to the change and the user's testing instructions; do not place live trades merely to verify wiring.
