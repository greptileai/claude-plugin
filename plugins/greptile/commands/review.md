---
allowed-tools: Bash(npx:*), mcp__plugin_greptile_greptile__get_code_review, mcp__plugin_greptile_greptile__list_code_reviews
argument-hint: [base branch] [what to focus on]
description: Review the current branch with Greptile
---

Run a Greptile review on the current branch, against its base branch.

Build the command from `$ARGUMENTS`, if the user supplied any:

- A branch name they want to review against becomes `--branch <BRANCH>`.
- Anything else they wrote is what they want the reviewer to focus on. Pass it
  as `--instructions '<their words>'` using **single** quotes, so the reviewer
  receives their exact text. If their text contains a `'`, replace each one
  with `'\''` and change nothing else. Never use double quotes: `$` and
  backticks are live inside them, so `$PATH` would silently disappear from the
  instructions and `$(...)` would stop the run at a permission prompt.

```
npx -y greptile@latest review --agent
```

Always pass `--agent`; it selects plain output intended for AI agents.

Give the Bash call a **600000 ms timeout**. A review commonly runs longer than
the two-minute default, and the first invocation also has to fetch the CLI.

If the run reports that no one is signed in, it prints `not signed in. Set
GREPTILE_API_KEY or run 'greptile login --api-key'`. Prefer OAuth over an API
key: tell the user to run `/greptile:login`, and stop there rather than
starting a browser sign-in inside this command.

This dispatches a **headless** review: it reviews the working branch and does
not need an open pull request. The review is stored on the user's Greptile
account, so the `greptile` MCP tools in this plugin can read it back —
`list_code_reviews` and `get_code_review` return it with `source: "headless"`.

After the review completes, summarize the findings and offer to fix them. Use
`get_code_review` for the full body of any finding you need more detail on.
