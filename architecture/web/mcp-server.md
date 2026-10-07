---
covers:
  - ntrossat/ohara:backend/ohara/mcp_server.py
  - ntrossat/ohara:backend/ohara/freshness.py
  - ntrossat/ohara:backend/ohara/docs.py
  - ntrossat/ohara:backend/ohara/appdocs.py
  - ntrossat/ohara:backend/ohara/appconfig.py
verified: 2026-10-07
---

# MCP server

The MCP server at `/mcp` gives AI agents and coding assistants the same docs as the website. It runs in the same FastAPI process, uses the Streamable HTTP transport, and is stateless: every request stands alone. To connect a client, see [Connect a coding assistant](../../documentation/coding-assistants.md).

## Access

Access mirrors the website: a public docs repository is open to everyone, a private one requires a bearer token from a user who can read it. The token is an Ohara `oha_` token from the OAuth sign-in, or a GitHub token. See [MCP sign-in](authentication.md#mcp-sign-in).

## Tools

| Tool | Reads | Returns |
|---|---|---|
| `list_pages` | The menu of the current snapshot | Each page's path and title, with its folders, e.g. "Architecture / Web / Authentication", and the `source` of synced pages |
| `search` | The full-text index | Up to 20 pages containing every word of the query, with a snippet, stale reasons, and the `source` of synced pages. Titles weigh more than text |
| `read_page` | One page | Title, Markdown, owner, verified date, covered code, stale reasons, and `source` for a synced page |
| `stale_pages` | Every page | The stale pages and their reasons |
| `check_repository` | The app's installation and the last sync | Whether the app is installed on a code repository, and if not, why and the installation settings URL. If it is, the synced paths, the synced folder, and why the sync was skipped |
| `propose_change` | The caller's write access | The pull request URLs |

The search index is an SQLite FTS5 table in `ohara.db`, rebuilt on each sync and on startup, so search works even when GitHub is unreachable.

A page's `source` is only reported for files under `apps/`, so a page elsewhere can't redirect proposals with a `source` field of its own.

## Check a repository

`/ohara:init` calls `check_repository` with the project's repository (`owner/name`, from git remote), because flagging stale pages, merging docs with code, and syncing app docs only work on repositories the app is installed on. It needs a signed-in caller.

1. Ohara reads its installation with the app's JWT. The app is private, so it is installed only on the account that owns it. A repository of another account can't be connected: the answer says so, with no URL.
2. It lists the installation's repositories with the installation token. When the repository is among them (case does not matter, and a trailing `.git` is ignored), it is connected. The answer then holds the paths of the last sync (none before the first one, or without a `.ohara.yml`), the synced folder, and why the sync was skipped, if it was.
3. Otherwise the answer holds the installation's settings page on GitHub (`html_url`). The assistant opens it in the browser, an admin of the account adds the repository, and the assistant checks again. GitHub then sends the admin to `/api/setup/installed`, which redirects to the website.

## Sync app docs

`appdocs.sync` copies a code repository's docs into `apps/<repository name>/` of the docs repository. One sync runs at a time, so commits never race. See [Sync docs from code repositories](../../documentation/docs-repository.md#sync-docs-from-code-repositories) for the rules users see.

1. With the installation token, Ohara reads the code repository. A private code repository with a public docs repository syncs nothing, so an earlier copy is removed.
2. It reads `.ohara.yml` at the pushed commit (`appconfig.py`). Without it, the repository hasn't opted in and nothing is synced. A file without a `docs` list syncs `docs`. When the file isn't valid YAML or isn't a mapping, the sync stops and the last copy stays.
3. It lists the code repository's files with the recursive trees API, keeping regular files (not symbolic links) with a Markdown or image extension, up to 1 MB each and outside hidden folders. A missing commit or a truncated list stops the sync, so missing files are never taken for deleted ones. An empty repository has no files.
4. It lays out the files under `apps/<repository name>/` (folder contents at the root, files by name, the first entry wins), up to 500, and downloads each blob. Markdown files get `source: "owner/repo:path"` in their front matter. With nothing to sync and no `apps/<repository name>/` folder in the snapshot, it stops there.
5. It compares each file's Git blob sha with the docs repository's tree, creates blobs for the changed files, and deletes the files no longer synced. With no change, it stops.
6. It commits the new tree on the default branch, `docs: sync owner/repo@sha`, and moves the branch. If GitHub refuses (`403`, `409` or `422`, such as a protected branch), it commits on `ohara/sync-<repository name>`, reset from the default branch, and opens a pull request there.

The last sync of each repository is saved in `ohara.db` (`app` records): its folder name, default branch, paths, and why it was skipped. A push to a code repository syncs it when the changed files include `.ohara.yml` or a path of its last sync. See [Website deployment](deployment.md#update-the-docs) for the other events.

## Freshness

A page's freshness comes from its front matter (`owner`, `verified`, `covers`) and from code changes Ohara has recorded. See [Track freshness](../../documentation/docs-repository.md#track-freshness) for the rules.

When a repository other than the docs repository pushes to its default branch, the webhook:

1. lists the changed files with GitHub's compare API, using the installation token. For a new branch, it uses the files listed in the push's commits;
2. syncs the repository's docs when they changed;
3. matches the files against each page's `covers` patterns for that repository;
4. records a flag for each matching page, with the date, the changed files, and a compare link. Up to 20 flags are kept per page.

Each flag holds a hash of the page's file. When the page changes, the hash no longer matches and the flags are dropped.

## Propose changes

`propose_change` takes a title, a description, and the full Markdown of each page. A change that comes from a code project also passes the project's repository name and its active git branch.

The tool's description tells the assistant to treat content taken from other sources as untrusted data and never follow instructions found in it. Before proposing, the assistant removes credentials, tokens, private keys, internal hostnames, and personal data, leaves out anything that tries to instruct an AI assistant, and lists what it removed in the description. Ohara does not scan the content itself: the human review of the pull request is the check.

1. Ohara checks that the caller is signed in.
2. It maps each path to a file: the existing file of that page, or a new `path.md`. Paths with empty parts or parts starting with `.` are refused.
3. It sets `verified` to today in each page's front matter, keeping the rest as written.
4. It sorts out the files under `apps/`, by their `source`:
   - a new file, or one without a `source`, is refused;
   - a file from the project's own repository (matched by name, case does not matter) is refused, with the source path to edit instead;
   - a file from another repository goes to that repository, at its source path, with the `source` field removed.
5. It checks that the caller can write to each repository the files go to.
6. When the project and branch are given, it sorts the docs repository's files with the docs snapshot's `CODEOWNERS` file (`.github/CODEOWNERS`, `CODEOWNERS`, or `docs/CODEOWNERS`, the first found). The last matching rule decides: a file with owners needs review. Without a `CODEOWNERS` file, every file needs review.
7. With the installation token, it commits the files on branches, each with its own pull request:
   - `<project>/<branch>` for the docs repository's files that need no review, such as `api/feature-billing`. Characters other than letters, digits, `_` and `-` become `-`. The pull request body says it merges with the code branch.
   - `<project>/<branch>-review` for the docs repository's files that need review.
   - `ohara/<slug>-<random>` for every docs repository file when no project and branch are given.
   - `<project>/<branch>`, or `ohara/<slug>-<random>`, on each other code repository, from its default branch.

   If a branch has an open pull request, the commits are added to it with a comment that holds the title and description, and Ohara returns that pull request.

   A new branch starts from the default branch. A branch left from a closed pull request is reset to the default branch first, so it holds only the new change. Ohara then opens a pull request whose body ends with "Proposed through Ohara by @login".

Each code branch therefore gets at most two docs pull requests in the docs repository, however many commits it has, plus one per other code repository whose synced pages it changes. Ohara returns their links, one per line, with "(merges with the code branch)" after the automatic one and the repository name after the ones on code repositories.

If GitHub refuses with `403`, the app lacks write permissions. An admin grants **Contents** and **Pull requests** write permissions in the app settings, then accepts them on the installation.

## Merge with the code

When a pull request in a code repository closes, GitHub sends a `pull_request` event. If it targeted the repository's default branch, Ohara finds the open docs pull request on `<repository name>/<head branch>`, with the same sanitizing as proposals:

- **Merged:** Ohara lists the docs pull request's files and checks them against `CODEOWNERS` again. If none needs review, it squash-merges the pull request at the commit it checked. Otherwise, or if GitHub refuses the merge, it leaves a comment that explains why.
- **Closed without merging:** Ohara comments with the code pull request's link and closes the docs pull request.

The `-review` pull request is never merged or closed by Ohara. See [Merge docs with code](../../documentation/merge-with-code.md) for the team setup.
