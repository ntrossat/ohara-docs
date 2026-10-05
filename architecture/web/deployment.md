# Website deployment

Ohara uses two repositories:

| Repository | Role |
|---|---|
| Ohara repository | Application code, including the HTML website |
| Docs repository | Documentation storage only. Configurable: each company points Ohara to its own repository |

Ohara runs as one Docker container. It downloads the docs repository through its GitHub App and serves the website itself. There is no build step and no CI pipeline: a merge to the docs repository's default branch updates the website within seconds.

## Run Ohara

```bash
OHARA_URL=https://docs.acme.com docker compose up -d
```

| Setting | Value |
|---|---|
| `OHARA_URL` | The address people use to open Ohara. The only environment variable |
| Port | `8000` |
| Data | The `ohara-data` volume, mounted at `/data` |

The image builds the React website and serves it from the FastAPI server, next to the API. Everything else, including the GitHub App and the docs repository, is set up from the setup page on first launch. See [Configure a docs repository](../../documentation/docs-repository.md).

## HTTPS

Put Ohara behind a reverse proxy that terminates TLS and forwards to port `8000`. Ohara trusts the proxy's `X-Forwarded-*` headers.

When `OHARA_URL` starts with `https://`, Ohara marks its cookies `Secure`.

## Data

All state lives in the `/data` volume:

| Path | Content |
|---|---|
| `settings.json` | GitHub App credentials and the docs repository. Readable only by the server |
| `sessions.json` | Signed-in users and their GitHub tokens. Readable only by the server |
| `docs/` | The latest snapshot of the docs repository |

To back up Ohara, back up the volume. `docker compose down -v` deletes it and resets Ohara to the setup page.

## Update the docs

1. A PR is merged to the default branch of the docs repository.
2. GitHub sends a `push` event to `OHARA_URL/api/github/webhook`. Ohara checks the signature with the app's webhook secret.
3. Ohara downloads the branch as a tarball with the app's installation token.
4. It extracts the tarball next to the current snapshot, then swaps the two folders. Readers never see a half-written snapshot.

A `repository` event, sent when the repository's settings change, triggers the same update. It also refreshes the repository's visibility and default branch, so making the repository public or private changes who can read the website.

Ohara also updates the docs each time it starts. When `OHARA_URL` is `localhost` or a private address, GitHub can't reach the webhook, so a restart is the only way to update.

## Update Ohara

Pull the new code and rebuild:

```bash
git pull
docker compose up -d --build
```

The data volume is kept: settings, sessions, and docs survive the update.

## API

The website is a React app that reads everything from the API. The docs routes follow the [access check](authentication.md#access-check).

| Route | Purpose |
|---|---|
| `GET /api/status` | Setup state, docs repository, and the signed-in user |
| `GET /api/nav` | The menu, built from the folder tree |
| `GET /api/page?path=` | One page's title and Markdown |
| `GET /api/files/*` | Images and other files from the docs repository |
| `/api/setup/*` | GitHub App creation and installation. Locked once setup is done |
| `/api/auth/*` | Sign-in and sign-out. See [Authentication](authentication.md) |
| `POST /api/github/webhook` | GitHub events that update the docs |
