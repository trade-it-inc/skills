# Typed trade configuration

Use this reference for `tradeIt.openTrade({ launch: { mode: 'trade', url, config } })` or a trade-mode `TradeItModal`. Consult the installed `TradeItTradeConfig` and [trade guide](https://docs.tradeit.app/sdk/trades) for the pinned version. These are SDK fields, not the REST API's request parameters.

## Configuration families

| Family | Required | Additional fields |
| --- | --- | --- |
| Simple stock/crypto | `ticker` | `tradeType: TradeType.Simple`, `action`, `amount`, `unit`, order fields, optional Buy exit triggers |
| Single-/multi-leg options | `ticker`, `tradeType: TradeType.MultiLeg`, `legs` | `direction` and compatible order fields |

Common order fields are `orderType`, `limitPrice`, `stopPrice`, `timeInForce`, and optional `referral`. Use the published enums from `@trade-it/react`; do not mix compact URL keys (`ot`, `lp`) or REST snake_case (`order_type`, `limit_price`) into `launch.config`.

## Simple order

```ts
import { OrderType, TimeInForce, TradeAction, TradeType, TradeUnit } from '@trade-it/react';
import type { TradeItTradeConfig } from '@trade-it/react';

const config = {
  tradeType: TradeType.Simple,
  ticker: 'AAPL',
  action: TradeAction.Buy,
  amount: 100,
  unit: TradeUnit.Dollars,
  orderType: OrderType.Market,
  timeInForce: TimeInForce.Day,
} satisfies TradeItTradeConfig;
```

`TradeUnit.Dollars` makes amount a notional value; `TradeUnit.Shares` makes it units. Confirm fractional/notional support for the selected brokerage rather than assuming every account accepts both.

Use `OrderType.Limit` with `limitPrice`, `OrderType.Stop` with `stopPrice`, and `OrderType.StopLimit` with both. Brokerage support also determines available time-in-force values. Prefill remains a reviewable order configuration, not authority to execute.

## Attached exits

`takeProfit` and `stopLoss` apply to simple Buy entries, not Sell or options configurations. Each is `{ value, unit }`; units come from `TriggerUnit.Price` and `TriggerUnit.Percent`. The selected brokerage must support the requested exit type.

```ts
import { OrderType, TimeInForce, TradeAction, TradeType, TradeUnit, TriggerUnit } from '@trade-it/react';
import type { TradeItTradeConfig } from '@trade-it/react';

const config = {
  tradeType: TradeType.Simple,
  ticker: 'AAPL',
  action: TradeAction.Buy,
  amount: 100,
  unit: TradeUnit.Dollars,
  orderType: OrderType.Market,
  timeInForce: TimeInForce.Day,
  takeProfit: { value: 10, unit: TriggerUnit.Percent },
  stopLoss: { value: 5, unit: TriggerUnit.Percent },
} satisfies TradeItTradeConfig;
```

## Options and spreads

Use `TradeType.MultiLeg` even for a single option leg. Each leg uses `type`, `action`, `positionEffect`, `occ`, and `quantity`. Option legs use `LegType.Option` and a position effect; equity legs use `LegType.Equity` with `occ: null` and `positionEffect: null`. Quantity is contracts for options and shares for equity legs.

Supply real, current contracts obtained from the app's market data or user selection. Do not invent a live option symbol from an illustrative example. The examples here use the documented compact OCC suffix with `ticker` identifying the underlying; check the accepted format for the actual endpoint you are calling.

This illustrative long-call spread buys the lower strike and sells the higher strike, consistent with a debit order:

```ts
import { LegType, OrderDirection, OrderType, PositionEffect, TimeInForce, TradeAction, TradeType } from '@trade-it/react';
import type { TradeItTradeConfig } from '@trade-it/react';

const config = {
  tradeType: TradeType.MultiLeg,
  ticker: 'MSFT',
  direction: OrderDirection.Debit,
  orderType: OrderType.Limit,
  timeInForce: TimeInForce.Day,
  limitPrice: 1.25,
  legs: [
    {
      type: LegType.Option,
      action: TradeAction.Buy,
      positionEffect: PositionEffect.Open,
      occ: '270617C00390000',
      quantity: 1,
    },
    {
      type: LegType.Option,
      action: TradeAction.Sell,
      positionEffect: PositionEffect.Open,
      occ: '270617C00420000',
      quantity: 1,
    },
  ],
} satisfies TradeItTradeConfig;
```

For priced spreads, specify `OrderDirection.Debit` or `OrderDirection.Credit` consistently with the intended order. Use `OrderDirection.Even` only for a zero-price limit order. Quantities are per leg, not the SDK simple trade's `amount`.

## Parameter boundaries

| Surface | Order type | Position effect | Configuration transport |
| --- | --- | --- | --- |
| React SDK | `orderType` | `positionEffect` | Typed `launch.config` |
| REST API | `order_type` | `position_effect` | Operation `params` |
| Public Trade Link | `ot` | `position_effect` inside legs JSON | URL query, encoded with `URLSearchParams` |

Do not reuse a configuration object across these surfaces without mapping its documented fields. The SDK handles its own URL serialization; direct API calls must follow [backend-api.md](backend-api.md).
