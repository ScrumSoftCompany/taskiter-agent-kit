# Changelog

## 3.1.0

- Claude Code: document the required token step. `claude plugin install` in a terminal does not ask for the token; set it with `claude plugin configure taskiter@taskiter --values-stdin` (Claude Code 2.1.285 or later) from a hidden prompt, then check `claude mcp list` and open a new session.
- Every skill first checks that the Taskiter MCP tools are available. If not, it says the server is not configured and shows the setup commands for Windows, macOS and Linux; it never asks for the token in chat.
- Codex on macOS: the token prompt works in zsh (no `read -p`).
- Start task: live time tracking starts first (when authorized), then open questions, a full plan reviewed and corrected by the agent and explicit acceptance by the person before any implementation (new rule 13).

## 3.0.1

- Explain how to read task attachments through native MCP image blocks and embedded binary resources, including client support limits and download fallback.

## 3.0.0

- Remove Taskiter task review gates, review queues, review tools and the review-fix skill.
- Finish work through ordinary task statuses and finish_agent_session; retain explicit time choices and human confirmation for writes influenced by external content.

## 2.2.0

- Ask about live tracking and billing at task start unless the person already chose; PMs and admins can correct ordinary time entries without a separate agent-time approval workflow.
- Support billable, client-non-billable (employee paid), and fully non-billable live timers; verify the returned billing mode.

## 2.1.1

- Expose version reporting in skill descriptions, document native shell reading on Windows and the daily session limit of 50.

## 2.1.0

- Write brake after content from outside the team: the agent explains it, shares the confirmation link from prepare_operation and waits for the person instead of retrying.
- Daily and review-fix use list_agent_sessions and the search_tasks review filters (reviewState, reviewableByMe).

## 2.0.0

- Seven skills: start, finish (request review), spec, review-fix, split, daily, sprint-plan.
- Agents no longer start the person's timer (time is tracked only on explicit request, with confirmation in the panel).
- Finishing sends the task to human review with request_review; criteria are proposals, never ticked by the agent.
- Session end hook closes the Taskiter agent session when the client exits.
- Hosted marketplace with versions; get_context reports the kit version and suggests updates.

## 1.0.0

- First starter kit (ZIP): four skills and the MCP server.
