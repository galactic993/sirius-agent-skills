# Sirius Agent Skills

Resumable delivery-loop skills for coordinating Slack and native LINE intake, deduplicated [Addness](https://addness.com) planned Todo creation, dependency-aware implementation, Codex review, human approval, and SHA-pinned GitHub merge.

These skills are designed to be driven by a home-scoped project document at `~/.sirius.md`. They do **not** embed your repositories, channels, chats, or customer data.

## What is included

| Skill | Role |
| --- | --- |
| `run-sirius` | Orchestrator and shared contracts |
| `slack-new-activity-to-issue` | Slack intake (legacy name; creates Addness planned Todos) |
| `line-new-chats-to-issue` | Native LINE desktop intake |
| `create-addness-task-from-action` | Validation, deduplication, planned Todo creation |
| `execute-addness-implement-tasks` | Approved implementation workers |
| `merge-approved-pull-requests` | Human-approved merge and Addness completion |
| `write-like-kazuki` | Optional outbound drafting helper |

## Install

Copy the `skills/` directory into your agent skills location, for example:

```bash
cp -R skills/* ~/.agents/skills/
# or
cp -R skills/* ~/.claude/skills/
```

Then create your local project document:

```bash
cp examples/sirius.example.md ~/.sirius.md
```

Edit `~/.sirius.md` with your exact GitHub repositories, Slack workspace/conversation pairs, native LINE chat titles, Addness project keys, and notes.

## Required tooling

- Addness MCP with `list_planned_todos`, `create_planned_todo`, and `update_planned_todo`
- Authenticated `gh`, `git`, and `codex`
- Slack and native LINE intake components when those sources are in scope

Addness MCP endpoint used by the skills:

```text
https://vt.api.addness.com/mcp
```

## Human gates

Only a human may:

- add `[implement]` to an Addness planned Todo title
- add the GitHub `merge` label to a ready pull request

Source intake may create `[queued]` or evidence-backed `[waiting]` tasks only.

## Run once

In a supported Codex or Claude Code session with the skills installed:

```text
/run-sirius
```

For periodic execution, combine with your loop skill, for example:

```text
/loop 5m /run-sirius
```

## Local state paths

Sirius stores only IDs, hashes, scope, cursors, timestamps, and counts under:

- `~/.sirius/state`
- `~/.sirius/runs`

Never commit your real `~/.sirius.md`, migration ledgers, run ledgers, or auth files. This repository's `.gitignore` lists the patterns to keep out of version control.

## License

MIT
