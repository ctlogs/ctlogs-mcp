# ctlogs.dev MCP server

A [Model Context Protocol](https://modelcontextprotocol.io) server for
[ctlogs.dev](https://ctlogs.dev): search Certificate Transparency logs from an
AI assistant or agent — subdomains, TLS certificates, organizations, CAs.

The server is hosted. There is nothing to install or run; point a client at

```
https://api.ctlogs.dev/mcp
```

(Streamable HTTP). It needs no key and no sign-in.

## Tools

| Tool | What it does |
|---|---|
| `find_subdomains` | List the hostnames found under a domain in TLS certificates: one row per hostname with the number of certificates and first/last seen dates. |
| `search_certificates` | List TLS certificates issued for a domain name: id, matching names, issuer, serial number and validity dates. |
| `search_organization` | List TLS certificates whose subject organization matches a name, e.g. "PayPal, Inc.": id, subject common name, domains, issuer and validity dates. |
| `get_certificate` | Return the full record of one certificate: subject, issuer, serial number, validity, DNS names, key and signature details, the CT log entries that recorded it and download URLs for the certificate body. |
| `get_ca` | Return one issuing CA certificate by its SHA-256 fingerprint (the `issuer_ca` field of `get_certificate`): subject, issuer, validity, parent and cross-signed certificates, the number of certificates it issued and a page of them. |

All five tools are read-only.

## Connect

### Claude Code

```bash
claude mcp add --transport http ctlogs https://api.ctlogs.dev/mcp
```

With a key:

```bash
claude mcp add --transport http ctlogs https://api.ctlogs.dev/mcp --header "Authorization: Bearer YOUR_KEY"
```

### claude.ai

Under Customize › Connectors, choose Add, then Add custom connector, and enter
the URL; choose no sign-in. On Team and Enterprise plans an Owner adds it under
Organization settings › Connectors.

### ChatGPT

Under Plugins, choose **+**, then Add custom MCP server, and enter the URL; the
server asks for no sign-in.

### Cursor

In `~/.cursor/mcp.json`, or `.cursor/mcp.json` in a project:

```json
{
  "mcpServers": {
    "ctlogs": { "url": "https://api.ctlogs.dev/mcp" }
  }
}
```

With a key:

```json
{
  "mcpServers": {
    "ctlogs": {
      "url": "https://api.ctlogs.dev/mcp",
      "headers": { "Authorization": "Bearer YOUR_KEY" }
    }
  }
}
```

### VS Code

In `.vscode/mcp.json`:

```json
{
  "servers": {
    "ctlogs": { "type": "http", "url": "https://api.ctlogs.dev/mcp" }
  }
}
```

With a key:

```json
{
  "servers": {
    "ctlogs": {
      "type": "http",
      "url": "https://api.ctlogs.dev/mcp",
      "headers": { "Authorization": "Bearer YOUR_KEY" }
    }
  }
}
```

## Example prompts

- Which subdomains of example.com have TLS certificates right now? Group them
  by what they appear to be used for.
- List the certificates issued for login.example.com and its subdomains, and
  tell me which certificate authorities issued them.
- Find certificates issued to the organization "Example Corp" and show me
  domains on them that are not under example.com.

## Limits

A tool call is one API request and runs under the anonymous API's limits, or
the key's plan (see [limits](https://api.ctlogs.dev/#auth));
`search_organization` counts as 10, as `/v1/org` does. The key goes in the
`Authorization` header only. Without a key or with a free key, a call that
arrives while another is running waits up to 5 seconds for its turn.
Certificate lists return 25 rows per call, `limit` up to 100;
`find_subdomains` returns up to 100 hostnames per call. A list with more rows
returns `next_cursor` for the next call.

## Documentation and contact

- MCP server: <https://api.ctlogs.dev/#mcp>
- API reference: <https://api.ctlogs.dev/>
- Keys and plans: <https://api.ctlogs.dev/#auth>
- Contact: <contact@ctlogs.dev>

## This repository

It holds the connection instructions and [`server.json`](server.json), the
server's manifest in the format of the
[MCP Registry](https://registry.modelcontextprotocol.io). The server's code is
not here.

## License

[MIT](LICENSE), for the contents of this repository.
