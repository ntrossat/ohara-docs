---
verified: 2026-10-09
---

# Install Ohara

Ohara runs as one Docker container. Setup takes about five minutes and happens in the browser.

## What you need

- Docker with Docker Compose.
- A GitHub account or organization that owns the docs repository, and permission to create a GitHub App there.
- A docs repository on GitHub. It can be empty: Ohara shows how to start.

## Start Ohara

1. Get the code:

   ```bash
   git clone https://github.com/ntrossat/ohara.git
   cd ohara
   cp .env.example .env
   ```

2. In `.env`, set `OHARA_URL` to the address people will use to open Ohara, such as `https://docs.acme.com`. It is the only setting.
3. Start it:

   ```bash
   docker compose up -d
   ```

4. Open `OHARA_URL`. Ohara shows the setup page.

## Connect GitHub

1. **Create the GitHub App.** Enter the organization that owns the docs repository, or leave the field empty for a personal account, then click **Create GitHub App**. GitHub shows the app's name and permissions. Confirm.
2. **Install the app.** Pick the docs repository, and the code repositories Ohara should follow. You can add more later.
3. **Choose the docs repository.** Back in Ohara, pick it and click **Use this repository**. With a single repository, Ohara skips this step.
4. **Done.** Ohara downloads the docs and opens the website.

The app is private: only the account that created it can install it.

## Try it on your computer

Set `OHARA_URL=http://localhost:8000` to try Ohara before you deploy it.

| Feature | On your computer |
|---|---|
| Website and setup | Works |
| Sign-in for coding agents | Works |
| Updates after a merge | GitHub can't reach your computer: the docs update when Ohara restarts |
| Flags when code changes | Don't work |

To move to a real address later, start over with `make init`, which removes the data and creates a new app. Delete the old app on GitHub by hand.

## Next

- [Deploy to production](deploy.md)
- [Connect your coding agent](connect-your-coding-agent.md)
- Reference: [Install](../apps/ohara/install/README.md)
