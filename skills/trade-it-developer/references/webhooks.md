# Signed webhooks

Read the current [webhook guide](https://docs.tradeit.app/sdk/webhooks). Configure a public HTTPS receiver through **Organizations → Webhooks**, selecting the needed events. Keep each endpoint's signing secret on the receiver server.

Webhooks deliver durable account/trade updates; they do not carry the portal's OAuth authorization code. Use `onAuthorization` for code exchange and SDK `onEvent` for the embedded UI lifecycle.

## Event envelope

```json
{
  "id": "0a67c0b8-57ae-4cdf-ab8b-5b249e83c100",
  "type": "TRADE_UPDATED",
  "created_at": "2026-10-07T14:31:12.000Z",
  "data": {}
}
```

| Type | Meaning |
| --- | --- |
| `ACCOUNT_ADDED` | An account was linked or newly discovered |
| `ACCOUNT_DELETED` | The account was removed; its ID can no longer be used |
| `ACCOUNT_DISCONNECTED` | Brokerage access broke; reconnection is needed |
| `ACCOUNT_RECONNECTED` | An existing account's brokerage was reconnected |
| `TRADE_UPDATED` | A trade reached a final state |

Account events are per account, so linking one brokerage can produce multiple events. `TRADE_UPDATED` covers final statuses; do not expect partial-fill or every intermediate state notification. Follow the guide for current event data models instead of inferring fields from the browser event payload.

## Verify before processing

Headers include `TradeIt-Signature: t=<unix-seconds>,v1=<hex-hmac>` and `TradeIt-Event-Id`. Verify HMAC-SHA256 over the exact timestamp text followed by `.` and the original body bytes, using the complete `whsec_…` secret. Do not decode or trim the secret prefix, or reserialize JSON before verification.

Node example:

```ts
import { createHmac, timingSafeEqual } from 'node:crypto';

/** Verifies the signed bytes before parsing the event. */
export function verifyWebhook(rawBody: Buffer, header: string, secret: string): unknown {
  const parts = header.split(',').map(part => part.trim());
  const timestampText = parts.find(part => part.startsWith('t='))?.slice(2) ?? '';
  const signature = parts.find(part => part.startsWith('v1='))?.slice(3) ?? '';
  const timestamp = Number(timestampText);

  if (!/^\d+$/.test(timestampText) || !Number.isSafeInteger(timestamp) || Math.abs(Date.now() / 1000 - timestamp) > 300 || !/^[a-f0-9]{64}$/.test(signature)) throw new Error('Invalid webhook signature');

  const expected = createHmac('sha256', secret).update(`${timestampText}.`).update(rawBody).digest();
  if (!timingSafeEqual(expected, Buffer.from(signature, 'hex'))) throw new Error('Invalid webhook signature');
  return JSON.parse(rawBody.toString('utf8'));
}
```

Configure the framework to provide raw bytes before any JSON parser modifies them. In a non-Node runtime, use the platform's cryptographic verification APIs while preserving the same signed bytes. Validate the parsed envelope before routing it to application logic.

## Delivery behavior

- Persist a verified event to a durable inbox and return 2xx promptly. Slow processing belongs after durable acceptance; do not acknowledge and then rely on a process-local task that can be lost.
- Deduplicate on `event.id` with a storage uniqueness constraint. Delivery is at least once and can arrive out of order.
- Retries retain the event ID/body and use a new signature timestamp. Fresh signature timestamps are not resource-version ordering; use the event/resource timestamps and reconcile current state when needed.
- Update receiver secrets promptly after rotation. A public HTTPS URL is required and redirects are not followed.
- Check current timeout/retry limits in the guide rather than building application correctness around a hardcoded schedule.

The partner portal's event log helps inspect payloads and attempts. The SDK harness has a manual webhook Refresh button; it is a development receiver, not the production durable inbox pattern.
