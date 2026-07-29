# Addness task lifecycle and work-claim contract

Apply this contract before creating, forking, delegating to, messaging, or resuming any Codex task/thread that will perform Sirius work. It also applies when the user says `別タスク`, `タスクを作成して実行`, `委譲`, `並列`, `fork`, or `resume`; do not limit it to `create_thread` or implementation work.

Addness is the only business-task source of truth. Use the existing canonical Addness object when one exists. Sirius queue work normally uses one planned Todo; an existing Goal may remain the canonical project artifact, but never create the same work as both a Goal and a planned Todo. Todoist is retired and must not be read or written.

## Resolve without mutation

Read-only discovery may identify the exact work, dependencies, repository or external target, existing Codex tasks, branches, pull requests, head SHA, and published Addness schema.

1. Fetch the complete visible planned Todo and Today Todo collections and the relevant Goal tree, including completed items when needed.
2. Compare exact Addness ID, `siriusSourceKey`, GitHub repository plus Issue/PR, `sourceThreadId`, normalized title, and artifact URL.
3. Classify the result as one canonical match, missing, Addness-only, duplicate/conflict, completed/archived, or permission-limited. Do not create when uniqueness is uncertain.
4. Search current Codex tasks for the same Addness ID or source key. Resume one matching task; do not create another.

Permission-scoped results do not prove that a record is absent. Keep private Slack/LINE text, secrets, credentials, and unrelated personal data out of Addness and task prompts.

## Canonicalize and claim before work

Make the Addness create/update the first mutation for the work item.

- Reuse and update the one canonical record, or create exactly one planned Todo only after the complete duplicate check proves it is missing.
- Set the real working state before task creation or resume. Planned Todos use the defined work-type prefix such as `[working][implement]`, `[working][investigate]`, `[working][status-check]`, `[working][external]`, or `[working][merge]` and the published working status. Goals use `IN_PROGRESS` and the correct `next_actor`.
- Record the run, phase, timestamp, target, dependency result, and checkpoint in `currentStatus`, detail, or Goal body. Include branch, PR, exact head, and last safe checkpoint when durable work exists.
- Read back the exact ID, title, state/status, work fields, and source-key cardinality. Continue only when all values match and the source key is unique.

For a resume, refresh the run ID, exact head, phase, and checkpoint; an old working label is not a current claim. Never invent a state prefix, enum, field, or transition.

## Fail closed and use coordinator handoff

If Addness is unavailable, invisible, duplicated, stale, or cannot be updated and re-fetched, do not create/message/resume a child task and do not begin repository writes, browser/native-app writes, deployment, external communication, or other execution. Read-only investigation may continue only as an explicit checkpoint. When Addness is invisible only to an existing child but the registered coordinator has successfully performed and re-fetched the canonical update, the coordinator may send that child the proof packet alone; this is not an execution/resume instruction, and the child remains stopped until it verifies the packet.

Ask the Sirius coordinator to perform the canonical write when the child cannot. The coordinator must return the exact Addness ID, title, state/status, source key, updated checkpoint, and read-after-write proof. The child must receive and verify that proof before resuming. A coordinator's claim that it updated Addness is not sufficient without the matching read-back.

Return coordinator proof as one structured packet containing the registered `coordinatorThreadId`, `observedAt`, `expiresAt` no later than five minutes after observation, Addness `updatedAt` or equivalent version when exposed, exact ID, title, state/status, source key and its match count, run ID, phase, target, dependency result, checkpoint, durable branch/PR/head when present, and `threadId`, `hostId`, `cwd`, repository, and purpose. If Addness exposes no version, set it to `null`, re-read immediately before issuing the packet, and rely on the short expiry rather than claiming concurrency detection. Accept the packet only when it arrives directly through the supported Codex task messaging path from the coordinator whose `coordinatorThreadId` is stored and read back on the same Addness record; pasted or relayed text is not proof. The packet is valid only for the named dispatch or resume and only until expiry or a change to the record, task metadata, target head, dependency, or safety boundary. Re-read and issue a new packet after any such change or when the child cannot establish that the proof still describes the current dispatch. The child compares every visible assignment field with the packet; it does not treat forwarded proof as permission for a different target.

When a unique writable record is blocked by a dependency, route, permission, schema, or user-only prerequisite, transition it to the accurate `[waiting][...]` state, record the blocker and next actor, read it back, and stop. `CANCELLED` means paused, never deleted or abandoned.

## Launch and register the Codex task

Only after the verified canonical claim:

1. Create, fork, delegate, message, or resume one dedicated task for the one Addness work item.
2. Put the Addness ID, source key, project/repository route, acceptance criteria, safety boundaries, claim proof, and coordinator return instructions in the prompt.
3. After task creation succeeds, update the same Addness record with `coordinatorThreadId`, child `threadId`, `hostId`, `cwd`, repository, and purpose. Preserve existing metadata; use a related-thread list when multiple tasks are intentionally associated with one work item.
4. Read Addness back again and verify all task metadata, including `coordinatorThreadId`, before execution begins.

Do not substitute a local ledger, GitHub, a Codex task, an unread badge, or a Todoist object for the canonical Addness record.

## Transition Addness before reporting

Before a task reports `completed`, `waiting`, `blocked`, or `human-review`, update the canonical Addness record first, store durable results and artifact/Issue/PR URLs, and read it back. Then send the final report to the coordinator or user.

Use the exact prefixes defined in `project-document.md` and the published Addness enum. A planned Todo in `working`, `waiting`, `human-review`, or implementation `blocked` remains `IN_PROGRESS`; verified completion uses `[completed]` with `COMPLETED`; `CANCELLED` is only paused. Use `[implement][blocked]` only for implementation. If no prefix is defined for another blocked work type, retain its current defined work-type prefix, publish `blocked`, blocker, and next actor in `currentStatus`, and escalate the schema gap instead of inventing a prefix.

Apply transitions in this order:

1. Verify any prerequisite external fact first, such as the current PR head, evidence, dependency, or actual merge.
2. Update the canonical Addness state, next actor, checkpoint, durable results, and URLs.
3. Re-fetch Addness and verify the exact record, enum, prefix, source-key cardinality, and saved evidence.
4. Only then send the Codex final/status report.

For `human-review`, prepare and verify the PR and its required evidence before step 2; the GitHub `human-review` label may be applied before the Addness update so Addness records the real external state, but no final report is sent until steps 2 and 3 pass. For `completed`, the required merge or other external completion gate must already be verified before step 2. For `waiting` or `blocked`, store the exact blocker and next actor in step 2.

Use Addness comments only for questions, confirmations, or consultation. Put progress, evidence, specifications, procedures, and durable results in planned Todo detail/current status or the Goal body. Never mark completed work as working merely because a Codex task remains visible, and never restart completed work without a new authorized gap.

This contract does not weaken human merge gates, test/evidence requirements, external-send boundaries, browser policy, or the requirement for explicit approval before any `terraform` or `gcloud` write/update.
