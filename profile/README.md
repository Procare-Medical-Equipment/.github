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

## Never in a repo

- Secrets or keys (`.env.example` holds names only). PHI. Pay or people data.
- Binary documents (`.docx` `.xlsx` `.pptx` `.pdf`). Documents live in Notion or SharePoint; link them from `docs/`.

Full detail for members: the `dev-keys` README.
