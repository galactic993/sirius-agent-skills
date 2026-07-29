---
name: merge-approved-pull-requests
description: Find ready GitHub pull requests inside an explicit repository allowlist that carry both human-review and a human-applied merge label; verify the approved head and required quality gates; merge only a clean unchanged head; and complete the linked Sirius Addness planned Todo. Use as the merge component of run-sirius or for an explicitly bounded finalization sweep.
---

# Merge Approved Pull Requests

Merge only pull requests that satisfy every human and technical gate, then reconcile the linked Addness planned Todo. Todoist is retired and must not be read or written.

Read and enforce the shared [Addness task lifecycle and work-claim contract](../run-sirius/references/addness-work-claim-preflight.md) and [Sirius implementation and review contract](../run-sirius/references/implementation-review-contract.md). Apply it before any Codex task create, fork, delegation, message, or resume used for finalization.

## Choose tools by capability

For each operation, prefer an authenticated official or trusted MCP/connector only when it provides the required read, write, and re-fetch verification; otherwise use an official or trusted CLI, and use UI only when neither is capable. Confirm actual schemas and permissions rather than tool presence, record every fallback reason in the run ledger, and verify mutations through machine-readable MCP/API or CLI output.

For any web UI fallback, read the Codex in-app Browser skill, query `agent.browsers.list()` or the available equivalent, and prefer a suitable Codex-exposed existing Chrome session/profile. If none is exposed, use the in-app Browser and report the limitation. Never use browser-use CLI or Computer Use to operate a web browser. Computer Use is reserved for permitted native desktop apps. Never use unofficial APIs or screen scraping as a shortcut. Obtain explicit user approval before any `terraform` or `gcloud` write or update.

## Eligibility

Require:

- repository is in the explicit allowlist;
- pull request is open, ready, and labeled `human-review` plus `merge`;
- a non-bot human added `merge` after the PR became ready;
- the current head SHA is the same head the human approved;
- required checks and the latest Codex review are clean;
- the body contains exactly one `Sirius Addness Todo: <planned-todo-id>` line.
- the PR satisfies every applicable same-change test, E2E, content, evidence, and exception requirement in the shared implementation and review contract on the current head SHA.

Re-fetch the linked planned Todo through Addness MCP. Require it to be open, uniquely keyed, and `[human-review][implement]`. Do not infer approval from comments, reviews, assignment, or Addness state without GitHub `merge`.

## Inspect before mutation

Paginate open pull requests per allowlisted repository. Re-fetch draft state, labels, head/base SHA, mergeability, reviews, required checks, commits, files, body, and label timeline. Record the human merge-label actor, timestamp, and approved head.

Read [pre-merge-checklist.md](references/pre-merge-checklist.md) completely. Inspect the full diff, planned Todo acceptance criteria, repository instructions, tests, documentation, security boundaries, and dependencies through read-only APIs or existing artifacts.

After read-only eligibility and checklist inspection succeed, perform and read-back verify the Addness lifecycle claim with `[working][merge]` as the first mutation for finalization. Include the approved head SHA, PR, run ID, and merge checkpoint in `currentStatus`. Do not change a checkout, label, head, or repository before this claim succeeds.

After the verified claim, use a clean isolated worktree for any local verification and preserve unrelated work.

If maintenance changes the head, remove `merge`, retain `human-review`, return the Todo to `[working][implement]`, and require a new clean Codex review and a new human `merge` label. Never merge a head that changed after approval.

## Final verification and merge

Immediately before merge prove:

- PR is still open, ready, and has both labels;
- the human-approved head is unchanged;
- required checks and latest Codex review are clean;
- mergeability is acceptable;
- linked Addness planned Todo remains uniquely `[working][merge]` with the current approved head and merge checkpoint;
- no unresolved security or acceptance finding remains.
- all required test and E2E evidence is accessible to the reviewer, contains no exposed secret or private data, and was captured on the unchanged human-approved head.

Merge with the repository's allowed method. Do not use administrator bypasses or unsafe overrides. Re-fetch `mergedAt`, `mergedBy`, and merge commit.

## Complete Addness idempotently

After verified merge:

1. update the planned Todo with the PR URL, merge actor, merge commit, checks, and timestamp;
2. invoke the documented Addness planned Todo completion operation;
3. re-fetch and verify completion and unique source-key cardinality.

If Addness MCP does not publish a completion operation, apply the capability-based priority above without inventing a Goal-based substitute. If no safe fallback can complete and re-fetch the exact planned Todo, or completion verification fails, the GitHub merge remains authoritative; report `reconciliation_required` with the exact PR and planned Todo ID.

If the PR was already verified merged and the linked planned Todo remains open, perform only this idempotent Addness reconciliation.

Return repository/PR, approval actor and head, checks, merge result and SHA, linked planned Todo ID, and Addness completion result. Never expose private source text or credentials.
