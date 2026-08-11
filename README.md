# daily-action

## Publish Paperclip to Aliyun

`Publish Paperclip to Aliyun` runs daily at 18:30 Asia/Shanghai, mirrors the
official `paperclipai@nightly` package, and publishes it as `latest` in an
Aliyun private npm registry. Configure these repository Actions secrets:

- `ALIYUN_NPM_REGISTRY`: the full private registry URL
- `ALIYUN_NPM_USERNAME`: the registry username
- `ALIYUN_NPM_PASSWORD`: the registry password

The private registry must proxy the public npm registry so Paperclip's public
dependencies remain installable. Configure it in the consuming project's
`.npmrc`, then update with `npm install paperclipai@latest`.

## Sync organization forks

`Sync organization forks` runs daily at 00:00 Asia/Shanghai and can also be
started manually. Add a repository Actions secret named `SYNC_FORKS_TOKEN`
using a fine-grained PAT with access to all organization repositories and
`Contents: Read and write` permission.
