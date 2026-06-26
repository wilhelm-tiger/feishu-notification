CONTEXT — feishu-notification
=============================

Product Vision
--------------

`feishu-notification` is a standalone, reusable GitHub composite Action that bridges GitHub pull-request activity to
Feishu (Lark), Waybox-AI's internal team communication platform.

The goal is a zero-configuration drop-in: any Waybox-AI repository (or any external project) should be able to add few
lines of YAML to a workflow and immediately receive rich, colour-coded Feishu card notifications for every meaningful PR
lifecycle event — without copy-pasting shell scripts or managing webhook logic themselves.

---

Business context
----------------

Waybox-AI uses Feishu as its primary team communication tool. GitHub PR notifications by email or in the GitHub UI are
easy to miss; the team needs them surfaced in the chat channel where discussion already happens.

Rather than embedding the notification logic in each project's CI file (which leads to drift and duplication), the logic
is extracted here as a versioned, taggable GitHub Action. Projects pin to `@vx.x.x` and get fixes for free when the tag
is moved. When notification requirements change (new event types, different card layout, additional fields), a single
change to this repository propagates everywhere.

---

Domain terminology
------------------

### Feishu / Lark

Feishu (飞书) is ByteDance's enterprise communication platform, sold internationally as Lark. The two names refer to the
same product. "Feishu" is used throughout this project.

### Incoming webhook

A Feishu bot webhook is an HTTPS endpoint that accepts a `POST` request with a JSON payload and delivers the resulting
message to a specific Feishu group chat. Each webhook URL is scoped to one chat. It is a secret and must never be
committed to source control.

### Interactive card (`msg_type: "interactive"`)

The Feishu message format used by this action. An interactive card has a structured layout: a coloured __header__, one
or more __div__ content elements (supporting `lark_md` markdown), and optional __action__ elements such as buttons. This
is richer than a plain-text (`msg_type: "text"`) message.

### `lark_md`

A Feishu-flavoured markdown dialect used inside card `div` elements. Supports `**bold**`, `` `inline code` ``, and
`[link text](url)`. Not identical to GitHub Flavoured Markdown.

### Card template colour

The `template` field on the card header controls the colour of the header bar. This action uses:

- `"blue"` — PR opened
- `"orange"` — new commits pushed to a PR
- `"green"` — PR merged into `main`

### Composite action

A GitHub Actions `action.yml` with `runs.using: "composite"` executes a sequence of shell steps directly on the caller's
runner — no Docker image, no Node.js build step. It is the simplest action type and has the fastest startup time.

### PR lifecycle events (GitHub)

The `pull_request` webhook event has an `action` field. This project handles 3:

- `opened` — a new PR has been created
- `synchronize` — one or more commits were force-pushed or pushed to the PR's head branch
- `closed` — the PR was either merged or closed without merging; the `merged` boolean on the payload disambiguates the two cases

---

Business logic
--------------

### Event routing

On every `pull_request` trigger the action inspects `github.event.action` and `github.event.pull_request.merged` to
decide which notification (if any) to send:

```
action = "opened"                                              → blue card  "新 PR 已创建"
action = "synchronize" AND draft = false                       → orange card "PR 有新提交"
action = "synchronize" AND draft = true                        → exit 0 (no notification)
action = "closed" AND merged = true AND base = main            → green card  "PR 已合并到 main"
anything else                                                  → exit 0 (no notification)
```

The draft guard on `synchronize` prevents over-notification from intermediate commits. Developers push
work-in-progress commits to a Draft PR freely; only when the PR is marked "Ready for Review" (making
it non-draft) will subsequent pushes produce notifications.

Callers should also enable workflow-level concurrency with `cancel-in-progress: true` scoped per PR
number. This ensures that if multiple commits land in quick succession on a non-draft PR, only the
notification triggered by the last push actually completes — earlier runs are cancelled before they
reach the `curl` step.

The `base = main` guard on the merge case is intentional: merges into feature branches or release branches do not
trigger a notification because they are routine integration steps, not team-visible milestones.

### Notification card content

Every card contains:

- __PR reference__ - `#<number> <title>` as a hyperlink to the PR
- __Author__ - the GitHub login of the PR author
- __Branch__ - the head branch name (`github.head_ref`)
- __Detail line__ - event-specific context (waiting for review / short commit SHA / merged-by username)
- __"查看 PR" button__ - deep link directly to the PR

### Graceful degradation

If `FEISHU_WEBHOOK_URL` is empty (secret not configured), the action logs a message and exits with code `0`. This keeps
the workflow green for forks and repositories that have not yet configured the secret.

### Webhook URL handling

The webhook URL is passed as an action input (`webhook-url`) rather than read directly from an environment variable or
hardcoded context expression. This makes the interface explicit and allows callers to source the URL from
organisation-level secrets, environment-scoped secrets, or any other mechanism GitHub supports.
