# Sirius Agent Skills

Resumable delivery-loop skills for coordinating Slack and native LINE intake, deduplicated [Addness](https://addness.com) planned Todo creation, dependency-aware implementation, Codex review, human approval, and SHA-pinned GitHub merge.

These skills are designed to be driven by a home-scoped project document at `~/.sirius.md`. They do **not** embed your repositories, channels, chats, or customer data.

## Quick start

```bash
git clone https://github.com/galactic993/sirius-agent-skills.git
cd sirius-agent-skills

# Install skills
mkdir -p ~/.agents/skills
cp -R skills/* ~/.agents/skills/

# Create your local project document
cp examples/sirius.template.md ~/.sirius.md
${EDITOR:-nano} ~/.sirius.md

# Prepare local runtime directories
mkdir -p ~/.sirius/state ~/.sirius/runs
```

Then connect Addness MCP, authenticate `gh`, and run:

```text
/run-sirius
```

Full instructions: [docs/SETUP.md](docs/SETUP.md)

Project document templates:

- [examples/sirius.template.md](examples/sirius.template.md) — recommended multi-project template
- [examples/sirius.example.md](examples/sirius.example.md) — minimal starter

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

## Install locations

Copy the `skills/` directory into your agent skills location:

```bash
cp -R skills/* ~/.agents/skills/
# or
cp -R skills/* ~/.claude/skills/
```

Your real project scope always lives in:

```bash
~/.sirius.md
```

Never commit that file to git.

## Required tooling

- Addness MCP with `list_planned_todos`, `create_planned_todo`, and `update_planned_todo`
- Authenticated `gh`, `git`, and `codex`
- Slack and native LINE intake components when those sources are in scope

Addness MCP endpoint:

```text
https://vt.api.addness.com/mcp
```

Example Codex config:

```toml
[mcp_servers.addness]
url = "https://vt.api.addness.com/mcp"
oauth_resource = "https://vt.api.addness.com/mcp"
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

For periodic execution, combine with your loop skill:

```text
/loop 5m /run-sirius
```

## Local state paths

Sirius stores only IDs, hashes, scope, cursors, timestamps, and counts under:

- `~/.sirius/state`
- `~/.sirius/runs`

Never commit your real `~/.sirius.md`, migration ledgers, run ledgers, or auth files. This repository's `.gitignore` lists the patterns to keep out of version control.

## Documentation

- [Setup guide](docs/SETUP.md)
- [Project document template](examples/sirius.template.md)
- [Minimal example project document](examples/sirius.example.md)

## License

MIT
