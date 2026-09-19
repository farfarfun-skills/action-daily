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

Requires these repository Actions secrets:

- `ACTION_GITHUB_TOKEN`: fine-grained PAT with access to all organization
  repositories and `Contents: Read and write` permission
- `GITEE_RSA_PRIVATE_KEY` / `GITEE_TOKEN`: credentials for the Gitee mirror
  destination
