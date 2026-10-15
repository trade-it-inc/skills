# OAuth and token storage

Read the current [authentication guide](https://docs.tradeit.app/sdk/authentication). Use this reference for both standard authorization and the Trade It Portal in Onboarding mode; the delivery of the authorization code differs, but token exchange and ownership do not. Prefer the portal for a new partner integration; use redirect OAuth when the product needs a custom authorization experience.

## Choose credentials

Multi-user partner integrations use a registered confidential client and each user's OAuth grant. A user-created API key is an alternative for personal server-side API use, not a substitute for every partner user's authorization. Keep credentials in the server environment or secret manager.

For standard OAuth, discover endpoints from `https://tradeit.app/.well-known/oauth-authorization-server`. Portal creation instead uses `POST https://api.tradeit.app/api/portal` and returns `issuer` and `tokenEndpoint`; there is no portal discovery extension. Retain the trusted issuer and token endpoint with the attempt. Do not derive an arbitrary fetch target from an event's `issuer` or mix alpha credentials with production endpoints.

## PKCE and state

Generate a fresh verifier and random state per attempt:

```ts
import { createHash, randomBytes } from 'node:crypto';

const state = randomBytes(32).toString('base64url');
const verifier = randomBytes(32).toString('base64url');
const challenge = createHash('sha256').update(verifier).digest('base64url');
```

Before redirecting or returning a portal URL, persist state, verifier, issuer, token endpoint, exact redirect URI, expiry, and the authenticated partner user ID in the application's existing server-side session or attempt store. Support separate attempts when the app permits multiple tabs. PKCE prevents a stolen code from being exchanged without its verifier; state binds delivery to the local attempt.

## Standard authorization

Redirect to the discovered `authorization_endpoint` with `response_type=code`, `client_id`, the exact registered `redirect_uri`, `state`, `code_challenge`, and `code_challenge_method=S256`. Partner permissions follow the client type; do not invent additional scope requirements from OIDC profile scopes.

The callback must handle denied/error responses, require the same signed-in partner user, match state, check attempt expiry, and then exchange the code. Preserve the original redirect URI exactly, including any trailing slash. Do not take the user binding from an untrusted callback parameter.

## Portal authorization

Follow [Onboarding mode](onboarding-portal.md). Basic client credentials and `oauth_options` start this mode through `/api/portal`. The portal's registered redirect URI has the embedding website's origin, but is not navigated to. The backend completion route receives `{ code, state, issuer }` through `onAuthorization`; it applies the same user, state, expiry, and PKCE checks. Require the issuer to match the stored value and use the stored endpoint.

After either authorization path, use the saved access token with Bearer authentication on the same `/api/portal` endpoint to open Connection mode. Omit `oauth_options`; there is no new code exchange. See [Connection mode](api-contract.md#connection-mode).

## Code exchange

POST to the stored token endpoint with form-encoded `grant_type=authorization_code`, `code`, `redirect_uri`, and `code_verifier`. Use `URLSearchParams` to encode the form and the client authentication supported by discovery.

For `client_secret_basic`:

```ts
const basic = Buffer.from(`${encodeURIComponent(clientId)}:${encodeURIComponent(clientSecret)}`).toString('base64');
```

Send `Authorization: Basic <basic>` and `Content-Type: application/x-www-form-urlencoded`. Client authentication and PKCE are both needed; one does not replace the other.

Check HTTP status before treating the response as tokens. Persist the returned access/refresh pair, expiry, and issuer against the authenticated partner user before returning success. Complete the local attempt once; codes cannot be replayed. Keep access tokens, refresh tokens, and client secrets out of browser responses and logs. A debugging harness revealing tokens is not the production integration pattern.

## Refresh and revocation

Refresh on the backend when the access token approaches expiry. POST `grant_type=refresh_token` with the current refresh token and client authentication to the stored endpoint. Save the replacement pair atomically. Serialize refreshes per user/grant so concurrent requests do not overwrite rotated credentials.

An `invalid_grant` response requires fresh authorization. A transient network/server error does not establish that a grant was revoked; retain credentials and report/retry the failure appropriately. Do not retry a spent code, repeatedly refresh a revoked grant, or substitute another user's credentials.

Revocation invalidates the client's refresh path, but an issued access JWT may remain usable until `exp`. The current Trade It confirmation discloses up to one hour. Do not promise immediate invalidation or describe revoking partner access as deleting a brokerage connection.
