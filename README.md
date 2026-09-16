# daily-action

## Sync organization forks & mirror to Gitee

`Daily sync` runs hourly and can also be started manually. It updates every
fork repo (name contains `--`) from its upstream, then mirrors **every**
repository in the organization — including non-fork ones like `.github` and
this repo itself — to the `farfarfun-skills` organization on Gitee via
`Yikun/hub-mirror-action`. Requires these repository Actions secrets:

- `ACTION_GITHUB_TOKEN`: fine-grained PAT with access to all organization
  repositories and `Contents: Read and write` permission
- `GITEE_RSA_PRIVATE_KEY` / `GITEE_TOKEN`: credentials for the Gitee mirror
  destination
