---
allowed-tools: Bash(npx:*)
description: Review the current branch with Greptile
disable-model-invocation: false
---

Run a Greptile review on the current branch, against its base branch.

Run this command, streaming its output:

```
npx -y greptile@latest review --agent
```

Notes:

- `--agent` selects plain output intended for AI agents. Always pass it.
- If the user named a base branch, add `--branch <BRANCH>`.
- If the user described what to focus on, pass it through as
  `--instructions "<their words>"`.
- If the run reports that no one is signed in, tell the user to run
  `/greptile:login` — do not attempt an interactive login yourself.

This dispatches a **headless** review: it reviews the working branch and does
not need an open pull request. The review is stored on the user's Greptile
account, so the `greptile` MCP tools in this plugin can read it back —
`list_code_reviews` and `get_code_review` return it with `source: "headless"`.

After the review completes, summarize the findings and offer to fix them. Use
`get_code_review` for the full body of any finding you need more detail on.
