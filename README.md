# Taskiter Agent Kit 3.0.1

Skills, plugin and session hook for working on Taskiter tasks with Claude Code and Codex. People do the work with their own agent; Taskiter keeps the task, the agent session and the work summary.

- MCP server: https://api.taskiter.com/api/v1/mcp
- Create a personal access token (`tsk_...`) in Taskiter: Account, AI assistants. The token is never stored in these files.

## Claude Code

```
claude plugin marketplace add https://github.com/ScrumSoftCompany/taskiter-agent-kit.git
```

```
claude plugin install taskiter@taskiter
```

Claude Code asks for the token when the plugin is enabled and keeps it in the system credential store. Update: `claude plugin update taskiter@taskiter` or enable auto-update in `/plugin`, Marketplaces, taskiter. If you added the `taskiter` MCP server by hand before, remove it (`claude mcp remove taskiter`), otherwise the tools appear twice.

## Codex

1. Store the token without putting it in your shell history (see the install wizard in Taskiter for the exact commands for macOS, Linux and Windows PowerShell).
2. `codex mcp add taskiter --url https://api.taskiter.com/api/v1/mcp --bearer-token-env-var TASKITER_TOKEN`
3. Add the GitHub marketplace: `codex plugin marketplace add https://github.com/ScrumSoftCompany/taskiter-agent-kit.git`.
4. Install the skills and hook: `codex plugin add taskiter@taskiter`.
5. Session end hook: copy `codex/hooks.json` to `~/.codex/hooks.json` (merge if you already have one) and trust it in `/hooks`.

## What the kit sends to Taskiter

`get_context` reports the kit version and client (`kitVersion`, `kitClient`), shown next to the connection in the panel. The session tools send the model, branch and an identifier of the client session; the working directory path is hashed on the server and never stored.

## Security

The token works like a password with the scopes you chose. Revoke it in Taskiter at any time.
