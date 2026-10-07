---
covers:
  - ntrossat/ohara:backend/ohara/main.py
  - ntrossat/ohara:backend/ohara/db.py
  - ntrossat/ohara:Dockerfile
  - ntrossat/ohara:docker-compose.yml
verified: 2026-10-07
---

# Website deployment

Ohara uses two repositories:

| Repository | Role |
|---|---|
| Ohara repository | Application code, including the HTML website and the MCP server |
| Docs repository | Documentation storage only. Configurable: each company points Ohara to its own repository |

Ohara runs as one Docker container. It downloads the docs repository through its GitHub App and serves the website and the MCP server itself. There is no build step and no CI pipeline: a merge to the docs repository's default branch updates the website within seconds.

## Run Ohara

```bash
OHARA_URL=https://docs.acme.com docker compose up -d
```

| Setting | Value |
|---|---|
| `OHARA_URL` | The address people use to open Ohara. The only environment variable. It can include a path, such as `https://acme.com/docs` |
| Port | `8000` |
| Data | The `ohara-data` volume, mounted at `/data` |

The image builds the React website and serves it from the FastAPI server, next to the API and the MCP server. Everything else, including the GitHub App and the docs repository, is set up from the setup page on first launch. See [Configure a docs repository](../../documentation/docs-repository.md).

## Serve under a path

When `OHARA_URL` has a path, such as `https://acme.com/docs`, Ohara serves the website, the API, and the MCP server under it, and redirects the rest of the host there. OAuth discovery for MCP clients stays at the root of the host, where clients look for it: `/.well-known/oauth-authorization-server/docs` and `/.well-known/oauth-protected-resource/docs/mcp`.

The GitHub App's callback and webhook URLs include the path. To move an existing instance to a path, update them in the app's settings on GitHub: the callback URL to `OHARA_URL/api/auth/callback` and the webhook URL to `OHARA_URL/api/github/webhook`. MCP clients connect again at `OHARA_URL/mcp`.

## HTTPS

Put Ohara behind a reverse proxy that terminates TLS and forwards to port `8000`. Ohara trusts the proxy's `X-Forwarded-*` headers.

When `OHARA_URL` starts with `https://`, Ohara marks its cookies `Secure`. MCP sign-in also needs `https://`, except on `localhost`.

## Data

All state lives in the `/data` volume. Nothing is kept in process memory, apart from caches.

| Path | Content |
|---|---|
| `ohara.db` | SQLite database, readable only by the server: GitHub App credentials and the docs repository, sign-in sessions and their GitHub tokens, MCP clients and token hashes, cached access checks, code change flags, the last sync of each code repository's docs, and the search index |
| `docs/` | The latest snapshot of the docs repository |

Records that expire, such as sessions and tokens, are removed once their time has passed.

Earlier versions saved `settings.json`, `sessions.json`, and `oauth.json`. On first start, Ohara imports them into `ohara.db` and renames them `*.json.imported`.

To back up Ohara, back up the volume. `docker compose down -v` deletes it and resets Ohara to the setup page.

## Update the docs

1. A PR is merged to the default branch of the docs repository.
2. GitHub sends a `push` event to `OHARA_URL/api/github/webhook`. Ohara checks the signature with the app's webhook secret.
3. Ohara downloads the branch as a tarball with the app's installation token.
4. It extracts the tarball next to the current snapshot, then swaps the two folders. Readers never see a half-written snapshot.
5. It rebuilds the search index.

A `repository` event, sent when the repository's settings change, triggers the same update. It also refreshes the repository's visibility and default branch, so making the repository public or private changes who can read the website. When the docs repository is made public (`publicized`), Ohara also syncs every code repository's docs again, which removes those of private code repositories.

A `push` to the default branch of any other repository the app is installed on syncs its docs when they changed, then flags the pages that cover the changed code. See [Sync app docs](mcp-server.md#sync-app-docs) and [Freshness](mcp-server.md#freshness).

A `push` to the docs repository that changes `apps/<name>/`, made by anyone but the app itself (`<app slug>[bot]`), syncs that code repository again, so hand edits are overwritten.

An `installation_repositories` event, sent when repositories are added to or removed from the app's installation, syncs each added repository and deletes the synced folder of each removed one. GitHub sends it to every app, with no subscription.

A `pull_request` event, sent when a pull request in another repository closes, merges or closes the docs pull request of the same branch. See [Merge with the code](mcp-server.md#merge-with-the-code).

Ohara also updates the docs each time it starts. Then it syncs the docs of each code repository the app is installed on that has never been synced, such as those added before this feature. When `OHARA_URL` is `localhost` or a private address, the app has no webhook, so a restart is the only way to update, and code changes are neither flagged nor synced.

## Update Ohara

Pull the new code and rebuild:

```bash
git pull
docker compose up -d --build
```

The data volume is kept: settings, sessions, MCP sign-ins, and docs survive the update.

Instances set up before Ohara could propose changes have a read-only GitHub App. To enable proposals, grant **Contents** and **Pull requests** write permissions in the app settings on GitHub, then accept the new permissions on the installation.

Instances set up before Ohara could merge docs with code are not subscribed to pull request events. To enable it, check **Pull request** under **Subscribe to events** in the app settings on GitHub. See [Merge docs with code](../../documentation/merge-with-code.md#enable-it).

## API

The website is a React app that reads everything from the API. The docs routes and `/mcp` follow the [access check](authentication.md#access-check).

| Route | Purpose |
|---|---|
| `GET /api/status` | Setup state, docs repository, and the signed-in user |
| `GET /api/nav` | The menu, built from the folder tree |
| `GET /api/page?path=` | One page's title, file, and Markdown. For a synced page under `apps/`, its `source`: the code repository, the file, and the edit URL on its default branch |
| `GET /api/files/*` | Images and other files from the docs repository |
| `/api/setup/*` | GitHub App creation and installation. Locked once setup is done, except `/api/setup/installed`, which redirects to the website when an admin returns from adding a repository to the installation |
| `/api/auth/*` | Sign-in and sign-out. See [Authentication](authentication.md) |
| `POST /api/github/webhook` | GitHub events that update the docs, sync code repositories' docs, flag code changes, and merge docs with code |
| `/mcp` | The [MCP server](mcp-server.md) |
| `/.well-known/*`, `/register`, `/authorize`, `/token`, `/revoke` | OAuth for MCP clients. See [MCP sign-in](authentication.md#mcp-sign-in) |
