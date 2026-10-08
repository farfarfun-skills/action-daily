# daily-action

## Sync organization forks & mirror to Gitee

`Daily sync` runs hourly (and can also be started manually or is triggered
on push). It first updates every fork repo (name contains `--`) from its
upstream via `farfarfun-action/sync-forks`, then mirrors **every** repository
in the organization — including non-fork ones like `.github` and this repo
itself — to the `farfarfun-skills` organization on Gitee via
[`farfarfun-action/mirror-repo`](https://github.com/farfarfun-action/mirror-repo),
our own action built on top of [`farfarfun/funmirror`](https://github.com/farfarfun/funmirror)
(see that action's README for the full architecture diagram).

The hourly runs are **incremental**: `mirror-repo` only checks each repo's
commit id on GitHub, skips anything unchanged since the last recorded state
in `.mirror-state/gitee.json`, and never has to query Gitee for repos that
didn't move. Once a day the schedule flips to a **full sync**, which checks
both sides for every repo and self-heals any drift (e.g. someone pushing
directly to Gitee). The state file is committed back to this repo after
every run so it survives across the ephemeral runner.

## Keep Actions off everywhere else

`Disable org actions` turns GitHub Actions **off** in every non-archived
repository of the organization except this one — the forks we mirror carry
their upstream's workflows, and we don't want those burning our Actions
minutes. It runs daily (so freshly created or forked repos get switched off
too) and can be started manually with `workflow_dispatch`:

- `scope`: `repo` (default) flips the repository-level Actions switch, which
  also covers workflows added later; `workflows` instead disables each
  workflow individually (state `disabled_manually`), leaving the repo switch
  alone.
- `keep`: extra comma-separated repo names to leave enabled. This repo is
  always kept, regardless of the input.
- `dry-run`: report what would change without writing anything.

Repos that are already off are read and skipped, never re-disabled, so runs
are idempotent. Each run writes a scanned/disabled/skipped/failed table to
the job summary, and the job fails if any repo could not be updated.

To stop the daily enforcement (e.g. to re-enable Actions somewhere by hand),
delete the `schedule:` block from `.github/workflows/disable-actions.yml`.

## Secrets

Requires these repository Actions secrets:

- `ACTION_GITHUB_TOKEN`: fine-grained PAT with access to all organization
  repositories and `Contents: Read and write` permission. `Disable org
  actions` additionally needs `Administration: Read and write` for
  `scope=repo`, or `Actions: Read and write` for `scope=workflows`.
- `GITEE_RSA_PRIVATE_KEY` / `GITEE_TOKEN`: credentials for the Gitee mirror
  destination
