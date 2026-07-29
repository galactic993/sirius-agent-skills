# Pre-merge checklist

Apply this checklist proportionally to the pull request. Record evidence, commands, and findings; do not generate filler artifacts.

Read and apply the shared [Addness task lifecycle and work-claim contract](../../run-sirius/references/addness-work-claim-preflight.md). Complete only read-only eligibility checks before claiming, then require its verified `[working][merge]` claim before creating a worktree, running mutating local checks, changing GitHub, or performing the merge.

Read and apply the shared [Sirius implementation and review contract](../../run-sirius/references/implementation-review-contract.md). Missing or stale applicable contract evidence blocks merge.

## 1. Scope and approval

- Re-read the linked Sirius Addness planned Todo or legacy GitHub Issues, acceptance criteria, discussion, reviews, and repository instructions.
- Record the human-applied `merge` event and approved head SHA.
- Confirm every proposed maintenance change stays within that approved scope.
- Detect unrelated files, generated noise, accidental secrets, and unexplained dependency or lockfile changes.

## 2. Durable learning

Capture only verified knowledge likely to prevent repeated investigation or mistakes, for example:

- non-obvious bootstrap, validation, deployment, or recovery commands;
- architecture boundaries and invariants established by the change;
- recurring failure causes and evidence-backed diagnostics;
- repository-specific safety constraints or ownership rules.

Prefer an existing `AGENTS.md`, `CLAUDE.md`, or established docs file. Keep instructions current and operational. Do not include credentials, personal data, transient paths, timestamps, conversational narrative, or facts obvious from nearby code.

## 3. Skill decision

Update an existing skill when its workflow, commands, preconditions, failure handling, or validation contract changed.

Create a new skill only when all are true:

- the procedure will recur across future tasks;
- specialized sequencing or domain knowledge is needed;
- the procedure has a clear trigger and completion contract;
- a skill is more useful than a short project instruction.

Keep the canonical skill in the repository that owns it. Validate `SKILL.md` frontmatter and referenced resources. Never patch generated or installed skill copies as the source of truth.

## 4. Cleanup proof

Before deleting anything:

- search code, tests, docs, build files, CI, runtime loading, reflection, string references, and generated manifests;
- check package manager and compiler dependency graphs where available;
- understand compatibility, migrations, data retention, public API, and rollback impact;
- run targeted tests before and after deletion.

If use cannot be disproven, keep the artifact and report it. Do not turn the pre-merge pass into an unrelated refactor.

## 5. Security review

Inspect relevant categories:

- authentication, authorization, tenancy, roles, and privilege boundaries;
- injection, unsafe deserialization, path traversal, SSRF, XSS, CSRF, and command execution;
- tokens, credentials, logs, telemetry, personal data, and error disclosure;
- dependency vulnerabilities, integrity/lockfiles, license or provenance concerns;
- filesystem, network, subprocess, browser, webhook, and external-service trust boundaries;
- CI workflow permissions, untrusted pull-request execution, release credentials, and artifact publishing;
- infrastructure permissions, public exposure, destructive migrations, backup and rollback.

Use repository-native SAST, dependency audit, secret scanning, and policy checks when available. Never paste secrets into review output. Critical/high findings block merge; unresolved lower-severity findings must be explicitly justified or tracked according to repository policy.

## 6. Final verification

- Verify production-code changes include corresponding tests in the same pull request; verify code-free exceptions state a concrete reason and the closest available validation.
- Re-run formatting, lint, typecheck, unit/integration/E2E tests, build, and relevant manual behavior checks.
- For web UI changes, verify automated browser E2E and the shared contract's complete, sanitized evidence set on the current head.
- For mobile app changes, verify automated E2E on both iOS Simulator and Android Emulator, the framework choice, required video/screenshots, stable selectors, and any separately identified real-device verification.
- Verify the PR contains every required summary, acceptance-to-evidence mapping, command, environment, expected/actual result, bug, risk, test result, and justified exception from the shared contract.
- Validate modified skills and documentation links.
- Run a fresh Codex review on the exact final head.
- Confirm required GitHub checks and reviews pass.
- Re-fetch labels and head SHA immediately before merge.
- Merge with head-SHA matching and verify the resulting merge commit.
