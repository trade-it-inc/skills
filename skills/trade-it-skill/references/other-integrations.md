# Trade Links and MCP

Use these surfaces to build shareable trade experiences or connect an AI client to Trade It. They complement the portal/API paths and retain explicit user control over brokerage connections and order execution.

## Trade Links

Read the [Trade Links guide](https://docs.tradeit.app/trade-links). A shareable link opens Trade It's order UI; it neither gives the publisher an OAuth grant nor submits an order by itself.

```text
https://tradeit.app/t/AAPL?tt=0&t=buy&a=100&u=dollars&ot=market&tif=day
```

Use this for a blog, newsletter, community, or research product that wants a prefilled trade without storing user tokens. The user signs in, chooses/connects a brokerage, and reviews the order on Trade It.

Use current query parameters from the guide. When building complex links, use `URLSearchParams`; options `legs` is an encoded JSON array using `position_effect`. SDK `launch.config` uses `positionEffect` instead. Refer to [trade-config.md](trade-config.md) for typed embedded configurations; do not put OAuth tokens or partner credentials in a public link.

## MCP client integrations

Use the existing Trade It MCP server at `https://mcp.tradeit.app/mcp` for an AI client that needs brokerage tools. Read [MCP installation](https://docs.tradeit.app/installation) and the client's supported configuration format before changing its settings. This task does not require building another MCP server or installing the React SDK.

The separately installable [Trade It user plugin](https://github.com/trade-it-inc/trade-it-mcp) bundles this connection and personal account/trading guidance. Use it when someone wants to use their own brokerage accounts through an assistant. Installing Trade It Developer supplies integration guidance only. A developer's own MCP authorization is not a grant for the application's users; each user must authorize their own access.

Authentication paths:

- Use the client's OAuth discovery flow following an MCP/API `401`; follow [MCP authentication](https://docs.tradeit.app/mcp.html#authentication).
- Where the client supports bearer headers, use a user-created API key from [Trade It API keys](https://tradeit.app/account/api-keys), following [API authentication](https://docs.tradeit.app/api.html#authentication).

Discover current tools through the MCP client and follow their actual schemas. Do not assume REST envelope details apply to MCP calls or copy a backend parameter shape into the client blindly. For trading, review the returned draft and obtain explicit user confirmation before executing the exact trade ID. Respect the automatic-execution caveat in [backend-api.md](backend-api.md).

The user plugin's `trade-it-mcp` skill supplies personal account and trading workflow guidance. This developer skill does not depend on it being installed.
