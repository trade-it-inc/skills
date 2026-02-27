# Trade It SDK Integration Reference

## Canonical Docs

- Overview: [https://docs.tradeit.app/sdk](https://docs.tradeit.app/sdk)
- Authentication: [https://docs.tradeit.app/sdk/authentication](https://docs.tradeit.app/sdk/authentication)
- Connect: [https://docs.tradeit.app/sdk/connect](https://docs.tradeit.app/sdk/connect)
- Trades: [https://docs.tradeit.app/sdk/trades](https://docs.tradeit.app/sdk/trades)
- Enums: [https://docs.tradeit.app/sdk/enums](https://docs.tradeit.app/sdk/enums)

## Assumptions

- The partner has a Trade It `client_id` and `client_secret`.
- The partner already has (or is implementing) OAuth with Trade It.
- The partner can read a per-user Trade It access token from server-side code.

## Session URL Endpoint

Trade It session URLs must be requested server-side.

### Connect request

```http
POST <TRADE_IT_API_URL>/api/session/url
Content-Type: application/json
Authorization: Bearer <user's trade it access token>

{
  "target": "connect"
}
```

Optional direct brokerage launch:

```json
{
  "target": "connect",
  "brokerageId": 1
}
```

### Connect response

```json
{
  "url": "https://tradeit.app/connect?token=ti:...&embedded=1",
  "expiresAt": "2026-02-27T19:35:12.000Z",
  "feature": "connect"
}
```

### Trade request

```http
POST <TRADE_IT_API_URL>/api/session/url
Content-Type: application/json
Authorization: Bearer <user's trade it access token>

{
  "target": "trade"
}
```

### Trade response

```json
{
  "url": "https://tradeit.app/t/[ticker]?token=ti:...&embedded=1",
  "expiresAt": "2026-02-27T19:35:12.000Z",
  "feature": "trade",
  "placeholder": "[ticker]"
}
```

Use the returned `url` directly in the SDK. For trade flows, the URL contains the literal `[ticker]` placeholder and the SDK replaces it from `launch.config.ticker`.

## React SDK Usage

Install:

```bash
npm i @trade-it/react
```

Connect:

```tsx
tradeIt.openConnect({
  launch: {
    mode: 'connect',
    url,
  },
});
```

Trade:

```tsx
tradeIt.openTrade({
  launch: {
    mode: 'trade',
    url,
    config: {
      tradeType: TradeType.Simple,
      ticker: 'AAPL',
      action: TradeAction.Buy,
      amount: 100,
      unit: TradeUnit.Dollars,
      orderType: OrderType.Market,
      timeInForce: TimeInForce.Day,
    },
  },
});
```

## Multi-leg Example

```tsx
tradeIt.openTrade({
  launch: {
    mode: 'trade',
    url,
    config: {
      tradeType: TradeType.MultiLeg,
      ticker: 'MSFT',
      orderType: OrderType.Market,
      timeInForce: TimeInForce.GoodTillCanceled,
      limitPrice: 1.25,
      legs: [
        {
          type: LegType.Option,
          action: TradeAction.Sell,
          positionEffect: PositionEffect.Open,
          occ: '270617C00390000',
          quantity: 5,
        },
        {
          type: LegType.Option,
          action: TradeAction.Buy,
          positionEffect: PositionEffect.Open,
          occ: '270617C00420000',
          quantity: 5,
        },
      ],
    },
  },
});
```

## Important Rules

- Session URL calls must happen on the partner server.
- Partner browser code should call the partner's own server route, not Trade It directly.
- The SDK expects the full Trade It URL, not a raw token.
- `@trade-it/react` is the default package for partner implementations.
