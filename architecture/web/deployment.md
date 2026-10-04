# Website deployment

Ohara uses two repositories:

| Repository | Role |
|---|---|
| Ohara repository | Application code, including the HTML website |
| Docs repository | Documentation storage only. Configurable: each company points Ohara to its own repository |

The website is built and deployed from the Ohara repository. A merge to `main` in the docs repository triggers a rebuild.

## Configuration

Both sides are set as GitHub Actions variables, so no repository name is hard-coded.

| Variable | Stored in | Value |
|---|---|---|
| `DOCS_REPO` | Ohara repository | Docs repository, e.g. `acme/docs` |
| `OHARA_REPO` | Docs repository | Ohara repository, e.g. `acme/ohara` |

## Flow

1. A PR is merged to `main` in the docs repository.
2. The docs repository sends a `repository_dispatch` event (`docs-updated`) to `OHARA_REPO`.
3. The Ohara repository checks out its own code and `DOCS_REPO`, builds the site, and deploys it.

A push to `main` in the Ohara repository also triggers the deploy.

## Docs repository: `.github/workflows/notify.yml`

```yaml
name: Notify Ohara
on:
  push:
    branches: [main]

jobs:
  dispatch:
    runs-on: ubuntu-latest
    steps:
      - run: |
          gh api repos/${{ vars.OHARA_REPO }}/dispatches \
            -f event_type=docs-updated \
            -f "client_payload[sha]=${{ github.sha }}"
        env:
          GH_TOKEN: ${{ secrets.OHARA_DISPATCH_TOKEN }}
```

## Ohara repository: `.github/workflows/deploy-site.yml`

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
| `OHARA_DISPATCH_TOKEN` | Docs repository | Ohara repository | Contents: read & write |
| `DOCS_READ_TOKEN` | Ohara repository | Docs repository | Contents: read |
