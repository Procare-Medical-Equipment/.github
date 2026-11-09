# Procare Medical Equipment

How code lands in this org. The rules are enforced by GitHub; when something is rejected, the error tells you which rule.

## Naming dictionary

Every name in the org follows one of these. Details in the sections below.

| Thing | Format | Example |
|---|---|---|
| Repo | `<pc\|uq>-<type>-<dept>-<name>` | `pc-automation-ops-jessie-ivr` |
| Branch | `<type>/<slug>` | `feat/add-farewell` |
| PR title, squash commit | `<type>(<scope>)?: <subject>` | `fix(billing): round totals` |
| Promote PR | `promote: <what>` | `promote: add farewell` |
| Release tag | `v<major>.<minor>.<patch>` | `v1.4.0` |
| Automation workflow | `CCC-NN-NN-SSP [Verb Object]` | `TRD-01-00-03N [Send Daily Reminder]` |
| Project code | 3 uppercase letters, repo property `Project-Code` | `TRD` |
| Token or key at the provider | `<repo>--<env>--<client>-<access>[-<area>]` | `pc-automation-ops-jessie-ivr--prod--n8n-rw-ai-automations` |
| Env var in the repo | `<SERVICE>_<KIND>` | `AIRTABLE_API_KEY` |
| Lint-debt block | dated comment block in the lint config | `LINT DEBT — DEADLINE 2026-11-30` |

Vocabularies: `type` (repo) is `app` `api` `connector` `automation` `tool` `template` `docs`. `type` (branch, PR) is `feat` `fix` `chore` `docs` `refactor` `test` `ci` `perf` `build` `revert` `promote` `hotfix` `release`. `env` is `prod` `staging` `ci` `local-<user>`. `access` is `ro` `rw` `deploy` `admin`. Platform letter is `N` n8n, `Z` Zapier, `M` Make, `G` GitHub Actions. Everything lowercase except project codes, platform letters and env var names.

## Repos

- Name: `<pc|uq>-<type>-<dept>-<name>`. `pc` = ProCare-owned, `uq` = contract work for a client. Type is one of `app` `api` `connector` `automation` `tool` `template` `docs`. Dept is one word. All lowercase.
- Create it, push `main`. Within 30 minutes an onboarding PR adds the standard files; merge it. From then on `main` is PR-only.
- Description in the About field. Lifecycle, platforms and data sensitivity in Settings → Custom properties.
- New web apps and connectors start from the `pc-template-*` repos ("Use this template").

## Branches

- `main` is staging. `production` is the released version, promoted from `main` by a PR titled `promote: <what>` and tagged.
- Work on a branch named `<type>/<slug>`, pushed to the org repo. No forks.
- `main`, `production` and `staging` change only through a pull request. No direct pushes, no force-pushes.

## Pull requests

- Title: `<type>(<scope>)?: <subject>`, type one of `feat` `fix` `chore` `docs` `refactor` `test` `ci` `perf` `build` `revert` `promote` `hotfix` `release`. The title becomes the squash commit.
- The check `pr-checks / checks` must pass: naming, no secret files, lint, tests. Resolve every review thread, then squash-merge.
- Quick change to staging: `gh pr create --fill && gh pr merge --auto --squash`. It lands by itself when the check is green.

## Linting

- Every JS/TS repo has a `lint` script and it runs in CI. Python repos run `ruff`.
- Inherited findings are temporarily downgraded in a dated block; on the date in that block they become errors again.

## Automation workflows (n8n, Zapier, Make)

- Name every flow `CCC-NN-NN-SSP [Verb Object]`: 3-letter project code, major and minor version, 2-digit step, platform letter (`N` n8n, `Z` Zapier, `M` Make, `G` GitHub Actions), then a bracketed label. Example: `TRD-01-00-03N [Send Daily Reminder]`.
- The project code is the repo's `Project-Code` custom property; one code per repo, picked once. The platform letter is the system that hosts the flow.
- Bumping the version renames every flow of the project at once; that rename is the release.

## Tokens and keys

- Name every token or key at the provider `<repo>--<env>--<client>-<access>[-<area>]`. Example: `pc-automation-ops-jessie-ivr--prod--n8n-rw-ai-automations`.
- `env` is where the value lives: `prod` (the `production` deployment, host config), `staging` (the `main` deployment, host config), `ci` (GitHub Actions secrets), `local-<user>` (one person's machine, never shared).
- `client` is the system that holds and sends the token: `github` (Actions), `n8n`, `azure`, `claude`, `local` (a script on a machine). Not the provider you are creating it in.
- `access` is one of `ro` (read), `rw` (read and write), `deploy` (push builds or infra), `admin` (manage the account; needs an org owner).
- `area`, optional, is the slice of the provider the token can reach, as the provider names it, lowercase and hyphenated: an Airtable base, a SharePoint site, a Business Central company, a GHL location. Scope the token to that area when the provider allows it.
- One token per repo per env per client. Never reuse one across repos or environments. Rotate it when its creator leaves.
- In the repo it is an env var named `<SERVICE>_<KIND>` (`AIRTABLE_API_KEY`, `BC_CLIENT_SECRET`), listed by name only in `.env.example`.

## Never in a repo

- Secrets or keys (`.env.example` holds names only). PHI. Pay or people data.
- Binary documents (`.docx` `.xlsx` `.pptx` `.pdf`). Documents live in Notion or SharePoint; link them from `docs/`.

Full detail for members: the `dev-keys` README.
