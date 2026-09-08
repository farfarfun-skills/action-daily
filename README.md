# daily-action

## Publish Paperclip to Aliyun

`Publish Paperclip to Aliyun` runs every 6 hours, mirrors the official
`paperclipai@nightly` package, and publishes it as `latest` in an Aliyun private
npm registry. Versions use the Action's Asia/Shanghai run time in the official
`YYYY.MDD.P` format, where `P` is the current 6-hour slot from `0` to `3`.
Configure these repository Actions secrets:

- `ALIYUN_NPM_REGISTRY`: the full private registry URL
- `ALIYUN_NPM_USERNAME`: the registry username
- `ALIYUN_NPM_PASSWORD`: the registry password

The private registry must proxy the public npm registry so Paperclip's public
dependencies remain installable. Configure it in the consuming project's
`.npmrc`, then update with `npm install paperclipai@latest`.

Paperclip also pins several scoped dependencies (e.g. `@paperclipai/server`,
and the per-platform `@embedded-postgres/<platform>` optional dependencies)
that the proxy may not have synced yet, including exact prerelease versions
it can't resolve on its own. The workflow resolves and mirrors each of these
from npmjs before publishing, so `npm install`/`pnpm install` don't silently
skip an unresolved optional dependency.

## Sync organization forks

`Sync organization forks` runs daily at 00:00 Asia/Shanghai and can also be
started manually. Add a repository Actions secret named `SYNC_FORKS_TOKEN`
using a fine-grained PAT with access to all organization repositories and
`Contents: Read and write` permission.
