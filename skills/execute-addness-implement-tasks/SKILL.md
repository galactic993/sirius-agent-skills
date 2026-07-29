---
name: execute-addness-implement-tasks
description: Discover Sirius planned Todos in Addness that carry the human-applied implement gate; resolve repository routes and dependencies; implement and review independent work; capture real-browser E2E video and screenshot evidence for UI changes; and hand off ready pull requests with human-review. Use as the implementation component of run-sirius or for an explicitly bounded Addness implementation queue.
---

# Execute Addness Implement Tasks

Run the Sirius implementation queue from Addness planned Todos. GitHub is used for branches and pull requests; Todoist and Addness Goals are not part of this queue.

Read the shared [Addness task lifecycle and work-claim contract](../run-sirius/references/addness-work-claim-preflight.md), [worker-contract.md](references/worker-contract.md), and the shared [Sirius implementation and review contract](../run-sirius/references/implementation-review-contract.md) before launching workers.

## Choose tools by capability

For each operation, prefer an authenticated official or trusted MCP/connector only when it provides the required read, write, and re-fetch verification; otherwise use an official or trusted CLI, and use UI only when neither is capable. Confirm actual schemas and permissions rather than tool presence, record every fallback reason in the run ledger, and verify mutations through machine-readable MCP/API or CLI output.

For any web UI fallback or E2E flow, read the Codex in-app Browser skill, query `agent.browsers.list()` or the available equivalent, and prefer a suitable Codex-exposed existing Chrome session/profile. If none is exposed, use the in-app Browser and report the limitation. Never use browser-use CLI or Computer Use to operate a web browser. Computer Use is reserved for permitted native desktop apps. Never use unofficial APIs or screen scraping as a shortcut. Obtain explicit user approval before any `terraform` or `gcloud` write or update.

## Fixed gates

- Start new work only from `[implement]`.
- Resume `[working][implement]` only through the work-claim preflight, including a refreshed run ID, head, phase, and checkpoint. Exclude `[human-review][implement]`, `[waiting][implement]`, `[queued]`, and `[implement][blocked]`.
- Only a human may add `[implement]`.
- Execute each planned Todo in its own dedicated Codex task. Never implement it in the coordinator or intake conversation.
- Never add GitHub `merge`; only a human-applied `merge` label authorizes merge.
- Never complete a planned Todo before the linked pull request is verified merged.

## Claim, then create or resume the worker task

Use only read-only Codex task search for the planned Todo ID and source key before claiming. Then perform and read-back verify the shared Addness lifecycle claim as the first mutation for the work item.

- After the verified claim, resume an existing matching task with `send_message_to_thread`; do not duplicate it.
- Otherwise, after the verified claim, call `list_projects`, resolve the exact saved project, and call `create_thread` for one planned Todo only. Default to a worktree for a Git repository and to a projectless task for non-repository work.
- Include the planned Todo ID, source key, repository, acceptance criteria, dependency state, branch convention, required checks, the shared implementation and review contract, and coordinator handoff instructions in the prompt.
- Record the `coordinatorThreadId` and resulting child `threadId`, `hostId`, `cwd`, repository, and purpose in the run ledger and Addness detail/current status or schema-equivalent metadata.
- Re-fetch Addness after recording worker metadata. Never create or message a worker, checkout a repository, create a branch, or begin implementation before both the claim proof and task-metadata read-back exist.

The calling conversation remains a coordinator. It may dispatch, monitor with `wait_threads`, read status, reconcile Addness/GitHub, and report, but it must not perform the worker's code, browser, deployment, account, submission, or external-write steps itself. Do not substitute shell background jobs or invisible nested executions for a user-visible Codex task.

## Use authoritative tools

Use Addness MCP `list_planned_todos` and `update_planned_todo`, with a full read after every write. Do not read or write Todoist. If Addness MCP lacks an operation, apply the capability-based priority above and stop before mutation when no safe, verifiable fallback exists.

Use GitHub app or authenticated `gh` and `git` for repository and pull-request work. Use Codex CLI for review. Never bypass repository rules or approval gates.

## Discover the complete queue

1. Read exactly `~/.sirius.md` and the frozen project table from `run-sirius`.
2. Paginate all Addness planned Todos and parse `chat_metadata`.
3. Keep only entries with `canonicalSystem: "Addness planned TODO"` and a recognized `siriusProject`.
4. Require exactly one stable `siriusSourceKey`; flag duplicate keys without claiming either entry.
5. Parse project, exact `owner/repository`, dependencies, acceptance criteria, and safe links from the fields.
6. Verify the repository is in the frozen allowlist, is not archived, and can receive a branch and pull request.
7. Search open pull requests for the Addness planned Todo ID or source key; resume a matching draft instead of creating another.

Do not infer a repository from the current checkout. For a unique safely writable Todo with a missing or ambiguous route, record and re-fetch `[waiting][implement]` with the exact routing blocker as the first and only work-item mutation, then stop. If uniqueness or the update/re-fetch path is not provable, perform no mutation and report `confirmation_required`.

## Claim before every start or resume

Apply the shared Addness lifecycle and work-claim contract completely. For implementation, its canonical claim changes the exact unique Todo to `[working][implement]`, sets the published state/status to working, records run/target/head/checkpoint data in `currentStatus`, and reads all fields and source-key uniqueness back before any worker mutation.

Never claim before dependency analysis. When a dependency or route blocks a unique Todo, record and re-fetch `[waiting][implement]` as the only work-item mutation, then stop. When a duplicate source key or unavailable update/re-fetch path prevents a safe state record, perform no mutation and stop. Do not clear a claim that has durable work without recording the recovery state.

## Implement and review

Give each dedicated Codex task one planned Todo and one isolated checkout based on the verified default branch. Preserve unrelated dirty worktrees. Require it to follow the shared implementation and review contract, including same-change tests for every production-code change and the applicable web or mobile automated E2E.

Use a deterministic branch such as `codex/addness-<short-todo-id>-<slug>`. The worker implements only the acceptance criteria, runs repository-native checks, commits and pushes, and creates or updates one draft pull request.

The pull-request body must contain:

`Sirius Addness Todo: <planned-todo-id>`

It must also include the source key and every PR detail and artifact required by the shared implementation and review contract.

Run Codex review against the acceptance criteria and full diff. Fix in-scope findings, rerun affected checks, and repeat until the newest completed review explicitly has no actionable findings. Errors, timeouts, incomplete reviews, pending required checks, and unresolved security findings are not clean.

## Hand off to a human

When checks and review are clean:

1. Re-fetch the pull request and planned Todo.
2. Verify every test, E2E, pull-request field, artifact, exception, and evidence head SHA required by the shared implementation and review contract against the current head.
3. Mark the PR ready for review.
4. Ensure GitHub labels `human-review` and `merge` exist without altering existing definitions.
5. Add `human-review` to the PR.
6. Change the Todo prefix to `[human-review][implement]`.
7. Store the exact PR URL, reviewed and evidence head SHA, checks, evidence links, and timestamp in `currentStatus` or the schema-equivalent field.
8. Re-fetch both objects and verify the state.

Never add `merge`. Leave the Addness Todo open.

For an external dependency, use `[waiting][implement]` and record the blocker. Before durable work exists, a retryable setup failure may return the Todo to `[implement]`; after a branch or draft exists, retain `[working][implement]`.

Return one row per Todo: ID, project, repository, dependency state, branch, PR, Codex result, checks, required evidence links and local absolute paths when applicable, exceptions, risks, and final Addness/GitHub state. Never expose source-private text or credentials.
