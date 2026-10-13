---
name: fanvue-connect
description: Connect and verify this workspace's Fanvue account
disable-model-invocation: true
---

# Connect to Fanvue

Confirm the Fanvue MCP server is connected and report what the connection can reach. Do
not move on to an account action. This command's only job is the connection.

## Steps

1. **Call `get-current-user`** with `X-Fanvue-API-Version: 2025-06-26`.

   - **It works:** report the `displayName` and `@handle` in plain language. Then work
     out the account type:
     - `isCreator` is `true`: say the tools act on this creator's own account.
     - Otherwise, call `list-creators`. A list means an agency seat: name the creators
       it can manage and say the user must name one for each request. A `403` means the
       account is neither a creator nor an agency seat, so most tools will not apply.
       An empty list means the seat has no creators assigned yet.

     Do not report email addresses, fan counts or other profile fields unless asked.
     Then stop and ask what they want to do.
   - **The tool is missing entirely:** the MCP server has not loaded. Tell the user to
     open `/plugins` and confirm the `fanvue` plugin is enabled, then open `/mcps` to
     check the `fanvue` server's state. `grok mcp doctor fanvue` diagnoses configuration
     and connectivity.
   - **It returns unauthenticated or a reconnect error:** sign-in has not completed or
     was revoked. Tell the user to open `/mcps`, select `fanvue` and press `i`. The
     browser opens to Fanvue to sign in and approve access. **No API key is involved.**
     Grok also starts this flow the first time it uses a Fanvue tool.

2. **Say what they authorised.** Fanvue grants either read access
   (`fanvue_mcp:read`) or read and write access (`fanvue_mcp:write`), limited to what
   the account itself can do. Tools outside the granted access are hidden. They can
   revoke access at any time in Fanvue under Settings > Account > **Third Party Apps
   Consent**, or remove the server from `/mcps`.
