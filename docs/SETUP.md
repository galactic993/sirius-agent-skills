# Setup Guide

This guide installs Sirius agent skills locally, connects the required tools, and creates your home-scoped `~/.sirius.md` project document.

Sirius never stores credentials in git. Keep secrets in MCP OAuth, your shell keychain, or local auth files that are ignored by `.gitignore`.

## 1. Prerequisites

Install and authenticate these tools before the first run:

| Tool | Purpose |
| --- | --- |
| [Codex CLI](https://developers.openai.com/codex/cli) or Claude Code | Coordinator and worker sessions |
| [GitHub CLI](https://cli.github.com/) (`gh`) | Repository and pull request operations |
| `git` | Branch and checkout work |
| Addness account | Canonical planned Todo queue |

Optional, depending on your configured sources:

| Tool | Purpose |
| --- | --- |
| Slack MCP or desktop Slack | Slack intake and status refresh |
| Native LINE desktop + Computer Use | LINE intake on macOS |
| Loop skill | Periodic `/loop 5m /run-sirius` execution |

Verify the basics:

```bash
codex --version
gh auth status
git --version
```

## 2. Install the skills

Clone this repository, then copy the skills into your agent skills directory.

```bash
git clone https://github.com/galactic993/sirius-agent-skills.git
cd sirius-agent-skills
```

### Codex / Agents layout

```bash
mkdir -p ~/.agents/skills
cp -R skills/* ~/.agents/skills/
```

### Claude Code layout

```bash
mkdir -p ~/.claude/skills
cp -R skills/* ~/.claude/skills/
```

After copying, confirm the orchestrator exists:

```bash
test -f ~/.agents/skills/run-sirius/SKILL.md || test -f ~/.claude/skills/run-sirius/SKILL.md
```

## 3. Connect Addness MCP

Sirius requires Addness planned Todo as the only task queue.

Add this to your Codex config at `~/.codex/config.toml`:

```toml
[mcp_servers.addness]
url = "https://vt.api.addness.com/mcp"
oauth_resource = "https://vt.api.addness.com/mcp"

[mcp_servers.addness.tools.update_planned_todo]
approval_mode = "approve"

[mcp_servers.addness.tools.write_goal_detail]
approval_mode = "approve"

[mcp_servers.addness.tools.update_goal]
approval_mode = "approve"
```

If you use Cursor instead of Codex, register the same MCP URL in your Cursor MCP settings and authenticate OAuth when prompted.

Verify the server is visible:

```bash
codex mcp list
```

You should see `addness` with OAuth authenticated.

Required Addness operations:

- `list_planned_todos`
- `create_planned_todo`
- `update_planned_todo`
- a documented completion operation for finished work

Sirius does **not** use Addness Goals as the task queue.

## 4. Create `~/.sirius.md`

Copy the template and edit it with your exact project scope:

```bash
cp examples/sirius.template.md ~/.sirius.md
${EDITOR:-nano} ~/.sirius.md
```

Rules:

- Use exact `owner/repository` values or GitHub URLs
- Use exact Slack workspace plus conversation ID
- Use exact native LINE desktop chat titles
- Do not put API keys, tokens, or passwords in this file
- Do not infer repositories from the current checkout
- If routing is ambiguous, document the confirmation rule instead of guessing

See [examples/sirius.template.md](../examples/sirius.template.md) for a multi-project example.

Minimal single-project start:

```bash
cp examples/sirius.example.md ~/.sirius.md
```

## 5. Prepare GitHub workflow labels

For every repository listed in `~/.sirius.md`, ensure these pull request labels exist:

- `human-review`
- `merge`

Sirius may create missing labels, but only a human may apply `merge`.

Example:

```bash
gh label create human-review --repo your-org/example-api --color EDEDED --force
gh label create merge --repo your-org/example-api --color 0E8A16 --force
```

## 6. Create local runtime directories

Sirius stores checkpoints and run ledgers locally:

```bash
mkdir -p ~/.sirius/state ~/.sirius/runs
```

These directories must stay out of git. They are covered by this repository's `.gitignore` patterns if you keep a local working copy.

## 7. First run

Open Codex or Claude Code in any checkout. The project document is always read from your home directory, not from the repository you have open.

Run once:

```text
/run-sirius
```

Expected first-run behavior:

1. Read and freeze scope from `~/.sirius.md`
2. Verify Addness MCP, `gh`, `git`, and component skills
3. Inspect merge candidates, source checkpoints, and eligible work
4. Stop safely if Addness, routing, or a source component is unavailable

Run periodically:

```text
/loop 5m /run-sirius
```

## 8. Optional source connectors

### Slack

Connect a Slack MCP or authenticated Slack connector that can read the exact workspace and conversation IDs written in `~/.sirius.md`.

The intake skill name is legacy: `slack-new-activity-to-issue`. It creates Addness planned Todos, not GitHub Issues.

### LINE

LINE intake requires the native LINE desktop app on macOS and Computer Use access to the exact chat titles listed in `~/.sirius.md`.

Do not use LINE for Chrome.

### Outbound drafting

`write-like-kazuki` is optional. Use it only when you explicitly request an outbound draft or send action.

## 9. Human gates you must keep

| Gate | Who | Effect |
| --- | --- | --- |
| `[implement]` in Addness title | Human | Allows automated implementation |
| GitHub `merge` label | Human | Allows final merge |
| Routing confirmation | Human | Resolves ambiguous shared chats or missing repos |

Intake may create only:

- `[queued]`
- evidence-backed `[waiting]`

## 10. Troubleshooting

### Addness MCP unavailable

Symptom: run stops before any mutation.

Fix:

1. Confirm `codex mcp list` shows `addness` as OAuth authenticated
2. Re-authenticate Addness MCP
3. Confirm `list_planned_todos` is available in the connected server

### Slack workspace mismatch

Symptom: configured workspace is not visible to the connected Slack connector.

Fix:

- Use the exact workspace and conversation IDs from `~/.sirius.md`
- Do not infer channels from app visibility

### Shared LINE chat ambiguity

Symptom: one chat spans multiple projects.

Fix:

- Document the disambiguation rule in `~/.sirius.md`
- Let Sirius pause and ask before creating or implementing work

### Old Todoist workflow still referenced

Sirius now uses Addness planned Todo only. Do not point a new setup at Todoist `ALL` for normal runs.

## 11. Recommended file layout

```text
~/.agents/skills/run-sirius/
~/.agents/skills/create-addness-task-from-action/
~/.agents/skills/execute-addness-implement-tasks/
~/.agents/skills/merge-approved-pull-requests/
~/.agents/skills/slack-new-activity-to-issue/
~/.agents/skills/line-new-chats-to-issue/
~/.sirius.md
~/.sirius/state/
~/.sirius/runs/
~/.codex/config.toml
```

## Next steps

1. Fill in [examples/sirius.template.md](../examples/sirius.template.md)
2. Run `/run-sirius`
3. Review the run ledger at `~/.sirius/runs/<run-id>.md`
4. Add `[implement]` only to tasks you want automated workers to start
