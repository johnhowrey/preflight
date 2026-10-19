# Preflight

Check an MCP server before your agent trusts it.

**Live:** https://preflight.allthepossibles.com (docs: `/docs`, machine-readable: `/llms.txt`)
**Built by:** [All the Possibles](https://allthepossibles.com)

## What it does

Preflight scans an MCP server the moment before your agent connects to it,
before there is a human in the loop to notice something is wrong. It checks for:

- **Unauthenticated transport.** Did the server hand its full tool list to a
  client with zero credentials, or accept a garbage bearer token, or an unsigned JWT.
- **Cross-origin redirects.** Does the endpoint redirect to a host the user never configured.
- **Capability overreach.** Does a tool grant filesystem, shell, or credential
  access with no real scoping on the input, or does its schema constrain what it can touch.
- **Blind writes.** A write or delete tool with no read-only counterpart on the same server.
- **Unbounded destructive arguments.** A delete whose path, pattern or filter
  argument accepts any string, with no enum, pattern, or length cap.
- **Known-bad signatures.** A documented supply-chain incident, or an obfuscated
  payload hiding in plain text.
- **Prompt-injection phrasing** in tool descriptions, on Pro: a semantic pass
  powered by Claude, plus the same check for any document an agent is about to read
  (`scan_content`), and trust checks for VS Code extensions and Claude Skills.

Tool annotations are honored: a tool marked `readOnlyHint` is not a write.

The exact detection patterns are not published here on purpose, the same
tradeoff every open security scanner makes. The methodology and the results
are public; what trips a finding stays close, so it is harder to write a tool
description that slips past it.

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

## Pricing

The structural checks are free: no signup, no API key, 20 scans a minute per
caller IP. Basic ($5/month) raises that to 150 a minute. Pro ($19/month) raises
it to 500 and adds the semantic pass and content scans. Pro is in the
[Studio Bundle](https://allthepossibles.com).

Listed in the official MCP Registry as `com.allthepossibles/preflight`.

## Reporting a false positive or false negative

This is the single most useful thing you can do right now. Open an issue with
the manifest or server URL that got it wrong (redact anything sensitive first)
and what you expected instead. The ruleset is new and gets better in public,
not by us guessing in private.

## Support

Open an issue in this repo. That's it, no ticket system, no SLA yet, just a
real person reading it.
