# Contributing

Keep the skill useful to an agent implementing a real Trade It integration. Its package is in `skills/trade-it-skill/`. Prefer a short entry point that routes to focused references rather than loading every contract into each request.

## Sources

Read the relevant first-party Trade It page before changing API, OAuth, event, or configuration guidance. A link resolving successfully is not evidence that its content supports a claim. Check the installed SDK declarations for version-specific code. During development of an unreleased feature, use the corresponding Trade It source and describe the release prerequisite.

Use the public docs as the authority for current brokerage support, plan eligibility, limits, and exact schemas. Avoid duplicating full response models or lists that will drift. Cross-link shared OAuth and ownership rules instead of maintaining them separately for portal, SDK, and REST examples.

## Structure

- Keep `SKILL.md` focused on task selection, retrieval sources, and essential invariants.
- Add or change a reference only when it supports a concrete integration path. Link every reference from the entry point or another relevant reference.
- Keep examples small, syntactically complete in their stated context, and free of real credentials or personal data. Label supplied variables and illustrative HTTP values clearly.
- Preserve the developer skill name `trade-it-skill` and plugin name `trade-it-developer`. The user-facing plugin is named `trade-it` and lives in `trade-it-inc/trade-it-mcp`. Keep invocation examples, catalog metadata, and installation instructions consistent with the installation method.
- Do not add a dependency, plugin configuration, executable helper, or another skill solely to make the repository appear larger.

## Review

Review local reference links, SDK imports and enum names, request parameter casing, and the documented response shape. In trade examples, confirm direction agrees with the illustrated legs and that returned status is handled correctly. In auth examples, confirm state, issuer, PKCE, user binding, and server-only token storage.

Use appropriate checks or behavioral evaluation when requested or warranted, and report what was actually verified. Do not run a live trade or rotate credentials merely to validate documentation. Keep publication and external configuration changes within the user's authorization.

Update the README's reference map and `skills/trade-it-skill/agents/openai.yaml` when capabilities or entry points change. Do not edit the separate application docs as part of a skill-only change unless requested.

## Plugin packaging

Maintain one skill package, shared by all assistants. Root `plugin.json` provides the portable Agent Plugins manifest; native compatibility manifests live in `.codex-plugin/`, `.claude-plugin/`, and `.cursor-plugin/`. Codex's marketplace catalog lives in `.agents/plugins/marketplace.json`. Claude Code and Cursor keep their marketplace catalogs beside their manifests.

The Codex and Claude Code catalogs expose both offerings under the `trade-it` marketplace. Their `trade-it-developer` entries point here; their `trade-it` entries point to the separate MCP repository. Cursor installs each plugin from its own repository. Keep the brokerage MCP out of the developer manifests; personal account access belongs in the user package.

Keep the plugin version synchronized across all four manifests. The initial plugin release is `1.0.0`; its version is independent of the React/core SDK versions. Bump it when releasing updated guidance so plugin managers can recognize a new version. Preserve plugin-relative skill and branding paths. The developer package has no MCP configuration; the user package maintains portable `mcp.json` and native `.mcp.json` files for the same server.

Lead the README with native plugin installations. Verify assistant-specific commands against official documentation; retain the skills CLI and manual copy as alternatives. Keep manual-copy locations distinct from CLI-managed shared locations. Reuse the packaged light/dark logo assets and preserve their relative paths when moving the skill.

Publish reviewed changes by pushing this repository to GitHub. Native repository installs and the skills CLI read that source; listing in a public plugin directory is a separate submission. The developer manifests do not configure the Trade It MCP server or require credentials. Follow [RELEASING.md](RELEASING.md) when publishing the two offerings.

Use the bug-report form for installation or guidance problems, and follow [SECURITY.md](SECURITY.md) for vulnerabilities or exposed credentials. Do not request unsanitized token responses or brokerage account screenshots in public issues.
