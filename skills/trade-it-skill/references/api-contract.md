# Trade It Portal: Connection mode and trading

Use Connection mode after a user has authorized the app, either through [standard OAuth](oauth.md) or the portal's [Onboarding mode](onboarding-portal.md). One Trade It Portal handles both the first connection and later connection management; partners should not repeat onboarding for each connection. The trade portal has its own session URL endpoint below.

Read the current [connection guide](https://docs.tradeit.app/sdk/connect), [trade guide](https://docs.tradeit.app/sdk/trades), and installed SDK declarations before implementing.

## Connection mode

Require the partner application's session, load that user's saved Trade It access token, and refresh it when needed. Call the same endpoint used for Onboarding mode, with Bearer authentication and no `oauth_options`. No extra mode parameter is needed. Request a short-lived URL from the partner server, not browser code:

```http
POST https://api.tradeit.app/api/portal
Authorization: Bearer <the current user's Trade It access token>
Content-Type: application/json

{
  "connect_options": {}
}
```

Connection mode accepts any supported valid Bearer token with `brokerage:write`, including partner or public-client OAuth tokens, direct sign-in tokens, and API keys. It is not restricted to registered partner clients. The generated portal token stays within the original credential's permissions. Multi-user partner apps should still use each user's saved OAuth grant.

The response contains `url` and `expiresAt`; it does not issue another OAuth grant or emit `onAuthorization`. Invalid or expired tokens return `401`; missing permissions return `403`. Neither failure silently restarts onboarding.

```json
{
  "url": "https://tradeit.app/connect?ti_token=ti:...&embedded=1",
  "expiresAt": "2026-10-15T19:35:12.000Z"
}
```

## Connection options

The same `connect_options` works with Basic Onboarding mode and Bearer Connection mode. All API parameter names use snake_case:

| Picker | Body settings |
| --- | --- |
| Full list | Omit `connect_options` or send `{ "connect_options": {} }` |
| Filtered list | Set `connect_options.brokerage_filter` |
| One targeted brokerage | Set `connect_options.brokerage_code` to skip the picker |

For a customized picker, use:

```json
{
  "connect_options": {
    "brokerage_filter": {
      "supports": ["options"],
      "exclude": ["tastytrade"]
    }
  }
}
```

`supports` requires every listed capability. `include` is an optional brokerage-code allowlist; `exclude` takes precedence. Do not combine a direct code with a filter. Inactive brokerages are omitted. If no active brokerage matches, the restriction is omitted and the full picker appears; filters are not security boundaries. Retrieve current codes/capabilities from [Brokerages](https://docs.tradeit.app/brokerages).

For a targeted brokerage, use `{ "connect_options": { "brokerage_code": "charles_schwab" } }`. Do not add `target`, `brokerageCode`, or `brokerageFilter` to a `/api/portal` request.

## Trade session URLs

For the embedded trade UI, keep using the separate session endpoint:

```http
POST https://api.tradeit.app/api/session/url
Authorization: Bearer <the current user's Trade It access token>
Content-Type: application/json

{
  "target": "trade"
}
```

Responses contain `url`, `expiresAt`, `feature: "trade"`, and `placeholder: "[ticker]"`. Use the returned URL unchanged; the SDK fills its ticker from configuration. Do not substitute a raw token or construct a different path. This endpoint is for the trade UI; use `/api/portal` for the unified onboarding/connection integration.

URLs expire after 30 minutes and depend on the underlying authorization. Request them immediately before launch rather than caching them for a future visit. Treat the URL as a short-lived credential and exclude it from analytics/logs. Check the current permission requirements in the relevant guide.

## React SDK

Install `@trade-it/react` and render one shared `TradeItModal`. `@trade-it/core` supplies framework-independent URL/types/event primitives; it does not call authenticated API endpoints.

This component receives a freshly issued URL from the app's backend:

```tsx
import { TradeItModal, useTradeIt } from '@trade-it/react';

export function ConnectBrokerage({ url }: { url: string }) {
  const tradeIt = useTradeIt();

  return (
    <>
      <button type="button" onClick={() => tradeIt.openConnect({ launch: { mode: 'connect', url } })}>
        Connect a brokerage
      </button>
      {tradeIt.modalProps && <TradeItModal {...tradeIt.modalProps} />}
    </>
  );
}
```

For a trade, use `tradeIt.openTrade({ launch: { mode: 'trade', url, config } })`, where `config` satisfies `TradeItTradeConfig`. Read [trade-config.md](trade-config.md) for simple, exit-trigger, and options examples. Do not manually serialize SDK configuration into compact URL parameters.

## Events and completion

Use exported `EmbedEventType` values rather than hardcoded event strings. Generic messages contain `source: 'tradeit'`, `version: 1`, `type`, and optional `payload`.

| SDK enum | Use |
| --- | --- |
| `Ready` | Embedded UI readiness; not proof that onboarding or trading completed |
| `BrokerageConnected` | Brokerage connection event; fetch current accounts when needed |
| `TradeSubmitted`, `TradeExecuted` | Embedded trade lifecycle; use current API state to confirm order outcome |
| `LoginRequired` | Authorization is missing/expired; recover through the appropriate auth path |
| `Error` | Embedded error; read its actual payload and surface a useful action |
| `CloseRequested` | UI asks to close; closing alone is not a successful connection or fill |
| `Authorization` | Handled through async `onAuthorization`, not generic `onEvent` |
| `AuthorizationResult` | SDK acknowledgment to the portal; do not emulate it to bypass storage |

The SDK verifies the expected iframe origin and source window. If writing a host without the React SDK, preserve both checks and follow the published event protocol; do not accept a message solely because its `type` matches.

Portal automatic closure waits for `onAuthorization` to resolve after token storage. Brokerage connection starts in the same frame while that callback runs. User-triggered close/cancellation is separate from an OAuth grant or order result. Use [webhooks](webhooks.md) and authenticated reads for durable application state, not solely a browser listener.
