---
covers: [ntrossat/ohara:backend/ohara/sessions.py, ntrossat/ohara:backend/ohara/oauth.py, ntrossat/ohara:backend/ohara/github.py]
verified: 2026-10-06
---

# Authentication

Ohara signs users in with GitHub and mirrors the docs repository's access rights:

| Docs repository | Website and MCP server |
|---|---|
| Public | Open to everyone, no sign-in |
| Private | Sign-in required. Only users who can read the repository on GitHub get access |

One GitHub App handles sign-in, access to the docs repository, the webhooks that trigger rebuilds and flag code changes, and the pull requests that MCP clients propose.

## Configuration

`OHARA_URL` is the only environment variable. It is the address people use to open Ohara, e.g. `https://docs.acme.com`.

| Used for | Value |
|---|---|
| Sign-in callback | `OHARA_URL/api/auth/callback` |
| Setup callbacks | `OHARA_URL/api/setup/callback`, `OHARA_URL/api/setup/installed` |
| Webhook | `OHARA_URL/api/github/webhook`, only when the address is reachable from the internet |
| MCP OAuth issuer | `OHARA_URL`, only when it starts with `https://` or is `localhost` |
| Cookies | Marked `Secure` when the address starts with `https://` |

Everything else is created during setup and saved in `ohara.db` in the data volume, readable only by the server.

## Setup: create and install the GitHub App

Setup runs once, from the setup page on first launch.

1. **Create the app.** Ohara sends GitHub a manifest with the app's callback URLs, webhook, events (`push`, `repository`), and permissions (`contents: write`, `pull_requests: write`, `metadata: read`). The admin reviews it on GitHub and confirms.
2. **Save the credentials.** GitHub redirects to `/api/setup/callback`. Ohara checks the `state` value it sent, then exchanges the code for the app's credentials: App ID, slug, client ID, client secret, webhook secret, and private key. GitHub returns them only once.
3. **Install the app.** The admin installs the app on the docs repository, and only that one. GitHub redirects to `/api/setup/installed`. Ohara checks that the installation belongs to its app, saves the repository, and downloads the first snapshot of the docs.

Once setup is done, the setup routes are locked. Code repositories can be added to the installation afterwards to [flag code changes](../../documentation/docs-repository.md#add-code-repositories).

Every instance creates its own app. GitHub App names are unique across all of GitHub, so Ohara suggests a name based on the instance's host (e.g. `Ohara docs.acme.com`), or adds a random suffix for local runs. The admin can change the name on GitHub before confirming.

## Sign-in

1. The user clicks **Sign in with GitHub** and goes to `/api/auth/login`.
2. Ohara redirects to GitHub's authorize page with the app's client ID, the callback URL, and a random `state`. The state and the page to return to are kept in a short-lived cookie (10 minutes).
3. The user approves. The first time, GitHub asks them to authorize the app.
4. GitHub redirects to `/api/auth/callback`. Ohara:
   - checks `state` against the cookie;
   - exchanges the code for a user access token and a refresh token;
   - reads the user's login and avatar;
   - creates a session and sets the `ohara_session` cookie (HttpOnly, SameSite=Lax, 30 days);
   - returns the user to the page they came from. Only paths on the same site are accepted.

Sessions are saved in `ohara.db`, so restarting Ohara keeps everyone signed in. A session ends after 30 days without use.

## MCP sign-in

MCP clients sign in through OAuth. Ohara is the authorization server, and GitHub sign-in proves who the user is.

1. The client finds the endpoints at `/.well-known/oauth-protected-resource/mcp` and `/.well-known/oauth-authorization-server`, then registers itself at `/register`.
2. The client sends the user to `/authorize`. Ohara saves the pending request (10 minutes) and redirects to `/api/auth/login?mcp=…`.
3. The user signs in with GitHub as above. The callback creates a session but sets no cookie. If the user can read the docs repository, it redirects to the client with a one-time code. Otherwise it returns `access_denied`.
4. The client exchanges the code at `/token` and gets an Ohara access token (1 hour) and refresh token (30 days), both starting with `oha_`.

Each grant is backed by its own session, so the GitHub token stays on the server and access is re-checked like on the website. Revoking a token at `/revoke` ends its session. Ohara stores only hashes of its tokens. It keeps the newest 1,000 registered clients, since registration is open.

CI and headless agents can send a GitHub token as `Authorization: Bearer` instead. Ohara checks that token's access to the repository and caches the result for 5 minutes.

OAuth needs `OHARA_URL` to start with `https://` or to be `localhost`. Otherwise Ohara logs a warning, disables MCP sign-in, and only GitHub tokens work.

## Access check

Every docs request (`/api/nav`, `/api/page`, `/api/files/*`, `/mcp`) is checked:

| Situation | Response |
|---|---|
| Public repository | Served |
| Private repository, not signed in | `401`: the website shows the sign-in screen, and MCP clients start the OAuth sign-in |
| Private repository, signed in without access | `403`: the website shows "You don't have access" |
| Private repository, signed in with access | Served |

An invalid or expired bearer token on `/mcp` gets `401`, even for a public repository.

To check access, Ohara reads the repository from GitHub with the user's own token. A user token only sees repositories that both the user and the app can access, so success means the user can read the docs.

The result is cached for 5 minutes. A user whose access is removed on GitHub loses the website and the MCP server within 5 minutes.

When the user token expires or GitHub rejects it, Ohara uses the refresh token once. If that fails, the session ends and the user signs in again.

Proposing a change through MCP also requires write access to the docs repository. Ohara checks it with the caller's GitHub token on each proposal.

## Sign-out

Signing out deletes the session and clears the cookie.

## Tokens

| Token | Belongs to | Used for |
|---|---|---|
| Installation token, from the app's private key | The app | Downloading the docs, reading repository details and changed files, and opening pull requests |
| User access token, from sign-in | Each user | Checking that the user can read, or write to, the docs repository. Never leaves the server |
| Ohara access and refresh tokens (`oha_`) | Each MCP client grant | Calling `/mcp`. Point to a session, so its GitHub token is used for checks |
| GitHub token sent as `Authorization: Bearer` | CI and headless agents | Calling `/mcp`, and the same checks as a user token |

The docs are always fetched, and pull requests always opened, with the app's token. The user's token only decides what that user may do.
