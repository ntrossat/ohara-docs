---
order: 1
verified: 2026-11-26
---

# Installation

Ohara runs as one Docker container. Setup takes about five minutes and happens in the browser.

## What you need

- Docker with Docker Compose.
- A GitHub account or organization on github.com that owns the docs repository, and permission to create a GitHub App there.
- A docs repository on GitHub. It can be empty: Ohara shows how to start.
- For a team: an `https://` address that GitHub can reach. Without one, Ohara still runs on your computer, and updates when it restarts.

## Start Ohara

1. Download the Compose file, which runs the published image:

   ```bash
   curl -fsSLO https://raw.githubusercontent.com/ntrossat/ohara/main/compose.yml
   ```

2. Set the address people will use to open Ohara, in a `.env` file next to `compose.yml`. Skip this step to try Ohara at `http://localhost:8000`:

   ```bash
   echo "OHARA_URL=https://docs.acme.com" > .env
   ```

3. Start it:

   ```bash
   docker compose up -d
   ```

4. Open `OHARA_URL`. Ohara shows the setup page.

`OHARA_URL` is the only setting. Everything else is configured in the browser and saved in the container's data volume.

## Connect GitHub

1. **Create the GitHub App.** Enter the organization that owns the docs repository, or leave the field empty for a personal account, then click **Create GitHub App** and confirm on GitHub.
2. **Install the app.** Pick the docs repository, and the code repositories Ohara should follow. You can add more later.
3. **Choose the docs repository.** Back in Ohara, pick it and click **Use this repository**. With a single repository, Ohara skips this step.

Ohara then downloads the docs and opens the website. A public docs repository makes a public website. A private one asks readers to sign in with GitHub, and only those who can read the repository get in.

## Update

```bash
docker compose pull && docker compose up -d
```

The data volume keeps the settings, so there is no setup to redo.

## Go further

| You want to | Read |
|---|---|
| Follow the installation step by step | [Install Ohara](documentation/install-ohara.md) |
| Run it on a server your team can reach | [Deploy to production](documentation/deploy.md) |
| Use Ohara from Claude Code or another coding agent | [Connect your coding agent](documentation/connect-your-coding-agent.md) |
| Ask questions from claude.ai, ChatGPT, or another chat app | [Configure your AI assistant](documentation/configure-your-ai-assistant.md) |
| Bring in docs from other tools | [Import existing docs](documentation/import-existing-docs.md) |
| Look up every setting, HTTPS, and paths | [Install reference](repositories/ohara/install/README.md) |
