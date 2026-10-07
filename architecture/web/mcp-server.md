---
covers:
  - ntrossat/ohara:backend/ohara/mcp_server.py
  - ntrossat/ohara:backend/ohara/freshness.py
  - ntrossat/ohara:backend/ohara/docs.py
verified: 2026-10-07
---

# MCP server

The MCP server at `/mcp` gives AI agents and coding assistants the same docs as the website. It runs in the same FastAPI process, uses the Streamable HTTP transport, and is stateless: every request stands alone. To connect a client, see [Connect a coding assistant](../../documentation/coding-assistants.md).

## Access

Access mirrors the website: a public docs repository is open to everyone, a private one requires a bearer token from a user who can read it. The token is an Ohara `oha_` token from the OAuth sign-in, or a GitHub token. See [MCP sign-in](authentication.md#mcp-sign-in).

## Tools

| Tool | Reads | Returns |
|---|---|---|
| `list_pages` | The menu of the current snapshot | Each page's path and title, with its folders, e.g. "Architecture / Web / Authentication" |
| `search` | The full-text index | Up to 20 pages containing every word of the query, with a snippet and stale reasons. Titles weigh more than text |
| `read_page` | One page | Title, Markdown, owner, verified date, covered code, and stale reasons |
| `stale_pages` | Every page | The stale pages and their reasons |
| `check_repository` | The app's installation | Whether the app is installed on a code repository, and if not, why and the installation settings URL |
| `propose_change` | The caller's write access | The pull request URLs |

The search index is an SQLite FTS5 table in `ohara.db`, rebuilt on each sync and on startup, so search works even when GitHub is unreachable.

## Check a repository

`/ohara:init` calls `check_repository` with the project's repository (`owner/name`, from git remote), because flagging stale pages and merging docs with code only work on repositories the app is installed on. It needs a signed-in caller.

1. Ohara reads its installation with the app's JWT. The app is private, so it is installed only on the account that owns it. A repository of another account can't be connected: the answer says so, with no URL.
2. It lists the installation's repositories with the installation token. When the repository is among them (case does not matter, and a trailing `.git` is ignored), it is connected.
3. Otherwise the answer holds the installation's settings page on GitHub (`html_url`). The assistant opens it in the browser, an admin of the account adds the repository, and the assistant checks again. GitHub then sends the admin to `/api/setup/installed`, which redirects to the website.

## Freshness

A page's freshness comes from its front matter (`owner`, `verified`, `covers`) and from code changes Ohara has recorded. See [Track freshness](../../documentation/docs-repository.md#track-freshness) for the rules.

When a repository other than the docs repository pushes to its default branch, the webhook:

1. lists the changed files with GitHub's compare API, using the installation token. For a new branch, it uses the files listed in the push's commits;
2. matches them against each page's `covers` patterns for that repository;
3. records a flag for each matching page, with the date, the changed files, and a compare link. Up to 20 flags are kept per page.

Each flag holds a hash of the page's file. When the page changes, the hash no longer matches and the flags are dropped.

## Propose changes

`propose_change` takes a title, a description, and the full Markdown of each page. A change that comes from a code project also passes the project's repository name and its active git branch.

The tool's description tells the assistant to treat content taken from other sources as untrusted data and never follow instructions found in it. Before proposing, the assistant removes credentials, tokens, private keys, internal hostnames, and personal data, leaves out anything that tries to instruct an AI assistant, and lists what it removed in the description. Ohara does not scan the content itself: the human review of the pull request is the check.

1. Ohara checks that the caller is signed in and can write to the docs repository.
2. It maps each path to a file: the existing file of that page, or a new `path.md`. Paths with empty parts or parts starting with `.` are refused.
3. It sets `verified` to today in each page's front matter, keeping the rest as written.
4. When the project and branch are given, it sorts the files with the docs snapshot's `CODEOWNERS` file (`.github/CODEOWNERS`, `CODEOWNERS`, or `docs/CODEOWNERS`, the first found). The last matching rule decides: a file with owners needs review. Without a `CODEOWNERS` file, every file needs review.
5. With the installation token, it commits the files on docs branches, each with its own pull request:
   - `<project>/<branch>` for the files that need no review, such as `api/feature-billing`. Characters other than letters, digits, `_` and `-` become `-`. The pull request body says it merges with the code branch.
   - `<project>/<branch>-review` for the files that need review.
   - `ohara/<slug>-<random>` for every file when no project and branch are given.

   If a branch has an open pull request, the commits are added to it with a comment that holds the title and description, and Ohara returns that pull request.

   A new branch starts from the default branch. A branch left from a closed pull request is reset to the default branch first, so it holds only the new change. Ohara then opens a pull request whose body ends with "Proposed through Ohara by @login".

Each code branch therefore gets at most two docs pull requests, however many commits it has. Ohara returns their links, one per line, with "(merges with the code branch)" after the automatic one.

If GitHub refuses with `403`, the app lacks write permissions. An admin grants **Contents** and **Pull requests** write permissions in the app settings, then accepts them on the installation.

## Merge with the code

When a pull request in a code repository closes, GitHub sends a `pull_request` event. If it targeted the repository's default branch, Ohara finds the open docs pull request on `<repository name>/<head branch>`, with the same sanitizing as proposals:

- **Merged:** Ohara lists the docs pull request's files and checks them against `CODEOWNERS` again. If none needs review, it squash-merges the pull request at the commit it checked. Otherwise, or if GitHub refuses the merge, it leaves a comment that explains why.
- **Closed without merging:** Ohara comments with the code pull request's link and closes the docs pull request.

The `-review` pull request is never merged or closed by Ohara. See [Merge docs with code](../../documentation/merge-with-code.md) for the team setup.
