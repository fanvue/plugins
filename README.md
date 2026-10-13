# Fanvue plugins

Official Fanvue plugins for coding and chat clients. Each plugin connects the client to the
hosted Fanvue MCP server at `https://mcp.fanvue.com/mcp` and adds skills that teach it how to
run a Fanvue creator or agency account.

## Install

**Grok Build**

If `grok --version` does not print a version, install Grok Build and sign in first:

```bash
curl -fsSL https://x.ai/cli/install.sh | bash
grok   # opens your browser to sign in to Grok
```

Then install the plugin:

```bash
grok plugin install fanvue/plugins#grok/fanvue --trust
```

Then open `/mcps`, select `fanvue` and press `i` to sign in with Fanvue, and run
`/fanvue-connect` to check the connection.

## Clients

| Client | Plugin | Status |
| --- | --- | --- |
| Grok Build | [`grok/fanvue`](grok/fanvue) | Installs from this repo |

Any MCP client can also connect to the server directly. Setup for every client is in the
[Fanvue MCP server docs](https://api.fanvue.com/docs/mcp-server).

## License

MIT. See [LICENSE](LICENSE).
