<p align="center">
  <a href="https://tradeit.app">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="skills/trade-it-skill/assets/trade-it-wordmark-dark.svg">
      <source media="(prefers-color-scheme: light)" srcset="skills/trade-it-skill/assets/trade-it-wordmark-light.svg">
      <img src="skills/trade-it-skill/assets/trade-it-wordmark-light.svg" alt="Trade It" width="260">
    </picture>
  </a>
</p>

<h1 align="center">Trade It Developer</h1>

<p align="center">Give your coding assistant the context to build with Trade It.</p>
<p align="center">
  <a href="https://docs.tradeit.app/sdk">Developer docs</a> ·
  <a href="https://docs.tradeit.app/api">API reference</a> ·
  <a href="https://docs.tradeit.app/brokerages">Brokerages</a> ·
  <a href="CONTRIBUTING.md">Contribute</a>
</p>

Build onboarding, OAuth, brokerage connections, account views, trading, webhooks, Trade Links, and MCP integrations. The skill helps your assistant choose the right path and implement it using current Trade It contracts.

## Choose your plugin

| Plugin | For | Includes |
| --- | --- | --- |
| **Trade It Developer** — this repository | Developers building products with Trade It | `trade-it-skill`, SDK/API guidance, and integration references |
| **[Trade It](https://github.com/trade-it-inc/trade-it-mcp)** | People using their brokerage accounts through an AI assistant | Brokerage MCP connection and `trade-it-mcp` workflow guidance |

The shared **Trade It** marketplace lists both offerings in Codex and Claude Code. Install the one that matches your task; they can also be used together. Developer setup does not require connecting a personal brokerage account.

## Install

Choose your assistant. The **Trade It Developer** plugin bundles `trade-it-skill` and its reference guides. Install it once through your assistant's plugin manager, then start a new session.

### Codex

```bash
codex plugin marketplace add trade-it-inc/skills
codex plugin add trade-it-developer@trade-it
```

Select **Trade It Developer** through `/skills` or the `$` picker, or ask Codex to build a Trade It integration. [Codex plugin documentation](https://developers.openai.com/plugins/build/plugins)

### Claude Code

```text
/plugin marketplace add trade-it-inc/skills
/plugin install trade-it-developer@trade-it
```

Invoke **`/trade-it-developer:trade-it-skill`** in Claude Code, or ask it to implement a Trade It integration. [Claude Code plugin documentation](https://code.claude.com/docs/en/discover-plugins)

### Cursor

1. Open **Settings → Plugins**.
2. Paste `https://github.com/trade-it-inc/skills` into the plugin search box.
3. Open **Trade It Developer** and select **Install**.

Describe your Trade It task in Agent chat so the agent can select the skill. [Cursor plugin documentation](https://cursor.com/docs/plugins)

### VS Code / GitHub Copilot

1. Enable **`chat.plugins.enabled`** in VS Code settings.
2. Run **Chat: Install Plugin From Source** in the Command Palette.
3. Enter `https://github.com/trade-it-inc/skills` and install **Trade It Developer**.

Open Copilot Agent chat and ask it to use `trade-it-skill`. [VS Code plugin documentation](https://code.visualstudio.com/docs/agent-customization/agent-plugins)

### OpenCode

```bash
npx skills add trade-it-inc/skills --skill trade-it-skill --agent opencode
```

Ask OpenCode to use `trade-it-skill` for your integration. It discovers the skill and loads its instructions when relevant. [OpenCode skill documentation](https://opencode.ai/docs/skills/)

### Standalone skill installation

If you prefer installing only the skill, use the interactive [skills CLI](https://github.com/vercel-labs/skills):

```bash
npx skills add trade-it-inc/skills --skill trade-it-skill
```

Run this from your application repository for a project installation, or add `--global` for a personal installation. Add `--agent codex`, `--agent claude-code`, `--agent cursor`, `--agent github-copilot`, or `--agent opencode` to select an assistant directly. This installer requires Node.js/npm.

Install through either the plugin manager or the skills CLI to avoid duplicate copies. Standalone skills use `$trade-it-skill` in Codex and `/trade-it-skill` in Claude Code; plugin skills can have an additional plugin namespace.

### Clone or copy

Copy the **entire** `skills/trade-it-skill/` folder, including `references/`, `assets/`, and `agents/`, into a supported skill directory. Do not copy only `SKILL.md`.

| Assistant | Project directory | Personal directory | Documentation |
| --- | --- | --- | --- |
| Codex | `.agents/skills/` | `~/.agents/skills/` | [Docs](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills) |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` | [Docs](https://code.claude.com/docs/en/skills#choose-where-skills-load) |
| Cursor | `.cursor/skills/` | `~/.cursor/skills/` | [Docs](https://cursor.com/docs/skills#skill-directories) |
| VS Code / GitHub Copilot | `.github/skills/` | `~/.copilot/skills/` | [Docs](https://code.visualstudio.com/docs/agent-customization/agent-skills#create-a-skill) |
| OpenCode | `.opencode/skills/` | `~/.config/opencode/skills/` | [Docs](https://opencode.ai/docs/skills/#place-files) |

For example, a first-time personal Codex installation:

```bash
git clone https://github.com/trade-it-inc/skills.git trade-it-skills
mkdir -p ~/.agents/skills
cp -R trade-it-skills/skills/trade-it-skill ~/.agents/skills/
```

Some assistants also discover shared `.agents/skills/` directories. The CLI may use those shared locations instead of the native paths in the manual-copy table. Local personal skills are not automatically available to cloud agents; use the assistant's documented project or cloud distribution method.

### Update or remove

For native plugins, use your assistant's plugin manager to update or uninstall **Trade It Developer**. Refresh the Trade It marketplace when checking for a new release.

For skills CLI installations, run in the same project or select the same global scope used during installation:

```bash
npx skills update trade-it-skill
npx skills remove trade-it-skill
```

For a manual installation, replace the installed folder with the updated package or remove that folder. If you installed the previous `trade-it-react` skill, remove it and install `trade-it-skill` under the same scope.

The plugin contains integration guidance. Installing it does not install `@trade-it/react`, configure an MCP connection, or issue API credentials; those are separate steps in the integration guides.

If you installed the earlier developer package under the plugin name `trade-it`, uninstall that package before switching to `trade-it-developer`. The `trade-it` name now identifies the user-facing brokerage plugin. Standalone `trade-it-skill` installations keep their existing name.

## Brokerage MCP

The **[Trade It user plugin](https://github.com/trade-it-inc/trade-it-mcp#install-the-plugin)** connects an assistant to `https://mcp.tradeit.app/mcp` to work with the user's accounts, holdings, and orders. Authentication happens separately through the user's Trade It account. It is optional for developers building integrations.

To build an MCP client into your own product, read the [MCP integration reference](skills/trade-it-skill/references/other-integrations.md#mcp-client-integrations) and [installation guide](https://docs.tradeit.app/installation). Installing the user plugin does not authorize access to your application's other users.

## Use

Example prompts; use your assistant's invocation syntax shown above:

- “Add the Trade It Portal to our app: Onboarding mode for the first connection, then Connection mode for later visits. Store OAuth tokens against each signed-in user.”
- “Read connected accounts and holdings from our backend using Trade It.”
- “Add a signed webhook receiver for account connections and final trade updates.”
- “Build a draft review screen and submit an order only after the user approves it.”
- “Diagnose why our onboarding authorization callback never finishes.”

## What it covers

| Surface | Purpose |
| --- | --- |
| Trade It Portal | Onboarding mode combines consent, social/email sign-in, and the first connection; Connection mode manages later connections with a saved token |
| Standard OAuth | Separate authorization and connection for a custom experience |
| React SDK and trade portal | Embed the Trade It Portal and order entry/review through `@trade-it/react` |
| REST API | Read brokerage data and implement custom draft/review/execute flows |
| Webhooks | Verify signed account and trade events, deduplicate, and handle retries |
| Trade Links | Share prefilled orders without a partner backend |
| MCP client integration | Build or configure an AI client that connects to Trade It's brokerage tools |

## Repository layout

```text
README.md                        Installation, capabilities, and usage
CONTRIBUTING.md                   Sources and maintenance conventions
SECURITY.md                       Private vulnerability reporting
plugin.json                      Portable Agent Plugins manifest
.agents/plugins/marketplace.json Shared Codex catalog for both plugins
.codex-plugin/plugin.json        Codex plugin manifest and branding
.claude-plugin/                  Claude Code manifest and marketplace
.cursor-plugin/                  Cursor manifest and marketplace
.github/ISSUE_TEMPLATE/           Structured skill/documentation bug reports
skills/trade-it-skill/
  SKILL.md                       Entry point and integration selection
  agents/openai.yaml             Skill catalog metadata and branding
  assets/                        Light/dark Trade It logos and symbols
  references/
    onboarding-portal.md         Onboarding mode and authorization callback
    oauth.md                     PKCE, code exchange, refresh, and ownership
    api-contract.md              Connection mode, trade session URLs, and React SDK
    trade-config.md              Typed trade configuration and examples
    backend-api.md               REST operations and order lifecycle
    webhooks.md                  Signature verification and delivery
    other-integrations.md        Trade Links and MCP setup
    troubleshooting.md           Common failures and checks
```

The [entry point](skills/trade-it-skill/SKILL.md) routes to focused references so the agent loads only what it needs. First-party docs and the application's pinned SDK types take precedence over copied examples when contracts change. Onboarding requires a deployed portal endpoint and an SDK release that supports `onAuthorization`.

See [RELEASING.md](RELEASING.md) for the publication order and versioning of the two repositories.

For multi-user partner integrations, obtain client credentials through Trade It's organization setup and keep tokens on your backend. For personal API/MCP use, the docs also describe user-created API keys. The skill preserves user approval before live trading and does not execute trades simply because it was installed or invoked.

## Help and contributions

- Integration questions: [Trade It developer documentation](https://docs.tradeit.app/sdk) and [contact](https://tradeit.app/contact).
- Incorrect guidance or installation problems: [open a skill issue](https://github.com/trade-it-inc/skills/issues/new/choose) with sanitized reproduction details.
- Improvements: read [CONTRIBUTING.md](CONTRIBUTING.md).
- Vulnerabilities or exposed credentials: follow [SECURITY.md](SECURITY.md) and report privately.
