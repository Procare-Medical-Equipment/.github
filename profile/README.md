# Procare Medical Equipment

How code lands in this org. The rules are enforced by GitHub; when something is rejected, the error tells you which rule.

## Repos

- Name: `<pc|uq>-<type>-<dept>-<name>`. `pc` = ProCare-owned, `uq` = contract work for a client. Type is one of `app` `api` `connector` `automation` `tool` `template` `docs`. Dept is one word. All lowercase.
- Create it, push `main`. Within 30 minutes an onboarding PR adds the standard files; merge it. From then on `main` is PR-only.
- Every repo carries `project.yaml` at the root; its `name` equals the repo name.
- New web apps and connectors start from the `pc-template-*` repos ("Use this template").

## Branches

- `main` is staging. `production` is the released version, promoted from `main` by a PR titled `promote: <what>` and tagged.
- Work on a branch named `<type>/<slug>`, pushed to the org repo. No forks.
- `main`, `production` and `staging` change only through a pull request. No direct pushes, no force-pushes.

## Pull requests

- Title: `<type>(<scope>)?: <subject>`, type one of `feat` `fix` `chore` `docs` `refactor` `test` `ci` `perf` `build` `revert` `promote` `hotfix` `release`. The title becomes the squash commit.
- The check `pr-checks / checks` must pass: naming, `project.yaml`, no secret files, lint, tests. Resolve every review thread, then squash-merge.

## Linting

- Every JS/TS repo has a `lint` script and it runs in CI. Python repos run `ruff`.
- Inherited findings are temporarily downgraded in a dated block; on the date in that block they become errors again.

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
