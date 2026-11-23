# Procare Medical Equipment

How code lands in this org. Every rule below says how it is enforced:

- **Blocked**: GitHub refuses the action. You get an error naming the rule.
- **Checked**: the PR check `pr-checks / checks` fails, and the PR cannot merge until it is fixed.
- **Convention**: nothing stops you. Follow it anyway; a reviewer will send it back.

## Naming dictionary

Every name in the org follows one of these. Details and examples are in the sections below.

| Thing | Format | Example |
|---|---|---|
| Repo | `<pc\|uq\|t>-<type>-<dept>-<name>` | `pc-automation-ops-jessie-ivr` |
| Branch | `<type>/<slug>` | `feat/add-farewell` |
| Branch for an issue | `<type>/<issue#>-<slug>` | `fix/12-round-totals` |
| Issue title | plain imperative sentence, no prefix | `Round invoice totals to cents` |
| Board | the repo's name; the overview is `ProCare Work` | `pc-automation-finance-ar-agent` |
| PR title, squash commit | `<type>(<scope>)?: <subject>` | `fix(billing): round totals` |
| Promote PR | `promote: <what>` | `promote: add farewell` |
| Release tag | `v<major>.<minor>.<patch>` | `v1.4.0` |
| Workflow (GitHub Actions, Uptiq) | `<action>-<trigger>-<service> [Verb Object]` | `deploy-auto-azure [Deploy Function]` |
| Token or key at the provider | `<repo>--<env>--<client>-<access>[-<area>]` | `pc-automation-ops-jessie-ivr--prod--n8n-rw-ai-automations` |
| Env var in the repo | `<SERVICE>_<KIND>` | `AIRTABLE_API_KEY` |
| Lint-debt block | dated comment block in the lint config | `LINT DEBT — DEADLINE 2026-11-30` |

Everything is lowercase except env var names and the plain name in brackets.

## Repos

**Rule.** A repo is named `<pc|uq|t>-<type>-<dept>-<name>`.

- `pc` = ProCare owns it. `uq` = contract work for a client. `t` = a template: a starting point for a codebase, tied to neither.
- `type` is one of `app` `api` `connector` `automation` `tool` `template` `docs`.
- `dept` is one word: `ops`, `finance`, `sales`, `it`, `marketing`.
- `name` is lowercase words joined by hyphens.

| Right | Wrong | Why it is wrong |
|---|---|---|
| `pc-app-ops-staff-portal` | `StaffPortal` | no owner, type or dept; uppercase |
| `uq-api-sales-comet-reseller-registration` | `comet-api` | owner and dept missing |
| `pc-docs-marketing-brand-assets` | `Procare-Assets` | not the format; uppercase |
| `pc-tool-it-dashboard-engine` | `pc-tool-IT-dashboard-engine` | uppercase dept |
| `t-template-it-web-app` | `template-web-app` | no owner prefix or dept |

**Enforced: Checked.** A wrongly named repo still gets created, but every PR in it fails until it is renamed (Settings → General → Repository name).

**Creating one.** Name it, push `main`, and that is all:

```bash
gh repo create Procare-Medical-Equipment/pc-app-ops-staff-portal --private --source . --push
```

Within 30 minutes on a weekday between 9am and 5pm Mountain time (otherwise the next working morning) an onboarding PR titled `chore: adopt org PR flow` appears. It adds the check, CODEOWNERS and the PR template. Merge it. From that moment `main` only changes through pull requests. The repo also gets a board and has Issues switched on.

**About the repo** lives on the repo, not in a file: the description in the About field; lifecycle, platforms and data sensitivity in Settings → Custom properties.

New web apps and connectors start from the `pc-template-*` repos ("Use this template").

## Branches

There are two long-lived branches, and they mean two different things.

| Branch | What it is | What deploys from it |
|---|---|---|
| `main` | **Staging.** The newest merged work, being tested | The staging environment, if the project has one |
| `production` | **The released version.** What staff and customers actually use | The live system, and nothing else deploys there |

**Rule 1. Live systems deploy from `production` only.** A workflow that pushes to a live app, function, phone agent or database runs on `production`. A merge to `main` never changes what users see.

```yaml
# Right: the live deploy waits for a release
on:
  push:
    branches: [production]

# Wrong: every merge to main goes straight to users
on:
  push:
    branches: [main]      # deploying the live app from here breaks the rule
```

**Enforced: Convention.** Nothing checks which branch a deploy runs from. A repo that has nothing live yet has only `main`; create `production` the day it first ships:

```bash
git push origin main:production
```

**Rule 2. `production` only moves by a promote PR.** Open a PR into `production` from `main`, or from a `promote/<slug>` branch cut from `main`, titled `promote: <what>`.

```bash
gh pr create --base production --head main --title "promote: round invoice totals"
```

**Enforced: Blocked.** `production` needs the green check and a review from the `admin` team. A repo an org owner has marked `Solo-Release` skips the review; today that is only the staff dashboard.

**Rule 3. No direct pushes, force-pushes or deletion on `main` and `production`.**

**Enforced: Blocked.** The push is rejected with the rule's name.

**Rule 4. Work happens on a branch named `<type>/<slug>`, in the org repo.** `type` is one of `feat` `fix` `chore` `docs` `refactor` `test` `ci` `perf` `build` `revert` `promote` `hotfix` `release`. When the work is for an issue, put its number first in the slug.

| Right | Wrong | Why it is wrong |
|---|---|---|
| `feat/add-farewell` | `add-farewell` | no type |
| `fix/12-round-totals` | `tyler-test` | no type, says nothing |
| `chore/drop-old-scripts` | `Feature/AddFarewell` | type is not one of the list |
| `promote/va-round-10` | `patch-1` | no type, no meaning |

**Enforced: Checked, at PR time only.** GitHub cannot stop a badly named branch from being pushed on our plan. The PR from it fails the check. Fix it by renaming and reopening:

```bash
git branch -m feat/add-farewell && git push -u origin feat/add-farewell
```

Branches named `claude/...` and `dependabot/...` are made by tools and are accepted.

**Rule 5. No forks.** Clone the org repo and branch in it.

**Enforced: Blocked.** Forking private repos is switched off. A PR from an old fork can never merge, because the check does not run on fork PRs.

## Pull requests

**Rule 1. The title is `<type>(<scope>)?: <subject>`.** Same types as branches. The title becomes the one commit on `main`.

| Right | Wrong | Why it is wrong |
|---|---|---|
| `feat: add farewell` | `Add farewell` | no type |
| `fix(billing): round totals` | `Fix: round totals` | type is capitalised |
| `promote: VA round 10` | `feat - add farewell` | needs a colon and a space |
| `chore: drop old scripts` | `chore/drop old scripts` | that is a branch name, not a title |

**Enforced: Checked.** Edit the title and the check runs again by itself.

**Rule 2. The check must pass.** It runs these, in order, and reports every failure at once:

1. Naming: repo, branch, PR title.
2. No secret-shaped files tracked: `.env`, `.env.*`, `.envrc`, `*.local`, or a private key (`*.pem`, `*.key`, `*.p12`, `*.pfx`, `*.ppk`, `id_rsa` and the like). Only `.env.example` is allowed. A public certificate goes in a `.crt` or `.cer` file.
3. Workflows: no push trigger on a work branch, no schedule more often than hourly. See Actions minutes.
4. Lint: `npm run lint` when there is a `package.json`; `ruff check` when there are Python files.
5. No lint-debt block past its deadline.
6. .NET: the solution builds and its test projects pass.
7. Tests: `npm test` or `pytest`, when the repo has them.

**Enforced: Blocked** until it is green.

**Rule 3. Who has to approve.**

| Target | Needs |
|---|---|
| `main` | the green check. No review |
| `production` | the green check and a review from the `admin` team |

Every review thread must be resolved. Merge by squash; merge commits are switched off. **Enforced: Blocked.**

**Rule 4. One pull request per issue.** Work until the issue is done, then open one PR for it. Do not open a string of small PRs against the same issue; each one costs a reviewer's time.

**Enforced: Convention.**

**A whole change, start to finish:**

```bash
git switch -c fix/12-round-totals
# ...work, commit...
git push -u origin HEAD
gh pr create --title "fix: round invoice totals" --body "Closes #12" && gh pr merge --auto --squash
```

It lands on `main`, which is staging, when the check is green, and issue 12 closes. Releasing it is the promote PR in Branches, rule 2.

## Issues and boards

**Rule 1. Work is an issue in the repo it changes.** Pick the form; the form sets the type. There are no type labels.

| Form | Use it when |
|---|---|
| `Bug` | something works wrong |
| `Feature` | something new |
| `Task` | everything else: cleanup, upgrade, config, docs |
| `Ask` | we are waiting on an answer, decision or action from someone outside the repo team |

**Rule 2. The title is a plain imperative sentence.**

| Right | Wrong |
|---|---|
| `Round invoice totals to cents` | `bug: totals` |
| `Clear the lint-debt block before the Nov 30 deadline` | `Lint` |

One assignee, the person doing it. Unassigned means up for grabs.

**Rule 3. Priority and dates live on the issue.**

| Priority | Meaning |
|---|---|
| `Urgent` | production is broken or a dated commitment is at risk; other work stops |
| `High` | this cycle |
| `Medium` | next |
| `Low` | someday |

`Target date` only when there is a real deadline. `Blocked by` links an issue to the `Ask` it waits on.

**Rule 4. Each repo has a board named exactly like the repo.** Its `Status`:

| Status | Meaning |
|---|---|
| `Backlog` | recorded, not agreed to do |
| `Ready` | agreed and specified; anyone can start |
| `In progress` | assigned, branch exists |
| `Waiting` | blocked on an `Ask` or an outside party |
| `In review` | PR open |
| `Done` | merged to `main`, which is staging |

A team may add fields to its own board. The standard Status options stay. A repo rated `HIPAA` gets `PHI impact` and `Safeguard` automatically; set them on any issue whose change touches patient data. The board **ProCare Work** shows every issue from every repo, for admins.

**Rule 5. Backlogs, to-do files and decision decks do not live in a repo.** They are issues.

**Enforced: Convention** for all five. The forms, boards and fields are created for you; issues reach the boards within 30 minutes during working hours.

## Linting

**Rule 1. Every JS/TS repo has a `lint` script that passes. Python repos pass `ruff check`.**

```json
"scripts": { "lint": "eslint . && tsc --noEmit" }
```

**Enforced: Checked.** A `package.json` with no `lint` script fails the check.

**Rule 2. The rules are shared.** `app`, `api` and `connector` repos extend the public `eslint-config` repo:

```js
// eslint.config.js
import procare from '@procare/eslint-config'
export default [...procare]
```

**Enforced: Convention** for now.

**Rule 3. Lint debt is a loan with a date.** A repo that inherits findings may turn rules down inside a block marked exactly like this:

```js
// LINT DEBT — TEMPORARY. DEADLINE 2026-11-30: this block is deleted on that date.
rules: { '@typescript-eslint/no-explicit-any': 'warn' }
```

**Enforced: Checked** for the date: the day after it, every PR in that repo fails until the block is gone. Turning a rule off without a dated block is not allowed; that part is **Convention**.

## Workflow names

**Rule. A workflow is named `<action>-<trigger>-<service> [Verb Object]`.** This covers GitHub Actions workflows (the `name:` line of the file in `.github/workflows/`) and Uptiq flows (the flow's name in Uptiq).

| Part | Values | Meaning |
|---|---|---|
| `action` | `check` `test` `build` `deploy` `sync` `release` | the kind of work. `check` verifies and changes nothing; `sync` copies or sends data from one system to another |
| `trigger` | `auto` `manual` `called` | `auto` starts by itself: a push, a pull request, a schedule, a form or tag in Uptiq. `manual` runs only when a person starts it. `called` is run by something else: a reusable workflow run by another workflow, an Uptiq flow run by an agent or another flow. A workflow with both an automatic trigger and a Run button is `auto` |
| `service` | `github` `azure` `uptiq` … | the system it acts on or sends data to, one lowercase word. `github` when a GitHub workflow touches nothing outside GitHub |
| `[Verb Object]` | title case | what it does, in plain words |

No repo name, project code or version number in the name.

| Right | Wrong | Why it is wrong |
|---|---|---|
| `deploy-auto-azure [Deploy Function]` | `JES-01-00-95G [Deploy Function]` | a project code and a version |
| `check-auto-github [Check Pull Request]` | `PR Checks` | no action, trigger or service |
| `sync-called-azure [Post Call Details To Router]` | `Call end flow v2` | no action, trigger or service; a version |
| `test-manual-github [Run Org Tests]` | `Test-Manual-GitHub [tests]` | the code is lowercase; the name is a verb and an object in title case |

**Enforced: Convention.**

## Actions minutes

The org has 3,000 GitHub Actions minutes a month, shared by every repo. Each job is billed by the minute, rounded up. When the pool is empty no workflow starts in any repo: no check runs, so no pull request can merge, until the next month or until an org owner raises the budget. That happened on 2026-11-23.

**Rule 1. A workflow runs on push only for `main`, `production` or `staging`.** Work branches are checked by the pull request check; a second run on every push is the fastest way to empty the pool.

```yaml
# Right
on:
  push:
    branches: [main]

# Wrong: every commit on every branch, from every person and every agent
on:
  push:
    branches: ["**"]

# Wrong: no branches named means all of them
on: push
```

A push trigger that names only `tags` is fine.

**Enforced: Checked.**

**Rule 2. A schedule runs at most once an hour.** Give the cron one plain minute.

| Right | Wrong | Why it is wrong |
|---|---|---|
| `15 * * * *` | `*/10 * * * *` | six runs an hour |
| `0 7 * * 1` | `0,30 9-17 * * *` | two runs an hour |

Work that has to run more often is not a workflow: make it a timer in an Azure Function (start from `t-template-it-azure-function`).

**Enforced: Checked.** The one exception is the org's own onboarding job.

**Rule 3. Do not repeat the check.** Lint and tests already run on every pull request. A repo workflow does not run them again.

**Rule 4. Every job sets `timeout-minutes`.** Without it a hung job is billed for six hours.

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    timeout-minutes: 15
```

**Rule 5. Push to an open pull request in batches.** Every push to an open PR runs the check again. A push to a branch with no PR open costs nothing. Commit as often as you like; open the PR when the work is ready, and after that push finished pieces, not single lines. This goes for Claude Code sessions too.

**Enforced: Convention** for rules 3 to 5.

## Tokens and keys

**Rule 1. A token is named at the provider `<repo>--<env>--<client>-<access>[-<area>]`.**

| Part | Values | Meaning |
|---|---|---|
| `env` | `prod` `staging` `ci` `local-<user>` | where the value lives: the `production` deployment, the `main` deployment, GitHub Actions secrets, or one person's machine |
| `client` | `github` `n8n` `azure` `claude` `local` | the system that holds and sends the token, not the provider you create it in |
| `access` | `ro` `rw` `deploy` `admin` | read, read and write, push builds or infra, manage the account |
| `area` | optional | the slice of the provider it can reach: an Airtable base, a SharePoint site, a Business Central company |

| Right | Wrong | Why it is wrong |
|---|---|---|
| `pc-automation-ops-jessie-ivr--prod--n8n-rw-ai-automations` | `jessie token` | cannot be traced to a repo, environment or purpose |
| `pc-app-ops-staff-portal--ci--github-deploy` | `pc-app-ops-staff-portal-prod` | no client or access; single hyphens |

**Rule 2. One token per repo, per environment, per client.** Never reuse one across repos or environments. Rotate it when its creator leaves. `admin` access needs an org owner.

**Rule 3. In the repo it is an env var named `<SERVICE>_<KIND>`**, listed by name only in `.env.example`: `AIRTABLE_API_KEY`, `BC_CLIENT_SECRET`.

**Enforced: Convention** for the names. **Checked** for the files: a tracked `.env` or private key fails the check.

## Never in a repo

| Never | Where it goes instead | Enforced |
|---|---|---|
| Secrets or keys | the host's config or Actions secrets; `.env.example` holds names only | Checked for secret-shaped files |
| PHI: patient names, phone numbers, dates of birth, account or record numbers, call recordings | its own system of record | Convention |
| Pay or people data | HR's system | Convention |
| Binary documents: `.docx` `.xlsx` `.pptx` `.pdf` | Notion or SharePoint, linked from `docs/` | Convention |

The PHI rule also covers issues, pull requests, board fields, screenshots and pasted logs. Describe the case and point to the record in its own system.

Full detail for members: the `dev-keys` README.
