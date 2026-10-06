---
covers: [ntrossat/ohara:backend/ohara/docs.py, ntrossat/ohara:backend/ohara/freshness.py, ntrossat/ohara:backend/ohara/main.py]
verified: 2026-10-06
---

# Configure a docs repository

Ohara reads its pages from one GitHub repository of Markdown files. This page covers connecting that repository, how to lay it out, how to track freshness, and how updates reach the website.

## Connect the repository

Setup runs once, from the setup page Ohara shows on first launch.

1. **Start Ohara** with the address people will use to open it:

   ```bash
   OHARA_URL=https://docs.acme.com docker compose up -d
   ```

   Open that address. Ohara shows the setup page.

2. **Create the GitHub App.** Enter the organization that owns the docs repository, or leave the field empty for a personal account, then click **Create GitHub App**. GitHub shows the app's name and permissions. Confirm. You can rename the app before confirming.

3. **Install the app on the docs repository.** GitHub asks where to install it. Choose **Only select repositories** and pick the docs repository, and only that one. Setup needs exactly one repository: with more, it asks you to install again.

4. **Done.** Ohara downloads the repository's default branch and opens the website.

The app asks for read access to metadata and write access to contents and pull requests. Ohara only writes to the docs repository to open the pull requests that coding assistants propose, each on its own `ohara/…` branch. It never pushes to the default branch: a human merges every change.

## Lay out the repository

Plain Markdown files, no config file.

| Rule | Example |
|---|---|
| The folder tree is the menu | `guidelines/api.md` shows as "Api" under "Guidelines" |
| A page's title is its front matter `title`, then its first `# ` heading, then its file name | `# API guidelines` titles the page "API guidelines" |
| A folder's `index.md` or `README.md` is the folder's page, and its title names the folder. With both, `index.md` wins | `design/README.md` with `# Design` |
| A folder without an index page is named after the folder | `architecture/` shows as "Architecture" |
| The root `README.md` is the overview, the home page | |
| Files and folders starting with `.` are hidden | `.github/` |
| Folders with no Markdown files are hidden | `assets/` with only images |

Pages are sorted by title. To choose the order, add `order` to the front matter. Lower numbers come first, and pages with an order come before pages without one.

```markdown
---
order: 1
title: Getting started
---

# Getting started
```

Link between pages with relative paths to the Markdown files, like `[style guide](../design/style-guide.md)`. Images and other files in the repository work the same way: `![Logo](logo.svg)`.

## Track freshness

Optional front matter tells Ohara who keeps a page up to date, when a human last checked it, and which code it describes.

```markdown
---
owner: ada
verified: 2026-03-01
covers: [acme/api:src/billing/*, acme/web:src/checkout/*]
---
```

| Field | Meaning |
|---|---|
| `owner` | Who keeps the page up to date |
| `verified` | The date a human last verified the page. Every change proposed through Ohara sets it to the day of the proposal, so merging the change verifies the page |
| `covers` | The code the page describes, as `owner/repository:pattern`. In patterns, `*` also matches across folders |

A page is stale when:

- it was verified more than 180 days ago, or
- a push to the default branch of a covered repository changed files that match its patterns. The flag stays until the page itself changes.

Coding assistants see stale pages and the reasons through the [MCP server](coding-assistants.md), and propose updates for review.

### Add code repositories

To flag pages when code changes, Ohara must receive pushes from the repositories that `covers` names:

1. Finish setup with only the docs repository.
2. In the GitHub App's installation settings, add the code repositories to the same installation.

Ohara only reads which files changed in those repositories. The app's permissions still apply to them, so it could write to them, but Ohara never does.

## Access

The website follows the repository's visibility on GitHub.

| Docs repository | Website |
|---|---|
| Public | Open to everyone, no sign-in |
| Private | Sign in with GitHub. Only people who can read the repository can read the website |

Access is checked again every 5 minutes. Remove someone from the repository on GitHub and they lose the website within 5 minutes. The MCP server follows the same rules. See [Authentication](../architecture/web/authentication.md) for the details.

## Updates

A merge to the default branch updates the website within seconds. GitHub notifies Ohara, and Ohara downloads the new version.

Changes to the repository's settings are picked up the same way. Make the repository public or private and the website follows.

GitHub can only notify an address it can reach over the internet. When `OHARA_URL` is `localhost` or a private address, Ohara updates the docs each time it starts instead, and code changes are not flagged. See [Website deployment](../architecture/web/deployment.md#update-the-docs) for the details.

## Use another repository

The repository is chosen once, at setup. To point Ohara to another one:

1. Delete the Ohara GitHub App in your GitHub settings, under **Developer settings › GitHub Apps** (for an organization, the organization's settings).
2. Remove Ohara's data volume: `docker compose down -v`. This also signs everyone out and disconnects every coding assistant.
3. Start Ohara again and follow the setup above.
