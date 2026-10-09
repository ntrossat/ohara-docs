---
verified: 2026-10-09
---

# Ohara - The Tree of Knowledge

**One central place for all enterprise knowledge.**

AI keeps your docs up to date. You approve every change.

![The Ohara flow](resources/ohara-flow.mp4)

**Ohara keeps your docs and engineering rules in one place, for your team and for your AI assistants.** Your team reads them on a website, and asks questions or proposes updates from any chat or agent. Your coding assistants follow the approved rules when they write code, and propose a doc update each time they change the code.

Ohara is open source. It runs on your own servers, and your docs stay in your GitHub repository.

*You are reading Ohara now: this site runs on it.*

## Why teams use Ohara

- **Every doc and rule lives in one place.** Ohara imports what you already have.
- **Architects write the rules once.** Every assistant, in every project, follows them.
- **The assistant updates the docs in the same change as the code.** Ohara flags the pages that fall behind.

## Quick setup

1. **Start Ohara.** Clone the code, set `OHARA_URL` in `.env`, and run it:

   ```bash
   git clone https://github.com/ntrossat/ohara.git
   cd ohara
   cp .env.example .env
   docker compose up -d
   ```

2. **Connect GitHub.** Open `OHARA_URL`. The setup page creates a GitHub App, installs it on your docs repository, and opens the website.
3. **Connect your coding assistant,** then run `/ohara:init` in each project:

   ```bash
   claude mcp add --transport http ohara <OHARA_URL>/mcp
   ```

See [Install](apps/ohara/install/README.md) for HTTPS, local runs, and the GitHub App's permissions.

## Authentication

Ohara uses GitHub sign-in only. There are no Ohara accounts or passwords to manage.

Ohara copies the access rights of the docs repository on GitHub:

- **Public repository:** everyone can read the docs, without signing in.
- **Private repository:** only people who can read the repository can read the docs.
- **Proposing a change** needs write access to the repository.

To give someone access, add them to the repository on GitHub. To remove access, remove them there: they lose access within 5 minutes.

The same rules apply on the website, in coding assistants, and in claude.ai and ChatGPT.

## Add an existing code repository

1. **Run `/ohara:init` in the project** from your coding assistant. It:

   - checks that the GitHub App is installed on the repository, and opens the app's settings on GitHub when it isn't, so you can add it. Ohara only reads the repository, and never writes to it;
   - finds the rules and docs that apply to the project;
   - adds the Ohara server to `.mcp.json`, so the whole team gets it;
   - writes `.claude/rules/ohara.md`, which Claude Code loads like `CLAUDE.md` (other agents: a section of `AGENTS.md`), with those pages and the workflow;
   - allows the read-only Ohara tools in `.claude/settings.json`, so only proposals ask for confirmation.

   Running it again is safe: it replaces its own earlier setup.

2. **Optional: keep the docs next to the code.** Add a `.ohara.yml` at the root of the repository:

   ```yaml
   docs:
     - docs
   ```

   Ohara shows those docs under `apps/<repository name>/`, and syncs them on each push to the default branch.

See [Code repositories](apps/ohara/configure/code-repositories.md) for the details.

## Import existing documentation

Ohara imports docs from Confluence, Jira, Google Drive, GitHub, files, and URLs, from a coding assistant or a chat app.

1. **Connect the tool that holds the docs,** such as Confluence or Google Drive, next to Ohara.
2. **Ask for the import.** In Claude Code, run `/ohara:ingest` and name the sources. In claude.ai or ChatGPT, ask, for example: "Import the Engineering space from Confluence into Ohara."
3. **Approve the plan.** The assistant maps what already exists and proposes a structure.
4. **Review and merge the pull requests.** It opens one per folder or topic. Each lists its sources and the secrets and personal data it removed.

Importing needs write access to the docs repository. See [Configure your AI assistant](documentation/configure-your-ai-assistant.md) to connect claude.ai, ChatGPT, or another chat app.

## About this site

This site is the Ohara project's own docs, served by Ohara. The folder tree is the menu.

| Folder | Holds |
|---|---|
| `guidelines/` | Engineering rules every project follows |
| `apps/ohara/` | Product docs, synced from the [ohara code repository](https://github.com/ntrossat/ohara) |
| `design/` | Brand, style guide, UI kit, and design tokens |
| `documentation/` | Step-by-step guides: install, deploy, connect agents and chat apps, import docs |
| `references/` | Reference material, such as the FastAPI user guide |

## License

[Apache-2.0](https://github.com/ntrossat/ohara/blob/main/LICENSE)
