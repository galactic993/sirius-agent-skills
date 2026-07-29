# Sirius implementation and review contract

Apply this contract to every Sirius implementation, worker handoff, review, and merge decision.

Apply [addness-work-claim-preflight.md](addness-work-claim-preflight.md) before every Codex task/thread create, fork, delegation, message, or resume and before reporting a final lifecycle transition.

## Keep tests in the same change

- Whenever production code changes, create or update the corresponding test code in the same branch and pull request.
- Cover the changed behavior at the lowest useful levels and include the required end-to-end path. Do not defer tests to a follow-up task.
- For web UI changes, run automated browser E2E against the exact final head.
- For mobile app changes, run automated E2E on both iOS Simulator and Android Emulator against the exact final head.
- Prefer Maestro for mobile E2E because its flows, stable selectors, screenshots, and video artifacts are agent-friendly. Use Detox or an official platform test framework when the repository architecture or existing suite makes it more reliable; record the choice and reason.
- Use stable semantic selectors or test IDs. Avoid selectors coupled to layout, translated display text, timing, or generated class names when a stable identifier can be added.
- Mark hardware, OS integration, push delivery, camera, Bluetooth, biometric, carrier, or other device-specific behavior as requiring separate real-device verification. Do not represent simulator or emulator coverage as real-device proof.
- For a code-free change such as docs-only, metadata-only, or policy-only work, state the concrete reason tests or E2E do not apply and run the closest available validation.

## Produce honest review evidence

Use sanitized test data and inspect every artifact before publishing it. Never include credentials, tokens, cookies, session identifiers, private Slack or LINE content, or personal information.

For UI work, capture:

- one short video of the primary successful flow;
- screenshots at the initial state, after each major operation, and at completion;
- screenshots of relevant error, validation, empty, loading, permission, offline, or boundary states;
- any responsive states required by the acceptance criteria.

Store large videos in a safe CI artifact area, access-controlled artifact store, or PR attachment. Do not commit large video files to git. Keep local evidence outside the repository unless an established ignored artifact directory exists.

If an artifact cannot be captured, record the exact reason and link or attach the strongest real alternative evidence. Never fabricate a result, link, screenshot, video, command, environment, or state.

## Require a reviewable pull request

Require the pull request body or an attached review comment to contain at least:

1. a concise diff summary;
2. an acceptance-criterion-to-evidence table;
3. detailed reproduction and verification steps with exact commands;
4. the execution environment, including relevant OS, runtime, browser or device target, viewport, and tested URL or route without secrets;
5. the exact tested head SHA;
6. expected and actual results;
7. bugs discovered and fixed while implementing or testing;
8. remaining suspected bugs, risks, limitations, and any separate real-device verification;
9. test results for unit, integration, E2E, build, lint, typecheck, and other applicable repository-native checks;
10. the successful-flow video and the required screenshots, tied to the tested head SHA; and
11. any justified code-free or evidence-capture exception, with real alternative validation.

A head change invalidates affected test and UI evidence. Re-run the impacted checks and recapture stale artifacts on the new head before human review or merge.

Do not move work to `human-review`, apply the `human-review` label, or merge while required tests, E2E coverage, PR details, or evidence are missing or stale. A documented exception is valid only when the change is genuinely code-free or the user explicitly accepts the stated alternative; it never bypasses security findings, required checks, fresh Codex review, or the human-applied `merge` gate.
