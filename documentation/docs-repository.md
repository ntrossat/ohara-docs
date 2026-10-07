---
covers:
  - ntrossat/ohara:backend/ohara/docs.py
  - ntrossat/ohara:backend/ohara/freshness.py
  - ntrossat/ohara:backend/ohara/main.py
  - ntrossat/ohara:backend/ohara/appdocs.py
  - ntrossat/ohara:backend/ohara/appconfig.py
verified: 2026-10-07
---

# Configure a docs repository

Ohara reads its pages from one GitHub repository of Markdown files. This page covers connecting that repository, how to lay it out, how to track freshness, how docs from code repositories join it, and how updates reach the website.

## Connect the repository

Setup runs once, from the setup page Ohara shows on first launch.

1. **Start Ohara** with the address people will use to open it:

   ```bash
   OHARA_URL=https://docs.acme.com docker compose up -d
   ```

   Open that address. Ohara shows the setup page.

2. **Create and install the GitHub App.** Enter the organization that owns the docs repository, or leave the field empty for a personal account, then click **Create GitHub App**. GitHub shows the app's name and permissions. Confirm. You can rename the app before confirming. GitHub then asks where to install it: pick the docs repository, and the code repositories whose changes should update the docs.

3. **Choose the docs repository.** GitHub sends you back to the setup page, which lists the repositories you picked. Choose the one that holds the docs and click **Use this repository**. With a single repository, Ohara skips this step. If GitHub doesn't send you back, click **Check again** on the setup page.

4. **Done.** Ohara downloads the repository's default branch and opens the website.

The app asks for read access to metadata and write access to contents and pull requests. Ohara writes to the docs repository to open the pull requests that coding assistants propose, to merge or close the ones tied to a code branch when that code merges or closes, and to commit the docs it syncs from code repositories into `apps/`. It commits nothing else to the default branch. Folders listed in a `CODEOWNERS` file always need a human review, see [Merge docs with code](merge-with-code.md).

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
| `apps/` is reserved for docs synced from code repositories | `apps/api/` |

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

To flag pages when code changes, to merge docs pull requests with their code branch, and to sync a code repository's own docs, Ohara must receive events from the code repositories. Install the app on them at setup, next to the docs repository, or add them later in the GitHub App's installation settings. `/ohara:init` checks this for a project and opens those settings when the app is missing.

Ohara reads which files changed in those repositories, which of their pull requests closed, and the docs it syncs. It writes to a code repository only to open the pull requests that coding assistants propose for its synced pages.

## Sync docs from code repositories

A code repository can keep its own docs next to the code, so they change in the same pull request. Ohara syncs them into `apps/<repository name>/` of the docs repository, one way:

- On each push to the code repository's default branch that changes its docs, Ohara replaces the whole folder in one commit, `docs: sync owner/repo@sha`. Renames and deletions carry over.
- When the app is added to a code repository, Ohara syncs it. When it is removed, Ohara deletes the folder.
- Ohara syncs Markdown files and images (`png`, `jpg`, `gif`, `svg`, `webp`) up to 1 MB each, and up to 500 files per repository. Hidden files and symbolic links are skipped.
- Each synced page gets a `source` field in its front matter, such as `source: "acme/api:docs/billing.md"`. The website's edit link and coding assistants' proposals go to that file.
- When the default branch is protected, Ohara opens an `ohara/sync-<repository name>` pull request instead of committing. Merge it as is.

`apps/` belongs to the sync. Edit synced pages in their code repository: a hand edit in the docs repository is overwritten by the next sync, and a hand-made folder named after a connected repository is replaced by that repository's docs.

### Choose what to sync

Without a config file, Ohara syncs the code repository's `docs/` folder. To sync other paths, add `.ohara.yml` at the root of the code repository:

```yaml
docs:
  - documentation
  - README.md
```

| `.ohara.yml` | Synced |
|---|---|
| Missing | `docs/` |
| `docs: [...]` | The listed folders and files |
| `docs: []` | Nothing. The synced folder is removed |

- The contents of a listed folder go to the root of `apps/<repository name>/`: `documentation/billing.md` becomes `apps/api/billing.md`.
- A listed file goes to the root by its name: `README.md` becomes `apps/api/README.md`, the folder's page.
- When two entries land on the same path, the first one in the list wins.
- Absolute paths and paths with `..` are ignored. If the file isn't valid YAML, Ohara keeps the last synced copy and logs the error.

### Access to synced docs

Synced docs follow the docs repository's access: everyone who can read it can read them, even without access to the code repository.

Ohara never syncs a private code repository into a public docs repository, since the website would publish its docs. It logs the reason, and `check_repository` reports it. When a private docs repository is made public, Ohara removes the folders of private code repositories. Their files stay in the docs repository's git history: rewrite that history before making the repository public if they must not be seen.

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

GitHub can only notify an address it can reach over the internet. When `OHARA_URL` is `localhost` or a private address, Ohara updates the docs each time it starts instead, and code changes are not flagged or synced. See [Website deployment](../architecture/web/deployment.md#update-the-docs) for the details.

## Use another repository

The repository is chosen once, at setup. To point Ohara to another one:

1. Delete the Ohara GitHub App in your GitHub settings, under **Developer settings › GitHub Apps** (for an organization, the organization's settings).
2. Remove Ohara's data volume: `docker compose down -v`. This also signs everyone out and disconnects every coding assistant.
3. Start Ohara again and follow the setup above.
