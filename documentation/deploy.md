---
verified: 2026-10-09
---

# Deploy to production

In production, Ohara needs an `https://` address that GitHub can reach. GitHub then notifies Ohara of each merge and push, and coding agents can sign in.

## Serve Ohara over HTTPS

1. Run Ohara on a server, as in [Install Ohara](install-ohara.md), with `OHARA_URL` set to its public address.
2. Put a reverse proxy in front that handles TLS and forwards to port `8000`. Ohara trusts the proxy's `X-Forwarded-*` headers.

With an `https://` address, Ohara marks its cookies `Secure` and turns on sign-in for coding agents.

To serve Ohara under a path, include it in the address, such as `OHARA_URL=https://acme.com/docs`. The website, the API, and the MCP server all move under that path.

## Keep the data

All state lives in the data volume, mounted at `/data`: the GitHub App's credentials, sign-ins, and the search index. The docs themselves stay in GitHub.

- **Treat the volume as a secret.** It holds the app's private key and users' GitHub tokens.
- **Back it up.** A lost volume only costs the setup and the sign-ins: start again with the setup page.
- `docker compose down -v` deletes the volume and resets Ohara to the setup page.

## Update Ohara

```bash
git pull
docker compose up -d --build
```

Settings, sign-ins, and docs survive the update.

## Deploy each change automatically

Each push to `main` that passes CI publishes an image to `ghcr.io/<owner>/ohara`. To deploy it too, set these in the repository's **Settings › Secrets and variables › Actions**:

| Setting | Kind | Content |
|---|---|---|
| `DEPLOY_COMMAND` | Variable | A shell script that deploys, with `$IMAGE` set to the published image |
| `DEPLOY_TOKEN` | Secret | The credential the script needs, available as `$DEPLOY_TOKEN` |

For example, a host with a deploy hook:

```bash
curl -fsS -X POST "https://deploy.example.com/hooks/ohara?image=$IMAGE" -H "Authorization: Bearer $DEPLOY_TOKEN"
```

## When something goes wrong

| Symptom | Fix |
|---|---|
| The website doesn't update after a merge | GitHub can't reach Ohara. Check that `OHARA_URL` is public, and look at **Recent Deliveries** in the app's settings on GitHub. Restarting Ohara also updates the docs |
| Ohara answers "Ohara is not set up yet" | Open `OHARA_URL` and finish the setup |
| Coding agents can't sign in | Sign-in needs an `https://` address or `localhost` |

Read the logs with `docker compose logs -f`.

## Next

- [Connect your coding agent](connect-your-coding-agent.md)
- Reference: [Operate](../apps/ohara/install/operate.md)
