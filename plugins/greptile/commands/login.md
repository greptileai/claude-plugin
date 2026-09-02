---
allowed-tools: Bash(npx:*)
description: Sign the Greptile CLI in to your Greptile account
disable-model-invocation: false
---

Sign the Greptile CLI in to the user's Greptile account.

Tell the user a browser window will open, then run:

```
npx -y greptile@latest login
```

This opens `auth.greptile.com` and completes an OAuth flow. It signs in the
**CLI**, which keeps its own credentials in `~/.greptile/auth.json`.

The `greptile` MCP server in this plugin authenticates separately, through
Claude Code's own `/mcp` menu — same Greptile account, same OAuth provider,
but a separate token store. A user who wants both surfaces signs in twice, and
signing in here does not authenticate the MCP server (or the reverse).

If the user wanted MCP tools rather than the CLI, point them at `/mcp` instead.
