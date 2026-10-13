# Fanvue plugin for Grok Build

Connect Grok Build to [Fanvue](https://www.fanvue.com), the creator subscription
platform. Read and reply to chats, message lists of fans, create and schedule posts,
manage the vault, read earnings and audience insights, and create checkout and tracking
links, on a creator account or across every creator an agency manages.

## Installation

If `grok --version` does not print a version, install Grok Build and sign in first:

```bash
curl -fsSL https://x.ai/cli/install.sh | bash
grok   # opens your browser to sign in to Grok
```

Then install the plugin from this repository:

```bash
grok plugin install fanvue/plugins#grok/fanvue --trust
```

Then run `/fanvue-connect`.

On first use, Grok opens Fanvue sign-in in your browser. Authorise with the creator or
agency account you want Grok to work with. No API key is needed and nothing should be
pasted into chat. To authorise ahead of time, open `/mcps`, select `fanvue` and press `i`.

## What you get

- **MCP server** `fanvue` at `https://mcp.fanvue.com/mcp` (Streamable HTTP, OAuth 2.1).
- **Skill** `fanvue`: which tool starts each job, the creator and agency patterns, money
  and date conventions, and a rule to preview and confirm before anything that messages
  fans, publishes, changes prices, blocks or deletes.
- **Command** `/fanvue-connect`: checks you are signed in, then names the account and
  whether it is a creator or an agency.

### Tools

The server exposes the Fanvue API as tools, one per operation, plus custom tools for
image posts. Tools your granted access does not cover are hidden. The full list is in
the [Fanvue MCP server docs](https://api.fanvue.com/docs/mcp-server).

| Area | Examples |
| --- | --- |
| Chats | `list-chats`, `list-messages`, `send-message`, `send-mass-message` |
| Posts | `get-posts`, `create-post`, `custom__create-image-post`, `pin-post` |
| Vault and media | `list-vault-folders`, `list-media`, `grant-media` |
| Insights | `get-earnings-summary`, `get-top-spenders`, `get-fan-retention-summary` |
| Selling and growth | `create-checkout-link`, `create-free-trial-link`, `create-tracking-link` |
| Agency | `list-creators`, then the `*-creator-*` variant of each tool |

## Example requests

- "How many unread chats do I have, and who has spent the most this month?"
- "Draft a thank-you message to my top 10 spenders. Show me before sending."
- "Schedule this photo as a \$9.99 pay-to-view post for Friday at 8pm London time."
- "Compare my net earnings this month with last month, by source."
- "Create a tracking link for my TikTok bio."

## Authentication and network

The plugin connects only to Fanvue. Authentication is OAuth 2.1 authorization code with
PKCE and dynamic client registration; Grok handles the flow.

Network endpoints:

- `https://mcp.fanvue.com/mcp`: hosted MCP server (Streamable HTTP)
- `https://mcp.fanvue.com/.well-known/oauth-protected-resource`: resource metadata
- `https://auth.mcp.fanvue.com`: OAuth 2.1 authorization server with DCR (`/reg`)
- `https://auth.fanvue.com`: Fanvue sign-in and consent screen during authorization

Credentials: a Fanvue creator or agency account. Access is either read
(`fanvue_mcp:read`) or read and write (`fanvue_mcp:write`), never more than the account
itself can do. The plugin stores no API key and requests no filesystem or shell access.
Revoke access in Fanvue under Settings > Account > **Third Party Apps Consent**.

## License

MIT for the files in this plugin (see `LICENSE`). Use of Fanvue and the hosted MCP
server is governed by the [Fanvue terms](https://legal.fanvue.com/terms-conditions) and
[privacy policy](https://legal.fanvue.com/privacy-policy).
