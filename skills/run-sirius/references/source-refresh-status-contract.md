# Source-refresh status contract

Apply this contract whenever the user asks for a task status, training status, customer-response status, current owner, next action, deadline, or any equivalent current-state report.

If this status check is dispatched, forked, delegated, or resumed as a Codex task, apply [addness-work-claim-preflight.md](addness-work-claim-preflight.md) before the task operation and register its task metadata in Addness.

Do not answer from Addness, a run ledger, a prior summary, unread badges, or memory alone. Treat saved state only as the previous checkpoint to refresh.

## Claim the status check first

1. Resolve the exact Addness planned Todo and project through read-only queries.
2. Read the project's exact configured Slack conversations, native LINE chats, email mailboxes/queries, and other source identifiers from `~/.sirius.md`.
3. Read each source's last fully completed cutoff and last successful verification time.
4. Apply [addness-work-claim-preflight.md](addness-work-claim-preflight.md) with `[working][status-check]` as the first mutation for this status check.
5. Put the status-check run ID, requested Todo/project, frozen current cutoff, per-source start cutoff, configured source list, previous task state, and `coverage: pending` in `currentStatus` or its schema-equivalent field.
6. Read Addness back and prove the same planned Todo ID, unique source key, `[working][status-check]` title, working state/status, and checkpoint payload before opening any source.

If the exact Todo is missing or duplicated, or the status-check checkpoint cannot be updated and re-fetched, do not inspect sources and do not report a confirmed-current status.

## Refresh every configured source

For every source configured for the project, scan the half-open interval `last_completed_cutoff < timestamp <= frozen_current_cutoff` without using read/unread state, badges, inbox category, mention state, or notification state to exclude content.

- Slack: enumerate every configured conversation, paginate every root message in the interval, and fetch every reply for every included root thread. Include replies posted in the interval to roots older than the cutoff when the source API can discover them; otherwise disclose that coverage limitation.
- LINE: inspect every configured native chat and every message in the interval, including chats without unread badges. LINE has no thread-reply model; preserve sender and timestamp for each relevant message.
- Email: inspect every configured mailbox/query across all matching messages and complete conversation threads, including sent replies and messages already marked read.
- Other sources: enumerate all configured roots, child replies/events, and pages exposed by the authoritative reader.

Keep the refresh read-only at the source. Do not post, reply, react, mark read intentionally, send email, download attachments, or widen the configured scope. A pending intake confirmation does not permit skipping a user-requested status refresh; keep the status scan separate from intake decisions and do not advance or consume the intake resume cursor.

## Reconcile Addness and answer

After scanning, derive from the newest verified evidence:

- last actor and exact timestamp;
- current factual state;
- next actor;
- next action;
- explicit due date/time, or `not stated`;
- blockers, dependencies, and resolution evidence;
- per-source coverage and cutoff.

Update the same Addness planned Todo so its title, published state/status, and `currentStatus` reflect the actual durable state rather than the temporary status-check claim. Record the status-check run ID, last actor/time, next actor/action, due, per-source coverage, evidence references, completed cutoff, and verification timestamp. Re-fetch the exact ID and source-key cardinality after this write.

Advance a source cutoff only when its full configured scope, roots, replies, and pagination are complete. If any configured source is inaccessible, truncated, ambiguous, or partially paginated:

- set overall coverage to `partial` and record the exact source and constraint;
- preserve that source's last fully completed cutoff and last successful verification time;
- use `[waiting][status-check]` when the status itself cannot be confirmed, unless another existing canonical state is more accurate and the limitation is fully represented in `currentStatus`;
- do not describe saved or partially refreshed state as confirmed current.

Return the freshest verified facts, source coverage, frozen cutoff, Addness verification result, and the last successful verification time for every unavailable source. Use explicit wording such as `current status unconfirmed after <timestamp>` rather than silently presenting stale data.
