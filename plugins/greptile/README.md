# Greptile

[Greptile](https://greptile.com) is an AI code review agent for GitHub and GitLab that automatically reviews pull requests. This plugin gives Claude Code two ways to work with it:

- the **Greptile MCP server**, for reading and resolving review results, and for searching your organization's knowledge base and coding patterns
- the **Greptile CLI**, for dispatching a review of your working branch before a pull request exists

They are two ends of one pipeline. The CLI dispatches reviews; the MCP server reads them back — both the ones the CLI dispatched (`source: "headless"`) and the ones Greptile ran on your pull requests (`source: "pr"`).

## Setup

Nothing to install and no API key to create.

**MCP server.** Open the `/mcp` menu and authenticate **greptile**. Your browser opens [auth.greptile.com](https://auth.greptile.com); sign in and approve access. Claude Code stores and refreshes the tokens.

**CLI.** Run `/greptile:login` once and sign in through the same OAuth provider. The CLI ships with this plugin, so there is no npm or Homebrew install to do — but it is a Node program, so Node must be on your machine.

The two sign-ins are separate: same Greptile account, same OAuth provider, but the CLI keeps its own credentials in `~/.greptile/auth.json` while Claude Code keeps the MCP tokens in its own store. Use whichever surface you need; you only have to sign in to that one.

## Commands

- `/greptile:review` - Review the current branch against its base. Accepts a base branch and free-text instructions ("focus on the auth changes").
- `/greptile:login` - Sign the CLI in to your Greptile account.

## Tools

### Pull requests
- `list_merge_requests` / `list_pull_requests` - List PRs, filtered by repository, branch, author, or state
- `get_merge_request` - Detailed PR info, including which review comments have been addressed by later commits
- `list_merge_request_comments` - All comments on a PR, with Greptile, human, and other bot comments distinguished by `sourceType`

### Code reviews
- `list_code_reviews` - List code reviews, filtered by repository or status
- `get_code_review` - Full review body, status, summary citations, and review metadata
- `trigger_code_review` - Start a Greptile review on a pull request (GitHub and GitLab)
- `search_greptile_comments` - Search Greptile's review comments across every review, on pull requests and on headless CLI runs alike

### Knowledge base
- `list_knowledge_bases` - Repositories your organization has knowledge base data for
- `list_knowledge_base_documents` - Document paths in a repository's published knowledge base
- `get_knowledge_base_document` - Markdown body of a single knowledge base document
- `search_knowledge_base` - Substring search across one repository's knowledge base

### Custom context
- `list_custom_context` - Your organization's coding patterns and rules
- `get_custom_context` - Details for one entry, including evidence and linked comments
- `search_custom_context` - Search entries by content
- `create_custom_context` - Create a new entry, either a custom instruction (the default) or a pattern

### Analytics
- `get_analytics_overview` - Summary metrics and period changes, chart series, and repository, contributor, and pull request rankings
- `list_analytics_findings` - Findings with severity and security totals and trends, filterable by team, repository, author, severity, or status
- `list_analytics_filter_options` - The teams, repositories, and authors available to you as analytics filters

## Example usage

- "Review my current branch with Greptile and fix what it finds"
- "Show me Greptile's comments on my current PR and help me resolve them"
- "What issues did Greptile find on PR #123?"
- "Search our knowledge base for how authentication works in this repo"

## Bundled CLI

`scripts/greptile.mjs` is the Greptile CLI, vendored from the published npm package
`greptile` (its `dist/greptile.js`, renamed only so Node reads it as ESM without a
sibling `package.json`). `scripts/greptile.version` records which release it is, and
CI verifies the file byte-for-byte against that version's npm tarball, so the copy
running here is the same one npm serves.

Because the CLI ships with the plugin, it updates with the plugin — not through
`greptile update`, `npm`, or `brew`. Any separate `greptile` you have installed is
untouched and unused by these commands, though both share your login at
`~/.greptile/auth.json`.

## Network access

This plugin registers no hooks and sends no telemetry or analytics. Everything
it contacts, it contacts because you asked it to:

- `api.greptile.com` — the MCP server, and the CLI's API.
- `auth.greptile.com` — OAuth sign-in, for both surfaces.
- `app.greptile.com` — link targets printed in review output.
- `127.0.0.1` — a loopback listener the CLI opens to receive the OAuth
  callback during `/greptile:login`, closed as soon as the redirect arrives.

There is one exception, and it is the only thing either surface downloads.
When a review summary contains a Mermaid diagram, the CLI renders it with
`mmdr`, a small standalone binary fetched on first use from
[github.com/1jehuang/mermaid-rs-renderer](https://github.com/1jehuang/mermaid-rs-renderer)
into `~/.cache/greptile/bin/`. The archive is checked against a SHA-256 pinned
per platform inside the bundle and is deleted rather than run if the hash does
not match; the download announces itself on stderr, and failing to get it
degrades the diagram to a link instead of failing the review. Setting
`GREPTILE_NO_AUTO_INSTALL=1` skips it. **`/greptile:review` already sets that
variable**, so the plugin does not download it — the code path is reachable
only if you invoke the bundled CLI yourself without it.

## Documentation

See [greptile.com/docs/mcp-v2/overview](https://www.greptile.com/docs/mcp-v2/overview).
