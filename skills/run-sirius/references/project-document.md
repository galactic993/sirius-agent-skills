# Human-readable project document

Use `~/.sirius.md` as the machine-local source of truth for every project visible to Sirius. Read this exact path completely. Never merge a repository-local alternate.

## Required interpretation

For each top-level project, resolve:

- exact Addness project key;
- exact title prefix;
- exact GitHub repositories;
- exact Slack workspace/conversation pairs;
- exact native LINE chat titles;
- exact email mailbox/account and query, folder, sender, recipient, or thread scope;
- exact identifiers and readers for any other status-bearing source;
- dependencies, commands, constraints, and notes.

Accept repositories only as exact `owner/repository` values or GitHub URLs. Accept Slack only with exact workspace and conversation. Accept LINE only with an exact native desktop chat title. Accept email only with an exact connected account plus bounded query/scope. Treat wildcards, broad team names, inferred repositories, ambiguous shared chats, and unbounded mailboxes as unresolved.

A project without a repository may receive a non-code planned Todo. An implementation candidate requires one exact repository before creation.

## Fixed Addness contract

| Policy | Fixed value |
| --- | --- |
| Canonical task object | Addness planned Todo |
| Goal usage | Not used for the Sirius task queue |
| Project grouping | title prefix plus `chat_metadata.siriusProject` |
| Deduplication | unique `chat_metadata.siriusSourceKey` |
| Canonical marker | `chat_metadata.canonicalSystem = "Addness planned TODO"` |
| State prefixes | `[queued]`, `[implement]`, `[working][implement]`, `[working][investigate]`, `[working][status-check]`, `[working][external]`, `[working][merge]`, `[human-review][implement]`, `[waiting]`, work-type `[waiting][...]`, `[implement][blocked]`, `[completed]` |
| GitHub PR labels | `human-review`, `merge` |
| Global workers | 4 |
| Workers per repository | 1 |
| New tasks per run | 10 |
| Implementations per run | 4 |
| Merges per run | 4 |
| Run budget | 240 minutes |
| Lease heartbeat | 5 minutes |
| Stale lease threshold | 180 minutes |
| Retry backoff | 15 minutes |
| Implementation and review | Apply [implementation-review-contract.md](implementation-review-contract.md) to tests, E2E, PR content, and evidence on the exact tested head |
| Current-status reporting | Apply [source-refresh-status-contract.md](source-refresh-status-contract.md) to every user-requested task/training/customer status |
| Codex task lifecycle | Apply [addness-work-claim-preflight.md](addness-work-claim-preflight.md) before every create, fork, delegate, message, or resume |
| State paths | `~/.sirius/state`, `~/.sirius/runs` |

Only a human may add `[implement]` or the GitHub `merge` label. Source intake creates `[queued]` or evidence-backed `[waiting]`. Apply the shared lifecycle contract before every Codex task/thread create, fork, delegation, message, or resume, including requests phrased as `別タスク`, `タスクを作成して実行`, `委譲`, or `並列`. Implementation moves through `[working][implement]` and `[human-review][implement]`; investigation, external operation, and approved merge finalization use their defined working expressions. Verified merge permits planned Todo completion.

Treat all exact sources listed under a project as configured status sources. On every current-status question, refresh all of them through the shared source-refresh contract; do not assume the Todo's originating source is sufficient. Store per-source completed cutoffs and last successful verification times without storing private message bodies.

The shared implementation and review contract is required before `human-review`. Its evidence supplements rather than replaces fresh Codex review, security checks, required checks, and the human merge gate.

Todoist is retired: no project, section, label, task, comment, or completion reads/writes are permitted in normal Sirius runs.

## Fail closed

Do not mutate when the project document, Addness MCP, full planned Todo pagination, project route, source key, repository, or required operation is missing or ambiguous. Re-read after every Addness write.

Store only hashes, scopes, cursors, timestamps, IDs, URLs, worker metadata, and counts. Never store source message bodies, secrets, repository contents, or review transcripts.
