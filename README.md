# Preflight

Check an MCP server before your agent trusts it.

**Live:** https://preflight.allthepossibles.com (docs: `/docs`, machine-readable: `/llms.txt`)
**Built by:** [All the Possibles](https://allthepossibles.com)

## What it does

Preflight scans an MCP server the moment before your agent connects to it —
before there's a human in the loop to notice something's wrong. It checks for:

- **Unauthenticated transport** — did the server hand its full tool list to a
  client with zero credentials, before anyone decided whether to trust it.
- **Capability overreach** — does a tool grant filesystem, shell, or credential
  access with no real scoping on the input, or does its schema actually
  constrain what it can touch.
- **Known-bad signatures** — does it match a documented supply-chain incident,
  or contain an obfuscated payload hiding in plain text.

A fourth check — semantic detection of prompt-injection phrasing in tool
descriptions, powered by Claude — is built and tested but currently gated off
by default while we get billing in front of it. It'll ship as a paid tier.

We don't publish the exact detection patterns here on purpose — the same
tradeoff every open security scanner makes, from antivirus signatures to WAF
rules. What's public is the methodology and the results; the implementation
that decides what specifically trips a finding stays close, so it's harder to
write a tool description that slips past it on purpose.

## Using it

**As an MCP server:**

```json
{
  "mcpServers": {
    "preflight": { "url": "https://preflight.allthepossibles.com/mcp" }
  }
}
```

Then call `scan_mcp_server` before wiring anything new into your agent's toolset.

**Or the REST API directly:**

```
curl -X POST https://preflight.allthepossibles.com/api/scan \
  -H "content-type: application/json" \
  -d '{"url": "https://example.com/mcp"}'
```

Full API reference: https://preflight.allthepossibles.com/docs

## Free during preview

20 scans/minute per caller IP, no signup, no API key. That's a real limit on
a real budget — if it's getting in the way of a legitimate use case, open an
issue and say so.

## Reporting a false positive or false negative

This is the single most useful thing you can do right now. Open an issue with
the manifest or server URL that got it wrong (redact anything sensitive first)
and what you expected instead. The ruleset is new and gets better in public,
not by us guessing in private.

## Support

Open an issue in this repo. That's it — no ticket system, no SLA yet, just a
real person reading it.
