# Website deployment

UNITED uses two repositories:

| Repository | Role |
|---|---|
| [`ntrossat/united`](https://github.com/ntrossat/united) | Application code, including the HTML website |
| [`ntrossat/united-docs`](https://github.com/ntrossat/united-docs) | Documentation storage only |

The website is built and deployed from `united`. A merge to `main` in `united-docs` triggers a rebuild.

## Flow

1. A PR is merged to `main` in `united-docs`.
2. `united-docs` sends a `repository_dispatch` event (`docs-updated`) to `united`.
3. `united` checks out its own code and `united-docs`, builds the site, and deploys it.

A push to `main` in `united` also triggers the deploy.

## `united-docs`: `.github/workflows/notify.yml`

```yaml
name: Notify UNITED
on:
  push:
    branches: [main]

jobs:
  dispatch:
    runs-on: ubuntu-latest
    steps:
      - run: |
          gh api repos/ntrossat/united/dispatches \
            -f event_type=docs-updated \
            -f "client_payload[sha]=${{ github.sha }}"
        env:
          GH_TOKEN: ${{ secrets.UNITED_DISPATCH_TOKEN }}
```

## `united`: `.github/workflows/deploy-site.yml`

```yaml
name: Deploy site
on:
  push:
    branches: [main]            # site code changed
  repository_dispatch:
    types: [docs-updated]       # docs changed
  workflow_dispatch:

concurrency:
  group: deploy-site
  cancel-in-progress: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/checkout@v4
        with:
          repository: ntrossat/united-docs
          path: docs-content
          token: ${{ secrets.DOCS_READ_TOKEN }}

      - run: echo "build site from ./docs-content into ./site"   # build command
      - run: echo "deploy ./site"                                 # host deploy step
```

`repository_dispatch` only runs workflows on the default branch, so `deploy-site.yml` must be on `main`.

`concurrency` cancels an older build when merges land back to back, so the newest one always wins.

## Secrets

Both repositories are private, so each side needs a token. Use fine-grained personal access tokens or a GitHub App token.

| Secret | Stored in | Repository access | Permission |
|---|---|---|---|
| `UNITED_DISPATCH_TOKEN` | `united-docs` | `united` | Contents: read & write |
| `DOCS_READ_TOKEN` | `united` | `united-docs` | Contents: read |

## Hosting

- **GitHub Pages:** needs a paid plan for private repositories. The site is public unless the account is on Enterprise Cloud.
- **Cloudflare Pages:** can sit behind Cloudflare Access for private viewing.
- **UNITED server:** serves the site behind UNITED's own login. The same trigger can also refresh the MCP server and AI chat.

Only the deploy step in `deploy-site.yml` changes between hosts.
