# Trade Config Reference

Use this reference when building `launch.config` for `tradeIt.openTrade(...)` or the trade-mode `TradeItModal`.

## Trade Modal Config Parameters (`launch.config`)

| Field | Meaning | Type / Enum | Used In | Default | Notes |
|---|---|---|---|---|---|
| `ticker` | Asset symbol | `string` | `simple` `options` | — | Required. Example: `AAPL`, `BTC-USD` |
| `tradeType` | Trade type | `TradeType` | `simple` `options` | `TradeType.Simple` | Use `TradeType.MultiLeg` for options/multi-leg |
| `action` | Buy or sell | `TradeAction` | `simple` | `TradeAction.Buy` | Simple trades only |
| `amount` | Trade amount | `number` | `simple` | `100` | Interpreted by `unit` |
| `unit` | Amount unit | `TradeUnit` | `simple` | `TradeUnit.Dollars` | `Dollars` or `Shares` |
| `orderType` | Order type | `OrderType` | `simple` `options` | `OrderType.Market` | `market`, `limit`, `stop`, `stop_limit` |
| `limitPrice` | Limit price | `number` | `simple` `options` | — | Used for `limit` / `stop_limit` |
| `stopPrice` | Stop trigger price | `number` | `simple` `options` | — | Used for `stop` / `stop_limit` |
| `timeInForce` | Order duration | `TimeInForce` | `simple` `options` | `TimeInForce.Day` | `day`, `gtc`, `ioc`, `fok` |
| `legs` | Options leg payload | `TradeItMultiLegTradeLegConfig[]` | `options` | — | Required for options/multi-leg trades |
| `legs[].type` | Leg type | `LegType` | `options` | — | `option` or `equity` |
| `legs[].action` | Leg action | `TradeAction` | `options` | — | `buy` or `sell` |
| `legs[].positionEffect` | Position effect | `PositionEffect \| null` | `options` | `null` | Usually `open` / `close`; `null` for equity legs |
| `legs[].occ` | OCC contract string | `string \| null` | `options` | — | Required for option legs |
| `legs[].quantity` | Leg quantity | `number` | `options` | `1` | Contracts for options, shares for equity legs |

## Common Enum Values

### `TradeType`
- `TradeType.Simple`
- `TradeType.MultiLeg`

### `TradeAction`
- `TradeAction.Buy`
- `TradeAction.Sell`

### `TradeUnit`
- `TradeUnit.Dollars`
- `TradeUnit.Shares`

### `OrderType`
- `OrderType.Market`
- `OrderType.Limit`
- `OrderType.Stop`
- `OrderType.StopLimit`

### `TimeInForce`
- `TimeInForce.Day`
- `TimeInForce.GoodTillCanceled`
- `TimeInForce.ImmediateOrCancel`
- `TimeInForce.FillOrKill`

### `LegType`
- `LegType.Option`
- `LegType.Equity`

### `PositionEffect`
- `PositionEffect.Open`
- `PositionEffect.Close`

## Examples

### Simple buy

```tsx
{
  tradeType: TradeType.Simple,
  ticker: 'AAPL',
  action: TradeAction.Buy,
  amount: 100,
  unit: TradeUnit.Dollars,
  orderType: OrderType.Market,
  timeInForce: TimeInForce.Day,
}
```

### Sell with stop-limit

```tsx
{
  tradeType: TradeType.Simple,
  ticker: 'AAPL',
  action: TradeAction.Sell,
  amount: 10,
  unit: TradeUnit.Shares,
  orderType: OrderType.StopLimit,
  stopPrice: 260,
  limitPrice: 250,
  timeInForce: TimeInForce.Day,
}
```

### Multi-leg options

```tsx
{
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
}
```
