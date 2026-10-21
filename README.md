# Taskiter Agent Kit 3.1.0

Skills, plugin and session hook for working on Taskiter tasks with Claude Code and Codex. People do the work with their own agent; Taskiter keeps the task, the agent session and the work summary.

- MCP server: https://api.taskiter.com/api/v1/mcp
- Create a personal access token (`tsk_...`) in Taskiter: Account, AI assistants. The token is never stored in these files. Never paste it into a chat with the agent.

## Claude Code

Requires Claude Code 2.1.285 or later (`claude --version`; update with `claude update`).

1. Add the marketplace and install the plugin:

   ```
   claude plugin marketplace add https://github.com/ScrumSoftCompany/taskiter-agent-kit.git
   claude plugin install taskiter@taskiter
   ```

2. Set the token (required). `claude plugin install` in a terminal does not ask for it, and without it the plugin's MCP server does not start, so the skills have no Taskiter tools. Run the two commands for your system in the same terminal window: the first asks for the token without showing it, the second saves it with the server address. The token stays out of your shell history.

   Windows PowerShell:

   ```
   $s = Read-Host 'Token Taskiter' -AsSecureString
   @{ api_token = [Net.NetworkCredential]::new('', $s).Password.Trim(); api_url = 'https://api.taskiter.com/api/v1/mcp' } | ConvertTo-Json -Compress | claude plugin configure taskiter@taskiter --values-stdin; Remove-Variable s
   ```

   macOS and Linux (bash, zsh):

   ```
   printf 'Token Taskiter: ' && read -rs taskiter_token && echo
   printf '{"api_token":"%s","api_url":"%s"}' "$taskiter_token" 'https://api.taskiter.com/api/v1/mcp' | claude plugin configure taskiter@taskiter --values-stdin; unset taskiter_token
   ```

   Claude Code keeps the token as a sensitive value outside its settings files (macOS Keychain; on Windows and Linux the Claude Code credentials file).

3. Check the connection: `api_token` is `set` and `plugin:taskiter:taskiter` is `Connected`.

   ```
   claude plugin configure taskiter@taskiter
   claude mcp list
   ```

4. Open a new Claude Code session. The plugin's MCP server is loaded when a session starts.

Inside a Claude Code session you can do the same with `/plugin marketplace add https://github.com/ScrumSoftCompany/taskiter-agent-kit.git` and `/plugin install taskiter@taskiter`: after installing, Claude Code opens the "Configure Taskiter" dialog with a masked token field. `/plugin configure taskiter@taskiter` opens the dialog again later. After saving, run `/reload-plugins`.

Update: `claude plugin update taskiter@taskiter` or enable auto-update in `/plugin`, Marketplaces, taskiter. The token is kept across updates. If you added the `taskiter` MCP server by hand before (`claude mcp add`), remove it (`claude mcp remove taskiter`), otherwise the tools appear twice.

When a session ends, Claude Code may print `SessionEnd hook [plugin:taskiter:taskiter/finish_agent_session] failed: ...`. It does not block anything. If it ends with `MCP server 'plugin:taskiter:taskiter' not connected`, the token is not set (step 2). Claude Code 2.1.306 prints it also with the token set (`no MCP client context` after `/exit`, `not valid JSON` in `claude -p`); the agent session is then closed by `taskiter-finish-task` or expires in Taskiter.

## Codex

1. Store the token in the `TASKITER_TOKEN` environment variable without putting it in your shell history.

   Windows PowerShell:

   ```
   $s = Read-Host 'Token Taskiter' -AsSecureString
   [Environment]::SetEnvironmentVariable('TASKITER_TOKEN', [Net.NetworkCredential]::new('', $s).Password, 'User')
   ```

   macOS and Linux (zsh; for bash use `~/.bashrc`):

   ```
   mkdir -p ~/.config/taskiter && printf 'Token Taskiter: ' && read -rs TASKITER_TOKEN && echo
   printf 'export TASKITER_TOKEN=%s\n' "$TASKITER_TOKEN" > ~/.config/taskiter/env && chmod 600 ~/.config/taskiter/env
   echo '[ -f ~/.config/taskiter/env ] && . ~/.config/taskiter/env' >> ~/.zshrc
   ```

2. Open a new terminal window and add the server: `codex mcp add taskiter --url https://api.taskiter.com/api/v1/mcp --bearer-token-env-var TASKITER_TOKEN`
3. Add the GitHub marketplace: `codex plugin marketplace add https://github.com/ScrumSoftCompany/taskiter-agent-kit.git`.
4. Install the skills and hook: `codex plugin add taskiter@taskiter`.
5. Session end hook: copy `codex/hooks.json` to `~/.codex/hooks.json` (merge if you already have one) and trust it in `/hooks`.

## What the kit sends to Taskiter

`get_context` reports the kit version and client (`kitVersion`, `kitClient`), shown next to the connection in the panel. The session tools send the model, branch and an identifier of the client session; the working directory path is hashed on the server and never stored.

## Security

The token works like a password with the scopes you chose. Revoke it in Taskiter at any time.
