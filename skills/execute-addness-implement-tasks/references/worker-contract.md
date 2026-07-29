# Addness worker contract

Read and follow the shared [Addness task lifecycle and work-claim contract](../../run-sirius/references/addness-work-claim-preflight.md) and [Sirius implementation and review contract](../../run-sirius/references/implementation-review-contract.md) completely.

Before changing a checkout, branch, code, browser, deployment, external system, or work-item checkpoint, verify the coordinator's current Addness claim proof and the registered `coordinatorThreadId`, child `threadId`, `hostId`, `cwd`, repository, and purpose. If Addness is missing, invisible, stale, duplicated, or not read back, do not start. Return control for a coordinator-side update and resume only after receiving and verifying its exact read-back proof. Read-only investigation is allowed only as an explicit checkpoint. For a resume, require the refreshed run ID, exact head, phase, and last safe checkpoint.

Each worker receives exactly one Addness planned Todo, one verified repository, and one isolated checkout. The worker:

1. reads repository instructions and the Todo's acceptance criteria;
2. implements only that scope;
3. preserves unrelated work;
4. creates or updates tests in the same change whenever production code changes;
5. runs repository-native checks and the applicable web or mobile automated E2E on the exact head;
6. commits and pushes one deterministic branch without committing large video files;
7. creates or updates one draft pull request containing the planned Todo ID, source key, and every field and artifact required by the shared contract;
8. reports the branch, commit, PR, checks, evidence links and local absolute paths, exceptions, risks, and blockers.

The worker must be a dedicated, user-visible Codex task created or resumed through the supported task tools. The Sirius coordinator conversation must never act as the worker. The task prompt must carry the planned Todo ID and source key, and the worker must report its durable result back to the coordinator.

Before web-browser verification, query `agent.browsers.list()` or the available equivalent and prefer a suitable Codex-exposed existing Chrome session/profile. If none is exposed, use the Codex in-app Browser and report the limitation. Never use browser-use CLI or Computer Use for web browsing.

Before reporting `completed`, `waiting`, `blocked`, or `human-review`, update the canonical Addness record first with the real state, next actor, durable results, and artifact/Issue/PR URLs, then read it back. Use comments only for questions or confirmations.

The worker must not take another Todo, change the human execution gate, mark the pull request ready, add `merge`, merge, or write to Todoist. It may complete Addness only when the task explicitly delegates that lifecycle transition and the required external completion gate is satisfied.
