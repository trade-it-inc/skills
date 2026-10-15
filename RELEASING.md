# Releasing the Trade It plugins

The **Trade It** marketplace offers two independent packages:

| Package | Repository | Contents |
| --- | --- | --- |
| `trade-it-developer` | `trade-it-inc/skills` | Developer skill and SDK/API references |
| `trade-it` | `trade-it-inc/trade-it-mcp` | User workflow skill and brokerage MCP connection |

## Publish the initial split

1. Review the changes in both repositories, including untracked manifests, skill folders, and logo assets. Keep credentials and personal account data out of both repositories.
2. In each repository, keep the version synchronized across root `plugin.json`, `.codex-plugin/plugin.json`, `.claude-plugin/plugin.json`, and `.cursor-plugin/plugin.json`. Both plugins start at `1.0.0`; subsequent releases can version independently. Plugin versions are independent of SDK and MCP server versions. Include the user package's `mcp.json` and `.mcp.json`; both must name the same server and endpoint, with `streamable-http` and `http` transports respectively.
3. Commit and push the **MCP repository first**. The shared marketplace references its root as the `trade-it` plugin source. Its package must exist before publishing the updated catalog.
4. Commit and push the **developer repository second** to `trade-it-inc/skills`. Include the renamed developer manifests and both entries in the Codex and Claude Code marketplace catalogs. The MCP package remains in `trade-it-inc/trade-it-mcp`.
5. Refresh the marketplace in each assistant. Check each installation separately in a fresh session: the developer package should offer integration guidance without brokerage authentication; the user package should expose the MCP connection and request the user's own authorization.
6. Confirm the user package can read a designated user's accounts after authorization. Available tools depend on the client and permissions; do not place a live trade solely to check installation. Native installation checks have not been run while preparing this split.
7. Update any existing public directory listings to distinguish **Trade It Developer** from **Trade It**. A GitHub push makes repository installs available; it does not submit or update public plugin directory listings automatically.

The MCP registry's `server.json` and its publish workflow describe the hosted server, not these assistant plugins. This packaging change does not require a new MCP registry version or a Worker deployment. SDK/API feature releases still follow their application release process before developer guidance can promise the new behavior.

## Existing installations

The earlier developer plugin used `trade-it@trade-it`. Users of that unreleased package should uninstall it, refresh the marketplace, and install `trade-it-developer@trade-it` for development work. The `trade-it@trade-it` entry now installs the brokerage user package.

The standalone developer skill remains `trade-it-skill`. Replace old `trade-it-react` installations as described in the README. The user skill is `trade-it-mcp`; standalone installation supplies instructions only and requires a separate MCP connection.

## Future releases

Change guidance in the repository that owns the workflow. Bump that plugin's four manifest versions together, review its README and source links, and push the release. Keep portable and native MCP configurations aligned when the user endpoint changes. The shared catalogs use repository sources, so they need changing only when package names or source locations change.
