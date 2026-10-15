# Troubleshooting

Follow the failed boundary. Record HTTP status, request/event type, expiry, and non-sensitive identifiers. Keep full tokens, client secrets, codes, and launch URLs out of production logs. Do not weaken origin checks, CSP, or identity verification to make a development failure disappear.

| Symptom | Check | Next action |
| --- | --- | --- |
| Onboarding-mode URL creation fails | Basic confidential partner credentials, provisioned client, required `oauth_options`, correct environment | Send nested OAuth fields to `/api/portal`; do not use an old identity-worker onboarding endpoint |
| Connection-mode URL creation fails | Bearer token validity and `brokerage:write`, no `oauth_options` in body | Use any supported valid token with the required permission; a registered partner client ID is not required |
| Portal cannot be framed | Exact registered redirect URI, parent scheme/host/port, portal CSP | Register the correct website URI and create a fresh URL; do not wildcard credentials |
| OTP request returns 200 but no email arrives | Auth/provider logs, email configuration, primary account email versus a social alias, resend cooldown | A 200 is not proof of inbox delivery; check delivery before resending repeatedly |
| Link expires just after successful OTP | Portal attempt expiry versus OTP expiry | Request a fresh portal URL; a new OTP does not renew an old portal attempt |
| `onAuthorization` never runs | Installed SDK exports the callback, one modal mounted, iframe origin/source, actual event name | Upgrade to the onboarding-capable release and configure the callback before launch |
| Only generic event names appear | Authorization callback versus `onEvent` | Log non-sensitive lifecycle fields; handle `{ code, state, issuer }` in `onAuthorization` |
| Modal reports it could not save access | Completion route authentication, user-bound state/verifier, issuer, token response, storage result | Fix the failed exchange/storage boundary and start fresh if the code was consumed |
| Continue with connected accounts fires an event but does not close | Whether `onAuthorization` is still pending or failed | Save the exchanged tokens and resolve the callback; the SDK queues automatic closure until authorization succeeds |
| Brokerage connects but host never receives completion | Browser errors, SDK listener/source, backend callback/polling origin, popup state | Separate brokerage completion from OAuth token exchange; check each boundary |
| API returns 401 | Token expiration, correct environment, refresh outcome | Refresh once through the serialized server path; reauthorize on `invalid_grant` |
| API returns 403 | ClientType/API permissions and current plan eligibility | Correct access or plan setup; repeating token refresh will not add permissions |
| Revoked access token still works | Token's `exp`, whether it is the partner OAuth token or another credential | Existing JWTs can remain valid until expiry; revocation stops refresh |
| Picker shows all brokerages despite a filter | Available capabilities and whether any active brokerages match | An empty match falls back to the full picker; filters are UI configuration |
| Holdings look stale | Refresh eligibility and freshness timestamps | Use documented refresh behavior; do not poll faster than its cooldown |
| Webhook signature fails | Original bytes, complete secret, timestamp tolerance, endpoint rotation | Verify before parsing; synchronize the clock and use the correct endpoint secret |
| Duplicate/out-of-order events appear | Event ID and durable inbox uniqueness, resource version/freshness | Deduplicate and reconcile current state rather than replaying mutations blindly |

## Local development

`localhost` and `127.0.0.1`, or two different ports, are different origins. Use the exact registered harness origin. If brokerage polling calls an alpha API from a local frontend, inspect that API's CORS response; a successful brokerage callback does not make a blocked polling request succeed.

A browser autofill stack that reports blocked cross-origin frame access during input focus can be separate from an OAuth/network failure. Inspect the actual thrown operation and whether verification proceeds. Partner sites and Trade It remain cross-origin in production; do not attempt parent document access as the event transport.

Restart a stale harness backend after server code changes and create a fresh attempt; old server processes can return an outdated response shape, and restart clears in-memory attempts/grants. Automatic mode onboards when no grant is saved; explicit Bearer mode uses a saved grant or the configured `TRADE_IT_ACCESS_TOKEN`. The harness shows the token source, full authorization event, manual **Exchange & save tokens** action, and actual exchange/refresh responses with token reveal controls. Webhooks refresh on button press. Production partner code must keep tokens server-side and persist them across restarts.

## Check the finished path

For portal work, check consent → sign-in (OTP with a hint; social/email choices without one) → user-bound token exchange/storage → brokerage selection/connection → Connection mode using the saved grant. For standard OAuth, also check redirect denial, state mismatch, and refresh. For webhook work, verify signed bytes and duplicate handling. For trading UI work, verify the review/approval boundary without placing a live order merely to check wiring.

Follow the user's requested validation scope. If live credentials, email delivery, or a deployed SDK release are unavailable, identify the specific remaining check rather than claiming an end-to-end success.
