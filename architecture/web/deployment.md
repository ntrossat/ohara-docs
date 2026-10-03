# Website deployment

UNITED uses two repositories:

| Repository | Role |
|---|---|
| UNITED repository | Application code, including the HTML website |
| Docs repository | Documentation storage only. Configurable: each company points UNITED to its own repository |

The website is built and deployed from the UNITED repository. A merge to `main` in the docs repository triggers a rebuild.

## Configuration

Both sides are set as GitHub Actions variables, so no repository name is hard-coded.

| Variable | Stored in | Value |
|---|---|---|
| `DOCS_REPO` | UNITED repository | Docs repository, e.g. `acme/docs` |
| `UNITED_REPO` | Docs repository | UNITED repository, e.g. `acme/united` |

## Flow

1. A PR is merged to `main` in the docs repository.
2. The docs repository sends a `repository_dispatch` event (`docs-updated`) to `UNITED_REPO`.
3. The UNITED repository checks out its own code and `DOCS_REPO`, builds the site, and deploys it.

A push to `main` in the UNITED repository also triggers the deploy.

## Docs repository: `.github/workflows/notify.yml`

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
          gh api repos/${{ vars.UNITED_REPO }}/dispatches \
            -f event_type=docs-updated \
            -f "client_payload[sha]=${{ github.sha }}"
        env:
          GH_TOKEN: ${{ secrets.UNITED_DISPATCH_TOKEN }}
```

## UNITED repository: `.github/workflows/deploy-site.yml`

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
          repository: ${{ vars.DOCS_REPO }}
          path: docs-content
          token: ${{ secrets.DOCS_READ_TOKEN }}

      - run: echo "build site from ./docs-content into ./site"   # build command
      - run: echo "deploy ./site"                                 # host deploy step
```

`repository_dispatch` only runs workflows on the default branch, so `deploy-site.yml` must be on `main`.

`concurrency` cancels an older build when merges land back to back, so the newest one always wins.

## Secrets

When both repositories are private, each side needs a token. Use fine-grained personal access tokens or a GitHub App token.

| Secret | Stored in | Repository access | Permission |
|---|---|---|---|
| `UNITED_DISPATCH_TOKEN` | Docs repository | UNITED repository | Contents: read & write |
| `DOCS_READ_TOKEN` | UNITED repository | Docs repository | Contents: read |
