# Sirius project document template

Save this file as `~/.sirius.md`.

Replace every placeholder with your exact project scope. Sirius accepts only exact identifiers. It does not infer repositories, channels, or chats from the current checkout.

Do not store credentials, tokens, or private message content in this file.

## Global Addness queue policy

- Task system: Addness organization `YOUR_ORG` planned Todos
- Do not use Addness Goals as the Sirius task queue
- Project grouping: title prefix `[Project Display Name]` plus `chat_metadata.siriusProject`
- Deduplication key: `chat_metadata.siriusSourceKey`
- Canonical marker: `chat_metadata.canonicalSystem = "Addness planned TODO"`
- Do not create GitHub Issues from Slack or LINE intake
- Write the implementation repository into the planned Todo `detail` field

### Title state prefixes

| Prefix | Meaning |
| --- | --- |
| `[queued]` | Intake only; no automation |
| `[implement]` | Human authorized implementation |
| `[working][implement]` | Worker claimed |
| `[human-review][implement]` | Ready PR awaiting human |
| `[waiting]` | External dependency |
| `[waiting][implement]` | Implementation blocked by dependency |
| `[implement][blocked]` | Routing or prerequisite missing |

Only a human may add `[implement]` or the GitHub `merge` label.

### Addness project keys

| Project display name | `siriusProject` | Title prefix |
| --- | --- | --- |
| Example Platform | `ExamplePlatform` | `[Example Platform]` |
| Example Mobile App | `ExampleMobile` | `[Example Mobile App]` |
| Internal Training | `Training` | `[Internal Training]` |

Add one row per top-level project heading below.

---

## Example Platform

Customer-facing web platform and API maintained together.

### Dir

`~/projects/example-platform`

### GitHub repo

- API: `your-org/example-api`
- Web: `your-org/example-web`
- Infra: `your-org/example-infra`

Use exact `owner/repository` values. Add local checkout notes only when they help avoid pushing to the wrong remote.

### Slack

- Internal engineering: workspace `your-workspace.slack.com`, channel `C0123456789`
- Customer support: workspace `your-customer-workspace.slack.com`, channel `C9876543210`

Use exact workspace hostname and conversation ID. Wildcards and team nicknames are not valid scope.

### LINE

- Not used for this project

### Notes

- Route API schema work before dependent web work
- Keep PRs small; split follow-up refactors instead of expanding an approved scope
- Required checks must pass before `human-review`

---

## Example Mobile App

Mobile app with a single canonical repository.

### Dir

`~/projects/example-mobile`

### GitHub repo

- Mobile app: `your-org/example-mobile`

If local `origin` still points to an old personal fork, document the canonical GitHub repo here and verify the redirect before any write.

### Slack

- Not used for this project

### LINE

- Product owner direct chat: native desktop chat `Product Owner`
- Shared chat note: if one LINE chat covers multiple projects, document the disambiguation rule here

Example disambiguation rule:

- Messages about release timing go to this project
- Messages about billing go to a different project
- If the target project or repository cannot be determined uniquely, stop and ask the user before creating the next planned Todo

### Current status

- iOS release: done
- Android release: pending

---

## Internal Training

Non-code coordination project with Slack intake only.

### Dir

- Not specified yet

### GitHub repo

- None
- Do not route implementation work to a guessed repository

### Slack

- Workspace `your-workspace.slack.com` (team ID `T0123456789`)
- Private channel `#internal-training` (conversation ID `C0ABCDEF12`)

### LINE

- Not used for this project

### Notes

- Training planning, instructor coordination, estimates, proposals, and scheduling belong here
- Create new actions as Addness planned Todos with `siriusProject: Training`

---

## Shared-source routing example

Use this section when one chat spans multiple projects.

### Shared LINE direct chat

- Native desktop chat title: `Shared Partner Chat`
- Used by: Example Platform, Example Mobile App
- Rule: determine the target project from message topic before intake continues
- If the repository or project is ambiguous, pause that source and ask the user

### Shared Slack channel

- Channel `C0123456789` is read only for Example Platform
- Do not route Example Mobile App actions from this channel unless you add an explicit note here

---

## Project without repository yet

Use this pattern when coordination exists but implementation routing is not finalized.

### Dir

- Not specified yet

### GitHub repo

- None
- Do not create an implementation planned Todo until one exact repository is confirmed

### Slack

- Not used yet

### LINE

- Native desktop chat `New Initiative Group`

### Notes

- Safe first actions: estimate, proposal, meeting follow-up
- Do not start repository writes until a human confirms the target repo

---

## Authoring checklist

Before your first `/run-sirius`, confirm:

- [ ] Every top-level project has a unique display name
- [ ] Every project has a unique `siriusProject` key and title prefix
- [ ] Every repository is an exact `owner/repository`
- [ ] Every Slack source has workspace plus conversation ID
- [ ] Every LINE source has an exact native desktop chat title
- [ ] Ambiguous shared chats have an explicit stop-and-ask rule
- [ ] No secrets, tokens, or private message bodies are stored here
- [ ] GitHub repos have `human-review` and `merge` labels available
