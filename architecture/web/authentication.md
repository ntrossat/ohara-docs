# Authentication

Ohara signs users in with GitHub and mirrors the docs repository's access rights:

| Docs repository | Website |
|---|---|
| Public | Open to everyone, no sign-in |
| Private | Sign-in required. Only users who can read the repository on GitHub can read the website |

One GitHub App handles sign-in, read access to the docs repository, and the webhooks that trigger rebuilds.

## Configuration

`OHARA_URL` is the only environment variable. It is the address people use to open Ohara, e.g. `https://docs.acme.com`.

| Used for | Value |
|---|---|
| Sign-in callback | `OHARA_URL/api/auth/callback` |
| Setup callbacks | `OHARA_URL/api/setup/callback`, `OHARA_URL/api/setup/installed` |
| Webhook | `OHARA_URL/api/github/webhook`, only when the address is reachable from the internet |
| Cookies | Marked `Secure` when the address starts with `https://` |

Everything else is created during setup and saved in `settings.json` in the data volume, readable only by the server.

## Setup: create and install the GitHub App

Setup runs once, from the setup page on first launch.

1. **Create the app.** Ohara sends GitHub a manifest with the app's callback URLs, webhook, and permissions (`contents: read`, `metadata: read`). The admin reviews it on GitHub and confirms.
2. **Save the credentials.** GitHub redirects to `/api/setup/callback`. Ohara checks the `state` value it sent, then exchanges the code for the app's credentials: App ID, slug, client ID, client secret, webhook secret, and private key. GitHub returns them only once.
3. **Install the app.** The admin installs the app on the docs repository, and only that one. GitHub redirects to `/api/setup/installed`. Ohara checks that the installation belongs to its app, saves the repository, and downloads the first snapshot of the docs.

Once setup is done, the setup routes are locked.

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

Sessions are kept in memory. Restarting Ohara signs everyone out.

## Access check

Every docs request (`/api/nav`, `/api/page`, `/api/files/*`) is checked:

| Situation | Response |
|---|---|
| Public repository | Served |
| Private repository, not signed in | `401`: the website shows the sign-in screen |
| Private repository, signed in without access | `403`: the website shows "You don't have access" |
| Private repository, signed in with access | Served |

To check access, Ohara reads the repository from GitHub with the user's own token. A user token only sees repositories that both the user and the app can access, so success means the user can read the docs.

The result is cached for 5 minutes. A user whose access is removed on GitHub loses the website within 5 minutes.

When the user token expires or GitHub rejects it, Ohara uses the refresh token once. If that fails, the session ends and the user signs in again.

## Sign-out

Signing out deletes the session and clears the cookie.

## Tokens

| Token | Belongs to | Used for |
|---|---|---|
| Installation token, from the app's private key | The app | Downloading the docs and reading repository details |
| User access token, from sign-in | Each user | Checking that the user can read the docs repository |

The docs are always fetched with the app's token. The user's token only decides whether that user may see them.
