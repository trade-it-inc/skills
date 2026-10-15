# Security

This repository distributes agent instructions and reference material for Trade It integrations. It does not issue credentials or grant access to a brokerage account.

## Report a vulnerability

Report suspected vulnerabilities privately through [Trade It contact](https://tradeit.app/contact). Identify this skill repository and describe the affected integration boundary. Do not publish exploitable details in a public issue before the Trade It team has reviewed them.

Include the affected skill revision or SDK version, assistant/runtime, a minimal reproduction with test data, and the expected versus observed behavior. Remove access/refresh tokens, authorization codes, client secrets, API keys, signing secrets, authenticated launch URLs, and personal account data from shared logs and screenshots.

Ordinary documentation or installation issues can use the repository's bug-report form. See [CONTRIBUTING.md](CONTRIBUTING.md) for source and review conventions.

## Credentials and generated code

Keep credentials in the integrating application's server-side secret storage. Do not commit them to this repository or include them in skill instructions. If a credential is exposed, its owner should revoke or rotate it through the issuing service; deleting the visible copy alone does not revoke it.

Review generated integration code against the current first-party contract, especially user binding, OAuth state/PKCE, token storage, webhook verification, and explicit order approval. Installing or invoking the skill does not authorize credential rotation, account changes, or live trading.
